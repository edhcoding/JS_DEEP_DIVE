# 1. 실행컨텍스트란?

- 코드를 실행하는데 필요한 환경을 제공하는 객체입니다.
  - 여기서 환경은 코드 실행에 영향을 주는 조건 혹은 상태를 나타냅니다.
- 식별자 결정을 더욱 효율적으로 하기 위한 수단이라고 할 수도 있어요.

## 1-1. 실행컨텍스트는 언제 구성될까요?

- 자바스크립트 파일이 열리는 순간 전역 컨텍스트가 콜스택에 담기게 됩니다.
  - 전역 컨텍스트는, 전역 공간의 실행 컨텍스트 입니다.
- 함수를 실행 할 때 구성됩니다.

## 1-2. 소스코드의 평가와 실행

- 모든 소스코드는 실행에 앞서서 평가 과정을 거치며 실행하기 위한 준비를 합니다.
- 따라서 자바스크립트 엔진은 소스코드를 `소스코드 평가`와 `소스코드 실행` 과정으로 나누어 처리한다고 생각하면 됩니다.
- `소스코드 평가 과정`에서는 실행 컨텍스트를 생성하고, 변수,함수 등의 선언문만 먼저 실행하여 생성된 변수나 함수 식별자를 키로 하여, 실행 컨텍스트가 관리하는 스코프에 등록합니다. (추후에 더 자세히 설명합니다.)
- `소스코드 실행 과정`은 평가 과정이 끝나면 시작됩니다. 이를 다시 말해서 ‘런타임’이라고 부를 수도 있어요.
  - 이 때, 소스코드 실행에 필요한 정보 (변수/함수의 참조)를 실행 컨텍스트가 관리하는 스코프에서 검색하여 취득합니다.
  - 그리고 변수 값의 변경 등, 소스코드의 실행 결과는 다시 실행 컨텍스트가 관리하는 스코프에 등록하게 됩니다.

# 2. 실행컨텍스트의 역할

- 다시 말하자면, 소스코드를 실행하는 데 필요한 환경을 제공하고, 코드의 실행 결과를 실제로 관리하는 영역입니다.
- 실행 컨텍스트는 식별자 (변수, 함수, 클래스 등의 이름) 를 등록하고, 관리하는 스코프와 코드 실행 순서 관리를 구현한 내부 메커니즘으로, 모든 코드는 실행 컨텍스트를 통해 실행되고 관리됩니다.
- 실행 컨텍스트에서 `식별자`와 `스코프`는 실행 컨텍스트의 **렉시컬 환경** 으로 관리하고, 코드 실행 순서는 **실행 컨텍스트 스택**으로 관리합니다.

<br/>

그러면 실행컨텍스트 스택 (콜스택)과 렉시컬 환경을 하나씩 살펴봅시다.

<br/>

# 3. 실행컨텍스트 - 콜스택

```javascript
var a = 1;

function outer() {
  var b = 2;
  function inner() {
    var c = 3;
  }
  inner();
}

outer();
```

위와 같은 코드가 있다고 생각해봅시다. 자바스크립트는 어떤 과정을 거쳐서 코드를 실행할까요?

<br/>

일단 실행 컨텍스트의 콜스택이 어떻게 코드의 실행 순서를 관리할까요?

- 스택 구조에 따라서 한 실행 컨텍스트가 맨 위에 쌓이는 순간이 곧 현재 실행할 코드에 관여하게 되는 시점입니다.
- 즉, 실행 컨텍스트 스택의 최상위에 존재하는 실행 컨텍스트는 언제나 **현재 실행 중인 코드의 실행컨텍스트**가 됩니다.

<br/>

1. 전역 코드의 평가와 실행

   - 자바스크립트 파일이 열리자마자, 자바스크립트 엔진은 먼저 전역 코드를 평가하여전역 실행 컨텍스트를 생성하고, 스택에 푸시합니다.
   - 이 때 전역 변수 a 와 전역 함수 outer 는 전역 실행 컨텍스트에 등록됩니다.
   - 이 후 전역 코드가 실행되어 전역 변수 a 에 값이 할당 되고, 전역 함수 outer 가 호출됩니다.

2. Outer 함수 코드의 평가와 실행

   - 전역 함수 outer 가 호출 되면 전역 코드의 실행은 일시 중단되고, 코드의 제어권이 outer 함수 내부로 이동합니다.
   - 자바스크립트 엔진은 이제, outer 함수 내부의 함수 코드를 평가하여 outer 함수 실행 컨텍스트를 생성하고, 스택에 푸시합니다.
   - 이 때 지역 변수 b 와 중첩 함수 inner 가 outer 함수 실행 컨텍스트에 등록됩니다.
   - 이 후 outer 함수가 실행되기 시작하여 지역 변수 b 에 값이 할당되고 중첩함수 inner 가 호출됩니다.

