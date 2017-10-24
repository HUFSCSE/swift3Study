# Chapter 18 상속
 * 상속 : 상속(相續)이란 사람의 사망에 의한 재산 및 신분상의 지위의 포괄적인 승계를 말한다.
* 부모의 기능을 자식이 물려받는것
* ParentsClass/SuperClass ---상속---> ChildClass/SubClass
* 스위프트에서 상속은 오직 클래스
* 기반 클래스 : 상속을 받지않은 클래스

~~~swift
//기반 클래스
Class Person{
    //저장 프로퍼티
    var name: String = "" 

    //연산 프로퍼티
    var introduction: String{
        return "이름: \(name)" 
    }

    //메서드
    func speak(){
        print("안녕하세요")
    }
}
~~~
## 18.1 클래스 상속

* 기반클래스를 다른 클래스가 물려받는것
* 기반클래스의 기능, 프로퍼티 받고, 자신의 기능 추가

~~~swift
Class Student: Person{
    var grade: String = "F"

    func study(){
        print("study hard..")
    }
}

let jay = Student()
jay.name = "Jay"
print(jay.introduction) // 이름: Jay
print(jay.speak) //안녕하세요
print(jay.study) //study hard..
~~~

## 18.2 재정의
* 자식클래스는 부모클래스의 특성을 재정의 할수있다. ==  Override
* 자식클래스에서 부모클래스특성을 사용할때는 super 프로퍼티를 사용하면 접근가능하다.

### 18.2.1 메서드 재정의

* 부모 클래스로 상속 받은 인스턴스 메서드나, 타입 메서드를 재정의 할수있다.

### 18.2.2 프로퍼티 재정의

* 저장 프로퍼티는 재정의 될수없다.
* 프로퍼티 재정의는 프로퍼티 자체를 제정의 하는것이 아닌, 접근자(getter), 설정자(setter), 감시자(Property Observer)등을 재정의 한다는 것.
* 조상 클래스에서 읽기전용이었더라도, 자식 클래스에서 읽기, 쓰기로 바꿀수있다.
* 그러나, 읽기 쓰기가 가능했던 프로퍼티를 읽기 전용으로 바꿀수 없다.

~~~swift
class Person{
    var name: String = "james"
    
    var introduction: String{
        return "이름 : \(name)"
    }
    
    func speak(){
        print("안녕하세요")
    }
    
    class func introduceClass() -> String{
        return "인류의 소원은 평화입니다."
    }
}

class Student: Person{
    
    //프로퍼티 재정의
    override var introduction: String{
        //부모 저장 프로퍼티 호출
        return "저의 이름은 : \(super.name)"
    }
    
    //부모 메서드 호출
    class func introduceClass(){
        print(super.introduceClass())
    }
    //메소드 재정의
    override class func introduceClass() -> String{
        return "내 소원은 파멸입니다."
    }
}

let student = Student()

print(student.introduction)                 //저의 이름은 : james
print(Person.introduceClass())              //인류의 소원은 평화입니다.
print(Student.introduceClass() as Void)     //인류의 소원은 평화입니다.
print(Student.introduceClass() as String)   //내 소원은 파멸입니다.
~~~

### 18.2.3 프로퍼티 감시자 재정의

* 읽기전용, 상수로된 감시자는 재정의 불가능
* 프로퍼티 감시자를 재정의 하더라도, 조상클래스에 정의된 감시자도 동작한다!

~~~swift
class Parents{
    var age: Int? {
        didSet{
            guard let age = age else {return}
            print("Parents age \(age)")
        }
    }
}

class Child: Perents{
    override var age: Int? {
        didSet{
            guard let age = age else {return}
            print("Child age \(age)")
        }
    }
}

let c = Child()
c.age = 145 
//Parents age 145
//Child age 145
~~~

### 18.2.4 서브스크립트 재정의
* 메서드와 같다. 생략.
### 18.2.5 재정의 방지

* 재정의 방지 키워드 final (var, func, subscript)
* 클래스 상속 방지 final class

## 18.3 클래스의 이니셜라저 - 상속과 재정의
* 상속이 가능한 클래스에서는 Initiallizer의 재정의에대한 구분도 필요하다.

### 18.3.1 지정 이니셜라이저와 편의 이니셜라이저
    지정 이니셜라이저(Designated Initializer)
    * 필요에 따라 부모이니셜라이저 호출가능.
    * 정의된 클래스의 모든 프로퍼티 초기화 임무.
    
    편의 이니셜라이저(Convenience Initializer)
    * 지정 이니셜라이저를 내부에서 호출
    * 간단하게 Test용도로 많이 사용.
~~~swift
convenience Init(매개변수){
        (지정 이니셜라이저)
    }
~~~
### 18.3.2 클래스의 초기화 위임
* 자식 클래스의 지정 이니셜라이저는 부모 클래스의 지정 이니셜라이저를 반드시 호출해야한다.
* 편의 이니셜라이저는 자신이 정의된 클래스의 다른 이니셜라이저를 반드시 호출해야합니다.
* 편의 이니셜라이저는 궁극적으로 지정 이니셜라이저를 반드시 호출해야한다.

