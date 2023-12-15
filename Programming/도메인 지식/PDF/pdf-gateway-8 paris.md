# PDF 변환 솔루션 Paris 버전 소스 분석

이파피루스에서 제공하는 대표적인 솔루션 중 하나인 pdf-gateway-8 Paris 버전에 대해서 간단하게 소스 코드 분석을 통해서 리뷰를 하도록 정리한 md 파일입니다.

이 솔루션은 간단하게 MS Office, hwp, html과 같은 정적인 문서를 pdf로 변환을 해주는 애플리케이션입니다. 변환 방식은 2가지가 있는데 첫 번째로는 DB 연계방식과 폴더 연계방식이 존재합니다. 여기서는 대표적으로 많이 쓰이는 DB 연계 방식에 대해서 살펴보겠습니다. DB 연계 방식으로 pdf를 변환하기 위해서는 DB table 중에서 JOB_CONV 테이블에 식별자인 기본키 컬럼인 jobId, 원본 파일의 경로인 srcFile, 변환 파일을 업로드할 목적지 경로인 destFile 컬럼에 insert를 하나의 작업단위로 생각해야 합니다.



## DBJobTicketManager 객체의 역할은 무엇인가?
위에서 말한 JOB_CONV 테이블은 애플리케이션 레벨에서 JobTicket 객체와 매핑됩니다.
이러한 JobTicket을 어떻게 DB에서 가져오고 관리하는지는 JobTicektManager 객체가 역할을 담당하고 있습니다.

하지만 pdf 변환 요청은 DB 연계와 폴더 연계 방식이 존재하기 때문에 JobTicketManager는 추상화를 위해 인터페이스로 빼놓고 실제로 DB에서 JOB_CONV 테이블의 작업 단위를 JobTicet 객체와 바인딩하고 관리하는 역할은 JobTicektManager의 구현체인 DBJobTicektManager가 수행합니다.

```java
public interface JobTicketManager<T> {
	
	void publish();
	
	T getRecentJobTicket();
	
	boolean hasActiveJobTicket();

}
```


가져온 JobTicket을 처리하는 역할을 하는 객체는 JobHandler라는 객체가 하고 있습니다. 

가장 먼저, 이 프로젝트에서는 서버가 켜지면 스프링 스케줄러에 의해서 아래와 같이 4초를 주기로 DB에서 select 쿼리를 요청하여 가져오도록 설정되어 있습니다.


```properties
//gateway-pdf.properties
// 초 분 시 일 월 요일  0/4은 0초에 시작해서 4초 간격으로 동작하라는 뜻입니다. 
job.ticket.publish.schedule.cron = 0/4 * * * * ?+


// @Scheduled 및 @Async Annotation을 활성화 하기위하여 "<annotation-driven>" Element를
 추가해야한다.
<task:annotation-driven executor="executor"/>

// ThreadPoolTaskExecutor 인스턴스를 정의합니다.
<task:executor id="executor" rejection-policy="DISCARD" 
		pool-size="${job.handler.thread.max.active.size}" queue-capacity="${job.handler.thread.queue.capacity}"/>

// 스프링 스케줄러에 의해서 4초를 주기로 publish() 메소드를 수행되도록 설정됨.
// 일반적으로 태스크를 구현하려면 Runnable 인터페이스를 구현하여 빈으로 등록하고 스케줄러에 등록하는 기능 구현하고 이러한 등록 기능이 실행 되도록 해야하는 등 번잡한 과정을 구현 해야한다.
scheduled-tasks를 이용하면 일반 빈의 메소드를 태스크로 활용할 수 있다.

<task:scheduled-tasks scheduler="scheduler">
<task:scheduled ref="${job.ticket.manager}" method= "publish" cron="${job.ticket.publish.schedule.cron}" />	
</task:scheduled-tasks>
```

위의 설정파일을 보면 


`${job.ticket.manager}`은 properties 파일에서 정의된 값을 가져올 때 사용하는 표현식으로 현재는 DB 연계 방식이기 때문에 실제로 dbJobTicetManager로 등록되어 있습니다.

DBJobTicketManager.publish() 메소드가 최초로 호출되면 @PostConsturctor가 적용된 initJobStatus() 메소드를 호출하여 DB JOB_CONV 테이블에서 status 컬럼값이 'S', 'F'를 제외한 모든 상태 값을 'W'로 update를 수행하는 쿼리를 요청합니다. 상태값은 pdf 변환 요청에 대한 성공 여부를 말하는 것으로 정상적으로 변환이 되었다면 'S'로 수록이 되어야합니다. 실패면 'F'로 수록됩니다. 하지만 작업도중에 서버가 내려가거나 DB에 문제가 생겨서 중간에 상태값이 'S' 혹은 'F'로 변경이 안되고 `R,C,I`로 존재할 수 있기 때문에 이 값들을 처리할 수 있도롟 'W'로 값을 변경해야 합니다.


## publish() 메소드

이 publish() 메소드는 pdf-gateway.xml에 정의되어 있는 스케줄러에 의해서 0초를 기준으로 4초마다 수행되도록 설정되어 있습니다. DB에서 변환 요청을 JobTicet 객체 요소를 가진 List 컬렉션 객체를 얻어와서 ArrayBlockingQueue 객체에 조건에 따라 값을 넣는 책임을 가진 메소드입니다.