3. Inner 함수 코드의 평가와 실행

   - 중첩 함수 Inner 가 호출되면 Outer 함수 코드 실행이 일시 중단되고, 코드의 제어권이 Inner 함수로 넘어갑니다.
   - 자바스크립트 엔진은 이제 Inner 함수 내부 코드를 평가하여 Inner 함수 실행 컨텍스트를 생성하고, 스택에 푸시합니다.
   - 이 때 Inner 함수 내부 지역 변수 c 가 Inner 함수의 실행 컨텍스트에 등록 됩니다.
   - 이 후 Inner 함수 코드가 실행되기 시작하여 지역 변수 c 에 값이 할당되고, inner 함수는 종료됩니다.

4. Outer 함수 코드로 복귀

   - inner 함수가 종료되면 코드의 제어권은 다시 outer 함수로 이동합니다.
   - 이 때 자바스크립트 엔진은 inner 함수 실행 컨텍스트를 스택에서 pop 하여 제거합니다.
   - 이제 더이상 outer 함수에서 실행할 코드가 남아있지 않으므로, outer 함수 역시 종료됩니다.

5. 전역 코드로 복귀

   - Outer 함수가 종료되면 코드의 제어권은 다시 전역 코드로 이동합니다.
   - 이 때 자바스크립트 엔진은 outer 함수 실행 컨텍스트를 스택에서 pop 하여 제거합니다.
   - 그리고 이제 더 이상 실행 할 전역 코드가 남아있지 않으므로, 전역 실행 컨텍스트도 스택에서 pop 되어 제거합니다.

   <br/>

# 4. 실행컨텍스트 - 렉시컬 환경

- 사실, 실행 컨텍스트 객체는 크게 Variable Environment 와 Lexical Environment, 그리고 This Binding 으로 로 나누어져 있습니다.
- 그런데 우리는 Variable Environment 는 생략 합니다. 왜냐하면, VE와 LE 는 구조가 동일하지만, VE가 최초의 스냅샷을 저장하는 반면, LE 는 코드 진행 상황에 따라 달리지게 되기 때문입니다. 따라서, VE 에 대한 설명은 생략하고 LE 를 집중적으로 다루어 봅니다.
- 렉시컬 환경은 때때로 스코프를 벗어난 뒤에도 존재하므로 (클로저 현상), 스택이 아니라 힙에 저장됩니다.
- 렉시컬 환경 (Lexical Environment) 란 식별자와 식별자에 바인딩 된 값, 그리고 상위 스코프에 대한 참조를 기록하는 자료구조로, 실행 컨텍스트를 구성하는 컴포넌트입니다.
- 간단하게, 렉시컬 환경은 스코프와 식별자를 관리한다고 생각하면 됩니다.

## 4-1. 렉시컬 환경의 구조

먼저 간단하게 렉시컬 환경이 어떻게 구성되어있는지 보고, 추후에 실행 컨텍스트 생성,과 식별자 검색 과정을 통해 하나씩 살펴봅니다.

1. 환경 레코드 (Environment Record)
   - 스코프에 포함된 식별자를 등록하고, 등록된 식별자에 바인딩 된 값을 관리합니다.
2. 외부 렉시컬 환경에 대한 참조 (Outer Lexical Environment Reference)
   - 상위 스코프를 가리킵니다.
   - 상위 스코프란, 외부 렉시컬 환경,즉 해당 실행 컨텍스트를 생성한 소스코드를 포함하는 상위 코드의 렉시컬 환경을 말합니다.

## 4.2. 실행 컨텍스트 생성과 식별자 검색 과정

### 1. 전역 객체 생성

- 전역 객체는 전역 코드가 평가 되기 이전에 생성됩니다.
- 전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않는 최상위 객체입니다.
- 브라우저 환경에서는 window가 전역 객체를 가리키고, Node.js 환경에서는 global이 전역 객체를 가리킵니다.
- 전역 객체는 표준 빌트인 객체 (Object, String, Number, Function, Array 등)와 환경에 따른 호스트 객체 (클라이언트 Web API 혹은 Node.js의 호스트 API) 를 프로퍼티로 갖습니다.
- 전역 객체는 또한 var 키워드로 선언한 전역 변수와 전역 함수 역시 프로퍼티로 갖게 됩니다.

### 2. 전역 코드 평가

- 소스코드가 로드 되면, 자바스크립트 엔진은 전역 코드를 평가합니다
- 전역 코드 평가는 아래와 같은 순서로 진행됩니다.

```
1. 전역 실행 컨텍스트 생성
2. 전역 렉시컬 환경 생성
   2.1 전역 환경 레코드 생성
       2.1.1 객체 환경 레코드 생성
       2.1.2 선언적 환경 레코드 생성
    2.2 this 바인딩
   2.3 외부 렉시컬 환경에 대한 참조 결정
```

위의 세부적인 생성 과정을 순서대로 살펴봅시다.

<br/>

