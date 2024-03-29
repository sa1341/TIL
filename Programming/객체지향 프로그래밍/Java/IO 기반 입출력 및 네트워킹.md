## IO 패키지

프로그램에서는 데이터를 외부에서 읽고 다시 외부로 출력하는 작업이 빈번합니다. 데이터는 사용자로부터 키보드를 통해 입력될 수도 있고, 파일 또는 네트워크로부터 입력될 수도 있습니다. 데이터는 반대로 모니터로 출력될 수도 있고, 파일로 출력되어 저장될 수도 있으며 네트워크로 출력되어 전송될 수도 있습니다.

자바에서 데이터는 스트림(Stream)을 통해 입출력되므로 스트림의 특징을 잘 이해해야 합니다. 스트림은 단일 방향으로 연속적으로 흘러나가는 것을 말합니다. 물이 높은 곳에서 낮은 곳으로 흐르듯이 데이터는 출발자에서 나와 도착지로 들어간다는 개념입니다.

## 입력 스트림과 출력 스트림

프로그램이 출발지냐 또는 도착지냐에 따라서 스트림의 종류가 결정되는데, 프로그램이 데이터를 입력받을 때에는 입력 스트림(InputStream)이라고 부르고, 프로그램이 데이터를 보낼 때에는 출력 스트림(OutputStream)이라고 부릅니다. 입력 스트림의 출발지는 키보드, 파일, 네트워크상의 프로그램이 될수 있고, 출력 스트림의 도착지는 모니터, 파일, 네트워크상의 프로그램이 될 수 있습니다.