```java
@Component
public class DbJobTicketManager implements JobTicketManager<JobTicket> {


    //512개의 사이즈를 가진 ArrayBlockingQueue 객체  
    //FIFO 구조이며 큐의 크기가 Full이거나  데이터를 꺼낼때 존재하지 않을 시에 더 이상 삽입이나 꺼내는것을 막도록 구현됨
	@Resource(name = "jobTicketQueue")
	private ArrayBlockingQueue<JobTicket> jobTicketQueue;
    
    // PdfConvDaoImpl 빈 주입
	@Resource(name = "pdfConvDao")
	private PdfConvDao pdfConvDao;

    // ThreadPoolTaskExecutor 빈 주입
	@Resource(name = "executor")
	private ThreadPoolTaskExecutor executor;


	//512로 설정
	@Value("${job.ticket.queue.capacity}")
	private int jobTicketQueueCapacity;

    // 현재는 DBJobTicketManager 인 상태
	@Value("${job.ticket.manager}")
	private String jobTicketManager;


    // 설정파일을 통한 추가 작업에 대한 정보가 들어있는 설정 변수
	@Value("${common.extra.jobs}")
	private String commonExtraJobs;

    // 애플리케이션 수행 시 해당 객체가 생성자 호출 후 초기화 된 후 최초로 호출 되는 메소드
	// 해당 메소드가 서버 시작할 때 시작하고 싶을 때 @PosrtConstruct를 사용하면 WAS가 띄워질 때 실행된다.
	@PostConstruct	
	private void initJobStatus() {
				
		if (StringUtils.equalsIgnoreCase(jobTicketManager, "dbJobTicketManager")) {
			// 서버가 내려가거나 db 문제로 인해 기존에 jobstatus 값이 R,C,I 인 상태 값을 처리 가능하도록 W로 변경하는 쿼리 수행
			Status result = pdfConvDao.initJobStatus();

			if (result == Status.EMPTY) {
				SdpLogger.info("DbJobTicketManager-initJobStatus INFO: 초기화할 작업이 없습니다.", DbJobTicketManager.class);
			} else if (result == Status.FAIL) {
				SdpLogger.error("DbJobTicketManager-initJobStatus ERROR: DB의 초기화 작업에 실패하였습니다.", DbJobTicketManager.class);
			}			
		}
	}

// 4초마다 스프링 스케줄러에 의해 수행되는 메소드
	// DB로부터 JobTicket을 가지고 있는 리스트 객체 반환 후 큐에 넣는 책임을 가짐
	@Override
	public void publish() {

		if (StringUtils.notEqIgnoreCase(jobTicketManager, "dbJobTicketManager")) {
			SdpLogger.error("DbJobTicketManager-publish ERROR: 현재 JobTicketManager의 설정이 dbJobTicketManager가 아닌 "	
				+ jobTicketManager + "입니다. dbJobTicketManager로 설정을 수정하세요.", getClass());
			
			return;
		}
        

		// 큐에서 저장 할 수 있는 크기를 리턴(남는 공간). 
        // 큐의 공간이 남아있을 경우 true
		if (jobTicketQueue.remainingCapacity() > 0) {
		
			// JOB_CONV 테이블에서 JobTicket ROW를 100개씩 리스트로 가져오는 메소드
			List<JobTicket> jobTicketList = pdfConvDao.getJobTicketList();
			
			if (jobTicketList == null) {
				SdpLogger.error("DbJobTicketManager-publish ERROR: SQL문 수행 중에 Runtime Exception이 발생하였습니다.", getClass());
			}

			// DB에서 조회한 JobTicketList 컬렉션에 있는 JobTicket 객체 요소들을 순회하면서 큐에 해당 객체를 저장하는 for문
			for (JobTicket jobTicket : jobTicketList) {
				// 큐의 크기가 풀이면 (512) 더 이상 큐에 JobTicket 객체를 넣을 수 없기 때문에 해당 메소드를 종료시킵니다.
				if (jobTicketQueue.size() == jobTicketQueueCapacity) {
					return;
				}
				
				try {				
					// 추가 작업을 수행하는 메소드
					addCommonExtraJobs(jobTicket);
					// 해당 JobTicket의 상태값 W -> R로 업데이트
					Status result = pdfConvDao.updateJobStatus(jobTicket.getJobId(), JobStatus.READY);
					
					if (result == Status.SUCCESS) {
						//SUCCESS시에 큐에 JobTicket 저장함.
						jobTicketQueue.put(jobTicket);
						
					} else if (result == Status.FAIL) {
						SdpLogger.error("DbConvertJobTicketManager-publish ERROR: " 
							+ "jobId[" + jobTicket.getJobId() + "]의 Ready 상태 갱신에 실패하였습니다.", getClass());
						continue;
					}
					
				} catch (InterruptedException e) {				
					
					Status result = pdfConvDao.updateJobStatus(jobTicket.getJobId(), JobStatus.WAIT);
					
					if (result == Status.FAIL) {
						SdpLogger.error("DbConvertJobTicketManager-publish ERROR: " 
							+ "jobId[" + jobTicket.getJobId() + "]의 Wait 상태 갱신에 실패하였습니다.", getClass());
					}
					
					SdpLogger.error("DbConvertJobTicketManager-publish ERROR: " 
							+ "jobId를 queue에 저장하는 동안 에러가 발생하였습니다. 상세에러 -> " + e.getMessage(), getClass());
					continue;				
				}
			
			}	
		}
		
	}
	
	
	// commonExtraJobs 공통 작업이 정의 되어 있는 설정 값입니다.
    //예를 들어서 문서 5개를 pdf로 변환 시에 5개 다 공통적으로 수행되는 추가작업이 있을때 수행되는 메소드입니다. 설정파일에 따로 값을 넣어줘야됩니다.
	// 실제로 db 연계할때 insert로 넣는 extraJob은 process() 메소드에서 수행됩니다.
	private void addCommonExtraJobs(JobTicket jobTicket) {
		//commonExtraJobs 값이 있을 때 true 리턴
		if (StringUtils.isNotEmpty(commonExtraJobs)) {
			System.out.println("extraJobs: " + commonExtraJobs);
            // DB Extrajobs 컬럼에도 값이 있을 경우가 존재함
			String extraJobs = jobTicket.getExtraJobs();
			
            // db에 넣은게 없다면 commonExtraJobs 값을 JobTicket 객체의 Extrajobs로 설정합니다.
			if (StringUtils.isEmpty(extraJobs)) {
				
				jobTicket.setExtraJobs(commonExtraJobs);
				
			} else {
				// commonExtraJobs, JOB_CONV 테이블 둘다 추가작업이 존재할 경우
				String[] extraJobsArray = StringUtils.split(commonExtraJobs, "|");
				
				for (String extraJob : extraJobsArray) {
					
					if (!extraJob.matches(extraJobs)) {
						
						extraJobs += "|" + extraJob;
					}	
				}
				
				jobTicket.setExtraJobs(extraJobs);
			}
		}
	}

	// ArrayBlocking Queue 객체에서 가장 먼저 들어온 JobTicket 객체를 꺼내는 메소드, 꺼낸 후에 제거합니다.
	@Override
	public JobTicket getRecentJobTicket() {
		try {
			return jobTicketQueue.take();
		} catch (InterruptedException e) {
			SdpLogger.error("DbConvertJobTicketManager-getRecentJobTicket ERROR: " 
				+ "jobTicket을 가져오는 중에 에러가 발생하였습니다. 상세내용-> " + e.getMessage(), getClass());
			return null;
		}
	}

	// 스레드풀의 스레드가 수행하는 작업의 수가 8개 이하이거나 큐의 크기가 0이상일때 true를 리턴합니다.
	@Override
	public boolean hasActiveJobTicket() {
		
		SdpLogger.debug("DbJobTicketManager-hasActiveJobTicket INFO: ThreadPool ActiveCount: " 
			+ String.valueOf(executor.getActiveCount()), getClass());
		
		return (jobTicketQueue.size() > 0) && (executor.getActiveCount() < handlerMaxActiveSize) ? true : false;
	}	
}
```

