# Chapter 20 프로토콜

## 20.1 프로토콜이란
* 정의 : 특정 역할을 수행하기위한 프로퍼티, 메서드, 기타 요구사항 등의 청사진
## 20.2 프로토콜 정의
#### 프로토콜 정의
protocol ` 프로토콜이름 ` {
    ` 프로토콜 정의 `
}
#### 프로토콜 채택
```swift
    struct SomeStruct: SomeProtocol, AnotherProtocol{

    }
    class SomeClass: SomeProtocol, AnotherProtocol{

    }
    enum SomeEnum: SomeProtocol, AnotherProtocol{

    }
```

## 20.3 프로토콜 요구사항
### 20.3.1 프로퍼티 요구
* 프로퍼티의 이름, 프로퍼티의 타입
* 클래스 타입 프로퍼티에는 static과 class가 있으나, 따로 구분하지않고 static키워드를 사용하면 된다.

* {get} , {get set}
```swift
protocol SomeProtocol{
    var r: String {get}
    var rw: String {get set}
}

class SomeClass: SomeProtocol{
    var r: String = ""
    var rw: String = ""
}

var some:SomeProtocol = SomeClass()
some.rw = "읽고쓰기" 
some.r = "읽기전용" //Compile ERROR
```

* Sendable 프로토콜을 Mail과 Message클래스
~~~swift
protocol Sendable{
    var from: String {get}
    var to: String {get}
}

class Message: Sendable{
    var sender: String
    var from: String{
        return self.sender
    }
    
    var to: String
    
    init(sender: String, receiver: String) {
        self.sender = sender
        self.to = receiver
    }
}

class Mail: Sendable{
    var from: String
    var to: String
    
    init(sender: String, receiver: String) {
        self.from = sender
        self.to = receiver
    }
}
~~~

### 20.3.2 메서드 요구
~~~swift
protocol Receiveable{
    func received(data: Any, from: Sendable)
}

protocol Sendable{
    var from: Sendable {get}
    var to: Receiveable? {get}
    
    func send(data: Any)
    
    static func isSendableInstance(_ instance: Any) -> Bool
}

class Message: Sendable, Receiveable{
    var from: Sendable{
        return self
    }
    var to: Receiveable?
    
    func send(data: Any) {
        guard let receiver: Receiveable = self.to else {
            print("Message have no receiver")
            return
        }
        receiver.received(data: data, from: self.from)
    }
    
    func received(data: Any, from: Sendable) {
        print("Message received \(data) from \(from)")
    }
    //상속 가능
    class func isSendableInstance(_ instance: Any) -> Bool {
        if let sendableInstance: Sendable = instance as? Sendable{
            return sendableInstance.to != nil
        }
        return false
    }
}

class Mail: Sendable, Receiveable{
    var from: Sendable{
        return self
    }
    
    var to: Receiveable?
    
    func send(data: Any) {
        guard let receiver: Receiveable = self.to else {
            print("Mail have no receiver")
            return
        }
        receiver.received(data: data, from: self.from)
    }
    
    func received(data: Any, from: Sendable) {
        print("Mail received \(data) from \(from)")
    }
    //상속 불가능
    static func isSendableInstance(_ instance: Any) -> Bool{
        if let sendableInstance: Sendable = instance as? Sendable{
            return sendableInstance.to != nil
        }
        return false
    }
}

let myPhoneMessage: Message = Message()
let yourPhoneMessage: Message = Message()

myPhoneMessage.send(data: "halo")

myPhoneMessage.to = yourPhoneMessage
myPhoneMessage.send(data: "Halo")

let myMail: Mail = Mail()
let yourMail: Mail = Mail()

myMail.send(data: "Long time Ago")
myMail.to = yourMail
myMail.send(data: "Long time Ago")


print(Mail.isSendableInstance("sdf"))
print(Mail.isSendableInstance(myPhoneMessage))
myMail.to = myPhoneMessage
myMail.send(data: "schedule")
~~~

* 타입메서드의 상속
~~~swift
class Kakao: Message{
    override class func isSendableInstance(_ instance: Any) -> Bool{
        return false
    }
}
//compile error Mail의 타입 메서드는 static으로 선언되었다.
class Naver: Mail{
    override class func isSendableInstance(_ instance: Any) -> Bool{
        return false
    }
}
~~~
### 20.3.3 가변 메서드 요구

* 프로토콜의 메서드에서 내부 프로퍼티 변경이 필요한경우
* 내부의 값을 변경하는 경우라면 무조건, mutating 명시

~~~swift
protocol Resettable {
    mutating func reset()
}

class PersonClass: Resettable{
    var name: String? = ""
    var age: Int? = 123
    
    //Class의 경우 참조 형태이기 때문, mutating명시 해줄필요가 없다.
    func reset() {
        self.name = nil
        self.age = nil
    }
}
struct PersonStruct: Resettable {
    var name: String?
    var age: Int?
    
    mutating func reset() {
        self.name = nil
        self.age = nil
    }
}

enum PersonEnum: Resettable {
    case God,Slave,Unknown
    
    mutating func reset() {
        self = .Unknown
    }
}
~~~

### 20.3.4 이니셜라이저 요구

