# unity
unity study

C# 상속성과 다형성 공부(2022.09.27)

이하 마크다운 에디터

## 상속성

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CSharp
{
    class Player
    {
        protected int hp;
        protected int attack;
    }

    class Knight : Player
    {
    }

    class Mage : Player
    {
        public int mp;

    }
    class Program
    {
        static void EnterGame(Knight knight)
        {

        }
        static void EnterGame(Mage mage)
        {

        }
        // 이렇게 할 필요가 없다 왜?
        // Knight mage 둘 다 모두 Player를 상속받기 때문에.


        //이렇게 Player를 사용하면 가능!
        //하지만 이렇게 할 경우 현재 Mage에 따로 있는 mp는 사용 할 수 없다.
        static void EnterGame(Player player)
        {
            // crash 예측
            // is를 사용하는 방법과 as를 사용하는 방법
            // is 사용시 boolean 사용

            Mage mage = (player as Mage);
            if (mage != null)
            {
                mage.mp = 10;
            }
            //player가 Mage가 아닐시, mage -> Null
            //Null 이 아닌 경우에는 mage로 변환.
        }


        static void Main(string[] args)
        {
            Knight knight = new Knight();
            Mage mage = new Mage();
            EnterGame(knight);
            EnterGame(mage);

            Player magePlayer = mage;
            Mage mage2 = magePlayer; //불가능!

            // Mage 타입 -> Player 타입 OK
            // Player 타입 -> Mage 타입 ?

            Mage mage3 = (Mage)magePlayer;

            // 이렇게 강제로 캐스팅 하면 가능은 하다!
            // 하지만 이렇게 한 경우 문제점?
            // 원래는 Knight 인 애를 Player로 바꾸었다가 실수로 Mage로 변환한 경우?
            // 강제로 변환했기에 문법상으로는 아무런 문제가 없다
            // 하지만 실행하면 캐스팅에 실패했다는 문구가 발생한다 (crash 발생)

            // 정리
            // 자식 클래스에서 부모 클래스로 변환하는것은 아무런 문제가 되지 않는다.
            // 부모 클래스에서 자식 클래스로 변환하는 것은 될수도 있고 안 될수도 있다.
            // 강제로 변환은 가능하지만 크래쉬 위험이 있다.

            // 크래쉬 위험이 일어나기전 코드 단계에서 어떻게 알아차릴 수 있는가?

            // is or as 사용
            // EnterGame(Player player) 참조


        }
    }
}

```

## 다형성

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CSharp
{
    class Player
    {
        protected int hp;
        protected int attack;

        // 다형성
        public void Move()
        {
            Console.WriteLine("Player 이동!");
        }
    }

    class Knight : Player
    {
        public int mp;

        // public void Move() 로 할 시 경고, 기존의 Player.Move를 가리게 되니 조심하라
        // new 를 붙여라
        public new void Move()
        {
            Console.WriteLine("Knight move");
        }
    }

    class Mage : Player
    {
        public int mp;

        public new void Move()
        {
            Console.WriteLine("Mage move!");
        }

    }
    class Program
    {


        static void EnterGame(Player player)
        {
            // crash 예측
            // is를 사용하는 방법과 as를 사용하는 방법
            // is 사용시 boolean 사용

            Mage mage = (player as Mage);
            if (mage != null)
            {
                mage.mp = 10;
            }
            //player가 Mage가 아닐시, mage -> Null
            //Null 이 아닌 경우에는 mage로 변환.
        }


        static void Main(string[] args)
        {
            Knight knight = new Knight();
            Mage mage = new Mage();

            knight.Move();
            mage.Move();
            //이렇게 move 사용시 각 직업별 move 알림이 뜬다.
            //하지만 Player를 Parameter로 받는 EnterGame 내에서 move를 사용하게 되면
            //Player Move가 작동이 된다! (들어간 것이 사실 mage 혹은 Knight임에도 불구하고)
            //하지만 우리가 하고싶은건 직업별 움직임을 하고 싶은데...?
            //방법 1. as를 사용한다. Player 직업을 일일히 검사해야함.
            //그렇지만 직업이 많아지게 된다면 이는 너무 비효율적임!
            //So, how we can?
            //여기서 필요한 것이 다형성!

        }
    }
}
```

