# 클로저

> “ 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.”
> “클로저라는 이름은 함수의 자유로운 변수를 “감싼다”라는 특징에서 유래되었다.”
> (여기서 자유변수는 클로저에 의해 참조되는 상위스코프의 변수를 가리키며, ‘자유롭다’라는 뜻은 그 변수가 함수에서 선언되지 않았다, 즉 외부에서 왔다는 뜻을 가진다.)

<br/>
<br/>

## 1. 렉시컬 스코프 알아보기

- 자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라, 함수를 어디서 정의했는지에 따라 상위 스코프를 결정합니다. 우리는 이를 렉시컬 스코프 (정적 스코프) 라고 합니다.
- 실행 컨텍스트 내 렉시컬 환경의 “외부 렉시컬 환경에 대한 참조(Outer)”에 저장할 참조 값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경(위치)에 따라 결정됩니다.

<br/>

### 함수 객체의 내부 슬롯 [[Environment]]

- 함수가 정의된 환경(위치)와 호출되는 환경(위치)는 다를 수 있습니다. 따라서, 렉시컬 스코프가 가능하려면 함수는 자신이 호출되는 환경과는 상관없이 자신이 정의된 환경, 즉 상위 스코프를 기억해야 합니다. 이를 어떻게 해결할까요?
- 이를 위해 함수는 자신의 내부 슬롯 [[Environment]] 에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장해놓습니다.

<br/>

### 함수 코드 평가 순서

1. 함수 실행 컨텍스트 생성
2. 함수 렉시컬 환경 생성
   1. 함수 환경 레코드 생성
   2. This 바인딩
   3. 외부 렉시컬 환경에 대한 참조 결정

- 이 때 ‘외부 렉시컬 환경에 대한 참조’에는 함수 객체의 내부 슬롯 [[Environment]] 에 저장된 렉시컬 환경의 참조가 할당 됩니다.
- 이것이 바로 함수 정의 위치에 따라 상위 스코프를 결정하는 렉시컬 스코프의 실체입니다.

<br/>
<br/>

## 2. 클로저

```js
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };
  return inner;
}

const innerFunc = outer();
innerFunc(); // 10
```

- outer 함수를 호출하면 outer 함수는 inner 함수를 반환하고 생명주기를 마감합니다. 즉, outer 함수의 실행이 종료되면 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거됩니다.
- outer 함수의 실행컨텍스트가 제거되면, outer 함수 내부의 지역변수 x 역시 유효하지 않는게 맞지 않을까요?
- 하지만 innerFunc 을 실행 한 결과 값은 여전히 10을 가리킵니다.
- 즉, 외부 함수(outer) 보다 중첩 함수(inner)가 더 오래 유지되는 경우, 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 ‘클로저’라고 합니다.

<br/>

“ 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.”

- 그 함수가 선언된 렉시컬 환경 : 함수가 정의된 위치의 스코프, 즉 상위 스코프를 의미하는 실행 컨텍스트의 렉시컬 환경을 의미합니다.

<br/>

- 위에서 변수 x 가 소멸되지 않는 이유?
- outer 함수가 종료되면서 outer 함수의 실행 컨텍스트는 스택에서 제거됩니다.
- 하지만 위에서 언급했듯이, inner 함수 의 내부슬롯 [[Environment]] 에서 outer 함수를 가리키고 있기 때문에, outer 함수의 렉시컬 환경까지 소멸되지는 않습니다.

<br/>

- 사실상 자바스크립트의 모든 함수는 위와 같이 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저라고 할 수 있겠습니다. 하지만 모든 함수를 클로저라고 말하지는 않습니다.
- 왜냐하면, 상위 스코프의 어떤 식별자도 참조하지 않는 경우 대부분의 모던 브라우저는 최적화를 통해 상위 스코프를 기억하지 않기 때문입니다.
- 따라서, 클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고, 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적입니다.

<br/>
<br/>

## 3. 클로저의 활용 예시

### 1. 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때

```js
var fruits = ["apple", "banana", "peach"];
const $ul = document.createElement("ul");

fruits.forEach(function (fruit) {
  const $li = document.createElement("li");
  $li.innerText = fruit;
  $li.addEventListener("click", function () {
    alert("Your Choice is " + fruit);
  });
  $ul.appendChild($li);
});
```

- 위 함수는 과일 이름 배열에 따라 li 엘리먼트를 만들어주고, 해당 엘리먼트를 클릭하면 과일 이름을 포함한 alert를 띄워주는 코드입니다.
- 그런데 여기서 , alert를 띄워주는 함수를 외부로 꺼내면, 재사용성이 좋아지고 코드의 의존성도 줄어들겠죠? 한번 꺼내봅시다.

```js
var fruits = ["apple", "banana", "peach"];
const $ul = document.createElement("ul");

const alertFruit = function (fruit) {
  alert("Your Choice is " + fruit);
};

fruits.forEach(function (fruit) {
  const $li = document.createElement("li");
  $li.innerText = fruit;
  $li.addEventListener("click", alertFruit);
  $ul.appendChild($li);
});
```

- alertFruit 함수를 외부로 분리해주었습니다.
- 그런데 문제점은 li 를 클릭하면 과일 명이 아니라 [object MouseEvent] 라는 상태값이 출력됩니다.
- 왜냐하면 이벤트 리스너의 콜백함수의 첫번째 매개변수로 ‘Event’ 객체가 들어가기 때문이죠.
- 따라서 아래와 같이 수정하면 문제는 해결됩니다.