### 1. 전역 실행 컨텍스트 생성

- 먼저 비어있는 전역 실행 컨텍스트를 생성하여, 실행 컨텍스트 스택에 푸시합니다.

<br/>

### 2. 전역 렉시컬 환경 생성

- 전역 렉시컬 환경을 생성하고, 전역 실행 컨텍스트에 바인딩합니다.
- 위에서 잠깐 언급했듯이, 렉시컬 환경은 `환경 레코드`와 `외부 렉시컬 환경에 대한 참조`로 구성되어있습니다.
- `전역 환경 레코드 생성`
  - 전역 렉시컬 환경을 구성하는 컴포넌트입니다.
  - 전역 변수를 관리하는 전역 스코프, 전역 객체의 빌트인 전역 프로퍼티와 빌트인 전역 함수, 표준 빌트인 객체를 제공합니다.
  - var 키워드로 선언한 전역 변수와 let,const 키워드로 선언한 전역 변수를 구분 하여 관리하기 위해 전역 스코프 역할을 하는 전역 환경 레코드는 **객체 환경 레코드**와 **선언적 환경 레코드**로 구성되어 있습니다.
  - **객체 환경 레코드**는 기존의 전역 객체가 관리하던 var 키워드로 선언한 전역변수와 함수 선언문으로 정의한 전역 함수, 빌트인 전역 프로퍼티와 빌트인 전역 함수, 표준 빌트인 객체를 관리합니다.
    - 객체 환경 레코드는 BindingObject라고 부르는 객체와 연결됩니다.
    - var 키워드로 선언한 전역 변수와 함수 선언문으로 정의된 전역 함수는 전역 환경 레코드에 연결된 BindingObject 를 통해 전역 객체의 프로퍼티와 메서드가 됩니다.
  - **선언적 환경 레코드**는 let,const 키워드로 선언한 전역 변수를 관리합니다.
    - let, const 키워드로 선언한 전역 변수는 선언적 환경 레코드에 등록되고 관리됩니다.
    - let, const 키워드로 선언한 전역변수는 전역 객체의 프로퍼티가 되지 않고 ‘전역 환경 레코드의 선언적 환경 레코드’에 존재하게 됩니다.
- `this 바인딩`
  - 전역 환경 레코드의 [[GlobalThisValue]] 내부 슬롯에 this 가 바인딩 됩니다.
- `외부 렉시컬 환경에 대한 참조 결정`
  - 현재 평가 중인 소스코드를 포함하는 외부 소스코드의 렉시컬 환경, 즉 상위 스코프를 가리킵니다.

## 4-3. 환경 레코드 (Environment Record)

그러면 환경 레코드에 대해 더 자세히 알아보도록 하겠습니다.

- 현재 컨텍스트와 관련된 코드의 식별자 정보들이 저장 됩니다.
- 컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑으며 순서대로 수집합니다.
- `호이스팅` : 코드가 실행되기 전임에도 불구하고, 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명(식별자)을 모두 알고 있게 됩니다.

<br/>

## 4-4. 외부 환경 참조 (Outer Environment Reference)

- 외부환경 참조를 알기 전에 먼저 스코프에 대해서 알아보고 가도록 합니다.
- `스코프` : 식별자에 대한 유효범위 입니다.
- `스코프 체인`: 식별자의 유효범위를 안에서부터 바깥으로 차례대로 검색해나가는 것을 말합니다.
- 위의 스코프 체인을 가능하게 만드는 것이 바로 렉시컬 환경의 외부 환경 참조입니다.
- 만약 여러 스코프에서 동일한 식별자를 선언한 경우에는 무조건 스코프 체인 상에서 가장 먼저 발견된 식별자에만 접근이 가능합니다. 이러한 현상을 `변수 섀도잉` 이라고 합니다.

<br/>

# 5. 추가 설명

## 스코프

위에서 설명한 스코프에 대해서 더 자세히 알아보도록 합시다.

- 자바스크립트에서 스코프는 정적으로 지정됩니다.
  - 즉, 함수의 호출 시점이 아니라 선언 시점에 스코프가 지정됩니다.

## 호이스팅

### 함수 선언문과 함수 표현식

```javascript
function a() {} // 함수 선언문

var b = function () {}; // 익명 함수 표현식 변수명 b 가 곧 함수명

var c = function d() {}; // 기명 함수 표현식 , 변수명은 c , 함수명은 d
```

    - 함수 선언문은 함수 전체를 호이스팅 합니다.
    - 함수 표현식은 선언부만 끌어 올립니다.

<br/>

## 함수 선언문 vs 변수 - 누가 더 우선으로 호이스팅 되는가?

```javascript
foo(); // 1
var foo;

function foo() {
  console.log(1);
}

foo = function () {
  console.log(2);
};
```

- var foo 는 function foo 보다 앞서 선언되었지만, 함수 선언문이 일반 변수 위로 끌어올려집니다.
