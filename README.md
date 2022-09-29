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