## JobDispatcher의 역할
JobDispatcher 객체는 스프링 컨테이너에 빈으로 등록되어 있는 객체로 쓰레드 풀이 관리하는 쓰레드가 실제로 수행하는 task가 정의되어 있는 메소드를 가지고 있습니다.


```java
@Component
@SuppressWarnings({ "rawtypes", "unchecked" })
public class JobDispatcher implements ServiceController {
	
	@Resource(name = "${job.ticket.manager}")
	private JobTicketManager jobTicketManager;
	
	@Resource(name = "${job.handler}")
	private ServiceHandler jobHandler;

	// 스케줄러에 의해서 0.25초 마다 수행되는 메소드
	@Scheduled(fixedRateString = "${job.ticket.consume.fixed.rate}")
	public void process() {

		if (jobTicketManager.hasActiveJobTicket()) {
	
			jobHandler.handle(Status.START);
		}
	}
}
```

JobDispatcher는 인스턴스 변수로 JobTicketManager, ServiceHandler 타입의 객체를 가지고 있지만, 실제로 DB 연게 방식이기 때문에 해당 객체타입은 추상화되어 있습니다. 자바의 다형성을 이용하여 실제로 주입되는 빈의 타입은 DBJobTicketManager 객체와 DBJobHandler 객체입니다.

여기서 가장 중요한 메소드는 `process() 메소드로 실제로 원본 파일을 pdf로 변환해서 목적지 경로까지 업로드 해주는 작업이 정의되어 있는 JobHandler 객체의 handler() 메소드를 스케줄러에 의해서 0.25초마다 실행되도록 설정되어 있습니다.` 하지만 실제로 체감은 1초마다 실행되는거 같습니다.
그리고 DBJobhandler.handle() 메소드를 호출하고 있는데 해당 메소드에는 @Async 어노테이션이 적용되어 있습니다. 즉, 비동기적으로 수행되는 메소드입니다. 비동기로 적용한 이유는 pdf 변환을 하는 이유는 변환 대상 파일의 크기가 크면 변환하는데 시간이 오래걸릴 수 도 있고, 여러개를 동시에 변화할 경우에 해당 메소드가 호출될 때마다 하나의 변환에 대한 작업 결과값을 리턴할때까지 블로킹이 걸리면 다른 변환 요청은 수행이 되지 않기 때문에 비동기로 수행해야 합니다. 
실제로 handle() 메소드가 호출되면 결과를 바로 return하고 실제 실행은 ThreadPoolTaskExecutor 객체에 의해서 별도의 스레드에서 수행됩니다.



