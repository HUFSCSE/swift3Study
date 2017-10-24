# Chapter 17 서브스크립트
* 클래스, 구조체, 열거형에 컬렉션, 시퀀스, 리스트 등의 타입의 요소에 접근하는 단축문법 정의
* 별도의 메서드를 구현하지 않아도된다. 편리하다.
* return값은 필수.
* 여러개의 서브스크립트를 정의할수 있으며, 다른 타입을 인덱스로 갖는 여러개의 서브스크립트를 중복 정의 할 수 있다.


## 17.1 서브스크립트 문법
~~~swift
subscript(){
}
~~~
## 17.2 서브스크립트 구현
~~~swift
class TestSubscript{
    var number: Array<Int> = [1,2,3,4,5,6,7,4,4,4,4]
    
    //함수와 같은 형태, subscript사용
    subscript(index: Int) -> Int{
        return number[index]
    }
}

let test = TestSubscript()
let testsubscript = test[2] //3
~~~
## 17.3 복수 서브스크립트

~~~swift
struct Person {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    init(name: String) {
        self.name = name
        self.age = 0
    }
    init(){
        self.name = ""
        self.age = 0
    }
}
class Study{
    var member: Array<Person> = []
    
    subscript(age: Int) -> [Person]{
        get{
            return member.filter{$0.age == age}
        }
    }
    subscript(name: String) -> [Person]?{
        get{
            return member.filter{$0.name == name}
        }set{
            let pp = Person(name: name)
            self.member.append(pp)
        }
    }
    subscript() -> Person?{
        get{
            return nil
        }set{
            guard let person = newValue else {return}
            self.member.append(person)
        }
    }
    func addPerson(name: String, age: Int){
        self.member.append(Person(name: name, age: age))
    }
}

let s = Study()
s.addPerson(name: "송형수", age: 28)
s.addPerson(name: "최승진", age: 32)
s["s"] = [Person()]
s[] = Person(name: "하하", age: 33)
let t = s["송"]
~~~

* 서브스크립트의 set의 용도를 정확히 파악하기 힘듬.
* 변수와 상당히 유사.
* 서브 스크립트 제일 마지막 문장을 보면, 다양한 용도로 사용될수있어 편하다고하는데, 아직.. 느껴지지 않음.

* 결론적으로 간단한 변수명, 메서드를 만들필요없이 subscript로 해결할수있다.
