# Chapter9. 구조체와 클래스

* 용도에 맞게 묶어 표현 할 때 용이하다.
* 프로퍼티와 메서드를 사용하여 구조화된 데이터와 기능을 가질 수 있다.

```
참조타입? 포인터와 유사한 개념.. 나중에 나옴
```

* 타언어는 하나의 소스파일에 하나만 구현가능하지만 스위프트는 그런 제약이 없다.
* 구조체 안에 구조체, 클래스 안에 클래스 중첩정의 및 선언이 가능하다.

## 9.1 구조체
### 9.1.1 구조체 정의
* 구조체 정의는 대문자 카멜케이스 사용
* 프로퍼티와 메서드는 소문자 카멜케이스 사용

```swift
struct 구조체이름{
	프로퍼티와 메서드들
}
```

```swift
//9-1
struct BasicInfomation{
    var name: String
    var age: Int
}
```

### 9.1.2 구조체 인스턴스의 생성 및 초기화

* 구조체 정의 후 인스턴스를 생성하고 초기화? 맴버와이즈 이니셜라이저를 사용
* 인스턴스가 생성되고 초기화된 후 프로퍼티 값에 접근하고 싶으면 마침표(.)를 사용
* 구조체를 상수로 선언하면 값을 변경할 수 없다.

```swift
struct BasicInfomation{
    var name: String
    var age: Int
}

var heechanInfo: BasicInfomation = BasicInfomation(name: "heechan", age : 29)
heechanInfo.age = 100;
heechanInfo.name = "godheechan"

let chanchanInfo: BasicInfomation = BasicInfomation(name: "chanchan", age : 29);
chanchanInfo.name = "test"; // 변경불가
chanchanInfo.age = 140; // 변경불가
```

## 9.2 클래스

* 스위프트의 클래스는 부모클래스가 없더라도 상속 없이 단독으로 정의 가능

### 9.2.1 클래스 정의
* class 키워드를 사용하여 정의
* 명명법은 구조체와 같음

```swift
class 클래스 이름{
	프로퍼티와 메서드들
}
```

* 상속받을 때는 클래스 이름 뒤에 콜론(:)을 써주고 부모클래스 이름을 명시.
* 상속에대한 자세한내용은 나중에 나옴

```swift
class 클래스 이름 : 부모클래스 이름{
	프로퍼티와 메서드들
}
```

```swift
class Person{
    var height: Float = 0.0
    var weight: Float = 0.0
}
```

### 9.2.2 클래스 인스턴스의 생성과 초기화

* 이니셜라이저를 사용
* 프로퍼티의 기본값이 지정되어 있으면 전달인자로 초기값을 전달해 주지 않아도 됨.

```
타 언어에스는 클래스 인스턴스를 객체라고 부르지만 스위프트 공식문서에는 인스턴스라는 용어를 사용. 보통 객체지향 프로그램 패러다임을 지향하는 언어에스는 클래스의 인스턴스를 객체라고 통칭한다.
```

그렇답니다..

* 이니셜라이즈 된 후 프로퍼티 값에 접근하고싶으면 마침표(.) 이용.

```swift
class Person{
    var height: Float = 0.0
    var weight: Float = 0.0
}

var heechan: Person = Person()

heechan.height = 179.0
heechan.weight = 82.0

let heechanGod: Person = Person()
heechanGod.height = 179.0
heechanGod.weight = 82.0
```

상수로 선언해도 내부 프로퍼티 값을 변경할 수 있다.
**클래스의 인스턴스는 참조 타입이기때문이다!!**

### 9.2.3 클래스 인스턴스의 소멸

* 클래스의 인스턴스는 참조타입이므로 더 참조할 필요가 없을 때 메모리에서 해제된다.
* 이과정을 소멸이라고 칭하고 소멸되기 직전 deinit이라는 메서드가 호출된다.
* denit - > 디이니셜라이저
* 클래스당 하나만 구현가능하고 매개변수와 반한값이 없다. 소괄호도 없다.