## DBJobHandler의 역할

이제 본격적으로 PDF 변환 요청을 수행하는 DBJobHandler 객체를 살펴보겠습니다. 이 객체도 마찬가지로 JobHandler 인터페이스를 구현하는 구현체입니다. 

```java
@Component
public abstract class JobHandler implements ServiceHandler<AsyncResult<Status>, Status> {
    
    // 원본파일을 취득하여 가져올 경로를 정의한 디렉토리 경로입니다.
	@Value("${conv.in.tray.dir}")
	protected String inTrayDir;
	// 변환된 파일을 임시로 보관할 디렉토리 경로입니다.
	@Value("${conv.out.tray.dir}")
	protected String outTrayDir;
	
    // true로 설정됨. 작업 시간 측정 유무를 위해 필요한 값
	@Value("${pdf.conv.time.print}")
	private boolean printConvTime;
    
    // dev로 되어 있습니다.
	@Value("${license.name.site}")
	private String siteName;

	@Value("${mail.service.activation}")
	private boolean mailServiceActivation;
	
    // DB 접근 계층 Mapper를 이용하여 쿼리를 수행합니다.
	@Resource
	private PdfConvDao pdfConvDao;
	
    // ThreadPoolTaskExecutor 인스턴스 주입
	@Resource(name = "executor")
	private TaskExecutor executor;
	
	@Resource
	MailSenderService mailSenderService;
	
	private String jobHandlerProp = PropsUtils.getAsString("job.handler");

    @Async
    @Override
    public AsyncResult<Status> handle(Status status){

        check();

        fetch();

       process();

        commit();

    }
}
```
JobHandler 객체의 handle() 메소드는 @Async 어노테이션이 적용되어 있습니다. 실제로 이 메소드를 호출하면 `결과는 바로 리턴되고 실제 실행은 TaskExecutor에 의해 별도 Task 내부(스레드)에서 실행됩니다.` 그렇기 때문에 사용 가능한 쓰레드 수만큼 동시 변환이 가능해집니다.
이 JobHandler는 가장 먼저 웹 서버가 올라왔을때 @PostConstruct 어노테이션이 적용된 init() 메소드를 수행합니다.

```java
@PostConstruct
private void init() {

    checkJobTicketManagerType();
	// 디렉토리 정리하는 Util 메소드 호출	
	FileUtils.cleanDir(inTrayDir);
	FileUtils.cleanDir(outTrayDir);
}
```

가장 먼저 JobTicketManager가 DBJobTicketManager인지 확인하는 checkJobTicketManagerType() 메소드를 호출하여 검사를 합니다. 해당 작업이 폴더 연계나 그 외에 다른 방식일 수도 있기 때문입니다.

그리고 나서 pdf로 변환하는 코어 모듈인 CAS가 원본파일을 취득하고 가져오는 임시 작업 경로와 변환 후 변환된 파일을 임시로 넣을 경로의 디렉토리를 정리하는 작업을 수행합니다.

그리고 본격적으로 PDF 변환을 하기 위한 각 단계 check(), fetch(), process(), commit() 메소드를 호출합니다.

## check() 메소드의 책임은 무엇일까?

check() 메소드가 가장 먼저 pdf 변환 과정에서 호출이 되는데 이 메소드는 DBJobTicketManager에게 큐에 저장되어 있는 JobTicket 객체를 가져와달라고 `메시지(getRecentJobTicket)를 전송하게 됩니다.`
이때 가져오는 JobTicket 객체는 큐에 가장 먼저 들어온 객체입니다. 왜냐하면 현재 사용하는 ArrayBlockingQueue 객체는 FIFO(선입 선출)방식으로 데이터를 관리하는 자료구조이기 때문입니다.

```java
@Override
	public JobTicket check() {
		// 큐에서 가장 오래된 JobTicket 객체를 꺼내오는 메소드
		JobTicket jobTicket = jobTicketManager.getRecentJobTicket();
		// 값이 들어옴
		SdpLogger.info("uploadFile: " + jobTicket.getUploadFile(), getClass());

		if (jobTicket == null) {
			SdpLogger.error("DbJobHandler-check ERROR: jobTicket이 null입니다.", getClass());
			return jobTicket;
		}
		
		if (isAmqpJobTicketManager) {
			
			String serverType = PropsUtils.getAsString("amqp.server.type");
			
			String serverIp = NetUtils.getLocalIp();
						
			jobStatusResolver.resovleOnCheck(jobTicket, serverType, serverIp);
			
		} else {
			// 아규먼트에서 sertType, serverip가 null 값으로 전달해주는 이유 파악하기.
			jobStatusResolver.resovleOnCheck(jobTicket, null, null);
		}
		
		return jobTicket;
	}
```

그리고 정상적으로 JobTicket 객체를 큐에서 가져오게 되면 DBJobStatusResolver 객체를 이용하여 상태값을 `W(WAIT) -> C(CHECK)`로 변경하는 UPDATE 쿼리를 수행하게 됩니다. 그리고 JobTicket을 return하면 check() 메소드 부분은 종료됩니다.