### 18.3.3 2단계 초기화

    네가지 안정장치
    1. 자식 클래스의 지정 이니셜라이저가 부모 클래스의 이니셜라이저를 호출하기전에 자신의 프로퍼티를 모두 초기화 했는지 확인합니다.
    2. 자식클래스의 지정 이니셜라이저는 상속받은 프로퍼티에 값을 할당하기 전에 반드시 부모클래스의 이니셜라이저를 호출해야합니다.
    3. 편의 이니셜라이저는 자신의 클래스에 정의된 프로퍼티를 포함하여 그 어떤 프로퍼티도 값을 할당하기전에 다른 이니셜라이저를 호출해야합니다.
    4. 초기화 1단계를 마치기 전까지 이니셜라이저는 인스턴스 메서드를 호출할 수 없습니다. 또, 인스턴스 프로퍼티의 값을 읽어들일 수도 없습니다. self 프로퍼티를 자신의 인스턴스를 나타내는 값으로 활용할 수도 없습니다.

* 1단계 각각의 저장 프로퍼티 초기화
* 2단계 저장 프로퍼티 Custom가능


    #### 1단계 
    1. 클래스가 지정 또는 편의 이니셜라이져 호출
    2. 그 클래스의 새로운 인스턴스를 위한 메모리 할당
    3. 지정 이니셜라이저는 모든 저장 프로퍼티가 값을 가지고 있는지 확인, 현재 클래스는 이제 저장 프로퍼티 초기화 완료
    4. 지정 이니셜라이저는 부모 이ㅣ니셜라이저가 같은 동작하도록 초기화 양도
    5. 계속해서 상속 체인을 따라 작업 반복

    #### 2단계
    1. 최상위 클래스로부터 현재 클래스까지 상속 체인을 따라 내려오면서 지정 이니셜라이저들이 인스턴스를 제각각 커스터마이징 합니다. 이 단계에서는 self 프로퍼티를 통해 값을 수정할수 있고, 인스턴스 메서드를 호출하는 등의 작업을 진행할수 있습니다.
    2. 마지막으로 각각의 편의 이니셜라이저를 통해 self를 통한 커스터마이징 작업진행.
    

    


~~~swift
class Perents{
    var name: String?
    
    init() {
        print("parent")
        self.name = "parent"
    }
    init(name: String){
        print("before init")
        self.name = name
        print("after init")
    }
}

class Child: Perents{
    var age: Int?
    
    init(age: Int){
        self.age = age
        print("before init")
        super.init(name: "")
        print("after init")
    }
    convenience init(wow: String){
        print("before init")
        self.init(age: 11)
        print("after init")
    }
}

let c = Child(wow: "")
~~~
### 18.3.4 이니셜라이저 상속 및 재정의

* 일반적으로 부모 클래스의 이니셜라이저는 상속되지않는다.
* 자식 클래스에서 부모 클래스의 이니셜라이져는 호출할수 없다. 그러므로 재정의가 필요하지않다.
* 실패 가능한 이니셜라이져

~~~swift
class Person{
    var name: String
    var age: Int
    
    init() {
        self.name = ""
        self.age = 0
    }
    //실패 가능한 이니셜라이져
    init?(name: String?, age: Int?) {
        guard let n = name, let a = age else {return nil}
        self.name = n
        self.age = a
    }
}

class Strudent: Person{
    //상속받은 실패가능했던 이니셜라이져 재정의
    override init(name: String?, age: Int?) {
        super.init()
    }
}
~~~

### 18.3.5 이니셜라이저 자동 상속

- 부모 클래스의 이니셜라이져가 자동상속 되는 경우
1. 자식 클래스에서 지정이니셜라이져를 구현해줄 필요 없는경우
2. 부모 클래스의 모든 지정 이니셜라이져를 재정의 했을경우, 부모클래스의 편의 이니셜라이져를 사용할수있다.
3. 부모 클래스의 지정 이니셜라이져를 자식 클래스의 편의 이니셜라이져로 재정의 할수있다.


~~~swift
import Foundation

class Person{
    var name: String
    var age: Int
    
    init() {
        self.name = ""
        self.age = 0
    }
    
    init?(name: String?, age: Int?) {
        guard let n = name, let a = age else {return nil}
        self.name = n
        self.age = a
    }
    convenience init(wow: String){
        print("pa")
        self.init()
    }
}

class Strudent: Person{
    override init(name: String?, age: Int?) {
        super.init()
    }
    override init() {
        super.init()
    }
    convenience init(wow: String){
        print("child")
        self.init()
    }
}

let c = Strudent(wow: "") //무엇이출력될까?
~~~

### 18.3.6 요구 이니셜라이저
* 앞서 봤던 자동 상속 규칙에 부합하지 않는한, 반드시 구현해야하는 이니셜라이져
* 수식어는 override 대신 required
* required override ,required convenience

~~~swift
class Person{
    var name: String
    
    required init(){
        self.name = "Unknown"
    }
}

class Student: Person{
    var major: String = "Unknown"
}

let mijeong:Student = Student()
~~~

~~~swift
class Person{
    var name: String
    
    required init(){
        self.name = "Unknown"
    }
}

class Student: Person{
    var major: String = "Unknown"
}

let mijeong:Student = Student()
~~~

~~~swift
class Person{
    var name: String
    
    required init(){
        self.name = "Unknown"
    }
}

class Student: Person{
    
    var major: String = "Unknown"
    
    init(major: String) {
        self.major = major
    }
    
    required init(){
        super.init()
    }
    
}
let mijeong:Student = Student()
~~~

~~~swift
class Person{
    var name: String
    
    init(){
        self.name = "Unknown"
    }
}

class Student: Person{
    
    var major: String = "Unknown"
    
    required override init() {
        
    }
    
}

class University: Student{
    init(age: Int){
        
    }
    required init(){
        super.init()
    }
}

let mijeong:Student = Student()
~~~