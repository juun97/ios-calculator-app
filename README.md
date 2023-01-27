# 계산기 ReadMe


## 접니다 👀
 <center> [릴라](https://github.com/juun97)</center> |
| --- | 
|<img width="200" alt="image" src=https://cdn.discordapp.com/attachments/1054218081787973662/1058207490296262665/KakaoTalk_Image_2022-12-23-11-04-10.png> | 


## STEP 1 소개 🔎
> 사칙연산 계산을 위한 큐타입을 구현합니다.

- 프로젝트에 단위 테스트(Unit Test)를 위한 타깃을 추가합니다.
- 계산기가 입력받은 숫자와 연산자는 연산큐에 쌓입니다.
- 사칙연산 계산을 위한 큐(Queue) 타입(CalculatorItemQueue)과 큐타입 구현을 위한 List 타입을 직접 UML로 표현해봅니다.
- CalculatorItemQueue를 코드로 구현합니다.
    - CalculatorItemQueue의 내부구현은 자율로 합니다.
    - CalculatorItemQueue에서 다루는 요소는 CalculateItem 프로토콜을 준수합니다.
        - CalculateItem 프로토콜은 빈 프로토콜입니다.

## 타임라인 ⏰

| STEP  | 날짜             | 타임라인                                                                                                                                                                     |
| --------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|STEP1| **2023.01.24** |- CalculatorItemQueue타입 정의 </br>- CalculateItem 프로토콜 정의 
|STEP1| **2023.01.25** |- CalculatorItemQueue TDD 개발 진행 </br>- CalculatorItemQueue 유닛 테스트 진행 </br> -  CalculatorItemQueue 메서드 구현 
|STEP1| **2023.01.26** |- 피드백 사항 리팩토링 



## STEP1 트러블슈팅 🚀

### 1️⃣ dequeue의 로직
- Array에서 제공하는 메서드인 removeFirst를 통해 간단하게 구현을 할 수도 있었습니다. 하지만 removeFirst의 경우 첫 번째 요소를 삭제한 뒤 남은 요소들의 인덱스를 한 칸씩 당겨줘야하는 작업이 필요해 O(n) 의 시간복잡도를 가져 성능면에서 더 개선의 여지가 있을거라 생각했습니다.
- 그렇게 해서 생각한 로직이 firstIndex 변수를 만들어 삭제할때마다 Index를 제어해 firstIndex가 유효한 값이 있는 첫번 째 인덱스를 가르키도록 하는 것 이었습니다.
- dequeue 가 일어날 때 삭제된 요소에 nil을 할당하고 firstIndex를 1증가 시키게 되면 O(1)의 시간복잡도를 가지게 되어 더 개선된 성능을 가지게 됩니다.
```swift
mutating func dequeue() -> T? {
        guard queue.isEmpty != true,
              frontIndex <= count,
              let element = queue[frontIndex] else { return nil }
       
        queue[frontIndex] = nil
        frontIndex += 1

        return element
    }
```
- 물론 이 로직도 개선의 필요가 있습니다. 겉으로 보았을때 queue의 크기는 줄어든것 처럼 보일수도 있으나 실제로는 queue의 크기는 변하지 않고 nil이 할당 되있는 상태입니다. 

### 2️⃣ dequeue가 진행된 queue의 count 구하기
- 실제로 queue의 요소가 삭제된 것이 아닌 nil을 할당하는 것이었기에 array의 count를 사용하게 되면 nil을 포함한 count를 반환한다는 것을 Unit Test 를 통해 알게 되었습니다.
- queue에서 유효한 값이 있는 첫 번째 인덱스는 frontIndex가 보유하고 있었기에 endIndex 에서 frontIndex 를 빼주게 되면 실제 보유하고 있는 요소의 갯수가 나오게 됩니다.
```swift
public var count: Int {
        let count = queue.endIndex - frontIndex
        return count
    }
```
### 3️⃣ dequeue의 요소가 삭제되었는지 확인하는 유닛테스트
- 처음에 테스트를 진행할 때는 dequeue 로 요소가 삭제되면 count가 줄어들거라 생각해 dequeue가 실행 된 뒤 카운트와 기댓값을 비교하는 테스트를 진행했습니다.
- 하지만 count 가 줄어드는것이 요소가 삭제되었는지를 확인하는 테스트가 아닌것 같다는 피드백을 듣고 수정을 진행했습니다.
- Queue 안의 배열 프로퍼티에 접근하여 배열의 첫번 째 요소와 nil 을 비교하는 방식으로 진행하였습니다.

```swift
    func test_dequeue를_실행할때_첫번째요소가_삭제된다() {
        //given
        let firstElement = 1
        let secondElement = 2
        let thirdElement = 3
        //when
        sut?.enqueue(firstElement)
        sut?.enqueue(secondElement)
        sut?.enqueue(thirdElement)

        sut?.dequeue()
        
        let result = sut?.queue.first
        let expectation: Int? = nil
        //then
        XCTAssertEqual(result, expectation)
    }
```

## 스텝 수행 중 핵심 경험

- [x]  TDD 시작하기
- [x] 기존의 프로젝트에 Test Target 추가
- [x]  Queue 자료구조의 이해와 구현