## fetch() 메소드의 책임은 무엇일까?
fetch() 메소드는 JOB_CONV 테이블에 변환 요청을 insert를 할때 srcFile 컬럼 값에 넣은 변환 대상인 원본 파일 경로에서 파일을 취득하는 책임을 가지고 있습니다.

```java
	@Override	
	public JobTicket fetch(JobTicket jobTicket) {
		
		String jobId = jobTicket.getJobId();
		// C:/ePapyrus/PDFGateway/IN_TRAY/ + jobId + / custom://C:/hwp1.hwp
		String inTrayFile = inTrayDir + jobId + PATH_SEPERATOR + jobTicket.getSrcFileName();
		SdpLogger.info("inTrayFile 경로: " + inTrayFile, getClass());
		//cleanDirectory(inTrayDir, jobId);

		// JobTicket의 jobStatus 값 C -> I로 DB 업데이트 수행
		jobStatusResolver.resovleOnFetch(jobTicket, JobStatus.IN_PROCESS);

		JobStatus result = JobStatus.EMPTY;
		// 원본 파일 값을 JOB_CONV 테이블에 INSERT를 수행할때 넣는 프로토콜을 제거하는 부분
		String srcFile = FileNameUtils.removePrefixProtocol(jobTicket.getSrcFile());

		SdpLogger.info("Fetch() srcFile 경로: " + srcFile, getClass());
		if (isHotfolderJobTicketManager && MimeTypeUtils.isNotCompressedFile(srcFile)) {
			
			result = fileGatewayService.receive(jobTicket.getSrcFile(), inTrayFile);
		} else {

			String targetFile = jobTicket.getSrcFile();
			SdpLogger.info("Fetch() targetFile 경로: " + jobTicket.getSrcFile(), getClass());
			//BlobFile인지 확인하기기
		if (isBlobFile(targetFile)) {
				
				result = fileGatewayService.receiveBlob(jobId, targetFile);
				
			} else {
				// fileGatewayService 객체에게 receive 메시지를 전송 epapyrus 내부 디렉토리명과 원본파일 경로를 전송합니다.
			SdpLogger.info("fileGatewayService 호출 전의 srcFile 경로 : " +  jobTicket.getSrcFile(),getClass());
			result = fileGatewayService.receive(jobTicket.getSrcFile(), inTrayFile);
				
				if (result == JobStatus.SUCCESS) {
					// 추가작업 추출
					String extraJobs = jobTicket.getExtraJobs();
					//상태값 DD는 무엇일까?
					if (StringUtils.contains(extraJobs, "DD")) {
							//softcam은 무엇일까? 이 로직은 준영선임님께 문의드리기
						if (drmHandler.isEncryptedFile(inTrayFile)) {
							
							Map<String, Object> drmConfigMap = drmConfigVendorMap.get(drmVendor);
							
							drmConfigMap.put("drmId", "SECURITYDOMAIN");
							
							result = drmHandler.decrypt(inTrayFile, drmConfigMap);
						}					
					}					
				}				
			}			
		}
		
		Status status = Status.EMPTY;
		
		if (result == JobStatus.SUCCESS) {
			//원본파일을 다운로드할 inTrayFile 경로를 SETTER 해줌
			jobTicket.setSrcFile(inTrayFile);
			// 원본 파일 경로에서 파일이 잘못됬는지 검증하는 부분?
			status = validateSupportContentsType(jobTicket); 
			
		} else if (result.name().startsWith("ERROR")) {
						
			jobStatusResolver.resovleOnFetch(jobTicket, result);
			
			String errorMsg = "[" + result.getCode() + "] " + result.getMessage();
			SdpLogger.error("DbJobHandler-fetch ERROR: JobID[ " + jobId + " ] " + errorMsg, getClass());
			
			status = Status.FAIL;
		}
		
		checkSrcFileSize(jobTicket);
		
		jobTicket.setStatus(status);
		
		return jobTicket;
	}
```

inTrayFile은 설정 파일에서 정의되어 있는 값으로 원본 파일 취득을 위한 임시 경로로 사용하기 위해 정의하였습니다.

```properties
 conv.in.tray.dir = C:/ePapyrus/PDFGateway/IN_TRAY/

// 설정 파일의 값을 주입할때는 @Value 어노테이션 사용
@Value("${conv.in.tray.dir}")
protected String inTrayDir;
```
1. inTrayFile 변수에 설정 파일 경로와 jobId + / + 원본 파일 경로를 이용하여 내부적으로 원본 파일을 취득할 임시 경로를 구합니다.

2. check() 메소드와 마찬가지로 DBJobStatusResolver에게 요청하여 상태값을 `C(CHECK) -> I(IN_PROCESS)`로 변경하는 쿼리문을 수행합니다.

3. srcFile 변수에 프로토콜을 제외한 원본 파일 경로를 구합니다.

```java
String srcFile = FileNameUtils.removePrefixProtocol(jobTicket.getSrcFile());
```

> 참고로 JOB_CONV 테이블에 srcFile, destFile 컬럼에 값을 넣을때 `file://, ftp://, sftp://` 와 같은 프로토콜:// 값을 접두사로 넣어주는 규칙이 존재합니다. 그렇기 때문에 원본 파일의 경로를 인식하기 위해서 이렇게 접두사를 제거하는 메소드를 호출해줘야 합니다. 


