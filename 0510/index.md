# 07. 클래스

## 1. 클래스 vs 생성자 함수

| 클래스                                                                                                                              | 생성자 함수                                                                                                                |
| ----------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| new 연산자 없이 호출하면 에러가 발생한다.                                                                                           | new 연산자 없이 호출해도 일반 함수로서 호출된다.                                                                           |
| 상속을 지원하는 extends와 super 키워드를 제공한다.                                                                                  | extends와 super 키워드를 지원하지 않는다.                                                                                  |
| 호이스팅이 발생하지 않는 것처럼 동작한다. (선언문 이전에 TDZ에 위치)                                                                | - 함수 선언문으로 정의한 생성자 함수 : 함수 호이스팅 발생.<br />- 함수 표현식으로 정의한 생성자 함수 : 변수 호이스팅 발생. |
| 클래스 내 모든 코드가 암묵적으로 strict mode가 지정되어 실행되며 해제할 수 없다.                                                    | 암묵적으로 strict mode가 지정되지 않는다.                                                                                  |
| 클래스의 constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 [[Enumerable]]의 값이 false다. 즉, 열거되지 않는다. |                                                                                                                            |

## 2. 정적 메서드와 프로토타입 메서드의 차이

- 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
- 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
- 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.

## 3. 클래스와 인스턴스의 개념 이해

- 인스턴스를 생성할 때 호출할 수 있는 클래스는 오직 하나 뿐.
- 클래스가 먼저 정의되어야만 그로부터 공통적인 요소를 지니는 개체들을 생성할 수 있음.

## 4. 자바스크립트의 클래스

- 프로토타입을 일반적인 의미에서의 클래스 관점에서 접근해보면 비슷하게 해석할 수 있는 요소가 있음.

- 생성자 함수 Array를 new 연산자와 함께 호출하면 인스턴스가 생성됨. 이 때 Array를 일종의 클래스라고 하면, Array의 prototype 객체 내부 요소들이 인스턴스에 '상속'된다고 볼 수 있음.

- 엄밀히 말하면 프로토타입 체이닝에 의한 참조지만 결과적으로는 동일하게 동작.

- Array 내부 프로퍼티들 중 prototype 프로퍼티를 제외한 나머지는 인스턴스에 상속되지 않음.

- 예제

  ```js
  var Rectangle = function (width, height) {
    // 생성자
    this.width = width;
    this.heigth = height;
  };
  Rectangle.prototype.getArea = function () {
    // (프로토타입) 메서드
    return this.width * this.height;
  };
  Rectangle.isRectangle = function (instance) {
    // 스태틱 메서드
    return (
      instance instanceof Rectangle && instance.width > 0 && instance.heigth > 0
    );
  };

  var rect1 = new Rectangle(3, 4);
  console.log(rect1.getArea()); // 12
  console.log(rect1.isRectangle(rect1)); // error
  console.log(Rectangle.isRectangle(rect1)); // true
  ```

  - 인스턴스에서 직접 호출할 수 있는 메서드 === 프로토타입 메서드
    - `rect1.getArea()`
  - `rect1.__proto__`에도, `rect1.__proto__.__proto__`(Object.prototype)에도 isRectangle이라는 메서드는 없으므로, Uncaught TypeError: not a function 에러 발생.
    - 이렇게 인스턴스에서 직접 접근할 수 없는 메서드를 스태틱 메서드라고 함.
    - 스태틱 메서드는 생성자 함수를 this로 해야만 호출할 수 있음. 즉, 생성자 함수(클래스) 자신만이 호출 할 수 있음.

## 5. ES6의 클래스 및 클래스 상속

- ES6에서는 본격적으로 클래스 문법이 도입됨.

- ES5 vs ES6 클래스 문법 비교

  ```js
  var ES5 = function (name) {
    this.name = name;
  };
  ES5.staticMethod = function () {
    return this.name + " staticMethod";
  };
  ES5.prototype.method = function () {
    return this.name + " method";
  };
  var es5Instance = new ES5("est5");
  console.log(ES5.staticMethod()); // es5 staticMethod
  console.log(es5Instance.method()); // es5 method

  var ES6 = class {
    constructor(name) {
      this.name = name;
    }
    static staticMethod() {
      return this.name + " static Method";
    }
    method() {
      return this.name + " method";
    }
  };
  var es6Instance = new ES6("es6");
  console.log(ES6.staticMethod()); // es6 staticMethod
  console.log(es6Instance.method()); // es6 method
  ```

- ES6의 클래스 상속

  ```js
  var Rectangle = class {
    constructor(width, height) {
      this.width = width;
      this.height = height;
    }
    getArea() {
      return this.width * this.height;
    }
  };
  var Square = class extends Rectangle {
    constructor(width) {
      super(width, width);
    }
    getArea() {
      console.log("size is :", super.getArea());
    }
  };
  ```

  - 'extends 클래스명'으로 상속관계 설정.
  - super 키워드를 사용하면 SuperClass의 constructor를 실행.
  - constructor 메서드를 제외한 다른 메서드에서는 super 키워드를 마치 객체처럼 사용할 수 있고, 이때 객체는 SuperClass.prototype을 바라보는데, 호출한 메서드의 this는 'super'가 아닌 원래의 this를 그대로 따름.
