# pdf-gateway sydney 버전

용어 정리
URI(Uniform Resource Identifier) 통합 자원 식별자 URI는 인터넷에 있는 자원을 나타내는 유일한 주소입니다. URI의 존재는 인터넷에서 요구되는 기본조건으로 인터넷 프로토콜이 항상 붙어 다닙니다.

ex) ftp://, http:// 로 시작됩니다.

URI는 인터넷 상의 자원을 삭별하기 위한 문자열 정도로 생각할 수 있습니다. URL(Uniform Resource Locator)은 네트워크 상에서 자원이 어디있는지를 알려주기 위한 규약입니다. 이는 인터넷 사으이 자원 위치라고 생각하면 됩니다.

URI가 가장 큰 상위 개념이고 이것의 하위 개념으로 URL과 URN이 있습니다. 즉 URL의 한 형태로, URI는 URL을 포함하는 개념입니다. 인터넷 상의 자원 위치와 식별자를 잘 구분해야 합니다 `자원의 위치`라는 것은 결국 `하나의 파일의 위치`를 나타내는 것입니다. 


## URL과 URI 구분
https://tistoty.com라는 주소는 https://tistoty.com라는 서버를 나타내기 때문에 URL이면서 URI 입니다. https://tistoty.com/skin이와 같은 형식은 skin이라는 인터넷상의 자원 위치를 의미합니다. 역시 URL이면서 URI입니다. https://192.168.3.x/best/of/best/good.html라는 주소는 192.168.3.x 호스트 주소 하위에 /best/of/best라는 디렉토리 아래에 good.html이라는 자원의 위치를 가르키고 있으므로 URL이면서 URI입니다.

http://hack-cracker.tistory.com/132는 좀 다르게 볼 수 있습니다. 여기서 URL은 http://hack-cracker.tistory.com까지 입니다.

내가 원하는 정보가지 도달하기 위해서는 132라는 식별자(Identifier)가 필요한 것입니다. 즉 주소 http://hack-cracker.tistory.com/132는 URI지만, URL은 아니게 됩니다.

ex)
URL(Uniform Resource Locator)은 웹 상에서 서비스를 제공하는 각 서버들에 있는 파일들의 위치를 표시하기 위한 것으로 접속할 서비스의 종류,도메인명,파일의 위치 등을 포함한다.URI(Uniform Resource Identifier)는 존재하는 자원을 식별하기 위한 일반적인 식별자를 규정하기 위한 것으로 URL에서 HTTP프로토콜,호스트명,port 번호를 제외한 것이다.
예를들어 http://127.0.0.1:8180/study/ch04/requestTest1.jsp에서 URL은 http://127.0.0.1:8180/study/ch04/requestTest1.jsp 가 되고 study/ch04/requestTest1.jsp은 URI가 된다.


출처: https://lambdaexp.tistory.com/39 [프로그래머 인생길..]


## URI encode는 왜 필요할까?
URL 인코딩은, URL 스트링에 있는 텍스트를, 모든 브라우저에서 똑바로 전송하기 위해 존재합니다.

인터넷에서의 URL 은 ASCII 문자열을 이용해서만 전송될 수 있는데, 그렇지 않게 전송한 경우, 브라우저의 특성에 따라, question mark(?), ampersand(&), 슬래쉬(/), 공백문자 같은 특수문자의 경우, 잘리거나 (의도치 않게) 변형이 될 수 있습니다. 그래서, 이런 특수문자는, 인코딩이 되는 것이 좋습니다. ASCII 에 포함되지 않는 문자들(한글, 일본어 등등)은 더더욱 encoding 이 필요합다.



## 시드니 pdf 변환 테스트 샘플 소스 코드 분석


웹 어플리케이션 구동시 PdfGatewayContext 객체의 생성자가 호출되기 전에 static 블록을 먼저 실행합니다. 해당 객체는 싱글톤으로 관리되는 객체인거 같습니다. 멀티 스레드 환경에서 처음 이 객체를 사용하는 스레드에서 동기화 블록을 통해서 객체를 생성합니다.



## 노드 구성 
Admin Host, Collect, Queue, Exec로 구성되어 있습니다.


## 스펙 

1.7 이상
Java(J2SDK, J2ee)
Spring FrameWork
JPA HIBERNATE
Maven
Vertx(버텍스 1.8 이상부터 쓸수 있습니다.)

클라이언트 서버가 따로 있음..변환서버에 요청할 정보
pdf - gateway는 윈도우 환경에서만 동작한다.

파일 취득, 파일 업로드에 대한 도메인 지식 EDMS, ECMA 

