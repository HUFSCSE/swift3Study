# Chapter 19 타입캐스팅

## 19.1 기존 언어의 타입 변환과 스위프트의 타입 변환
~~~c
//c언어
double value = 3.3
int convertedValue = (int)value
convertedValue = 5.5        //Int로 선언하고, Int값을 넣었지만, 다시 Double을 넣는다.??
                            //이것을 Double -> Int 암시적 데이터 타입변환
~~~
~~~swift
//swift
value: Double = 3.3
convertedValue: Int = Int(value)
convertedValue = 5.5        //오류!
~~~

* 타언어에서 타입변환 혹은 타입캐스팅이라 하는 이것이 스위프트는 다르다. 그냥, 기존 값 Value 전달 인자로 하여 새로운 Int 구조체의 인스턴스 생성한다.

## 19.2 스위프트 타입캐스팅

* 스위프트의 타입캐스팅은 인스턴스의 타입을 확인하거나, 다른 타입인양 행세하는 형태로 사용될 수 있다.
* is == 타입 확인, as == 타입 전환(cast)
* 타입캐스팅을 통해 프로토콜 준수하는지 확인할수있다.
~~~swift
class Coffee{
    let name: String
    let shot: Int

    init(shot: Int){
        self.shot = shot
        self.name = "coffee"
    }
}
class Latte: Coffee{
    var flavor: String

    init(flavor: String, shot: Int){
        self.flavor = flavor
        super.init(shot: shot)
    }
}
class Americano: Coffee{
    let iced: Bool

    init(shot: Int, iced: Bool){
        self.iced = iced
        super.init(shot: shot)
    }
}
~~~
 ![test.jpeg](./test.jpeg)

* Coffee는 Latte나 Americano인척 할수없다.

## 19.3 데이터 타입 확인

~~~swift
let coffee = Coffee(shot: 1)
let mycoffee = Americano(shot: 2, iced: true)
let yourcoffee = Latte(flavor: "green tea", shot: 3)

print(coffee is Coffee) //true
print(coffee is Latte)  //false
print(mycoffee is Coffee)//true
print(mycoffee is Latte) //false
print(mycoffee is Americano)//true
print(yourcoffee is Latte) //true
~~~

* is 외에 다른방법 MetaType
* MetaType은 타입의 타입?

~~~swift
print(Coffee.self is Coffee.Type) //true
print(Coffee.self is Latte.Type)  //false
print(Americano.self is Coffee.Type)//true
print(Americano.self is Latte.Type) //false
print(Americano.self is Americano.Type)//true
print(Latte.self is Latte.Type) //true


let inttype = Int.self
let stringtype:String.Type = String.self
print(inttype)          //Int
print(stringtype)       //String
protocol SomeProtocol {
}
let protocolProtocol: SomeProtocol.Protocol = SomeProtocol.self
print(protocolProtocol) //SomeProtocol
~~~

* 실행중 instance의 타입을 표현한 값을 알아보고자 한다면,

~~~swift
type(of:)

print(type(of: coffee) == Coffee.self) //true
~~~

* .self 는 값을 나타낸다. 값의 뒤에서 값, 타입의 뒤에서는 타입의 값

## 19.4 다운캐스팅

~~~swift
class Coffee{
    let name: String
    let shot: Int
    
    var description: String{
        return "\(shot) coffee"
    }
    
    init(shot: Int){
        self.shot = shot
        self.name = "coffee"
    }
}
class Latte: Coffee{
    var flavor: String
    
    override var description: String{
        return "\(shot) \(flavor) Lattee"
    }

    func addSteam(){
        print("add Steam")
    }
    
    init(flavor: String, shot: Int){
        self.flavor = flavor
        super.init(shot: shot)
    }
}



let referenceLatte: Coffee = Latte(flavor: "banila", shot: 2)
print(referenceLatte.description)
~~~
* referenceLatte는 Coffee인스턴스를 참조하도록 선언을하고, Latte 인스턴스를 참조하고있습니다. -> Coffee타입인척 하는 Latte타입
* 그러나, Latte 타입에 정의되어있는 프로퍼티, 메서드를 사용해야할경우, Latte 타입으로 변환해주어야합니다. (다운캐스팅))

~~~~~~~~
타입캐스트 연산자: as?, as!
~~~~~~~~
~~~swift

let coffee = Coffee(shot: 1)
let mycoffee = Americano(shot: 2, iced: true)
let yourcoffee = Latte(flavor: "green tea", shot: 3)

if let americano = yourcoffee as? Americano {
    americano.description
}else{
    print("no ame")
}
let americano = yourcoffee as! Americano //Runtime ERROR


let referenceLatte: Coffee = Latte(flavor: "banila", shot: 2)
    referenceLatte.addSteam() //ERROR

if let realLatte = referenceLatte as? Latte{
    realLatte.addSteam()    //add Steam
}
~~~

## 19.5 Any, AnyObject의 타입캐스팅

* Any : 함수타입을 포함한, 모든 타입
* AnyObject : 클래스 타입만을 뜻함

~~~swift
func typechecker(item: Any){
    switch item {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case is Double:
        print("some Double")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x),\(y)")
    case let stringConverter as (String) -> (String):
        print(stringConverter("yagom"))
    default:
        break
    }
}
let zeroint: Int = 0                                        //
typechecker(item: zeroint)                                  //zero as an Int
let zerodouble: Double = 0                          
typechecker(item: zerodouble)                               //zero as an Double

typechecker(item: (1.0,3.4))                                //an (x, y) point at 1.0,3.4

typechecker(item: {(name: String) -> (String) in "\(name)"})//yagom
~~~