## Observable 클래스
- Observable
- Maybe
- Flowable 

상황에 맞게 3가지 클래스를 사용

### 2.1 Observable 클래스
- 옵저버 패턴 구현

> #### 옵저버 패턴  
> 객체의 상태 변화를 관찰하는 옵저버 목록을 객체에 등록    
> 상태변화가 있을 때마다 메서드를 호출하여 객체가 직접 목록의 각 옵저버에게 변화를 알려줌    

Observable은 세 가지의 알림을 구독자에게 전달함
> - onNext: Observable이 데이터의 발행을 알림
> - onComplete: 모든 데이터의 발행을 완료했음을 알림. onComplete 이벤트는 단 한번만 발생하고 발생한 이후에는 더 이상 onNext 이벤트가 발생해서는 안됨
> - onError: Observable에서 어떤 이유로 에러가 발생했음을 알림. onError 이벤트가 발생하면 이후에 onNext, onComplete 이벤트가 발생하지 않음(= Observable의 실행 종료)

#### 2.1.1 just() 함수
인자로 넣은 데이터를 차례로 발행하기 위해 Observable을 생성  
한개의 값을 넣을 수도 있고 여러개의 값(최대 10개)을 넣을 수 있음. 단, 타입은 모두 같아야함  
> ##### 예시
```java
public class FirstExample {
	public void emit(){
		Observable.just(1, 2, 3, 4, 5, 6)
		.subscribe(System.out::println);
	}
}
```
> ##### 실행결과
> 1  
> 2  
> 3  
> 4  
> 5  
> 6  

#### 2.1.2 subscribe() 함수
동작시키기 원하는 것을 사전에 정의해둔 다음 실제 실행되는 시점을 조절하기 위해 사용하는 것  
Observable은 just() 등의 팩토리 함수로 데이터 흐름을 정의한 후 subscribe() 함수를 호출해야 실제로 데이터를 발행함.
> ##### 인자가 없는 subscribe()
> onError 이벤트가 발생했을 때만 OnErrorNotImplementedException을 던짐(throw).
> Observable로 작성한 코드를 테스트하거나 디버깅할 때 사용
> ##### 인자가 1개
> onNext 이벤트 처리. onError 이벤트가 발생하면 OnErrorNotImplementedException을 던짐 
> ##### 인자가 2개
> onNext와 onError 이벤트 처리
> ##### 인자가 3개
> onNext, onError, onComplete 모두를 처리함

#### 2.1.3 create() 함수
개발자가 onNext, onComplete, onError같은 알림을 직접 호출해야함  
RxJava에 익숙한 사용자만 활용하도록 권고하기때문에 나는 아직 사용하지않을 것

#### 2.1.4 fromArray() 함수
배열에 들어있는 데이터를 처리할 때 사용(int[] 배열은 Integer로 변환해야 함)
> ##### 예시
```java
Integer[] arr = {100, 200, 300};
Observable<Integer> source = Observable.fromArray(arr);
source.subscribe(System.out::println);
```
> ##### 실행결과
> 100  
> 200  
> 300  

#### 2.1.5 fromIterable() 함수
Iterable 인터페이스를 구현한 클래스에서 Observable 객체를 생성하는 방법  
Iterable 인터페이스에는 ArrayList, HashSet, LinkedList 등이 있다.
> ##### 예시
```java
List<String> names = new ArrayList<>();
names.add("Jerry");
names.add("William");
names.add("Bob");

Observable<String> source = Observable.fromIterable(names);
source.subscribe(System.out::println);
```
> ##### 실행결과
> Jelly  
> William  
> Bob  

#### 2.1.6 fromCallable() 함수
Callable 인터페이스와의 연동을 위해 사용
> ##### 예시
```java
Callable<String> callable = () -> {
	Thread.sleep(1000);
	return "Hello Callable";
};

Observable<String> source = Observable.fromCallable(callable);
source.subscribe(System.out::println);
```

#### 2.1.7 fromFuture() 함수
Future 객체에서 사용  
보통 Executor 인터페이스를 구현한 클래스에 Callable 객체를 인자로 넣어 Future 객체를 반환한다.
> ##### 예시
```java
Future<String> future = Executors.newSingleThreadExcutor().submit(() ->{
	Thread.sleep(1000);
	return "Hello Future";
});

Observable<String> source = Observable.fromFuture(future);
source.subscribe(System.out::println);
```

#### 2.1.8 fromPublisher() 함수
사용하지 않을 것 같음

### 2.2 Single 클래스
Single 클래스는 오직 1개의 데이터만 발행함. 보통 결과가 유일한 서버 API를 호출할 때 사용

#### 2.2.1 just() 함수
Observable과 거의 같음

#### 2.2.2 Observable에서 Single 클래스 사용
Single은 Observable의 특수한 형태이므로 Observable에서 변환.

> ##### 예시
```java
1. 기존 Observable에서 Single 객체로 변환하기
Observable<String> source = Observable.just("Hello Single");
Single.fromObservable(source)
	.subscribe(System.out::pirntln);

2. single() 함수를 호출해 Single 객체 생성하기.
Observable.just("Hello Single")
	.single("default item")
	.subscribe(System.out::println);

3. first() 함수를 호출해 Single 객체 생성하기.
String[] colors = {"Red", "Blue", "Gold"};
Observable.fromArray(colors)
	.first("default value")
	.subscribe(System.out::println);

4. empty Observable에서 Single 객체 생성하기.
Observable.empty()
	.single("default value")
	.subscribe(System.out::println);

5. take() 함수에서 Single 객체 생성하기.
Observable.just(new Order("ORD-1"), new Order("ORD-2"))
	.take(1)
	.single(new Order("default order"))
	.subscribe(System.out::println);
```