1.8 client-api-http url 통신을 함


itg

### 전체 흐름

db요청 -> 콜렉터 db -> call api -> queue -> execute에서 가져옴


api요청 -> 콜렉터 api -> queue - > execute

OJT에 시드니 설치 관련해서 최신화 하기.
수요일 까지 기본설치 및 디벨롭 문서 확인하기.


# pdf-gateway sydney 버전

용어 정리
URI(Uniform Resource Identifier) 통합 자원 식별자 URI는 인터넷에 있는 자원을 나타내는 유일한 주소입니다. URI의 존재는 인터넷에서 요구되는 기본조건으로 인터넷 프로토콜이 항상 붙어 다닙니다.

ex) ftp://, http:// 로 시작됩니다.

URI는 인터넷 상의 자원을 삭별하기 위한 문자열 정도로 생각할 수 있습니다. URL(Uniform Resource Locator)은 네트워크 상에서 자원이 어디있는지를 알려주기 위한 규약입니다. 이는 인터넷 상에서 자원 위치라고 생각하면 됩니다.

URI가 가장 큰 상위 개념이고 이것의 하위 개념으로 URL과 URN이 있습니다. 즉 URL의 한 형태로, URI는 URL을 포함하는 개념입니다. 인터넷 상의 자원 위치와 식별자를 잘 구분해야 합니다 `자원의 위치`라는 것은 결국 `하나의 파일의 위치`를 나타내는 것입니다. 


## URL과 URI 구분
https://tistoty.com라는 주소는 https://tistoty.com라는 서버를 나타내기 때문에 URL이면서 URI 입니다. https://tistoty.com/skin이와 같은 형식은 skin이라는 인터넷상의 자원 위치를 의미합니다. 역시 URL이면서 URI입니다. https://192.168.3.x/best/of/best/good.html라는 주소는 192.168.3.x 호스트 주소 하위에 /best/of/best라는 디렉토리 아래에 good.html이라는 자원의 위치를 가르키고 있으므로 URL이면서 URI입니다.

http://hack-cracker.tistory.com/132는 좀 다르게 볼 수 있습니다. 여기서 URL은 http://hack-cracker.tistory.com까지 입니다.

내가 원하는 정보가지 도달하기 위해서는 132라는 식별자(Identifier)가 필요한 것입니다. 즉 주소 http://hack-cracker.tistory.com/132는 URI지만, URL은 아니게 됩니다.

ex)
URL(Uniform Resource Locator)은 웹 상에서 서비스를 제공하는 각 서버들에 있는 파일들의 위치를 표시하기 위한 것으로 접속할 서비스의 종류,도메인명,파일의 위치 등을 포함한다.URI(Uniform Resource Identifier)는 존재하는 자원을 식별하기 위한 일반적인 식별자를 규정하기 위한 것으로 URL에서 HTTP프로토콜,호스트명,port 번호를 제외한 것이다.
예를들어 http://127.0.0.1:8180/study/ch04/requestTest1.jsp에서 URL은 http://127.0.0.1:8180/study/ch04/requestTest1.jsp 가 되고 study/ch04/requestTest1.jsp은 URI가 된다.


출처: https://lambdaexp.tistory.com/39 [프로그래머 인생길..]


## URI encode는 왜 필요할까?
URL 인코딩은, URL 스트링에 있는 텍스트를, 모든 브라우저에서 똑바로 전송하기 위해 존재합니다.

인터넷에서의 URL 은 ASCII 문자열을 이용해서만 전송될 수 있는데, 그렇지 않게 전송한 경우, 브라우저의 특성에 따라, question mark(?), ampersand(&), 슬래쉬(/), 공백문자 같은 특수문자의 경우, 잘리거나 (의도치 않게) 변형이 될 수 있습니다. 그래서, 이런 특수문자는, 인코딩이 되는 것이 좋습니다. ASCII 에 포함되지 않는 문자들(한글, 일본어 등등)은 더더욱 encoding 이 필요합다.

Task는 TaskUnit의 상위 개념으로 고객 입장에서 pdf 변환 후 워터마크를 찍고 이미지를 추출하는 것이 하나의 작업 단위입니다. TaskUnit 워터마크를 찍는것 하나와 이미지를 추출하는 것도 하나의 TaskUnit입니다.