```swift
class Person{
    var height: Float = 0.0
    var weight: Float = 0.0
    
    deinit{
        print("Person 클래스이 인스턴스가 소멸됩니다.")
    }
}

var heechanDeinit: Person? = Person()
heechanDeinit = nil
```

## 9.3  구조체와 클래스의 차이

### 같은점
1. 값을 지정하기 위해 프로퍼티를 정의할 수 있다.
2. 기능 수행을 위해 메서드를 정의할 수 있다.
3. 서브스크립트 문법을 통해 가지는 값에 접근하도록 서브스크립트를 정의할 수 있다.
4. 초기화될때 상태를 지정하기 위해 이니셜라이저를 정의할 수 있다.
5. 초기구현과 더블어 새로운 기능을 추가하기 위해 익스텐션을 통해 확장할 수 있다.
6. 특정 기능을 수행하기 위해 특정 프로토콜을 준수할 수 있다.

### 다른점
1. 구조체는 상속이 불가능하다.
2. 타입캐스팅은 클래스의 인스턴스에만 허용된다.
3. 디이니셜라이저는 클래스의 인스턴스에서만 활용 할 수 있다.
4. 참조횟수계산은 클래스의 인스턴스에만 적용된다.

참조횟수계산??
참조 횟수 계산 방식(reference counting)은 메모리를 제어하는 방법 중 하나로, 쓰레기 수집의 한 방식이다. 구성 방식은 단순하다. 어떤 한 동적 단위(객체, Object)가 참조값을 가지고 이 단위 객체가 참조(참조 복사)되면 참조값을 늘리고 참조한 다음 더이상 사용하지 않게 되면 참조값을 줄이면 된다. 보통 참조값이 0이 되면 더이상 유효한 단위 객체로 보지 않아 메모리에서 제거한다.

라고 합니다.

### 9.3.1 값 타입과 참조 타입
구조체 : 값 타입, 클래스 : 참조타입

call by value, call by reference

```swift
struct BasicInfomation{
    var name: String
    var age: Int
}

var heechanInfo: BasicInfomation = BasicInfomation(name: "heechan", age : 29)
heechanInfo.age = 100;
heechanInfo.name = "godheechan"

//값을 복사하여 할당
var friendInfo: BasicInfomation = heechanInfo

print("heechan's age: \(heechanInfo.age)") // 100
print("friendInfo age: \(friendInfo.age)") // 100

friendInfo.age = 999
print("heechan's age: \(heechanInfo.age)") // 100
print("friendInfo age: \(friendInfo.age)") // 999


class Person{
    var height: Float = 0.0
    var weight: Float = 0.0
}

var heechan: Person = Person()
var friend: Person = heechan
heechan.height = 0.0
heechan.weight = 0.0

print("heechan's height: \(heechan.height)") // 0.0
print("friend's height: \(friend.height)") // 0.0

friend.height = 185.0

print("heechan's height: \(heechan.height)") // 0.0
print("friend's height: \(friend.height)") // 0.0

func changeBasicInfo(_ info: BasicInfomation){
    var copiedInfo: BasicInfomation = info
    copiedInfo.age = 1
}

func changePersonInfo(_ info: Person){
    info.height = 50.0
}

changeBasicInfo(heechanInfo)

print("heechan's age: \(heechanInfo.age)") // 100

changePersonInfo(heechan)

print("heechan's height: \(heechan.height)") // 50.0
```

* 클래스의 인스턴스끼리 참조가 같은지 확인하려면 **식별 연산자** 사용 (A===B)

```swift
print(heechan === friend)
print(heechan !== friend)
```


### 9.3.2 스위프트의 기본 데이터 타입은 모두 구조체

```swift
public struct String{
	///An empty 'String'
	public init()
}
```

### 9.4 구조체와 클래스 선택해서 사용

#### 구조체를 사용해야 할 때
* 연관된 간단한 값의 집합을 캡슐화하는 것만이 목적일 때
* 캡슐화된 값이 참조되는 것보다 복사되는 것이 합당할 때
* 구조체에 저장된 프로퍼티가 값 타입이며 참조되는 것보다 복사되는 것이 합당할 때
* 다른 타입으로부터 상속받거나 자신이 상속될 필요가 없을 때