```js
var fruits = ["apple", "banana", "peach"];
const $ul = document.createElement("ul");

const alertFruit = function (fruit) {
  alert("Your Choice is " + fruit);
};

fruits.forEach(function (fruit) {
  const $li = document.createElement("li");
  $li.innerText = fruit;
  $li.addEventListener("click", alertFruit.bind(null, fruit));
  $ul.appendChild($li);
});
```

- bind 함수를 사용하여 새로운 bound 함수를 생성했고, 매개변수로 fruit 를 넣어주었습니다.
- 이렇게 할 경우, 이벤트 객체가 인자로 넘어오는 순서가 바뀌는 점, 함수 내부에서의 this 가 달라지는 점 등의 단점이 생깁니다.
- 그러면 아래와 같이 클로저를 사용해볼까요?

```js
var fruits = ["apple", "banana", "peach"];
const $ul = document.createElement("ul");

const alertFruitBuilder = function (fruit) {
  return function () {
    alert("Your Choice is " + fruit);
  };
};

fruits.forEach(function (fruit) {
  const $li = document.createElement("li");
  $li.innerText = fruit;
  $li.addEventListener("click", alertFruitBuilder(fruit));
  $ul.appendChild($li);
});
```

- alertFruitBuilder 는 매개변수로 fruit를 받아 온 후, alertFruitBuilder 함수는 익명함수를 반환하는데, 이 익명함수가 바로 위에서 작성했던 alertFruit 함수입니다.
- 이렇게 반환된 함수를 리스의 콜백함수로 저장하게 되고, 클릭 이벤트가 발생하면 비로소 해당 함수의 실행 컨텍스트가 열리게 되면서 alertFruitBuilder의 인자로 넘어온 fruit 를 외부 환경 참조에 의해 접근 할 수 있게 됩니다.

<br/>

### 2. 접근 권한 제어 (정보은닉)

- 정보은닉이란, 어떤 모듈의 내부 로직에 대해 외부로의 노출을 최소화하여 모듈간의 결합도를 낮추고, 유연성을 높이고자 하는 개념입니다.
- 자바스크립트에서는 다른 언어와 달리 변수 자체에 접근 권한을 직접 부여하지 못합니다.
- 자바스크립트에서는 클로저를 통해서 private(외부에 노출X)와 public(외부에 노출O)을 구현할 수 있습니다.
- 먼저 접근 권한 제어가 적용되지 않은 아래 코드를 볼게요.

```js
var car = {
	fuel: Math.ceil(Math.random()),
	power: Math.ceil(Math.random()*3+2),
	moved: 0,
	run : function(){ // ... }
}
```

- 위 코드의 문제점은 객체 외부에서 `car.fuel, car.power, car.moved`로 접근하여 값을 조작 할 수 있다는 것입니다.
- 이를 아래와 같이 클로져를 사용해서 변경해보겠습니다.

```js
const carBuilder = function () {
  let fuel = Math.ceil(Math.random());
  let power = Math.ceil(Math.random() * 3 + 2);
  let moved = 0;

  return {
    run: function () {
      //..
    },
  };
};

const car = carBuilder();
```

- 이렇게 클로져를 사용하여 fuel, power, moved 변수에 접근 가능하지만, 외부에 return 해주는 값은 메서드에 한정지어서 정보 은닉을 구현 할 수 있습니다.

<br/>

### 3. 부분 적용 함수

- 부분 적용 함수란, n 개의 인자를 받는 함수에 미리 m 개의 인자만 넘겼다가, 나중에 (n-m)개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수입니다.
- 대표적인 예제로 디바운스 함수를 살펴봅시다.

```js
const debounce = function (eventname, func, wait) {
  let timeout = null;
  return function (event) {
    let self = this;
    clearTimeout(timeout);
    timeout = setTimeout(func.bind(self, event), wait);
  };
};

const mouseHandler = function (e) {
  console.log("move event 처리");
};

const wheelHandler = function (e) {
  console.log("wheel event 처리");
};

document.body.addEventListener(
  "mousemove",
  debounce("move", mouseHandler, 500)
);

document.body.addEventListener(
  "mousewheel",
  debounce("wheel", wheelHandler, 500)
);
```

- 위에서 debounce 함수의 첫번째 인자로 이벤트 이름, 이벤트 핸들러 함수, 타임아웃 시간을 받고 내부 중첩함수를 리턴합니다.
- 이 후, 해당 이벤트가 발동되면 리턴된 함수에 event 객체를 인자로 넣어 실행하게 됩니다.

<br/>

### 4. 커링함수

- 커링함수란 여러개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출 할 수 있게 체인형태로 구성한 것을 말합니다.
- 부분 적용함수와 맥락은 비슷하지만 커링함수의 특이점은 한번에 하나의 인자만 전달하는 것입니다.
- 또한, 중간 과정상의 함수를 실행한 결과는 그 다음 인자를 받기 위해 대기만 할 뿐이므로, 마지막 인자가 전달되기 전까지는 원본함수가 실행되지 않습니다.
- 부분적용 함수에서는 여러 개의 인자를 전달 할 수 있고, 실행 결과를 재실행 할 때, 원본 함수가 무조건 실행되는 것과 다른 점입니다.

```js
const curry = function (func) {
  return function (a) {
    return function (b) {
      return func(a, b);
    };
  };
};
```

- ES6의 화살표 함수에서는 아래와 같이 간단하게 표기 할 수 있습니다.

```js
const curry = (func) => (a) => (b) => (c) => (d) => func(a, b, c, d);
```
