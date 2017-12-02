# Filtering Operators

## Ignoring Operators
* `ignoreElements`
* `elementAt`
* `filter`

### ignoreElements
![Alt text](http://reactivex.io/documentation/operators/images/ignoreElements.c.png)

```swift

```

### elementAt
수신 받기 원하는 `element`의 `index`를 제외한 나머지의 모든 `element`들은 무시합니다. 다이어그램에서 `index`로 2를 지정하면 세 번째 `element`만 통과되고 나머지는 무시됩니다.

![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/elementAt.png)

```swift
let subject = PublishSubject<Int>()
let disposeBag = DisposeBag()

subject
  .elementAt(2)
  .subscribe(onNext: { print($0) })
  
subject.onNext(1)
subject.onNext(2)
subject.onNext(3)
```

```swift
3
```

### filter
`filter`는 각 `element`에 적용되어 `predicate`에 해당하는 `element`만 통과하도록 허용합니다.
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/filter.png)

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 5, 6)
  .filter { integer in
    integer % 2 == 0
  }
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)
```

```swift
2
4
6
```

## Skipping operators
* `skip`
* `skipWhile`
* `skipUntil`

### skip
특정 `element` 수를 건너 뛸 필요가 있을 때 사용한다. 아래 다이어그램을 보면 건너 뛸 `element`의 수를 2로 지정하면 2개의 `element`를 제외한 나머지 `element`들을 수신 받을 수 있다.
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/skip.png)


```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 5, 6)
  .skip(3)
  .subscribe(onNext: { print($0) })
```

```swift
4
5
6
```

### skipWhile
`skipWhile`은 지정된 조건이 false가 되기전까지 `element`들을 건너뜁니다.
![Alt text](http://reactivex.io/documentation/operators/images/skipWhile.c.png)


```swift
let disposeBag = DisposeBag()

Observable.of(2, 2, 3, 2, 4, 4)
  .skipWhile { integer in
    integer % 2 == 0
  }
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)
```

```swift
3
2
4
4
```

### skipUntil
다른 trigger Observable이 element를 방출하기 전까지, 원본 Observable이 방출하는 element들을 건너뛸 수 있다.
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/SkipUntil.png)

```swift
let disposeBag = DisposeBag()
let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()

subject
  .skipUntil(trigger)
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)
  
subject.onNext("A")
subject.onNext("B")

trigger.onNext("!")

subject.onNext("C")
subject.onNext("D")
```

```swift
C
D
```

## Taking operators
* `take`
* `takeWhile`
* `takeUntil`

### take

![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/take.png)

```swift

```

```swift
```

### takeWhile
![Alt text](http://reactivex.io/documentation/operators/images/takeWhile.c.png)

```swift

```

```swift
```

### takeUntil
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/takeUntil.png)

```swift
let disposeBag = DisposeBag()

let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()

subject
  .takeUntil(trigger))
  .subscribe(onNext: { print($0) })


subject.onNext("1")
subject.onNext("2")
trigger.onNext("!")
subject.onNext("3")
subject.onNext("4")

```

```swift
1
2
```

### takeLast
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images//takeLast.png)

## Distinct operators
* `distinctUntilChanged

### distinctUntilChanged

```swift
let disposeBag = DisposeBag()

Observable.of("A", "A", "B", "B", "A")
  .distinctUntilChanged()
  .subscribe(onNext: { print($0) })
```

```swift
A
B
A
```

# Transforming Operators

## toArray
`toArray`은 `Observable`의 `element`들을 배열로 변환하여 구독자에게 `.next` event에 배열을 담아  내보냅니다.
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/toArray.png)

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3)
  .toArray()
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)
```
```swift
"[1, 2, 3]"
```

## map
`map`은 swift 표준 `map`과 동일 하지만 단지 Observable의 operator라는 점만 다르다.
![Alt text](https://github.com/audrl1010/RxSwiftAnalysis/blob/master/images/map.png)

```swift
let disposeBag = DisposeBag()
Observable.of(1, 2, 3)
  .map { $0 * 2 }
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)
```
```swift
2
4
6
```
## flatMap
`Observable`의 각 `element`를 새로운 `Observable`로 생성시키고, 생성된 각각의 `Observable`들의 element들을 발행하는 것을 하나의  `Observable`에 합쳐서 내보냅니다.
![Alt text](http://reactivex.io/documentation/operators/images/flatMap.c.png)

```swift
let disposeBag = DisposeBag()

let sequence1 = Observable.of(1, 2, 3)
let sequence2 = Observable.of(4, 5, 6)

sequence1
  .flatMap { (x: Int) -> Observable<Int>
    print("\($0)\n")
    return sequence2
  }
  .subscribe(onNext: { print($0) })
  .disposed(by: disposeBag)
```

```swift
1

4
5
6

2

4
5
6

3

4
5
6
```

## flatMapFirst
flatMap와 똑같이 새로운 Observable을 만들지만, 새로운 Observable은 동작이 다 끝날 때 까지 새로 발행된 아이템을 무시합니다.

## flatMapLatest
새로운 Observable을 만들고, 새로운 Observable이 동작 하는 중에 새로 발행된 아이템이 전달되면, 만들어진 Observable은 dispose하고, 새로운 Observable을 만들고 발행합니다.




