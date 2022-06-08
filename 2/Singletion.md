# Singleton Pattern

## 1. Singleton Pattern 이란?

- 클래스의 인스턴스는 오직 하나임을 보장하며 이 인스턴스에 접근할 수 있는 방법을 제공하는 패턴

## 2. 의도 (Intent)와 동기(Motivation)

-  클래스에서 만들 수 있는 인스턴스가 오직 하나이고, 이에 대한 접근을 어디에서든지 하나로만 **통일하여** 제공

- 어떤 클래스 경우에는 정확히 하나의 인스턴스만을 갖도록 하는 것이 중요하다.

    한 회사에는 하나의 회계 시스템만이 운영

    DataBase와 연결하는 connection은 여러개일 수 있지만, connection pool은 한 개

- 자바에는 전역 변수가 존재하지 않으므로 인스턴스가 하나만 존재하도록 설계해야 하고 이에 접근 하는 방법을 제공

## 3. Class diagram

![singleton](./../img/Singleton.PNG)


## 4. 객체 협력 (collaborations)

- 클라이언트는 Singleton 클래스에 정의된 public 오퍼레이션을 통해서 유일하게 생성되는 Singleton 인스턴스에 접근할 수 있다.

## 5. 중요한 결론 (consequence)

- 유일하게 존재하는 인스턴스로의 접근을 통제할 수 있다.

- 전역 변수를 사용함으로써 발생할 수 있는 오류를 (C++ 의 경우) 줄 일수 있다.

## 6. 예제 

```
public class ConnectionPool {
	
	/**
	 * instace를 선언하는데 이 유일한 인스턴스는 private으로 선언하고, 하나만 사용하기 때문에 정적변수로 선언
	 * 
	 */
	private static ConnectionPool instance = new ConnectionPool();
	
	/**
	 * 자바에는 constructor가 있는데 자바를 배우신 분들은 아시겠지만 constructor를 안쓰면 default constructor가 제공이 됩니다. 자바의 default constructor는 무조건 public입니다.
	 * 그래서 여기에 constructor를 명시적으로 쓰지 않으면 외부에서 ConnectionPool을 계속 생성 할 수 있습니다. 그래서 외부에서 생성 할 수 없게 생성자의 접근제한자를 private로 막아야 합니다.
	 */
	private ConnectionPool() { }
	
	/*
	 * 외부에서 가져 갈 수 있는 public 인터페이스를 제공을 해줘야 되는데 ConnectionPool class 안에 instance가 하나  만들어져 있는데 이미 만들어진 instance를 또 만들수는 없습니다.
	 * 그래서 우리가 제공하는 접근제한자가 public인 함수는 어덯게 해야하냐면 ConnectionPool class를 생성하지 않아도 호출 할 수 있어야 합니다.
	 */
	public static ConnectionPool getInstance() {
		
		// 방어적인 코드
		if (instance == null) {
			instance = new ConnectionPool();
		}
		return instance;
	}
}


public class Test {

	public static void main(String[] args) {
		
		ConnectionPool instance1 = ConnectionPool.getInstance();
		ConnectionPool instance2 = ConnectionPool.getInstance();
		
		System.out.println(instance1 == instance2);
			
		/** ex
		 * Calendar class는 지금 우리가 쓰는 타임스탬프를 가져오는데 Calendar 객체의 프로퍼티가 여러개일 수는 없습니다. 이 말은 즉슨 프로퍼티가 하나입니다. 그렇기 때문에 Calendar class는
		 * 여러번 생성될 필요가 없습니다. 그래서 new Calendar()를 하면 인스턴스화 할 수 없다라고 에러가 발생합니다.
		 */
		Calendar calendar = Calendar.getInstance();
	}
}
```