* 상속이 필요한 Class의 경우 required 필요
* final Class의 경우 required가 필요하지않다.

~~~swift
protocol Named{
    var name: String {get}
    
    init(name: String)
}

struct Pet: Named {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class Person: Named{
    var name: String
    
    required init(name: String) {
        self.name = name
    }
}

final class Human: Named{
    var name: String
    
    init(name: String) {
        self.name = name
    }
}
~~~

* required와 override 동시에 써야할 경우
~~~swift
protocol Named{
    var name: String {get}
    
    init(name: String)
}

class School{
    var name: String
    
    init(name: String){
        self.name = name
    }
}

class MiddleSchool: School, Named{
    required override init(name: String) {
        super.init(name: name)
    }
}
~~~

## 20.4 프로토콜의 상속과 클래스 전용 프로토콜
* 프로토콜 상속은 클래스 상속과 유사하다.

~~~swift
protocol Readable {
    func read()
}
protocol Writeable {
    func write()
}
protocol ReadWriteSpeakable: Readable, Writeable {
    func speak()
}
class SomeClass: ReadWriteSpeakable{
    func read() {
        
    }
    func write() {
        
    }
    func speak() {
        
    }
}
~~~

* class 전용 프로토콜
* 프로토콜의 상속 리스트에 class키워드를 추가한다.

~~~swift
protocol ReadWriteSpeakable: class, Readable, Writeable {
}
//Compile Error
struct SomeStruct: ReadWriteSpeakable {
}
~~~




## 20.5 프로토콜 조합과 프로토콜 준수 확인

* 하나의 매개변수에 여러 프로토콜을 조합하여 사용할수있다.

~~~swift
protocol Named{
    var name: String {get}
}
protocol Aged{
    var age: Int{get}
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}

func celebrateBirthday(to celebrator: Named & Aged){
    print("Happy birthday \(celebrator.name) Now you are \(celebrator.age)")
}

let james = Person(name: "james", age: 18)
celebrateBirthday(to: james)
~~~

* 타입 캐스팅을 이용하여 프로토콜 준수하는 확인가능

~~~swift
protocol Named{
    var name: String {get}
}
protocol Aged{
    var age: Int{get}
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}

func celebrateBirthday(to celebrator: Named & Aged){
    print("Happy birthday \(celebrator.name) Now you are \(celebrator.age)")
}

let james = Person(name: "james", age: 18)

protocol Named{
    var name: String {get}
}
protocol Aged{
    var age: Int{get}
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}

func celebrateBirthday(to celebrator: Named & Aged){
    print("Happy birthday \(celebrator.name) Now you are \(celebrator.age)")
}

let james = Person(name: "james", age: 18)

print(james is Named)   //true
print(james is Aged)    //true

let bmw = Car(name: "BMW")

//Casting실패
if let castAged = bmw as? Aged {
    print("\(bmw.name) aged \(castAged.age)")
}else{
    print("fail cast")
}

~~~

## 20.6 프로토콜 선택적 요구

* ` @objc ` 속성을 이용하면 선택적 프로토콜을 구현할수 있다.
* ` @objc `는 해당 프로토콜을 Objective-C 에서 사용할수 있도록한다.

~~~swift
@objc protocol Moveable {
    func walk()
    @objc optional func fly()
}

class Tiger: NSObject, Moveable{
    func walk() {
        print("walking tiger")
    }
}
class Bird: NSObject, Moveable{
    func walk() {
        print("walking bird")
    }
    func fly() {
        print("flying bird")
    }
}

let tiger:Moveable = Tiger()
let bird:Moveable = Bird()

tiger.walk()
tiger.fly?()
bird.fly?()
bird.walk()
~~~

## 20.7 프로토콜 변수와 상수

~~~swift
protocol Named{
    var name: String {get}
}
protocol Aged{
    var age: Int{get}
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}

struct Animal: Named {
    var name: String
}

struct Car: Named {
    var name: String
}

struct Pet: Named, Aged {
    var name: String
    var age: Int
}
var somenamed: Named = Pet(name: "LU", age: 13)
somenamed = Person(name: "James", age: 13)
somenamed = Animal(name: "Hippo")
somenamed = Car(name: "Hyundai")
~~~

## 20.8 위임을 위한 프로토콜
* Delegate 정의 : 클래스나 구조체가 자신의 책임이나 임무를 다른 타입의 인스턴스에게 위임하는 것
* 책무를 위임하기 위해 정의된 프로토콜을 준수하는 타입은 자신에게 위임될 일정 책무를 수행할 수 있다는것을 보장합니다.
* 사용자의 특정 행동에 반응하기 위함, 비동기적인 처리에 많이 사용된다.

~~~swift
protocol SpeakAble {
    func speak()
}

class Person: SpeakAble{
    func speak() {
        print("나는 사람이다.")
    }
}

class God{
    var delegate: SpeakAble?
    
    func command(){
        delegate?.speak()
    }
}

let god = God()         //신 생성
let person = Person()   //인간 생성
god.delegate = person   //신이 인간에게 위임한다. 말할수있는 능력을 // 인간이 신에게 말할수있는 능력을 위임하고
god.command()           //신은 명령한다 인간에게 말을 하라고      // 신은 말을한다.?
~~~