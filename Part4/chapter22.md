# Chapter 22 제너릭

## 22.1 제너릭 함수
~~~swift
제너릭을 사용하고자 하는 타입 이름 <타입 매개변수>
제너릭을 사용하고자 하는 함수 이름 <타입 매개변수> (함수의 매개변수)
~~~

~~~swift
import Foundation

func swapTwoInts(a: inout Int, b: inout Int){
    let temp = a
    a = b
    b = temp
}

var one = 5
var two = 10

swapTwoInts(a: &one, b: &two)

print(one)
print(two)


func swapTwoGeneric<T>(a: inout T, b: inout T){
    let temp = a
    a = b
    b = temp
}
var histr = "hi"
var byestr = "bye"

swapTwoGeneric(a: &histr, b: &byestr)

print(a)
print(b)
~~~

~~~
    제너릭에서의 T

    책에서는 플레이스 홀더 타입(placehold type) 이라고 한다. 타입 매개변수의 한 예
    여러개의 타입 매개변수를 사용해야할경우 <T,G,H,..>  여러가지 타입을 사용할수있다.


~~~

## 22.2 제너릭 타입 
- 제너릭 타입 구현
~~~swift
stack 예제
struct IntStack {
    var items: Array<Int> = []
    mutating func push(item: Int){
        items.append(item)
    }
    mutating func pop() -> Int{
        return items.removeLast()
    }
}

struct GenericStack<T>{
    var items: Array<T> = []
    
    mutating func push(item: T){
        items.append(item)
    }
    mutating func pop() -> T{
        return items.removeLast()
    }
}
~~~

## 22.3 제너릭 타입 확장
~~~swift
//GenericStack의 연산 프로퍼티 추가
extension GenericStack{
    var topItem: T?{
        return self.items.last
    }
}
~~~

## 22.4 타입 제약
- 특정한 프로토콜을 따르는 타입만 사용해야 할 경우
- 타입제약은 클래스 타입 또는 프로토콜 타입으로만 줄수 있습니다.


~~~swift
func substractTwoValue<T>(a: T, b: T) -> T{
    return a - b 
}

func substractTwoValue<T: Integer>(a: T, b: T) -> T{
    return a - b
}
~~~

- where절을 통한 타입 제약 추가

~~~swift
func substractTwoValue<T: Integer>(a: T, b: T) -> T where T: FloatingPoint, T: Equatable{
    return a - b
}
//where 절의 자세한 설명은 p.435
~~~


## 22.5 프로토콜의 연관 타입

~~~swift
protocol Container{
    associatedtype E
    var items: Array<E> {get}
    var count: Int {get}
    mutating func append(item: E)
    subscript(i: Int) -> E {get}
}
struct CrycatStack: Container {
    typealias E = Int
    var count: Int{
        get{
            return self.items.count
        }
    }
    var items: Array<Int>
    
    mutating func append(item: Int) {
        self.items.append(item)
    }
    
    subscript(i: Int) -> Int {
        return self.items[i]
    }
}

struct ElementStack<Element>: Container {
    typealias E = Element
    var count: Int{
        return self.items.count
    }
    var items: Array<Element>
    mutating func append(item: Element) {
        self.items.append(item)
    }
    subscript(i: Int) -> Element{
        return self.items[i]
    }
}
~~~