## 다형성 2

위에서의 문제점을 해결하기 위한 다형성 코드

**오버로딩, 오버라이딩**

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CSharp
{
    class Player
    {
        protected int hp;
        protected int attack;

        // 다형성 virtual
        public virtual void Move()
        {
            Console.WriteLine("Player 이동!");
        }
    }

    class Knight : Player
    {
        public int mp;

        // 다형성 override
        // 다양한 형태에 따라 다른 동작을 하겠다.
        public override void Move()
        {
            Console.WriteLine("Knight move");
        }
    }

    class Mage : Player
    {
        public int mp;

        public override void Move()
        {
            Console.WriteLine("Mage move!");
        }

    }
    class Program
    {


        static void EnterGame(Player player)
        {
            player.Move();


            Mage mage = (player as Mage);
            if (mage != null)
            {
                mage.mp = 10;
            }
        }


        static void Main(string[] args)
        {
            Knight knight = new Knight();
            Mage mage = new Mage();

            EnterGame(knight);
            EnterGame(mage);
        }
    }
}
```

위의 다형성1 코드와의 차이점은  
Player의 Move에서 **'virtual'** 이 생겼다.

그리고 이 Player를 상속받는 Knight와 Mage의 Move 코드에는 'new' 대신 **override**가 들어간다.

오버라이딩을 오버로딩과 헷갈려서는 안된다!

오버로딩은 함수 이름의 재사용이다!  
이름은 같으나 인자가 다른 함수의 경우 -> 오버로딩

다형성을 이용한 위와 같은 방법(virtual, override)  
이것이 오버라이딩!

## 중간 메모 1

자식이 부모의 것을 사용하고 싶은 경우?  
base. 을 사용하자!

## 중간 메모 2

### sealed override

자신은 부모의 것을 override하여 사용하지만, 나의 자식은 override하는 걸 막겠다?  
override 앞에 sealed를 붙여주자.


# [골든벨] 서버 통신 공부(2022.10.10)

## Lobby System

Unity Netcode 를 활용한 예제 프로그램 ‘Boss Room’의 가이드 참고

[Lobby Creation | Unity Multiplayer Networking](https://docs-multiplayer.unity3d.com/netcode/0.1.0/learn/dapper/lobbycreation/index.html)

## 관련 코드 정리

```csharp
IEnumerator WaitToEndLobby()
        {
            yield return new WaitForSeconds(3);
            SceneLoaderWrapper.Instance.LoadScene("BossRoom", useNetworkSceneManager: true);
        }
```

```csharp
/// <summary>
        /// Loads a scene asynchronously using the specified loadSceneMode, with NetworkSceneManager if on a listening
        /// server with SceneManagement enabled, or SceneManager otherwise. If a scene is loaded via SceneManager, this
        /// method also triggers the start of the loading screen.
        /// </summary>
        /// <param name="sceneName">Name or path of the Scene to load.</param>
        /// <param name="useNetworkSceneManager">If true, uses NetworkSceneManager, else uses SceneManager</param>
        /// <param name="loadSceneMode">If LoadSceneMode.Single then all current Scenes will be unloaded before loading.</param>
        public void LoadScene(string sceneName, bool useNetworkSceneManager, LoadSceneMode loadSceneMode = LoadSceneMode.Single)
        {
            if (useNetworkSceneManager)
            {
                if (IsSpawned && IsNetworkSceneManagementEnabled && !NetworkManager.ShutdownInProgress)
                {
                    if (NetworkManager.IsServer)
                    {
                        // If is active server and NetworkManager uses scene management, load scene using NetworkManager's SceneManager
                        NetworkManager.SceneManager.LoadScene(sceneName, loadSceneMode);
                    }
                }
            }
            else
            {
                // Load using SceneManager
                var loadOperation = SceneManager.LoadSceneAsync(sceneName, loadSceneMode);
                if (loadSceneMode == LoadSceneMode.Single)
                {
                    m_ClientLoadingScreen.StartLoadingScreen(sceneName);
                    m_LoadingProgressManager.LocalLoadOperation = loadOperation;
                }
            }
        }
