
비디오 플레이어를 만든다고 해봅시다. 비디오 플레이어 프로그램은 상단에 제목, 중앙에는 영상 플레이어, 우측은 영상 목록, 하단에는 제어 영역으로 구성되어 있다고 합시다.

이 비디오 플레이어의 주요 기능은 아래와 같습니다.

- 동영상 목록에서 특정 제목을 클릭하면, 제목에 해당하는 동영상이 플레이 영역에 표시되고, 제목 표시 영역에 해당 제목을 표시합니다.

- 영상 제어 영역에서 재생/멈춤 버튼을 누르면 영상 플레이 영역은 영상을 재생하거나 멈추고, 시간 이동 조작을 하던 영상 플레이 영역은 알맞은 시점으로 이동해서 영상을 재생합니다.

- 영상 제어 영역에서 다음/이전 버튼을 영상 목록에서 다음이나 이전 영상을 재생합니다.

- 영상 플레이 화면을 터치하면 플레이가 멈추고 다시 터치하면 재생됩니다. 이때 영상 제어 부분의 UI도 중지/시작 모양으로 알맞게 변경됩니다.

위 기능을 제공하는 플레이어를 만들기 위해 각 영역은 별도 클래스로 구현한다면, 각 클래스는 아래 그림과 같은 의존을 갖게 될 것입니다.