4. FileGatewayService 객체에 요청하여 원본 파일을 취득하는 receive() 메소드를 호출합니다. 이때 아규먼트로 srcFile의 값과 inTrayFile을 보냅니다.

```java
result = fileGatewayService.receive(jobTicket.getSrcFile(), inTrayFile);

@Service
public class CommonFileGatewayService implements FileGatewayService {

    @Resource(name = "commonFileTransfer")
    private FileTransfer commonFileTransfer;

     public JobStatus receive(String remoteURI, String localFile) {
        return this.commonFileTransfer.download(remoteURI, localFile);
    }
}
```
CommonFileGatewayService가 실제로 FileTransfer의 구현체인 CommonFileTransfer 객체에게 download() 메소드 호출을 하도록 요청합니다. remoteURI는 실제 프로토콜이 포함된 원본 파일의 경로이고 localFile은 원본 파일을 취득할 임시 경로입니다.

```java
// 파일 다운로드하는 메소드 호출
 public JobStatus download(String remoteURI, String localFile) {
        Status result = Status.EMPTY;
        JobStatus jobResult = JobStatus.EMPTY;
        String remoteFile = this.parseRemoteURI(remoteURI);
        SdpLogger.info("remoteURI 경로: " + remoteURI,getClass());
        SdpLogger.info("remoteFile 경로: " + remoteFile,getClass());

        if (remoteURI.startsWith("file://") || remoteURI.startsWith("custom://")) {
            // 원본 파일을 localFile 경로로 copy하는 작업을 수행합니다.
            result = FileUtils.copyFile(remoteFile, localFile, false);
            if (result == Status.FAIL) {
                String errorMsg = remoteURI + "를 " + localFile + "로 복사하는 중에 에러가 발생하였습니다.";
                SdpLogger.error("FileTransfer-download ERROR: " + errorMsg, this.getClass());
                jobResult = (JobStatus)Enum.valueOf(JobStatus.class, "ERROR_FILE_TRAN_MINUS_101000");
                jobResult.setMessage(errorMsg);
                return jobResult;
            } else {
                return JobStatus.SUCCESS;
            }
        }
        return JobStatus.SUCCESS;
    }   

    // `\\`로 들어온 경로 값을 `/`로 변경하는 메소드입니다.
    private String parseRemoteURI(String remoteURI) {
        remoteURI = this.getPathWithoutPrefix(remoteURI);
        remoteURI = remoteURI.replace('\\', '/');
        return remoteURI;
    }
    // 프로토콜:// 문자열을 삭제한 원본 파일 경로를 문자열로 리턴합니다.
    private String getPathWithoutPrefix(String uri) {
        String result = "";
        if (!uri.startsWith("file://") && !uri.startsWith("sftp://")) {
            if (uri.startsWith("ftp://")) {
                result = uri.substring(6);
            }
            if(uri.startsWith("custom://")){
                result = customPath+ uri.substring(9);
                SdpLogger.info("remoteFile result 경로: " + result, getClass());
            }

        } else {
            result = uri.substring(7);
        }
        return result;
    } 
```
 만약 DB JOB_CONV 테이블에 srcFile, destFile 컬럼값에 custom://hwp1.hwp로 값을 insert했을 시에 원본 파일 경로와 목적지 파일 경로는 설정파일 경로/hwp1.hwp로 파싱하여 리턴을 해줍니다. 그럼 실제로 파일을 위의 경로로 취득하고, 업로드도 해당경로로 해주게 됩니다.


 

 5. 원본 파일을 취득을 하였으면 이제 inTrayFile 경로를 JobTicket 객체의 srcFile 값으로 setter를 해줍니다.
 
 ```java
	if (result == JobStatus.SUCCESS) {
			//원본파일을 다운로드할 inTrayFile 경로를 SETTER 해줌
			jobTicket.setSrcFile(inTrayFile);
			// 취득 경로에 있는 파일에 대해서 컨텐츠 타입을 지원하는지 검증하는 부분
			status = validateSupportContentsType(jobTicket); 
    }
 ```

checkSrcFileSize(JobTicket jobTicket) 메소드를 호출하여 inTrayFile 경로에 있는 변환 대상 파일의 파일 크기를 구합니다.



## process() 메소드의 책임은 무엇일까?
process()는 pdf 변환을 실제로 처리해주고, 추가작업이 존재하면 추가작업까지 담당하는 책임을 맡고 있습니다.

1. inTrayFile(원본 파일 취득 경로)에 있는 파일이 압축되었는지 검증을 합니다.

2. PdfConvHandler.hanleConvJob(JobTicket jobticket) 메소드를 호출하여 pdf 변환 작업을 수행합니다. 실제로 변환 작업은 코어인 CAS가 수행합니다.