![Untitled Diagram (2)](https://user-images.githubusercontent.com/22395934/83325655-db8edd80-a2a8-11ea-985e-167a20895b27.png)

항상 프로그램을 기준으로 데이터가 들어오면 입력 스트림이고, 데이터가 나가면 출력 스트림이라는 것을 명심해야 합니다. 프로그램이 네트워크상의 다른 프로그램과 데이터 교환을 하기 위해서는 양쪽 모두 입력 스트림과 출력스트림이 따로 필요합니다. 스트림의 특성이 단방향이므로 하나의 스트림으로 입력과 출력을 모두 할 수 없기 때문입니다.

![Untitled Diagram](https://user-images.githubusercontent.com/22395934/83325751-bfd80700-a2a9-11ea-8c23-87523df972da.png)

자바의 기본적인 데이터 입출력 API는 java.io 패키지에서 제공하고 있습니다. java.io 패키지에는 파일 시스템의 정보를 얻기 위한 File 클래스와 데이터를 입출력하기 위한 다양한 입출력 스트림 클래스를 제공하고 있습니다.



## 스트림의 종류

- 바이트 기반의 스트림
    - 그림, 멀티미디어, 문자 등 몬든 종류의 데이터를 받고 보낼 수 있습니다.

- 문자기반의 스트림
    - 오직 문자만 받고 보낼 수 있도록 특화되어 있습니다.

바이트 기반의 입력 스트림 중 대표적인 클래스는 InputStream이라는 최상위 클래스가 있고, 최상위 출력 스트림은 OutputStream이 있습니다. 이 클래스들을 각각 상속받는 하위 클래스는 접미사로 InputStream 또는 OutputStream이 붙습니다. Reader는 문자 기반 입력 스트림의 최상위 클래스이고, Wrtier는 문자 기반 출력 스트림의 최상위 클래스입니다. 이 클래스들을 각각 상속받는 하위 클래스는 접미사로 Reader 또는 Writer가 붙습니다. 예를 들어, 그림, 멀티미디어, 텍스트 등의 파일을 바이트 단위로 읽어들일 때에는 FileInputStream을 사용하고, 바이트 단위로 저장할 때에는 FileOutputStream을 사용합니다. 텍스트 파일의 경우, 문자 단위로 읽어들일 때에는 FileReader를 사용하고, 문자 단위로 저장할 때에는 FileWriter를 사용합니다.

## InputStream 클래스
InputStream은 바이트 기반 입력 스트림의 최상위 클래스로 추상 클래스입니다. 모든 바이트 기반 입력 스트림은 이 클래스를 상속받아서 만들어집니다. FileInputStream,, BufferedInputStream, DataInputStream 클래스는 모두 InputStream 클래스를 상속하고 있습니다.

### read() 메소드
read() 메소드는 입력 스트림으로부터 1바이트를 읽고 4바이트 int 타입으로 리턴합니다. 따라서 리턴된 4바이트 중 끝의 1바이트에만 데이터가 들어 있습니다. 예를 들어 입력 스트림에서 5개의 바이트가 들어온다면 다음과 같이 read() 메소드로 1바이트씩 5번 읽을 수 있습니다.

더 이상 입력 스트림으로부터 바이트를 읽을수 없다면 read() 메소드는 -1을 리턴하는데, 이것을 이용하면 읽을 수 있는 마지막 바이트까지 루프를 돌며 한 바이트씩 읽을 수 있습니다.

```java
Inputstream is = new FileInputStream("C:/test.jpg");
int readByte;
while ((readByte=is.read()) != -1) { ... }
```

### read(byte[] b) 메소드
read(byte[] b) 메소드는 입력 스트림으로부터 매개값으로 주어진 바이트 배열의 길이만큼 바이트를 읽고 저장합니다. 그리고 읽은 바이트 수를 리턴합니다. 실제로 읽은 바이트 수가 배열의 길이보다 적을 경우 읽은 수 만큼 리턴합니다. 에를 들어 입력 스트림에서 5개의 바이트가 들어온다면 다음과 같이 길이 3인 바이트 배열로 두 번 읽을 수 있습니다.

read(byte[] b) 역시 입력 스트림으로부터 바이트를 더 이상 읽을 수 없다면 -1을 리턴하는데, 이것을 이용하면 읽을 수 있는 마지막 바이트까지 루프를 돌며 읽을 수 있습니다.

```java
InputStream is = new FileInputStream("C/test.jpg");
int readByteNo;
byte[] readBytes = new Byte[100];
while (readByteNo = is.read(readBytes)) != -1 {...}
```

입력 스트림으로부터 100개의 바이트가 들어온다면 read() 메소는 100번을 루핑해서 읽어들여야 합니다. 그러나 read(byte[] b) 메소드는 한 번 읽을 때 매개값으로 주어진 바이트 배열 길이만큼 읽기 때문에 루핑 횟수가 현저히 줄어듭니다. 그러므로 많은 양의 바이트를 읽을 때는 read(byte[]) 메소드를 사용하는 것이 좋습니다.

### read(byte[] b, int off, int len) 메서드
read(byte[] b, int off, int len) 메소드는 입력 스트림으로부터 len개의 바이트만큼 읽고, 매개값으로 주어진 바이트 배열 b[off]부터 len개까지 저장합니다. 긜고 읽은 바이트 수인 len개를 리턴합니다. 실제로 읽은 바이트 수가 len개보다 작을 경우 읽은 수 만큼 리턴합니다. 

read(byte[] b, int off, int len) 역시 입력 스트림으로부터 바이트를 더 이상 읽을 수 없다면 -1을 리턴합니다. read(byte[] b) 메서드와의 차이점은 한 번에 읽어들이는 바이트 수를 len 매개값으로 조절할 수 있고, 배열에서 저장이 시작되는 인덱스를 지정할 수 있다는 점입니다. 만약 off를 0으로, len을 배열의 길이로 준다면 read(byte[] b)와 동일합니다.

### close() 메소드
InputStream을 더 이상 사용하지 않을 경우에는 close() 메소드를 호출해서 InputStream에서 사용했던 시스템 자원을 풀어줍니다.

```java
is.close();
```


## OutputStream
OutputStream은 바이트 기반 출력 스트림의 최상위 클래스로 추상 클래스입니다. 모든 바이트 기반 출력 스트림 클래스는 이 클래스를 상속받아서 만들어집니다. 다음과 같이 FileOutputStream, PrintStream, BufferedOutputStream, DataOutputStream 클래스는 모두 OutputStream 클래스를 상속하고 있습니다.

### write(int b) 메소드

write(int b) 메소드는 매개 변수로 주어진 int 값에서 끝에 있는 1바이트만 출력 스트림으로 보냅니다. 매개 변수가 int 타입이므로 4바이트 모두를 보내는 것으로 오해할 수 있습니다.

```java
OutputStream os = new FileOutputStream("C:/test.txt");
byte[] data = "ABC".getBytes();
for(int i = 0; i <data.length; i++) {
    os.write(data[i]); //"A", "B", "C"를 하나씩 출력
}
```

### write(byte[] b) 메소드
write(byte[] b)는 매개값으로 주어진 바이트 배열의 모든 바이트를 출력 스트림으로 보냅니다.

```java
OutputStream os = new FileOutputStream("C:/test.txt");
byte[] data = "ABC".getBytes();
os.write(data); //"ABC" 모두 출력
```


### write(byte[] b, int off, int len) 메소드
write(byte[] b, int off, int len)은 b[off] 부터 len 개의 바이트를 출력 스트림으로 보냅니다.

```java
OutputStream os = new FileOutputStream("C:/test.txt");
byte[] data = "ABC".getBytes();
os.write(data, 1, 2); // "BC"만 출력
```

### flush()와 close() 메소드

출력 스트림은 내부에 작은 버퍼(buffer)가 있어서 데이터가 출력되기 전에 버퍼에 쌓여있다가 순서대로 출력됩니다. flush() 메소드는 버퍼에 잔류하고 있는 데이터를 모두 출력시키고 버퍼를 비우는 역할을 합니다. 프로그램에서 더 이상 출력할 데이터가 없다면 flush() 메소드를 마지막으로 호출하여 버퍼에 잔류하는 모든 데이터가 출력되도록 해야 합니다. OuputStream을 더 이상 사용하지 않을 경우에는 close() 메소드를 호출해서 OutputStream에서 사용했던 시스템 자원을 풀어줍니다.

```java
OutputStream os = new FileOutputStream("C:/test.txt");
byte[] data = "ABC".getBytes();
os.write(data);
os.flush();
os.close();
```

## Reader
Reader는 문자 기반 입력 스트림의 최상위 클래스로 추상 클래스입니다. 모든 문자 기반 입력 스트림은 이 클래스를 상속받아서 만들어집니다. 대표적으로 FileReader, InputStreamReader, BufferedReader 클래스가 있고, 이들 모두 Reader 클래스를 상속하고 있습니다.


### read() 메소드
read() 메소드는 입력 스트림으로부터 한 개의 문자(2바이트)를 읽고 4바이트 int 타입으로 리턴합니다. 따라서 리턴된 4바이트 중 끝에 있는 2바이트에 문자가 들어있습니다. 예를 들어 입력 스트림에서 2개의 문자 (총 4바이트)가 들어온다면 다음과 같이 read() 메소드로 한 문자씩 두 번 읽을 수 있습니다.

read() 메소드가 리턴한 int 값을 char 타입으로 변환하면 읽은 문자를 얻을 수 있습니다.

```java
char charData = (char) read();
```

더 이상 입력 스트림으로부터 문자를 읽을 수 없다면 read() 메소드는 -1을 리턴하는데 이것을 이용하면 읽을 수 있는 마지막 문자까지 루프를 돌며 한 문자씩 읽을 수 있습니다.

```java
Reader reader = new FileReader("C:/test.txt");
int readData;

while ((readData=reader.read() != -1)) {
    char charData = (char) readData;
}
```


### read(char[] cbuf) 메소드

read(char[] cbuf) 메소드는 입력 스트림으로부터 매개값으로 주어진 문자 배열의 길이만큼 문자를 읽고 배열에 저장합니다. 그리고 읽은 문자 수를 리턴합니다. 실제로 읽은 문자 수가 배열의 길이보다 작을 경우 읽은 수만큼만 리턴합니다. 예를 들어 입력 스트림에서 세 개의 문자가 들어온다면 다음과 같이 길이가 2인 문자 배열로 두 번 읽을 수 있습니다.

입력 스트림으로부터 100개의 문자가 들어온다면 read() 메소드는 100번을 루핑해서 읽어들어야 합니다. 그러나 read(char[] cbuf) 메소드는 한번 읽을 때 주어진 배열 길이만큼 읽기 때문에 루핑 횟수가 현저히 줄어듭니다. 그러므로 많은 양의 문자를 읽을 때는 read(char[] cbuf) 메소드를 사용하는 것이 좋습니다.


## Wrtier
Writer는 문자 기반 출력 스트림의 최상위 클래스로 추상 클래스 입니다. 모든 문자 기반 출력 스트림 클래스는 이 클래스를 상속받아서 만들어집니다. 다음과 같이 FileWriter, BufferedWriter, PrintWriter, OutputStreamWriter 클래스는 모두 Writer 클래스를 상속하고 있습니다.

Writer 클래스에는 모든 문자 기반 출력 스트림이 기본적으로 가져야 할 메소드가 정의되어 있습니다.
Writer 클래스의 주요 메소드는 대표적으로 아래와 같은 메소드가 존재합니다.

### write(int c)  메소드
write(int c) 메소드는 매개 변수로 주어진 int 값에서 끝에 있는 2바이트(한개의 문자)만 출력 스트림으로 보냅니다. 매개 변수가 int 타입이므로 4바이트 모두를 보내는 것으로 오해할 수 있습니다.

```java
Writer writer = new FileWriter("C:/test.txt");
char[] data = "홍길동".toCharArray();
for(int i = 0; i < data.length; i++) {
    writer.write(data[i]); // "홍", "길", "동"을 하나씩 출력
}
```

### write(char[] cbuf) 메소드
write(char[] cbuf) 메소드는 매개값으로 주어진 char[] 배열의 모든 문자를 출력 스트림으로 보냅니다.

```java
Writer writer = new FileWriter("C:/test.txt");
char[] data = "홍길동".toCharArray();
writer.write(data); //"홍길동" 모두 출력
```


### write(char[] c, int off, int len) 메소드
write(char[] c, int off, int len)은 c[off] 부터 len개의 문자를 출력스트림으로 보냅니다.
마찬가지로 write(String str) 메소드가 있는데 문자열 전체를 출력 스트림으로 보냅니다.

문자 출력 스트림은 내부에 작은 버퍼(buffer)가 있어서 데이터가 출력되기 전에 버퍼에 쌓여있다가 순서대로 출력됩니다. flush() 메소드는 버퍼에 잔류하고 있는 데이터를 모두 출력시키고 버퍼를 비우는 역할을 합니다. 프로그램에서 더 이상 출력할 문자가 없다면 flush() 메소드를 마지막으로 호출하여 모든 문자가 출력되도록 해야 합니다. 마지막으로 Writer를 더 이상 사용하지 않을 경우에는 close() 메소드를 호출해서 Writer에서 사용했던 시스템 자원을 풀어줍니다.


## 콘솔 입출력
콘솔은 시스템을 사용하기 위해 키보드로 입력을 받고 화면으로 출력하는 소프트웨어를 말합니다. 유닉스나 리눅스 운영체제는 터미널에 해당하고, Windows 운영체제는 명령 프롬프트에 해당합니다. 이클립스에도 Console 뷰가 있는데, 키보드로 직접 입력을 받고 내용을 출력할 수도 있습니다. 자바는 콘솔로부터 데이터를 입력받을 때 Sysyem.in을 사용하고, 콘솔에 데이터를 출력할 때 System.out을 사용합니다. 그리고 에러를 출력할 때는 System.err를 사용합니다.



### System.in 필드
자바는 프로그램이 콘솔로부터 데이터를 입력받을 수 있도록 System 클래스의 in 정적 필드를 제공하고 있습니다. Sysyem.in은 InputStream 타입의 필드이므로 다음과 같이 InputStream 변수로 참조가 가능합니다.

```java
InputStream is = System.in;
```

키보드로부터 어떤 키가 입력되었는지 확인하려면 InputStream의 read() 메소드로 한 바이트를 읽으면 됩니다. 리턴된 int 값에는 십진수 아스키 코드가 들어 있습니다.


```java
int asciiCode = is.read();
```

컴퓨터는 0과 1만 이해할 수 있습니다. 그래서 미국표준협회가 컴퓨터에서 문자를 숫자로 매칭하는 방법을 표준화시킨 것이 아스키 코드입니다. 아스키 코드는 1byte로 표현되는 256가지의 숫자에 영어 알파벳, 아라비아 숫자, 특수 기호를 매칭하고 있습니다. 만약 키보드에서 a를 입력하고 엔터키를 눌렀다면 a키의 97번과 Enter키의 13번, 10번이 차례대로 읽혀집니다. Enter키는 캐리지 리턴(carriage return:13)과 라인 피드(line feed:10) 코드가 결합된 키라고 볼 수 있습니다. 숫자로된 아스키 코드 대신에 키보드에 입력한 문자를 직접 얻고 싶다면 read() 메소드로 읽은 아스키 코드를 char 타입 변환하면 됩니다.

```java
char inputChar = (char) is.read();
```

예를 들어 read() 메소드로 읽은 아스키 코드 97번을 다음과 같이 char 타입으로 변환하면 `a` 문자를 얻을 수 있습니다.

```java
char inputChar = (char) 97;
``` 
다음은 현금 자동 입출금기인 ATM(Automatic Teller Machine)과 비슷하게 사용자에게 메뉴를 제공하고 사용자가 어떤 번호를 입력했는지 알아내는 예제입니다.

```java
import java.io.InputStream;

public class SystemInExample1 {
    public static void main(String[] args) throws Exception {
        System.out.println("== 메뉴 ==");
        System.out.println("1. 예금 조회");
        System.out.println("2. 예금 출금");
        System.out.println("3. 예금 입금");
        System.out.println("4. 종료 하기");
        System.out.print("메뉴를 선택하세요: ");

        // 키보드 입력 스트림 얻기
        InputStream is = System.in;
        char inputChar = (char) is.read();
        switch (inputChar) {
            case '1':
                System.out.println("예금 조회를 선택하셨습니다.");
                break;

            case '2':
                System.out.println("예금 출금을 선택하셨습니다.");
                break;

            case '3':
                System.out.println("예금 입금을 선택하셨습니다.");
                break;

            case '4':
                System.out.println("종료 하기를 선택하셨습니다.");
                break;
        }
    }
}
```

InputStream의 read() 메소드는 1바이트만 읽기 때문에 1바이트의 아스키 코드로 표현되는 수자, 영어, 특수문자는 프로그램에서 잘 읽을 수 있지만, 한글과 같이 2바이트를 필요로하는 유니코드는 read() 메소드로 읽을 수 없습니다. 키보드로 입력된 한글을 얻기 위해서는 우선 read(byte[] b)나 read(byte[] b, int off, int len) 메소드로 전체 입력된 내용을 바이트 배열로 받고, 이 배열을 이용해서 string 객체를 생성하면 됩니다. read(byte[] b) 메소드를 사용하기 전에 우선 키보드에서 입력한 문자를 저장할 바이트 배열을 만들어야 합니다. 바이트 배열의 길이는 읽어야 할 바이트 수를 고려해서 적절히 주면 되는데, 영어 한 문자는 1바이트, 한글 한 문자는 2바이트를 차지하므로 최대 영문자 15자 또는 한글 7자를 저장하려면 아래와 같이 바이트 배열을 선언하면 됩니다.

```java
byte[] byteData = new byte[15];
```

다음과 같이 생성된 배열을 read(byte[] b) 메소드의 매개값으로 주면 키보드에서 입력한 문자를 저장할 수 있게 됩니다.

```java
byte[] byteData = new byte[15];
// 읽은 바이트 수 리턴
int readByteNo = System.in.read(byteData);
```

read(byte[] b) 메소드는 매개값으로 주어진 바이트 배열에 읽은 문자를 저장하고, 실제로 읽은 바이트 개수를 리턴합니다. 


프로그램에서 바이트 배열에 저장된 아스키 코드를 사용하려면 문자열로 변환해야 합니다. 변환할 문자열은 바이트 배열의 0번 인덱스에서 시작해서 읽은 바이트 수 -2 만큼입니다. 2를 빼는 이유는 Enter 키에 해당하는 마지막 두 바이트를 제외하기 위해서입니다. 바이트 배열을 문자열로 변환할 때는 다음과 같이 String 클래스의 생성자를 이용합니다.

다음은 이름과 하고 싶은 말을 키보드로 입력받아서 다시 출력하는 예제입니다.

```java
public class SystemInExample2 {

    public static void main(String[] args) throws Exception {

        InputStream is = System.in;

        byte[] datas = new byte[100];

        System.out.print("이름: ");
        int nameBytes = is.read(datas);
        String name = new String(datas, 0, nameBytes-2);

        System.out.print("하고 싶은말: ");
        int commentBytes = is.read(datas);
        String comment = new String(datas, 0, commentBytes-2);

        System.out.println("입력한 이름: " + name);
        System.out.println("입력만 하고 싶은말: " + comment);
    }
}
```


## System.out 필드
콘솔에서 입력된 데이터를 System.in으로 읽었다면, 반대로 콘솔로 데이터를 출력하기 위해서는 System 클래스의 out 정적 필드를 사용합니다. out은 PrintStream 타입의 필드입니다. PrintStream은 나중에 설명하기로 하고, 여기서는 PrintStream이 OutputStream의 하위 클래스이므로 out 필드를 OutputStream 타입으로 변환해서 사용할 수  있다는 것만 알면 됩니다.

```java
OutputStream os = System.out;
```

콘솔로 1개의 바이트를 출력하면 OutputStream의 write(int b) 메소드를 이용하면 됩니다. 이때 바이트 값은 아스키 코드인데, write() 메소드는 아스키 코드를 문자로 콘솔에 출력합니다. 예를 들어 아스키 코드 97번을 write(int b) 메소드로 출력하면 `a`가 출력됩니다.

```java
byte b = 97;
os.write(b);
os.flush();
```

OutputStream의 write(int b) 메소드는 1바이트만 보낼 수 있기 때문에 1바이트로 표현 가능한 숫자, 영어, 특수 문자는 출력이 가능하지만, 2바이트로 표현되는 한글은 출력할 수 없습니다.


## 파일 입출력
### File 클래스

IO 패키지에서 제공하는 File 클래스는 파일 크기, 파일 속성, 파일 이름 등의 정보를 얻어내는 기능과 파일 생성 및 삭제 기능을 제공하고 있습니다. 그리고 디렉토리를 생성하고 디렉토리에 존재하는 파일 리스트를 얻어내는 기능도 있습니다. 그러나 파일의 데이터를 읽고 쓰는 기능은 지원하지 않습니다. 파일의 입출력 스트림을 사용해야 합니다. C:\temp 디렉토리의 file.txt 파일을 File 객체로 생성하는 코드입니다.

```java
File file = new File("C:\\Temp\\file.txt");
File file = new File("C:/Temp/file.txt");
```

디렉토리 구분자는 운영체제마다 조금씩 다릅니다. 윈도우에서는 \ 또는 /를 사용할 수 있고, 유닉스나 리눅스에서는 /를 사용합니다. File.separator 상수를 출력해보면 해당 운영체제에서 사용하는 디렉토리 구분자를 확인할 수 있습니다. 만약 \를 디렉토리 구분자로 사용한다면 이스케이프 문자(\\)로 기술해야 합니다.

File 객체를 생성했다고 해서 파일이나 디렉토리가 생성되는 것은 아닙니다. 생성자 매개값으로 주어진 경로가 유효하지 않더라도 컴파일 에러나 예외가 발생하지 않습니다. File 객체를 통해 해당 경로에 실제로 파일이나 디렉토리가 있는지 확인하려먼 exists() 메소드를 호출할 수 있습니다. 디렉토리 또는 파일이 파일 시스템에 존재한다면 true를 리턴하고 존재하지 않는다면 false를 리턴합니다.

```java
boolean isExist = file.exists();
```

exists() 메소드의 리턴값이 false라면 createNewFile(), mkdir(), mkdirs() 메소드로 파일 또는 디렉토리를 생성할 수 있습니다.


|  <center>리턴타입</center> |  <center>메소드</center> | <center>설명</center>
|:--------|:--------:|:--------|
| boolean | <center>createNewFile()</center> | 새로운 파일을 생성 |
| boolean | <center>mkdir()</center> | 새로운 디렉토리 를 생성 |
| boolean | <center>mkdirs()</center> | 경로상에 없는 모든 디렉토리를 생성 | 
| boolean | <center>delete()</center> | 파일 또는 디렉토리 삭제 |

파일 또는 디렉토리가 존재할 경우에는 다음 메소드를 통해 정보를 얻어낼 수 있습니다.

|  <center>리턴타입</center> |  <center>메소드</center> | <center>설명</center>
|:--------|:--------:|:--------|
| boolean | <center>canExecute()</center> | 실행할 수 있는 파일인지 여부 |
| boolean | <center>canRead()</center> | 읽을 수 있는 파일인지 여부 |
| boolean | <center>canWrite()</center> | 수정 및 저장할 수 있는 파일인지 여부 | 
| boolean | <center>getParent()</center> | 부모 디렉토리를 리턴 |
| File | <center>getParentFile()</center> | 부모 디렉토리를 File 객체로 생성 후 리턴 |
| String | <center>getPath()</center> | 전체 경로를 리턴 |
| boolean | <center>isDirectory()</center> | 디렉토리인지 여부 |
| boolean | <center>isFile()</center> | 파일인지 여부 |
| boolean | <center>isHidden()</center> | 숨김 파일인지 여부 |
| long | <center>lastModified()</center> | 마지막 수정 날짜 및 시간을 리턴 |
| long | <center>length()</center> | 파일의 크기를 리턴 |
| String[] | <center>list()</center> | 디렉토리에 포함된 파일 및 서브디렉토리 목록 전부를 String[] 배열로 리턴 |
| String[] | <center>list(FileNameFilter filter)</center> | 디렉토리에 포함 된 파일 및 서브 디렉토리 목록 중에 FileNameFilter에 맞는 것만 String 배열로 리턴 |
| File[] | <center>listFiles()</center> | 디렉토리에 포함된 파일 및 서브 디렉토리 목록 전부를 File 배열로 리턴 |
| File[] | <center>listFiles(FileNameFilter filter)</center> | 디렉토리에 포함된 파일 및 서브 디렉토리 목록 중에 FileNameFilter에 맞는 것만 File 배열로 리턴 |


다음은 C:/Temp 디렉토리에 Dir 디렉토리와 file1.txt, file2.txt, file3.txt 파일을 생성하고, Temp 디렉토리에 있는 파일 목록을 출력하는 예제입니다.

```java
import java.io.File;
import java.text.SimpleDateFormat;
import java.util.Date;

public class FileExample {
    public static void main(String[] args) throws Exception {
        File dir = new File("/Users/limjun-young/Temp/Dir");
        File file1 = new File("/Users/limjun-young/Temp/file1.txt");
        File file2 = new File("/Users/limjun-young/Temp/file2.txt");

        if (!dir.exists()) {
            dir.mkdir();
        }

        if (!file1.exists()) {
            file1.createNewFile();
        }

        if (!file2.exists()) {
            file2.createNewFile();
        }

        File temp = new File("/Users/limjun-young/Temp");
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd a HH:mm");
        File[] contents = temp.listFiles();

        System.out.println("날짜        시간       형태     크기     이름");
        System.out.println("---------------------------------------------------");

        for (File file : contents) {
            System.out.print(sdf.format(new Date(file.lastModified())));
            if (file.isDirectory()) {
                System.out.print("\t<DIR>\t\t\t" + file.getName());
            } else {
                System.out.print("\t\t\t" + file.length() + "\t" + file.getName());
            }
            System.out.println();
        }
    }
}
```

![image](https://user-images.githubusercontent.com/22395934/84907085-bc3be100-b0ed-11ea-829f-603cf0e8a457.png)


## FileInputStream

FileInputStream 클래스는 파일로부터 바이트 단위를 읽어들일때 사용하는 바이트 기반 입력 스트림입니다.바이트 단위로 읽기 때문에 그림, 오디오, 비디오, 텍스트 파일 등 모든 종류의 파일을 읽을 수 있습니다. 다음은 FileInputStream을 생성하는 두가지 방법을 보여줍니다.

```java
// 첫번째 방법
FileInputStream fis = new FileInputStream("C:/Temp/image.gif");

// 두번째 방법
File file = new File("C:/Temp/image.gif");
FileInputStream fis = new FileInputStream(file);
```

첫 번째 방법은 문자열로된 파일의 경로를 가지고 FileInputStream을 생성합니다. 만약 읽어야 할 파일이 File 객체로 이미 생성되어 있다면 두 번째 방법으로 좀 더 쉽게 FileInputStream을 생성할 수 있습니다. FileInputStream 객체가 생성될 때 파일과 직접 연결이 되는데, 만약 파일이 존재하지 않으면 FileNotFouException을 발생시키므로 try-catch문으로 예외 처리를 해야합니다.

FileInputStream은 InputStream의 하위 클래스이기 때문에 사용 방법이 InputStream과 동일합니다. 한 바이트를 읽기 위해서 read() 메서드를 사용하거나, 읽은 바이트를 byte 배열에 저장하기 위해서 read(byte[] b) 또는 read(byte[] b, int off, int len) 메소드를 사용합니다. 전체 파일의 내용을 읽기 위해서는 이 메소드들을 반복 실행해서 -1이 나올 때까지 읽으면 됩니다. 파일의 내용을 모두 읽은 후에는 close() 메소드를 호출해서 파일을 닫아줍니다.

```java
FileInputStream fis = new FileInputStream("C:/Temp/image.gif");

int readByteNo;
byte[] readBytes = new byte[100];
while ( readByteNo = fis.read(readBytes) != -1) {
    // 읽은 바이트 배열(readBytes)을 처리
} 
fis.close();
```

다음은 FileInputStreamExample.java 소스 파일을 읽고 콘솔에 보여주는 예제 입니다.

```java
public class FileInputStreamExample {
    public static void main(String[] args) {
        try {
            FileInputStream fis = new FileInputStream("/Users/Temp/FileInputStreamExample.java");

            int data;
            while (data = fis.read() != -1) {
                System.out.write(data);
            }
            fis.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```


## FileOutputStream

FileOutputStream은 바이트 단위로 데이터를 파일에 저장할 때 사용하는 바이트 기반 출력 스트림입니다. 바이트 단위로 저장하기 때문에 그림, 오디오, 비디오, 텍스트 등 모든 종류의 데이터를 파일로 저장할 수 있습니다. 다음은 FileOutputStream을 생성하는 두 가지 방법을 보여줍니다. 첫 번째 방법은 파일의 경로를 가지고 FileOutputStream을 생성하지만, 저장할 파일이 File 객체로 이미 생성되어 있다면 두 번째 방법으로 좀 더 쉽게 FileOUtputStream을 생성할 수 있습니다.

```java
// 방법 1
FileOutputStream fos = new FileOutputStream("C:/Temp/image.gif");

// 방법 2
File file = new File("C:/Temp/image.gif");
FileOutputStream fos = new FileOutputStream(file);
```
주의할 점은 파일이 이미 존재할 경우, 데이터를 출력하면 파일을 덮어쓰게 되므로, 기존의 파일 내용은 사라지게 됩니다. 기존의 파일 내용 끝에 데이터를 추가할 경우에는 FileOutputStream 생성자의 두번 째 매개값을 true로 주면 됩니다.

```java
FileOutputStream fos = new FileOutputStream("C:/Temp/data.txt", true);
FileOutputStream fos = new FileOutputStream(file, true);
```

FileOutputStream은 OuputStream의 하위 클래스이기 때문에 사용 방법이 OutputStream과 동일합니다. 한 바이트를 저장하기 위해서 write() 메소드를 사용하고 바이트 배열을 한 꺼번에 저장하기 위해서 write(byte[] b) 또는 write(byte[] b, int off, int len) 메소드를 사용합니다.

```java
FileOutputStream fos = new FileOutputStream("C:/Temp/image.gif");
byte[] data = ...;
fos.write(data);
fos.flush();
fos.close();
```

write() 메소드를 호출한 이후에 flush() 메소드로 출력 버퍼에 잔류하는 데이터를 완전히 출력하도록, close() 메소드를 호출해서 파일을 닫아줍니다. 다음은 원본 파일을 타겟 파일로 복사하는 예제입니다. 복사 프로그램의 원리는 원본 파일에서 읽은 바이트를 바로 타겟 파일로 저장하는 것이기 때문에 FileInputStream에서 읽은 바이트를 바로 FileOutputStream으로 저장하면 됩니다.

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;

public class FileOutputStreamExample {
    public static void main(String[] args) throws Exception {

        String originalFileName = "/Users/limjun-young/Temp/test0622.txt";
        String targetFileName = "/Users/limjun-young/Temp/test0622_output.txt";

        FileInputStream fis = new FileInputStream(originalFileName);
        FileOutputStream fos = new FileOutputStream(targetFileName);

        int readByteNo;
        byte[] readBytes = new byte[100];
        while ( (readByteNo = fis.read(readBytes)) != -1) {
            System.out.println("readByteNo: " +readByteNo);
            fos.write(readBytes, 0, readByteNo);
        }

        fos.flush();
        fos.close();
        fis.close();

        System.out.println("복사가 잘 되었습니다.");

    }
}
```

변수 readByteNo는 실제로 읽은 바이트 수가 저장될 변수이고, readBytes는 실제로 읽은 바이트가 저장되는 배열입니다. FileInputStream의 read(byte[] b) 메소드로 한 번에 100 바이트를 읽어 readBytes에 저장하고 100을 readByteNo에 저장합니다. 그리고 readByteNo가 -1이 아닌지를 검사합니다.

이렇게 계속해서 루핑을 돌다가 마지막 루핑 시에는 100개보다 작은 바이트를 읽어 readBytes에 저장하고 바이트 수를 readByteNo에 저장합니다. 에를 들어 파일 사이즈가 520 바이트라면 while 문을 6번 루핑하는데 마지막 루핑 시에는 20 바이트만 읽어 readBytes에 저장하고 20을 readByteNo에 저장합니다. 

## FileReader
FileReader 클래스는 텍스트 파일을 프로그램으로 읽어들일 때 사용하는 문자 기반 스트림입니다. 문자 단위로 읽기 때문에 텍스트가 아닌 그림, 오디오, 비디오 등의 파일은 읽을 수 없습니다. 다음은 FileReader를 생성하는 두 가지 방법을 보여줍니다. 첫 번째 방법은 전체 파일 경로를 가지고 FileReader를 생성하지만, 읽어야 할 파일이 File 객체로 이미 생성되어 있다면 두 번째 방법으로 좀 더 쉽게 FileReader를 생성할 수 있습니다.

```java
// 방법1
FileReader fr = new FileReader("/Users/limjun-young/Temp/file.txt");

// 방법2
File file = new File("/Users/limjun-young/Temp/file.txt");
FileReader fr = new FileReader(file);
```

FileReader 객체가 생성될 때 파일과 직접 연결이 되는데, 만약 파일이 존재하지 않으면 FileNotFoundException을 발생시키므로 try-catch 문으로 예외 처리를 해야합니다. FileReader는 Reader의 하위 클래스이기 때문에 사용 방법이 Reader와 동일합니다. 한 문자를 읽기 위해서 read() 메소드를 사용하거나, 읽은 문자를 char 배열에 저장하기 위해서 read(char[] cbuf)또는 read(char[] cbuf, int off, int len) 메소드를 사용합니다. 전체 파일의 내용을 읽기 위해서는 이 메소드를 반복 실행해서 -1이 나올때 까지 읽으면 됩니다.

```java
FileReader fr = new FileReader("/Users/limjun-young/Temp/file.txt");
int readCharNo;
char[] cbuf = new char[100];
while ((readCharNo = fr.read(cbuf)) != -1 ) {
    // 읽은 문자 배열(cbuf) 처리
}
fr.close();
```
파일의 내용을 모두 읽은 후에는 close() 메소드를 호출해서 파일을 닫아줍니다. 다음은 FileReader Example.java 소스 파일을 읽고 콘솔에 출력하는 예제입니다.

```java
public class FileReaderExample {
    public static void main(String[] args) throws Exception {
        FileReader fr = new FileReader("/Users/limjun-young/Temp/FileReaderExample.java");

        int readCharNo;
        char[] cbuf = new char[100];
        while ((readCharNo=fr.read(cbuf)) != -1 ) {
            String data = new String(cbuf, 0, read(CharNo);
            System.out.println(data)l
        }
        fr.close();
    }
}
```
코드를 보면 cbuf 배열에 저장되어 있는 문자들을 연결해서 문자열(String 객체)로 생성하였습니다. String 생성자의 첫 번째 매개값에는 cbuf, 두 번째 매개값은 0 인덱스를, 세 번째 매개값은 읽은 문자 수를 지정했습니다.

## FileWriter
FileWriter는 텍스트 데이터를 파일에 저장할 때 사용하는 문자 기반 스트림입니다. 문자 단위로 저장하기 때문에 텍스트가 아닌 그림, 오디오, 비디오, 등의 데이터를 파일로 저장할 수 없습니다. 다음은 FileWriter를 생성하는 두 가지 방법을 보여줍니다. 첫 번째 방법은 전체 파일의 경로를 가지고 FileWriter을 생성하지만, 저장할 파일이 File 객체로 이미 생성되어 있다면 두 번째 방법으로 좀 더 쉽게 FileWriter를 생성할 수 있습니다.

```java
// 방법 1
FileWriter fw = new FileWriter("/Users/limjun-young/Temp/file.txt");

// 방법 2
File file = new File("/Users/limjun-young/Temp/file.txt");
FileWriter fw = new FileWriter(file);
```

위와 같이 FileWriter를 생성하면 지정된 파일이 이미 존재할 경우 그 파일을 덮어쓰게 되므로, 기존의 파일 내용 끝에 데이터를 추가할 경우에는 FileWriter 생성자에 두 번째 매개값으로 true를 주면 됩니다.

```java
FileWriter fw = new FileWriter("/Users/limjun-young/Temp/file.txt", true);
FileWriter fw = new FileWriter(file, true);
```

FileWriter는 Writer 하위 클래스이기 때문에 사용 방법이 Writer와 동일합니다. 한 문자를 저장하기 위해서 write() 메소드를 사용하고 문자열을 저장하기 위해서 write(String str) 메소드를 사용합니다.

```java
FileWriter fw = new FileWriter("/Users/limjun-young/Temp/file.txt");
String data = "저장할 문자열";
fw.write(data);
fw.flush();
fw.close();
```

write() 메소드를 호출한 이후에 flush() 메소드로 출력 버퍼에 있는 데이터를 파일로 완전히 출력하도록 하고, close() 메소드를 호출해서 파일을 닫아줍니다. 다음 예제는 문자열 데이터를 `/Users/limjun-young/Temp.file.txt` 파일에 저장합니다.

```java
public class FileWriterExample {
    public static void main(String[] args) throws Exception {
        File file = new File("/Users/limjun-young/Temp/file.txt");
        FileWriter fw = new FileWriter(file, true);
        fw.write("FileWriter는 한글로 된 " + "\r\n");
        fw.write("문자열을 바로 출력할 수 있다 " + "\r\n");
        fw.flush();
        System.out.println("파일에 저장되었습니다.");
    }
}
```

#### 실행 결과

![image](https://user-images.githubusercontent.com/22395934/85298506-259d6480-b4df-11ea-9081-d95924c0bc4a.png)
 
 ## 보조 스트림
 보조 스트림이란 다른 스트림과 연결되어 여러 가지 편리한 기능을 제공해주는 스트림을 말합니다. 보조 스트림을 필터(filter) 스트림이라고도 하는데, 이는 보조 스트림의 일부가 FileInputStream, FileOutputStream의 하위 클래스이기 때문입니다. 하지만 다른 보조 스트림은 이 클래스들을 상속받지 않기 때문에 필터 스트림이란 대신 사용 목적에 맞게 보조 스트림이라고도 부릅니다.

 보조 스트림은 자체적으로 입출력을 수행할 수 없기 때문에 입력 소스와 바로 연결되는 InputStream, FileInputStream, Reader, FileReader, 출력 소스와 바로 연결되는 OutPutStream, FileOutputStream, Writer, FileWriter 등에 연결해서 입출력을 수행합니다. 보조 스트림은 문자 변환, 입출력 성능 향상, 기본 데이터 타입 입출력, 객체 입출력 등의 기능을 제공합니다. 

 보조 스트림을 생성할 때에는 자신이 연결될 스트림을 다음과 같이 생성자의 매개값으로 받습니다.
 
 ```java
보조스트림 변수 = new 보조스트림(연결 스트림)
 ```

 에를 들어 콘솔 입력 스트림을 문자 변환 보조 스트림인 InputStreamReader에 연결하는 코드는 다음과 같습니다.

 ```java
InputStream is = System.in;
InputStreamReader reader = new InputStreamReader(is);
 ```

그리고 문자 변환 보조 스트림인 InputStreamReader를 다시 성능 향상 보조 스트림인 BufferedReader에 연결하는 코드는 아래와 같습니다.

```java
InputStream is = System.in;
InputStreamReader reader = new InputStreamReader(is);
BufferedReader br = new BufferedReader(reader);
```

## 문자 변환 보조 스트림
소스 스트림이 바이트 기반 스트림이면서 입출력 데이터가 문자라면 Reader, Writer로 변환해서 사용하는 것을 고려해야 합니다. 그 이유는 Reader와 Writer는 문자 단위로 입출력하기 때문에 바이트 기반 스트림보다는 편리하고, 문자셋의 종류를 지정할수 있기 때문에 다양한 문자를 입출력할 수 있습니다.

## 객체의 입출력 보조 스트림
자바는 메모리에 생성된 객체를 파일 또는 네트워크로 출력할 수가 있습니다. 객체는 문자가 아니기 때문에 바이트 기반 스트림으로 출력해야 합니다. 객체를 출력하기 위해서는 객체의 데이터(필드값)를 일렬로 늘어선 연속적인 바이트로 변경해야 하는데, 이것을 객체 직렬화라고 합니다. 반대로 파일에 저장되어 있거나 네트워크에서 전송된 객체를 읽을 수도 있는데, 입력스트림으로부터 읽어들인 연속적인 바이트를 객체로 복원하는 것을 역직렬화라고 합니다.

## ObjectInputStream, ObjectOutputStream
자바는 객체를 입출력 할 수 있는 두개의 보조 스트림인 ObjectInputStream과 ObjectOutputStream을 제공합니다. ObjectOutputSteeam은 바이트 출력 스트림과 연결되어 객체를 직렬화하는 역할을 하고, ObjectInputStream은 바이트 입력 스트림과 연결되어 객체로 역직렬화하는 역할을 합니다.

ObjectInputStream과 ObjectOutputStream은 다른 보조 스트림과 마찬가지로 연결할 바이트 입출력 스트림을 생성자의 매개값으로 받습니다.

```java
ObjectInputStream ois = new ObjectInputStream(바이트입력스트림);
ObjectOutputStream oos = new ObjectOuputStream(바이트출력스트림);
```

ObjectOutputStream으로 객체를 직렬화하기 위해서는 writeObject() 메소드를 사용합니다.

```java
oos.wirteObject(객체);
```

반대로 ObjectInputStream의 readObject() 메소드는 입력 스트림에서 읽은 바이트를 역직렬화해서 객체로 생성합니다. readObject() 메소드의 리턴 타입은 Object 타입이기 때문에 객체의 원래 타입으로 변환해야 합니다.

```java
객체타입 변수 = (객체타입) ois.readObject();
```

다음은 다양한 객체를 파일에 저장하고, 다시 파일로부터 읽어 객체로 복원하는 예제입니다. 복수의 객체를 저장할 경우, 출력된 객체 순서와 동일한 순서로 객체를 읽어야 합니다.

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class StreamObjExam {
    public static void main(String[] args) throws Exception {

        FileOutputStream fos = new FileOutputStream("/Users/limjun-young/Temp/file1.txt");
        ObjectOutputStream oos = new ObjectOutputStream(fos);

        oos.writeObject(new Integer(10));
        oos.writeObject(new Double(3.14));
        oos.writeObject(new int[] { 1, 2, 3 });
        oos.writeObject(new String("홍길동"));

        oos.flush();
        oos.close();
        fos.close();

        FileInputStream fis = new FileInputStream("/Users/limjun-young/Temp/file1.txt");
        ObjectInputStream ois = new ObjectInputStream(fis);

        Integer obj1 = (Integer) ois.readObject();
        Double obj2 = (Double) ois.readObject();
        int[] obj3 = (int[]) ois.readObject();
        String obj4 = (String) ois.readObject();

        ois.close();
        fis.close();

        System.out.println(obj1);
        System.out.println(obj2);
        System.out.println(obj3[0] + "," + obj3[1] + "," + obj3[2]);
        System.out.println(obj4);

    }
}
```

## 직렬화가 가능한 클래스(Serializable)
자바는 Serializable 인터페이스를 구현한 클래스만 직렬화할 수 있도록 제한하고 있씁니다. Serializable 인터페이스는 필드나 메소드가 없는 빈 인터페이스지만, 객체를 직렬화할 때 private 필드를 포함한 모든 필드를 바이트로 변환해도 좋다는 표시 역할을 합니다.

```java
public class XXX implements Serializable {}
```

객체를 직렬화하면 바이트로 변환되는 것은 필드들이고, 생성자 및 메소드는 직렬화에 포함되지 않습니다. 따라서 역직렬화 할때에는 필드의 값만 복원됩니다. 하지만 모든 필드가 직렬화 대상이 되는 것은 아닙니다. 필드 선언에 static 또는 transient가 붙어있을 경우에는 직렬화가 되지 않습니다.

```java
public class XXX implements Serializable {
    public int field1;
    protected int field2;
    int field3;
    private int field4;
    public static int field5;
    transient int field6;
}
```

다음은 직렬화되는 필드와 그렇지 못한 필드가 어떤 것이 있는지 확인해 주는 예제입니다.

```java
public class ClassA implements Serializable {
    int field1;
    ClassB field2 = new ClassB();
    static int field3;
    transient int field4;
}

public class ClassB implements Serializable {
    int field1;
}
```

```java
import java.io.FileOutputStream;
import java.io.ObjectOutputStream;

public class SerializableWriter {
    public static void main(String[] args) throws Exception {
        FileOutputStream fos = new FileOutputStream("/Users/limjun-young/Temp/file2.txt");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        ClassA classA = new ClassA();
        classA.field1 = 1;
        classA.field2.field1 = 2;
        classA.field3 = 3;
        classA.field4 = 4;
        oos.writeObject(classA);
        oos.flush();
        oos.close();
        fos.close();
    }
}
```

```java
import java.io.FileInputStream;
import java.io.ObjectInputStream;

public class SerializableReader {
    public static void main(String[] args) throws Exception {

        FileInputStream fis = new FileInputStream("/Users/limjun-young/Temp/file2.txt");
        ObjectInputStream ois = new ObjectInputStream(fis);

        ClassA v=  (ClassA) ois.readObject();
        System.out.println("field1: " + v.field1);
        System.out.println("field2.field1: " + v.field2.field1);
        System.out.println("field3: " + v.field3);
        System.out.println("field4: " + v.field4);
        
    }
}
```

먼저, SerializableWriter 클래스를 실행하면 ClassA 객체를 직렬화해서 file2.txt 파일에 저장합니다. 그리고 나서 SerializableReader 클래스를 실행하면 file2.txt 파일에 저장된 데이터를 읽고, ClassA 객체로 역직렬화합니다. 실행결과를 보면 field1과 field2는 값이 복원되는 것을 알 수 있으나, static 필드인 field3과 transient 필드인 field4는 값이 복원되지 않습니다. file2.txt 파일에는 field1과 field2의 데이터만 저장되어 있기 때문입니다.


## SerialVersionUID 필드
직렬화된 객체를 역직렬화할 때는 직렬화했을 때와 같은 클래스를 사용해야 합니다. 클래스 이름이 같더라도 클래스의 내용이 변경되면, 역직렬화는 실패하여 다음과 같은 예외가 발생합니다.

```java
java.io.InvalidClassException: XXX; local class incompatable: stream classdesc
seriaVersionUID = -9130799490637378756, local class serialVersionUID = -1174725809595957294
```

위 예제에서 예외의 내용은 직렬화 할 때와 역직렬화할 때 사용된 클래스의 serialVersionUID가 다르다는 것입니다. serialVersionUID는 같은 클래스임을 알려주는 식별자 역할을 하는데, Serializable 인터페이스를 구현한 클래스를 컴파일하면 자동적으로 serialVersionUID 정적 필드가 추가됩니다. 문제는 클래스를 재컴파일하면 serialVersionUID의 값이 달라진다는 것입니다. 네트워크로 객체를 직렬화하여 전송하는 경우, 보내는 쪽과 받는 쪽이 모두 같은 serialVersionUID를 갖는 클래스를 가지고 있어야 하는데 한 쪽에서 클래스를 변경해서 재컴파일하면 다른 serialVersionUID를 가지게 되므로 역직렬화에 실패하게 됩니다.


## WriteObject()와 readObject() 메소드
두 클래스가 상속관계에 있다고 가정해봅시다. 부모 클래스가 Serializable 인터페이스를 구현하고 있으면 자식 클래스는 Serializable 인터페이스를 구현하지 않아도 자식 객체를 직렬화하면 부모 필드 및 자식 필드가 모두 직렬화 됩니다. 하지만 그 반대로 부모 클래스가 Serializable 인터페이스를 구현하지 않고, 자식 클래스만 Serializable 인터페이스를 구현하고 있다면 자식 객체를 직렬화 할 때 부모의 필드는 직렬화에서 제외됩니다. 이 경우 부모 클래스의 필드를 직렬화 하고 싶다면 다음 두가지 방법 중 하나를 선택해야합니다.

- 부모 클래스가 Serializable 인터페이스를 구현하도록 합니다.
- 자식 클래스에서 writeObject()와 readObject() 메소드를 선언해서 부모 객체의 필드를 직접 출력시킵니다.

첫 번재 방법이 제일 좋은 방법이 되겠지만, 부모 클래스의 소스를 수정할 수 없는 경우에는 두 번째 방법을 사용해야 합니다. writeObject() 메소드는 직렬화될 때 자동으로 호출되고, readObject() 메소드는 역직렬화될 때 자동적으로 호출됩니다. 다음은 writeObject()와 readObject() 메소드의 선언 방법을 보여줍니다.

```java
private void writeObject(ObjectOutputStream out) throws IOException {
    out.wirteXXX(부모필드); // 부모 객체의 필드값을 출력함
    ..
    out.defaultWriteObject(); // 자식 객체의 필드값을 직렬화
}
```

```java
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
    부모필드 = in.readXXX();  // 부모 객체의 필드값을 읽어옴
    ..
    in.defaultReadObject(); // 자식 객체의 필드값을 역직렬화
}
```
두 메소드를 작성할 때 주의할 점은 접근 제한자가 private가 아니면 자동 호출되지 않기 때문에 반드시 private를 붙어주어야 합니다. 