![Untitled Diagram (1)](https://user-images.githubusercontent.com/22395934/81063005-012b0000-8f12-11ea-878b-753a733d045b.png)

위 그림의 구조는 책임 별로 알맞게 클래스가 분리되었지만, 몇 가지 단점이 있습니다. 첫 번째 단점은 재사용이 어렵다는 것입니다.

예를 들어, 비디오가 아닌 MP3를 위한 플레이어에서 MediaController를 재사용하고 싶다고 가정해 보겠습니다. MediaController는 MP3 플레이어를 위한 제어 기능(재생/멈춤/시간 이동/다음 곡/이전 곡)을 제공하고 있지만, MedialController를 재사용하려면 MP3 플레이와 상관없는 VidioPlayer 클래스와 VidioListUI 클래스가 필요합니다. 비슷하게 동영상 재생 기능을 위해 VidioPlayer 클래스만 재사용하고 싶은 경우에도 MedialController 클래스를 필요로 합니다. 불필요한 클래스를 제거하고 싶다면, 소스 코드 수준에서 필요한 코드만 추려 내야만 합니다.

또 다른 단점은 함께 사용되는 클래스가 증가할수록 개별 클래스의 수정이 어려워진다는 점입니다. 예를 들어, 다음과 같은 요구사항이 새롭게 추가되었다고 합시다.

- 플레이 화면 좌측에 시스템이 추천한 영상 목록을 표시
- 영상 자막 존재 시, 자막 표시

이 요구 사항을 충족하기 위해 클래스 구조는 아래 그림처럼 바귈 것입니다. 이제 VidioPlayer 클래스의 변경은 너무 많은 클래스에 영향을 주게 됩니다. 심지어 CaptionUI의 변경이 VidioPlayer에 영향을 주고 이로 인해 다른 클래스가 변경될 수도 있습니다.


![Untitled Diagram](https://user-images.githubusercontent.com/22395934/81063928-ae524800-8f13-11ea-8f34-cfb525bd967b.png)

책임에 따라 알맞은 객체를 분리함으로써 객체의 재사용을 높일 수 있을거라 기대했지만, 위 그림은 역할에 맞게 객체를 분리했음에도 불구하고 오히려 전체 클래스가 하나의 단일 구조가 되어 변경이나 재사용이 어렵게 되는 상황이 발생하였습니다.

이런 문제가 발생한 이유는 객체 간의 의존이 직접 연결되어 있기 때문입니다. 클래스 다이어그램을 살펴보면 VidioPlayer 객체는 여러 객체와 통신하는 규칙이 잘 정해져 있지만, 이들 객체들에 대한 직접적인 의존을 갖고 있습니다.

예를 들어, MediaController 클래스의 시간 이동 관련 코드는 VidioPlayer 클래스의 메서드를 직접 실행하고, VidioPlayer 클래스는 다시 CaptionUI 클래스의 메서드를 직접 실행합니다. 이렇게 객체 간의 메시지 흐름을 각 클래스에 직접적인 의존으로 구현하게 되면, 개별 클래스의 재사용이 어려워지고 메시지 흐름을 변경하려면 관련된 클래스들을 모두 변경해 주어야 하는 문제가 발생하게 됩니다.

미디에이터(Mediator) 패턴을 사용하면 이런 문제를 해소할 수 있습니다. 미디에이터 패턴은 각 객체들이 직접 메시지를 주고받는 대신, 중간에 중계 역할을 수행하는 미디에이터 객체를 두고 미디에이터를 통해서 각 객체들이 간접적으로 메시지를 주고 받도록 합니다. 앞서 비디오 플레이어에 미디에이터 패턴을 적용하면 아래 그림과 같은 구조를 갖게 됩니다.

![Untitled Diagram (2)](https://user-images.githubusercontent.com/22395934/81065536-a0ea8d00-8f16-11ea-91f3-ebbfdb22a2d6.png)

위 그림에서 중간에 위치한 VidioMeiator 클래스가 미디에이터 역할을 하며, 나머지 VidioPlayer, MediaController, VidioListUIM TitleUI는 협업 대상 역할을 수행합니다. 이들 협업 대상 객체들은 서로 직접적인 의존을 맺기보다는, 미디에이터 객체를 통해서 간접적으로 연결됩니다.

예를 들어, VidioListUI 클래스는 VidioPlayer 클래스에 직접적으로 의존하지 않는 대신, VidioMediator 클래스에 대한 의존만 갖고 있습니다. VidioListUI 객체는 목록에서 특정 비디오가 선택되면, VidioListUI 객체는 아래 리스트처럼 VidioMediator 객체의 메서드를 호출합니다.

```java
//VidioListUI
private VideoMediator videoMediator;

public void onSelectedItem(int selectedIdx) {

    VideoInfo videoInfo = videoList.get(selectedIdx);
    videoMediator.selectVideo(videoInfo.getFile());
}
```

VideoListUI 객체로부터 재생할 비디오 정보를 받은 VideoMediator 객체는 아래 코드처럼 그 정보를 VidioPlayer 객체에 전달해서 영상을 재생하도록 하고 TitleUI 객체에 전달해서 제목을 변경하도록 합니다.

```java
// VideoMediator
private VideoPlayer videoPlayer
private TitleUI titleUI;

public void selectVideo(File videoFile) {
    // 미디에이터는 다른 협업 객체에게 요청을 전달합니다.
    videoPlayer.play(videoFile);
    titleUI.setTitle(videoFile);
}
```

비슷하게 다른 협업 객체들도 모든 요청을 미디에이터에 보내며, 미디에터는 그 요청을 처리에 알맞은 객체를 실행합니다. 이렇게 각 협업 객체가 서로 알 필요 없이 미디에이터가 각 객체 간의 메시지 흐름을 제어하기 때문에, 새로운 협업 객체가 추가되더라도 기존 클래스를 수정할 필요 없이 미디에이터 클래스만 수정해 주면 됩니다. 물론 메시지 흐름이 변경되더라도 메시지 흐름을 실제로 제어하는 건 미디에이터이므로 미디에이터만 수정될 뿐 각 협업 클래스를 수정할 필요는 없으며 수정하더라도 변경 범위가 최소화 됩니다.

미디에이터 패턴은 각 협업 클래스에 흩어져 있는 흐름 제어를 미디에이터로 모으기 때문에, 각 협업 클래스의 코드는 단순해 집니다. 각 협업 클래스는 미디에이터에만 의존하거나 또는 미디에이터나 다른 협업 클래스에 의존하지 않기 때문에, 개별 협업 클래스를 수정하거나 확장하거나 재사용하기 쉬워집니다. 또한 미디에이터에 각 협업 객체의 흐름 제어 코드가 모여 있기 때문에 전체 협업 객체 간의 메시지 흐름을 이해하고 수정하고 확장하는 것을 상대적으로 쉽게 만들어 줍니다.

반면에, 미디에이터 패턴을 사용할 때의 단점은 협업 클래스의 개수가 증가할수록 미디에이터의 코드는 복잡해지기 때문에, 미디에이터 자체를 유지 보수하는 것은 협업 클래스에 비해 어려워진다는 것입니다.

## 추상 미디에이터 클래스의 재사용
미디에이터 패턴을 적용할 때 협업 객체 간의 동일한 메시지 흐름이 서로 다른 기능에서 반복해서 사용될 경우, 미디에이터 추상 클래스를 사용함으로써 미디에이터 자체의 재사용을 높일 수 있습니다.

예를 들어, MP3 플레이어와 비디오 플레이어는 협업 객체의 구성이 미디어 제어기, 플레이 목록, 제목 표시로 비슷합니다. 이 경우, 상위의 추상 미디에이터 클래스를 만들고, 비디오 플레이어와 MP3 플레이어를 위한 하위 미디에이터를 만들어서 객체 간의 협업 흐름을 재사용할 수 있게 됩니다.


PlayMediator는 추상 클래스로서 MediaController, ListUI, TitleUI 객체들 간의 메시지 흐름을 제어하는 역할을 수행합니다. PlayerMediator 클래스에서 주의해서 살펴볼 부분은 아래 코드에서 보여준 것처럼 select() 메서드는 구현을 제공하는 반면에 volumeChanged() 메서드는 추상 메서드라는 점입니다.

```java
public abstract class PlayerMediator implements ControllerObserver {
    private MediaController mediaController;
    private TitleUI titleUI;

    public PlayerMediator() {
        this.mediaController = new MediaController();
        this.mediaController.addObserver(this);
        this.titleUI = new TitleUI();
        ...
    }

    public void select(File file) {
        titleUI.setTitle(file);
    }

    ...
    // volumeChanged의 구현은 제공하지 않음
    // MediaController 객체의 볼륨 조절 이벤트 발생시
    // volumeChanged() 메서드 호출

}
```

select() 메서드는 협업 객체 간의 흐름 제어를 제공하므로 하위 클래스에서는 select() 메서드를 재사용해서 기능을 확장합니다. 반면 volumeChanged() 메서드는 추상 메서드이므로 흐름 제어를 재사용해서 기능을 확장합니다. 반면 volumeChanged() 메서드는 추상 메서드이므로 흐름 제어를 재사용하기보다는 하위 클래스에서 알맞게 기능을 구현합니다. 예를 들어, 동영상 플레이 기능을 구현해야 할 경우, PlayerMediator 추상 클래스를 상속받은 VideoPlayerMediator 클래스를 아래 코드처럼 만들 수 있을 것입니다.

```java
//PlayerMediator 클래스를 재사용하면서 필요한 기능 확장
public class VideoPlayerMediator extends PlayerMediator {

    private VideoPlayer videoPlayer;

    public VideoPlayerMediator() {
        super();
        this.videoPlayer = new VideoPlayer();
    }

    @Override
    public void select(File file) {
        videoPlayer.play(file);
        super.select(file); // 상위 미디에이터에 정의된 협업 기능 재사용
    }

    // 하위 미디에이터에서 새로운 협업 기능 구현
    public void volumeChanged(int volume) {
        videoPlayer.changeVolume(volume);
    }
}
```

VideoPlayerMediator 클래스는 PlayerMediator 클래스가 제공하는 객체 연동 부분을 재사용하면서 (select() 메서드 재정의 부분) 동시에 비디오 플레이를 위한 객체와의 협업 기능을 추가하고 있습니다. 즉, VideoPlayerMediator는 PlayerMediator 클래스에 정의된 ListUI, MediaController,  TitleUI 객체 간의 메시지 흐름을 재사용하면서, 비디오 관련 기능을 확장하고 있는 것입니다.

 
 > 참조: 최범균의 객체 지향과 디자인 패턴
