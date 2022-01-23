### 실행 컨텍스트(Execution Context)

- 정의
  - 실행 컨텍스트는 `실행 가능한 코드가 실행되기 위해서 필요한 환경`이다.
  - ECMAScript 스펙의 정의는 `실행 가능한 코드를 형상화하고 구분하는 추상적인 개념`
- 실행 가능한 코드란?
  - `전역코드`
    - 전역 영역에 존재하는 코드
  - Eval 코드
    - eval 함수로 실행되는 코드 (보안에 위협적이라 쓰면 안됨)
  - `함수 코드`
    - 함수 내에 존재하는 코드
- js engine은 코드 실행에 필요한 여러 정보가 필요하다.

  - 변수 : 전역변수, 지역변수, 매개변수, 객체의 property
  - 함수 선언
  - 변수의 유효범위(Scope)
  - this

  ```js
  var x = "xxx";

  function foo() {
  	var y = "yyy";

  	function bar() {
  		var z = "zzz";
  		console.log(x + y + z);
  	}
  	bar();
  }
  foo();
  ```

  - 코드를 실행하면, `Context Stack`이 생성하고 소멸된다. 컨텍스트와 관련없는 코드(다른 함수)가 실행되면, 새로운 컨텍스트가 생성된다.
    ![ec_1](https://user-images.githubusercontent.com/60285506/150689437-0b5b22ee-e857-4a67-8529-07f905802e59.png)
  - 전역 코드(global code)로 제어권이 진입하면, 전역 실행 컨텍스트가 실행되고, 실행 컨텍스트 스택에 쌓인다. 전역 EC는 app 종료시까지 유지된다. (브라우저를 닫거나, 페이지에서 나가거나)
  - 함수를 호출하면 해당 함수의 EC가 생성되고, 직전에 실행된 EC위에 쌓인다.
  - 함수 실행이 끝나면, 해당 함수의 EC를 파기하고 직전의 EC에 컨트롤을 반환한다.

#### 실행 컨텍스트의 3가지 객체

- EC는 실행 가능한 코드를 형상화하고, 구분하는 추상적 개념이지만! 물리적으로는 객체의 형태를 가지고, 3개의 property가 존재한다.

- ![excute_context_structure](https://user-images.githubusercontent.com/60285506/150689621-0f0f91f3-e4a7-43c5-bf01-424c69048523.png)

#### Variable Object(VO/ 변수객체)

- 정의
  - EC가 생성되면, js engine은 `실행에 필요한 여러 정보들을 담을 객체`를 생성한다. 이것을 VO 라고 한다.
  - VO는 `엔진에 의해 참조되며, 코드에서는 접근 불가능하다`
- VO는 어떤 정보들을 담을까?
  - 변수
  - 매개변수(parameter), 인수 정보(argument)
  - 함수 선언(함수 표현식은 제외 const a = () =>~~)
- VO의 특징
  - VO는 EC의 property라서, 값을 갖지만, 이 값은 다른 객체를 가리킨다. 전역코드 실행시 생성되는 `전역 컨텍스트`와 함수를 실행할때 생성되는 `함수 컨텍스트`의 경우, 가리키는 객체가 다르다. 전역 코드와, 함수의 내용이 다르기 때문이다. (전역 코드는 매개변수가 없지만, 함수에는 매개변수가 있다.)
- 전역 컨텍스트

  > 전역 컨텍스트(GC)
  >
  > Variable object는 유일하며 최상위에 위치하고 모든 `전역 변수`, `전역 함수` 등을 포함하는 전역 객체(Global Object)를 가리킨다. 전역 객체는 전역에 선언된 전역 변수와 전역 함수를 프로퍼티로 소유한다.

  ![ec-vo-global](https://user-images.githubusercontent.com/60285506/150689913-f1c84ae0-8e59-4421-8dc0-84bdd34444c4.png)

- 함수 컨텍스트

  > 함수 컨텍스트
  >
  > Variable Object는 Activation Object(활성객체)를 가리키고, `매개변수`와 `인수들의 정보`를 `배열의 형태로 담고있는 argument Object`가 추가된다.

  ![ec-vo-foo](https://user-images.githubusercontent.com/60285506/150690121-8e301eed-bb8d-40c9-8864-a309aec77919.png)

#### Scope Chain (SC)

- 정의
  - 식별자를 결정할때 활용하는 스코프들의 연결리스트
- 스코프 체인은 일종의 리스트로서, 전역 객체와 중첩된 함수에 있는 스코프의 레퍼런스를 차례로 저장하고 있다.
- 전역 또는 함수가 참조할 수 있는 변수, 함수 선언등의 정보를 담고있는 `GO, AO의 리스트를 가리킨다`.
  ![ec-sc](https://user-images.githubusercontent.com/60285506/150690453-7c2ad7d4-8783-4aed-8099-46bf645821c0.png)

  > `스코프 체인은 식별자 중에서 객체(전역 객체 제외)의 프로퍼티가 아닌 식별자, 즉 변수를 검색하는 메커니즘`이다.

  > 식별자 중에서 변수가 아닌 객체의 프로퍼티(물론 메소드도 포함된다)를 검색하는 메커니즘은 `프로토타입 체인(Prototype Chain)`이다.

#### this value

- this property에는 this값이 할당된다. 이건 함수 호출 패턴에 의해 결정된다. (메소드 앞에 불리는 객체가 this의 대상을 가리킨다. 아무것도 없으면 전역객체 window)

## 실행 컨텍스트의 생성 과정

- 전역 코드로 진입

  - 제어권(control)이 EC에 진입하기 전에 유일한 GO가 생성된다. GO는 단일 사본으로 존재해서, 이 객체의 property는 어디서든 접근이 가능하다.
  - 초기 상태의 GO에는 빌트인 객체(Math, String, Array등)와 BOM, DOM이 설정되어 있다.
    ![ec_3](https://user-images.githubusercontent.com/60285506/150690691-4296e294-f578-4552-ab39-ddbbf3b98c83.png)

- 전역객체 생성 후, 전역코드로 control이 들어가면, global EC가 생성되고 EC에 쌓인다.
  ![ec_4](https://user-images.githubusercontent.com/60285506/150690697-19400cf2-cd54-4dd6-a42e-6c714aa8fa76.png)
- 이 이후 다음과 같은 처리들이 실행된다.

  - 1. 스코프 체인의 생성과 초기화
  - 2. Variable Instantiation(변수 객체화) 실행
  - 3. this value 결정

- 스코프 체인의 생성과 초기화
  - 실행 컨텍스트가 생성된 이후, 가장 먼저 스코프체인의 생성과 초기화가 실행된다. 이때 sc는 전역 객체의 레퍼런스를 포함하는 리스트가 된다.
    ![ec_5](https://user-images.githubusercontent.com/60285506/150690777-b8fb7ad2-11c6-4150-993a-f97763f8c08b.png)
- Variable Instantiation(변수 객체화) 실행
  - Variable Object에 프로퍼티와 값을 추가하는것을 의미한다. 변수, parameter, arguments, 함수 선언을 VO에 추가해서 객체화 하기 때문이다.
  - 전역 코드는 VO는 Global Object를 가리킨다.
    ![ec_6](https://user-images.githubusercontent.com/60285506/150690838-c8414bb3-0f17-4dfa-ab89-d95144306ef9.png)
- 변수객체화의 순서(\*순서 중요)
  - (Function Code인 경우) `매개변수(parameter)`가 Variable Object의 프로퍼티로, 인수(argument)가 값으로 설정된다.
  - 대상 코드 내의 `함수` 선언(함수 표현식 제외)을 대상으로 함수명이 Variable Object의 프로퍼티로, 생성된 함수 객체가 값으로 설정된다.(`함수 호이스팅`)
  - 대상 코드 내의 `변수` 선언을 대상으로 변수명이 Variable Object의 프로퍼티로, undefined가 값으로 설정된다.(`변수 호이스팅`)
