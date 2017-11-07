# Chapter 23 프로토콜 지향 프로그래밍

## 23.1 프로토콜 초기구현
---
### 프로토콜에서는 함수 선언 밖에 할수없다.

### 프로토콜 익스텐션을 활용하면, 함수를 구현할수있다.

# 이를 프로토콜 초기구현이라한다.
~~~swift
protocol SomeProtocol{
}
extension SomeProtocol{
    func introduceSelf(){
        print("my type is \(Self)")
    }
}

class Person:SomeProtocol{
    
}
let p = Person()
p.introduceSelf()
~~~


## 23.2 맵, 필터, 리듀스 직접 구현해보기
- 직접 해보자.
## 23.3 기본 타입 확장
~~~swift
protocol SelfPrintable{
    func printSelf()
}
extension SelfPrintable{
    func printSelf(){
        print(self)
    }
}
extension Int: SelfPrintable{}
extension String: SelfPrintable{}
extension Double: SelfPrintable{}

let i_num: Int = 10
let str: String = "hi"
let d_num: Double = 10.10

print(i_num.printSelf())
print(str.printSelf())
print(d_num.printSelf())
~~~