## 시드니 pdf 변환 테스트 샘플 소스 코드 분석
```java
public class ClientCustomExample {

    private static Logger logger = LoggerFactory.getLogger(ClientCustomExample.class);
    // 변환 대상인 원본 파일 경로
    String inputPdf1 = "file://C:/junyoung/hwp1.hwp";
    // 변환된 파일 경로 
    String outputPdf1=  "file://C:/test/hwp71.pdf";

    public static void main(String[] args) throws UnsupportedEncodingException, NumberFormatException, URISyntaxException {
        ClientCustomExample clientCustomExample = new ClientCustomExample();

        //기본 변환 테스트 수행
        clientCustomExample.conversionTest();

    }
    // Task 객체를 생성하고 실제 pg에게 요청하여 작업을 등록하는 메소드
    private void conversionTest(){
        try{
            Task task = this.conversion();
            execute(task);
        }catch (UnsupportedEncodingException e){
            e.printStackTrace();
        }catch (URISyntaxException e){
            e.printStackTrace();
        }
    }



    private PdfConversion createConversion(String url) throws UnsupportedEncodingException, URISyntaxException{
            PdfConversion conversion = PdfConversion.create(UriUtils.encodeUri(url)).conversionMethod(0);
            return conversion;
    }

    private  Distribution createDistribution (String output) throws URISyntaxException, UnsupportedEncodingException{

            Distribution distribution = Distribution.create().output(UriUtils.encodeUri(output));
            return distribution;
    }

    private Task conversion() throws NumberFormatException, UnsupportedEncodingException, URISyntaxException{
        TaskBuilder taskBuilder = new TaskBuilder("convertJob");
        Task task = taskBuilder.conversion(createConversion(inputPdf1)).next(createDistribution(outputPdf1)).build();

        return task;
    }

    public void execute(Task task){

        HttpGatewayOptions httpGatewayOptions = new HttpGatewayOptions("localhost", 8888);
        PdfGateway pg = PdfGateways.instance().create(httpGatewayOptions);
        try {
            Task pdfTask = (Task) pg.register(task);
            logger.debug("PdfTask : {}", pdfTask.getClass().getName());
            String taskId = pdfTask.getOid();
            while (true) {
                TaskStatus taskStatus = pg.getStatus(PdfGateway.IdType.OBJECT_ID, taskId);
                logger.debug("status : {}", taskStatus);
                if (taskStatus.isFinished()) {
                    if (taskStatus.isFailed()) {
                        Collection<TaskUnitErrorHistory> errors = pg.getErrors(PdfGateway.IdType.OBJECT_ID, taskId);
                        for (TaskUnitErrorHistory history : errors){
                            history.getCause();
                            history.getMessage();
                            history.getCode();
                        }
                        logger.debug("errors : {}", errors);
                    }
                    break;
                }
                Thread.sleep(5000);
            }
            logger.debug("taskId : {}", taskId);
        }catch (Exception e){
            logger.debug("failed convert", e);
        }
    }
}
```

## TaskBuilder의 역할은? 
TaskBuilder는 말 그대로 Task를 생성하는 객체입니다. 생성자로 String 타입의 객체를 받아서 Task.of(String name) 메소드를 호출하여 Task 객체를 생성합니다.

```java
// TaskBuilder 객체의 생성자 ex) name - convertJob 전달
// 호출 순서 1
public TaskBuilder(String name) {
        this(Task.of(name));
}

// Task 객체의 메소드
// 호출 순서 2
public static Task of(String name) {
    Task task = new Task();
    task.newId();
    task.setOid(UUID.randomUUID().toString());
    task.setName(name);
    return task;
}
    // 호출 순서 3
    public TaskBuilder(Task task) {
        this.task = task;
        construct();
    }


```



```java
// PdfJob 클래스에 name, input 컬럼이 정의되어 있습니다.
public class PdfConversion extends PdfJob<PdfConversion> {

    String task = "CONVERT";
    String target = "PDF";
    private String conversionMethod = null;
    private String taskUnitDefName = "default-conversion";

    private Boolean htmlUrlResource = null;


     // 호출 순서 3. 
    protected PdfConversion() {
        this.name("Conversion");
        this.command("pdfierTaskUnitExecutor");
    }
    // 호출 순서 2. file://C:/junyoung/hwp1.hwp 값이 들어옵니다.
    public PdfConversion(String input) {
        this();
        this.input(input);
    }
    // 호출 순서 1.
    public static PdfConversion create(String input) {
        return new PdfConversion(input);
    }

    public PdfConversion conversionMethod(String conversionMethod) {
        this.conversionMethod = conversionMethod;
        return this;
    }
    // 0은 가상 프린터를 의미합니다.
    public PdfConversion conversionMethod(int conversionMethod) {
        return conversionMethod(String.valueOf(conversionMethod));
    }

}
```