```java
public JobStatus handleConvJob(JobTicket jobTicket){

    JobStatus result = JobStatus.EMPTY;
	String jobId = jobTicket.getJobId();
    // 변환 작업을 수행합니다.
    result = executeJob("executeConvJob", jobTicket);

    if(result.name().startsWith("ERROR")) {	
		SdpLogger.error("PdfConvHandler-handleConvJob ERROR: jobId[ " + jobId + " ]의 변환 작업중에 에러가 발생하였습니다.", getClass(), jobId);
			return result;
	}

    // 변환 후 임시로 변환 파일을 가지고 있을 디렉토리 경로를 구합니다.	
	String srcFile = outTrayDir + jobId + PATH_SEPERATOR + jobTicket.getDestFileName();		
    // JobTicket 객체에 값을 넣어 줍니다.
	jobTicket.setSrcFile(srcFile);
	
    // page가 존재하면 추가작업을 수행하는 메소드입니다.
	if (StringUtils.isNotEmpty(jobTicket.getPage())){
		result = executeJob("executeExtractPageJob", jobTicket);
	}
		
	return result;		
}
```

위에서 excuteJob("executeConvJob", jobTicket) 메소드를 호출하는 부분이 있는데 이 메소드가 실제로 Map<String, Object> 객체에 `key = job, value = executeConvJob`, `key = jobTicket, value = JobTicket`로 값을 넣은 후에 PdfConvExecutor.execute(paramMap) 메소드를 호출하여 reflection을 이용하여 해당 변환 메소드를 수행을 하게 됩니다.


```java
// 리플렉션 기법을 사용해서 변환 작업을 수행하는 로직
@Override
public JobStatus execute(Map executorMap) {
	
	String methodName = (String) executorMap.get("job");
		
	Method method = ReflectionUtils.findMethod(this.getClass(), methodName, Map.class);
		
	ReflectionUtils.makeAccessible(method);
		
	return (JobStatus) ReflectionUtils.invokeMethod(method, this, executorMap);
}
```

그러면 실제 아래에 있는 메소드가 수행이 됩니다. 이 작업은 CAS가 수행합니다.

```java
result = pdfConvService.convert(jobTicket) 
```



```java
// process 전체 소스 코드
@Override
public JobTicket process(JobTicket jobTicket) {
			
	JobStatus result = JobStatus.EMPTY;

	// in_Tray 경로를 가져옵니다.
	String srcFile = jobTicket.getSrcFile();
		
	// 압축된 파일인지 체크하는 로직
	if (MimeTypeUtils.isCompressedFile(srcFile)) {
			
		result = handleCompressedFile(jobTicket);
			
		if (result.name().startsWith("ERROR")) {
				
			jobStatusResolver.resovleOnProcess(jobTicket, result);
				
			return jobTicket;
		} else {
			// 압축된 파일은 아니지만 변환할 파일이 PDF 파일이 아닌지 체크
		    if (MimeTypeUtils.isNotPDF(srcFile) 
				|| (MimeTypeUtils.isPDF(srcFile) && StringUtils.isNotEmpty(jobTicket.getPage()))) {

				SdpLogger.info("srcFile:" + srcFile,getClass());
				result = pdfConvHandler.handleConvJob(jobTicket);
				SdpLogger.info("jobTicket Status2: " + result.getCode() + " " + result.getMessage(),getClass());

				if (result.name().startsWith("ERROR")) {
					
					jobStatusResolver.resovleOnProcess(jobTicket, result);
					
					return jobTicket;
				}
			}
        }
		result = pdfConvHandler.handleExtraJob(jobTicket);
			
		if(result.name().startsWith("ERROR")) {
			SdpLogger.info("에러발생!!! " + result.name(), getClass());
				jobStatusResolver.resovleOnProcess(jobTicket, result);
				
			return jobTicket;
		}
			
		checkDestFileSize(jobTicket);	
	}
		
	jobTicket.setStatus(Status.SUCCESS);
		
	return jobTicket;
}
```



3. 변환 작업이 이상없이 수행 되었다면 이제 추가작업을 수행하는 메소드를 호출하게 됩니다.

```java
result = pdfConvHandler.handleExtraJob(jobTicket);

@Override
public JobStatus handleExtraJob(JobTicket jobTicket){
		
	String extraJobs = jobTicket.getExtraJobs();

    // 추가 작업이 없으면 상태값 EMPTY로 리턴을 합니다.
	if (StringUtils.isEmpty(extraJobs)) {
			return JobStatus.EMPTY;
	}

	if (StringUtils.contains(extraJobs, "PA")) {
			extraJobs = StringUtils.remove(extraJobs, "PA");
	}
		
	if (StringUtils.contains(extraJobs, "HF")) {
			extraJobs = StringUtils.remove(extraJobs, "HF");
	}
		
	if (StringUtils.contains(extraJobs, "DD")) {
			extraJobs = StringUtils.remove(extraJobs, "DD");
	}

	// 0, executeUploadFileJob 을 가진 list 객체 리턴.
	List<String> extraJobList = parseExtraJobs(extraJobs);		
		
	JobStatus result = JobStatus.EMPTY;
		
	for (String jobMethod : extraJobList) {
		//executeUploadFileJob이 들어온다고 가정
		SdpLogger.info("jobMethod: " + jobMethod, getClass());
		result = executeJob(jobMethod, jobTicket);
			
		if (result.name().startsWith("ERROR")) {
			SdpLogger.error("PdfConvHandler-handleConvExtraJob ERROR: jobId[ " + jobTicket.getJobId() + " ]의 " + StringUtils.replace(jobMethod, "execute", "") + " 작업중에 에러가 발생하였습니다.", getClass(), jobTicket.getJobId());
			return result;
		}			
	}

	SdpLogger.info("PdfConvHandlerImpl() jobStatus: " + result.getMessage(), getClass());
		return JobStatus.SUCCESS;
}
```

