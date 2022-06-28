# Builder Pattern

## 1. GoF 디자인 패턴에서의 Builder Pattern 이란? (메서드의 조합으로 결과물을 생성하는 방법)

- 생성에 대한 과정과 각 결과물을 표현하는 방법을 분리하여 동일한 생성 과정에 서로 다른 여러 결과물이 나올 수 있도록 함

- 클라이언트 코드는 Builder가 제공하는 메서드를 기반으로 원하는 결과물을 얻을 수 있음

- 단계별 생성에 중점을 두는 패턴

- 새로운 결과물이 필요한 경우에도 동일한 과정으로 생성을 할 수 있음

![builder](./img/builder.png)

## 2. 의도와 동기

- 생성 과정과 구현 방법을 분리하여 동일한 생성에서 여러 다른 표현이 나올 수 있음

## 3. Class diagram (GoF)
![builderdiagram](./img/builderdiagram.png)


## 4. 객체 협력 (collaborations)

- **Builder**
  Product의 각 요소들을 생성하는데 필요한 추상 메서드가 선언된 클래스나 인터페이스

- **ConcreteBuilder**
  Builder에 선언된 메서드를 구현한 클래스

- **Director**
  Builder 인터페이스를 사용하여 Product를 생성

- **Product**
  결과물 
  
## 5. 중요한 결론 (consequence)

- 생성과정과 구현을 분리함 

- 제품의 다양한 구현이 가능

- 제품의 생산 과정을 더 세분화 할 수 있음

- 클라이언트는 구체적인 사항을 알 필요가 없음

## 6. 예시

```
public interface MakeReport {

	public void MakeHeader();
	public void MakeBody();
	public void MakeFooter();
	
	public String getReport();
}
```
위 인터페이스를 구현하여 TextReport, HTMLReport 등을 생성할 수 있음

## 7. Effective Java 에서 사용하는 Builder Pattern (생성자를 대체하는 방법)

-  객체를 생성할 때 매개 변수가 여러개인 경우 각각의 매개 변수가 점점 늘어나는 여러 개의 생성자를 사용하기 보다는 
 
   인스턴스 생성을 위한 Builder를 제공함으로써, 오류를 방지하고 이후 매개 변수가 늘어나더라도 유연하게 수정할 수 있는 구조를 제공한다.

- 스프링에서 사용하고 있음

## 8. 예제 

- 여러가지 피자를 만드는 방법 

Pizza.java
```
public abstract class Pizza {
	
	public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE};
	final Set<Topping> toppings;
	
	abstract static class Builder {
		EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
		
		public Builder addTopping(Topping topping) {
			toppings.add(Objects.requireNonNull(topping));
			return self();
		}
		public Builder sauceInside() { 
			return self();
		}
		
		abstract Pizza build();
		protected abstract Builder self();
	}
	
	Pizza(Builder builder){
		
		toppings = builder.toppings.clone();
	}

	public String toString() {
		return toppings.toString();
	}
}
```

NyPizza.java
```
public class NyPizza extends Pizza{

	public enum Size { SMALL, MEDIUM, LARGE};
	private final Size size;
	
	public static class Builder extends Pizza.Builder {
			private final Size size;
			
			public Builder(Size size) {
				this.size = Objects.requireNonNull(size);
			}
			
			public NyPizza build() {
				return new NyPizza(this);
			}
			
			protected Builder self() {return this;}
	}
	
	private NyPizza(Builder builder) {
		super(builder);
		size = builder.size;
	}
}
```

Calzone.java
```
public class Calzone extends Pizza{

	private final boolean sauceInside;
	
	public static class Builder extends Pizza.Builder{
		
		private boolean sauceInside = false;
		
		public Builder sauceInside() {
			sauceInside = true;
			return this;
		}
		
		public Calzone build() {
			return new Calzone(this);
		}
		
		protected Builder self() {return this;}
		
	}
	
	private Calzone(Builder builder) {
		super(builder);
		sauceInside = builder.sauceInside;
	}
	
	public String toString() {
		return toppings.toString() + " sauceInside: " + sauceInside;
	}
}
```

PizzaTest.java
```
public class PizzaTest {

	public static void main(String[] args) {

		Pizza nyPizza = new NyPizza.Builder(Size.SMALL).addTopping(Topping.SAUSAGE)
				.addTopping(Topping.ONION).build();

		
		Pizza calzone = new Calzone.Builder().addTopping(Topping.HAM).addTopping(Topping.PEPPER)
				.sauceInside().build();
		
		System.out.println(nyPizza);
		System.out.println(calzone);
	}

}
```

