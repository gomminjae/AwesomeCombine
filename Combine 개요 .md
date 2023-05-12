# Combine 개요

- `Combine`은 2019년에 Apple에서 만든 비동기 이벤트 처리 프레임워크이다.

## Overview

Combine은 앱의 이벤트 처리를 위한 *선언적 접근*을 제공한다. 잠재적으로 많은 `Delegate`, `callback` 또는 closure의 `completion handler` 보다 편리하게 비동기적 이벤트 처리를 제공할 수 있다. 

## Combine 구성

![https://miro.medium.com/v2/resize:fit:1400/format:webp/1*jLmJpJX952LXGsqpOKYQfQ.png](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*jLmJpJX952LXGsqpOKYQfQ.png)

Combine은 비동기 코드를 작성할 때 사용하던 불필요한 보일러 플레이트 코드의 사용을 줄며주며 오류를 효율적으로 처리할 수 있다.

Combine은 `Publisher`, `Operator`, `Subscriber` 로 이루어져 있다. 이것은 평소 써오던 Rx와 비슷한 형태이다 . 

- **Publisher**은 순차적으로 전달할 데이터 타입을 선언하고 Operator을 이용하여 데이터를 전송한다.
- **Opertor**은 Publisher에서 전달한 데이터를 가공하는 역할을 한다 .
- **Subscriber**은 Publisher에서 보낸 데이터를 받아 소비하는 역할을 한다.

![https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uMKTUK7cK-gtNjdxEknWaQ.png](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uMKTUK7cK-gtNjdxEknWaQ.png)

**Publisher**와 **Subscriber**가 서로 데이터를 주고 받을때는 항상 두 가지의 타입이 있다. 먼저 **Publisher**을 보면 아래와 같은 프로토콜 형태로 구현이 되어있다. 에러가 발생하지 않으면 Output타입을 방출하지만 아니면 Failure타입을 방출하게된다. 

```swift
protocol Publisher {
    associatedtype Output
    associatedtype Failure : Error
    func receive<S>(subscriber: S) where S : Subscriber, Self.Failure == S.Failure, Self.Output == S.Input
}
```

**Subscriber** 프로토콜을 살펴보면 다음과 같다 

```swift
public protocol Subscriber : CustomCombineIdentifierConvertible {
    associatedtype Input
    associatedtype Failure : Error

    func receive(subscription: Subscription)
    func receive(_ input: Self.Input) -> Subscribers.Demand
    func receive(completion: Subscribers.Completion<Self.Failure>)
}
```

**Subscriber**은 데이터를 받을때 에러가 발생하지 않으면 Input타입을, 아니면 Failure타입을 가진다. 정리를 다시 해보면 이벤트 발생시 Publisher가 데이터를 전송 Operator을 거쳐 Subscriber가 데이터를 받아 처리하는 방식이다. 

## 시작해보기

```swift
import Combine 

let publisher = (1...5).publisher

publisher.sink(receiveCompletion: { _ in
    print("finished")
}, receiveValue: { value in
    print(value)
})
```

### 결과

```swift
1
2
3
4
5
finished
```

1. 1부터 5까지의 데이터를 가지는 **Publisher**을 생성한다 
2. sink를 통해 subscriber와 연결한다 receivecompletion과 receiveValue closure을 통해 원하는 기능을 작성해준다. 
3. `receiveCompletion` 은 모든 데이터 전송이 끝났을때 호출된다. 
4. `receiveValue` 를 통해 전송하는 데이터를 확인할 수 있다.

`sink(receiveCompletion:receiveValue:)` 은 **Attaches a subscriber with closure-based behavior** 라고 정의되어있다 이것은 클로저의 동작이 Subscriber라는 것을 의미한다.