handlerExtraJob() 메소드도 위에서 수행했던 handleConvJob() 과 마찬가지로 Map 객체에 추가작업을 수행할 메소드 명을 넣어줍니다. 그리고 해당 메소드를 Reflection 기법을 사용하여 호출하도록 정의 되어있습니다.


3. 변환 파일의 사이즈 크기를 구한 후 JobTicket 객체의 srcDestFileSize 필드에 저장합니다.




## commit() 메소드의 책임은 무엇일까?

commit() 메소드는 이제 변환 된 pdf 파일을 실제 destFile 컬럼에 넣은 경로로 upload하는 책임을 담당합니다. 


1.현재 srcFile은 변환된 pdf 파일을 임시로 보관하고 있는 디렉토리 경로입니다.

2. transferResultFile(JobTicket jobTicket) 메소드 호출

```java
private JobStatus transferResultFile(JobTicket jobTicket) {
		
	JobStatus result = JobStatus.EMPTY;
		
	String jobId = jobTicket.getJobId();
    // 변환 파일을 가지고 있는 임시 경로
	String targetFile = jobTicket.getSrcFile();
	// 업로드 할 경로
    String destFile = jobTicket.getDestFile();

	SdpLogger.info("Commit 단계 destFile 경로: " + destFile, getClass());
    // 프로토콜이 blob:// 인지 체크하는 부분
	if (isBlobFile(destFile)) {
		result = fileGatewayService.sendBlob(jobId, targetFile);
	} else {
        // 실제로 업로드를 요청하는 메소드입니다.
		result = fileGatewayService.send(targetFile, destFile);
	}
	
	if (ftpMonitorVersion == 7) {		
	    result = transferExtraJobResultFiles(jobTicket);
	}
	// 최종적으로 상태값을 update하는 부분입니다. 	
	jobStatusResolver.resovleOnCommit(jobTicket, result);
		
	return result;		
}
```


```java
// 파일 업로드하는 부분
public JobStatus upload(String localFile, String remoteURI) {
    JobStatus result = JobStatus.EMPTY;
    String remoteFile = this.parseRemoteURI(remoteURI);
    String ip;
    
    if (remoteURI.startsWith("file://") || remoteURI.startsWith("custom://")) {
        File dstFile = new File(remoteFile);
        
        // 부모 폴더를 File 형태로 return 합니다.
        // 그 후에 존재하지 않으면 존재하지 않는 부모 폴더까지 포함하여 해당 경로에 폴더를 생성하는 부분입니다.
        dstFile.getParentFile().mkdirs();
        // 실제로 업로드를 수행하는 copyFile 메소드 호출
        Status copyJobResult = FileUtils.copyFile(new File(localFile), new File(remoteFile), false);
        if (copyJobResult == Status.FAIL) {
            ip = localFile + "를 " + remoteURI + "로 복사하는 중에 에러가 발생하였습니다.";
            SdpLogger.error("FileTransfer-upload ERROR: " + ip, this.getClass());
            result = (JobStatus)Enum.valueOf(JobStatus.class, "ERROR_FILE_TRAN_MINUS_101001");
            
            result.setMessage(ip);
            return result;
        } else {
            return JobStatus.SUCCESS;
        }
    } else if (!remoteURI.startsWith("ftp://") && !remoteURI.startsWith("sftp://")) {
        return JobStatus.ERROR_FILE_TRAN_MINUS_101003;
    } else {
            Iterator var5 = this.ftpAccessInfoList.iterator();

        while(var5.hasNext()) {
                FtpAccessInfo ftpAceessInfo = (FtpAccessInfo)var5.next();
                ip = ftpAceessInfo.getIp();
                String transferType = ftpAceessInfo.getTransferType();
            if (!"0.0.0.0".equals(ip) && !StringUtils.equalsIgnoreCase(transferType, "download")) {
                    String ftpType = ftpAceessInfo.getFtpType();
                    FtpClientCallback ftpClient = FtpClientFactory.createInstance(ftpType);
                    ftpClient.setAccessInfo(ftpAceessInfo);
                    result = this.ftpClientTemplate.upload(ftpClient, localFile, remoteFile);
                if (result.name().startsWith("ERROR")) {
                        return result;
                }
            }
        }

        return JobStatus.SUCCESS;
    }
}
```

3. jobStatusResolver.resovleOnCommit(jobTicket, result) 메소드를 호출하여 최종적으로 상태값을 `I -> S or I -> F`로 변경하는 UPDATE 쿼리문을 수행합니다.
최종적으로 commit() 메소드가 종료되면 pdf 변환 사이클을 완료됩니다. 

그리고 JobStopWatch의 stop() 메소드를 호출하여 총 4 단계의 메소드를 수행하는데 걸린 시간을 구합니다. 즉 , 변환시간을 구해서 DB JOB_CONV 테이블에 convTime 컬럼 값을 UPDATE 쿼리를 수행합니다.



항상 디버깅 모드로 개발을 진행하고, 기능 하나씩 작성할때 마다 commit 하는 습관을 들이고 바로 바로 push를 수행해야 합니다. 그리고 브랜치를 항상 잘봐야 합니다.