```

Scene → Scene 

서버에서 일괄적으로 해당 씬에 있는 유저들 이동

```csharp
//Lobby and Relay calls done from UI

        public async void CreateLobbyRequest(string lobbyName, bool isPrivate, int maxPlayers)
        {
            // before sending request to lobby service, populate an empty lobby name, if necessary
            if (string.IsNullOrEmpty(lobbyName))
            {
                lobbyName = k_DefaultLobbyName;
            }

            BlockUIWhileLoadingIsInProgress();

            bool playerIsAuthorized = await m_AuthenticationServiceFacade.EnsurePlayerIsAuthorized();

            if (!playerIsAuthorized)
            {
                UnblockUIAfterLoadingIsComplete();
                return;
            }

            var lobbyCreationAttempt = await m_LobbyServiceFacade.TryCreateLobbyAsync(lobbyName, maxPlayers, isPrivate);

            if (lobbyCreationAttempt.Success)
            {
                m_LocalUser.IsHost = true;
                m_LobbyServiceFacade.SetRemoteLobby(lobbyCreationAttempt.Lobby);

                m_GameNetPortal.PlayerName = m_LocalUser.DisplayName;

                Debug.Log($"Created lobby with ID: {m_LocalLobby.LobbyID} and code {m_LocalLobby.LobbyCode}, Internal Relay Join Code{m_LocalLobby.RelayJoinCode}");
                m_GameNetPortal.StartUnityRelayHost();
            }
            else
            {
                UnblockUIAfterLoadingIsComplete();
            }
        }
```

[https://www.youtube.com/watch?v=RtBf4v0LjHU](https://www.youtube.com/watch?v=RtBf4v0LjHU)

## Lobby 구현을 위해 필요한 것.

1. 유저의 준비상태(READY 등)를 담을 수 있는 Network Variable
2. Session Scene
3. NetworkManager.SceneManager.LoadScnene

현재 예제로 구성되어 있는 스크립트가 Netcode 구버전으로 지금 버전과 호환되지 않은 부분이 있어 구현에 있어 어려움을 겪는중.

또한 모바일 환경에서 Host 를 운용하려면 유저가 서버가 되어야하는데, 이를 구현함에 있어서 포트포워딩 등 어려움이 있어 목표 구현인 데모버전에서는 하나의 서버가 일괄된 처리를 할 예정이므로 예제와 다른 구현방법이 필요할것으로 예상.




## 코루틴(Coroutine)(2022.10.17)

### Coroutine 코루틴의 사용이유

일반적으로 유니티에서 함수를 호출하면 값을 반환하기 전에 실행이 완료되어버린다.

즉, 함수에서 수행되는 모든 Action이 하나의 Frame에서 발생하고 그친다는것이다.

그렇기 때문에 Update() 같이 프레임간격으로 실행되는 함수안에서 실행하지 않고 단일 호출로 함수를 실행하게 되면?

그 함수는 아마 우리가 기대한 효과를 가질 수 없을것이다.

```
void Fade() 
{
    for (float f = 1f; f >= 0; f -= 0.1f) 
    {
        Color c = renderer.material.color;
        c.a = f;
        renderer.material.color = c;
    }
}
```

위의 코드가 그 예시와 같다.

alpha값을 점진적으로 줄이고 싶은 의도가 다분해보이지만 

실상 함수를 실행하게 되면 점진적으로 줄어드는 중간과정없이 함수가 실행된 오브젝트는 단숨에 투명해질것이다.

**하나의 프레임 업데이트에서 전체적으로 실행이 되어버렸기 때문이다.**

그렇다면 어떻게 해야할까? 원하는 효과를 내기 위해서는 Update()에 추가해야할까?

물론 불가능한것은 아니며, 어쩌면 이는 괜찮은 방법이 될 수 도 있을것이다.

하지만, 비슷한 효과를 다른 버전으로 10개 20개 수십개를 만들어야 한다고 가정하면 그 모든것을 update문에 넣고 조건문으로 처리를 한다는것은 유지보수측면이나 여러 관점에서 말도 안되는 일이 될 것이다.

따라서 이러한 경우에는 **Coroutine**을 사용하면 편리하다.

```
IEnumerator Fade() {
    for (float f = 1f; f >= 0; f -= 0.1f) {
        Color c = renderer.material.color;
        c.a = f;
        renderer.material.color = c;
        yield return null;
    }
}
```

코루틴은 이와 같이 IEnumerator을 반환값으로 가지며 yield로 구성되어있다.

**yield return** 에 조금 주목을 할 필요가 있는데, 이를 통해서 현재 위치를 기억을 하고 다른 루틴에게 수행권한을 넘겨서 여러개의 쓰레드가 마치 동시에 동작하는것과 같은 효과를 제공하게 된다.

이 부분에 대해서는 아래의 블로그에서 굉장히 상세히 설명되어있어. 참고하면 좋을 듯 하다.

[Unity C# &#8211; Coroutine 알아보기 | 아이군의 블로그](http://theeye.pe.kr/archives/2725)

**여러개의 쓰레드가 마치 동시에 동작하는것과 같아 보인다.**

우리는 이 말에 조금 더 관심을 가져야 한다. 왜냐하면 **유니티는 한 번에 하나의 작업만 처리하는 단일 스레드 방식을 사용**하기 때문이다.

[이벤트 함수의 실행 순서 - Unity 매뉴얼](https://docs.unity3d.com/kr/2021.3/Manual/ExecutionOrder.html)

이렇듯 위와같은 순서로 이벤트 함수를 사용한다, Update함수같은 경우에는 반복해서 그 부분이 사용된다고 보면된다.

그리고 Coroutine의 경우에는 보면 알겠지만 **Game Logic Layer**에 위치하여 Update와 함께 yield를 사용하여 루틴을 번갈아가며 실행한다고 생각하면 된다.

**따라서 이 yield를 반환하는 시점, 양보하는 시점을 프로그래머가 조율할 수 있기때문에**

**원하는 타이밍에 맞추어 코드를 실행하고 종료할 수 있다는것에서 큰 장점을 갖는다.**

**또한 원하는 Coroutine을 키고 끌 수 있는 함수를 지원하기에 필요한것만 실행 / 중지 할 수있다.**

- 여러 루틴을 yield를 통해 번갈아 가며 실행 가능하다
- 특정 조건에 맞춰 실행하는, 유연한 프로그래밍이 가능하다
- 쓰레드가 아니며, 비동기 즉 동시에 실행되지 않는다

장점은 이렇게 볼 수 있는데 그렇다면 Coroutine의 단점으로는 무엇이 있을까?

### Coroutine 코루틴의 단점

크게 두 가지로 볼 수있다.

첫 번째는 사실 단점이라고 하면 단점이고 아니라면 아니라고 할 수도 있겠지만

**1\. 오브젝트가 비활성화 되면 코루틴은 정지한다.**

단, 오브젝트는 활성화 되어있고 스크립트만 비활성화 되어있으면 코루틴은 유지된다.

이 부분에 대해서는 추후 Invoke와의 차이점에서 조금 더 상세히 다룰 예정이다.

**2\. Garbage를 많이 생성한다.**

코루틴을 시작하기 위한 StartCoroutine() 메서드는 이 코루틴을 관리하기 위해 인스턴트가 생성된다. 

여기서도 가비지가 생성이 되지만 이는 유니티 자체 엔진의 문제이고 통상보다 많은 가비지를 생성한다고 해서 우리가 크게 조치할 수있는 부분은없다.

다만, 되도록 불필요한 StartCoroutine()을 자제해야한다.

그리고 yield를 할때 보통 

```
yield new WaitforSeconds()
```

이렇게 new 를 통해 인스턴트를 생성해서 하게 되는데 이 부분에서 가비지를 생성하게 된다.

다행이도, 이 부분에 있어서 캐싱을 하면 가비지를 방지할 수 있다.

[코루틴 가비지 최적화](https://ejonghyuck.github.io/blog/2016-12-12/unity-coroutine-optimization/)

 [유니티 코루틴 최적화

YieldInstruction을 캐싱해서 코루틴에서 사용하는 yield 구문을 최적화하자

ejonghyuck.github.io](https://ejonghyuck.github.io/blog/2016-12-12/unity-coroutine-optimization/)

캐싱 방법에 대해서는 위의 글이 굉장히 좋아 첨부한다.
