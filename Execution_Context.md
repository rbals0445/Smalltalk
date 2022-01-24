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

- ### 코드 실행 환경을 갖추기 위한 사전 준비
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

- ### 스코프 체인의 생성과 초기화
  - 실행 컨텍스트가 생성된 이후, 가장 먼저 스코프체인의 생성과 초기화가 실행된다. 이때 sc는 전역 객체의 레퍼런스를 포함하는 리스트가 된다.
    ![ec_5](https://user-images.githubusercontent.com/60285506/150690777-b8fb7ad2-11c6-4150-993a-f97763f8c08b.png)
- ### Variable Instantiation(변수 객체화) 실행
  - Variable Object에 프로퍼티와 값을 추가하는것을 의미한다. 변수, parameter, arguments, 함수 선언을 VO에 추가해서 객체화 하기 때문이다.
  - 전역 코드는 VO는 Global Object를 가리킨다.
    ![ec_6](https://user-images.githubusercontent.com/60285506/150690838-c8414bb3-0f17-4dfa-ab89-d95144306ef9.png)
- 변수객체화의 순서(\*순서 중요)
  - (Function Code인 경우) `매개변수(parameter)`가 Variable Object의 프로퍼티로, 인수(argument)가 값으로 설정된다.
  - 대상 코드 내의 `함수` 선언(함수 표현식 제외)을 대상으로 함수명이 Variable Object의 프로퍼티로, 생성된 함수 객체가 값으로 설정된다.(`함수 호이스팅`)
  - 대상 코드 내의 `변수` 선언을 대상으로 변수명이 Variable Object의 프로퍼티로, undefined가 값으로 설정된다.(`변수 호이스팅`)
- 함수 foo()의 `선언` 처리
  - ![ec_7](https://user-images.githubusercontent.com/60285506/150704184-20ecae94-6b59-4cbc-87d2-2cc99377c731.png)
  - 생성된 함수 객체는 `[[Scopes]]` Property를 가지게 된다. 함수 객체가 실행되는 환경을 가리키고, 딱 한번만 선언됨.
  - `[[Scopes]]`는 자신의 Lexical Environment와 자신을 포함하는 외부함수의 실행환경과, 전역객체를 가리킨다. 이 때 자신을 포함하는 외부 함수의 실행컨텍스트가 소멸해도, `[[Scopes]]` property가 가리키는 외부함수의 실행환경(Activation Object)는 소멸하지 않고 참조할 수 있다. 이게 `클로저` 이다.
  - <img width="267" alt="foo-scopes" src="https://user-images.githubusercontent.com/60285506/150704187-71fedb8e-5d82-4197-934d-3e7fd69404d0.png">
  - 참고로 아직 코드가 실행되기 전이다. 하지만 Scope Chain이 가리키는 VO에 이미 함수가 등록되어 있으므로, 코드를 실행할때, 함수 선언식 이전에 함수를 호출해도 작동할 수 있게 되는것이다.
  - `함수 호이스팅` 여기서 함수 선언식은 VO에 함수명을 property로 추가하고 함수 객체를 즉시할당 하지만, 함수 표현식은 일반 변수의 방식을 따른다. 따라서 함수 선언식은 `선언문 이전에 함수를 호출할 수 있다.`
- 변수 x의 선언 처리

  - var로 선언된 변수 x는 VO에 undefined로 값이 설정되는데 왜 그럴까? (var는 선언과 초기화가 동시에 일어난다)

  ```js
  #선언 단계(Declaration phase)

  - 변수 객체(Variable Object)에 변수를 등록한다. 이 변수 객체는 스코프가 참조할 수 있는 대상이 된다.

  #초기화 단계(Initialization phase)

  - 변수 객체(Variable Object)에 등록된 변수를 메모리에 할당한다. 이 단계에서 변수는 undefined로 초기화된다.

  #할당 단계(Assignment phase)

  - undefined로 초기화된 변수에 실제값을 할당한다.
  ```

- 즉 SC이 가리키는 VO에 변수가 이미 선언 및 초기화가 되어있으므로, 변수 선언 이전에 x에 접근해도 오류가 나지 않는것임. 이걸 `변수 호이스팅` 이라고 함.
  ![ec_8](https://user-images.githubusercontent.com/60285506/150704535-6e9efe82-bcd2-4399-8417-f227656f0970.png)
- ### this value의 초기화
  - this value가 결정되기 이전에 this는 전역 객체를 가리키고 있다가, 함수 호출 패턴에 의해 this에 할당되는 값이 정해진다.
    전역코드의 this는 전역 객체를 가리킨다.
  - ![ec_9](https://user-images.githubusercontent.com/60285506/150704589-393e0350-6610-4d5e-9cf5-709705e749d3.png)
  - `전역 컨텍스트(전역 코드)의 경우, Variable Object, 스코프 체인, this 값은 언제나 전역 객체이다`

## 전역코드의 실행 (실제 실행부분)

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

- x의 할당과 foo()가 실행된다.
- ![ec_10](https://user-images.githubusercontent.com/60285506/150704711-4bb3a328-2935-4171-b203-6de08d8e0813.png)
- 전역 변수 x에 "xxx"를 할당할 때, 현재 EC의 SC이 참조하는 VO를 선두(0)부터 검색해서, 변수명에 해당하는 property가 발견되면, 값('xxx')을 할당한다.

- foo()의 실행
- 전역코드의 함수 foo()가 실행되면, 새로운 함수 EC가 생성된다. 그리고 foo로 EC의 control이 이동하면, 전역코드와 마찬가지로 1. SC 생성 및 초기화, 2. 변수 객체화 실행, 3. this value 결정이 실행된다.
- ![ec_11](https://user-images.githubusercontent.com/60285506/150704758-42bb9c88-6113-4ad7-bf28-c833c20385f2.png)
- 전역 코드의 룰이 아닌 함수 코드의 룰이 적용된다는 차이가 있음.
- 스코프 체인의 생성과 초기화
- ![ec_12](https://user-images.githubusercontent.com/60285506/150704826-571a72e3-bfee-4fa2-92a4-cea564408539.png)
- 가장 먼저 AO에 대한 reference를 SC의 선두(0)에 설정으로 시작한다.
- AO는 argument Property의 초기화를 실행하고, 변수 객체화가 실행된다.
- 참고로 AO에는 직접 접근할 수 없고, (스펙상의 개념) Property로의 접근은 가능하다.
- 그 이후에 Caller(전역 컨텍스트)의 Scope chain이 참조하는 객체가 SC에 push된다. 따라서 foo를 실행하고나서, EC의 SC는 AO-1, GO를 순차적으로 참조한다.
- ![ec_13](https://user-images.githubusercontent.com/60285506/150704918-064d0667-d3d1-49fc-baa3-9b69bdfda60c.png)
- Variable Instantiation (변수 객체화 실행)
- Function Code는 AO를 Variable object로서 변수 객체화를 실행시킨다. (이걸 제외하고는 전역 코드와 동일)
- 즉 함수 객체를 VO에 바인딩하고, 프로퍼티는 bar, 값은 새로 생성된 Function object. bar function object의 `[[Scope]]` 프로퍼티 값은 AO-1과 GO를 참조하는 리스트이다.
  ![ec_14](https://user-images.githubusercontent.com/60285506/150705000-ae0ab3a4-8e54-4dc3-9bd3-54636f076f9e.png)
- y를 AO-1에 설정한다. 이때 var이므로 초기화까지 한다.
  ![ec_15](https://user-images.githubusercontent.com/60285506/150705157-2092e1f1-7f24-43d2-ab70-41b30fdfed8e.png)
- 변수 선언 처리가 끝나면, this value가 결정된다. 내부 함수의경우 this의 value는 전역객체이다.
  ![ec_16](https://user-images.githubusercontent.com/60285506/150705230-5c4708b6-b356-4458-9688-45c1d5dc4a7f.png)

### foo 함수코드의 실행

- 실행이 되어서 현재 EC의 SC가 참조하는 VO를 선두(0)부터 검색해서, 그 변수명에 해당하는 프로퍼티가 발견되면 값을 할당한다.
  ![ec_17](https://user-images.githubusercontent.com/60285506/150705300-846921c4-2735-4089-9ded-22ba77df2f1f.png)

- 마지막으로 함수 bar실행 및 bar 코드 실행 (bar가 실행되면 새 EC 생성)
  ![ec_18](https://user-images.githubusercontent.com/60285506/150705339-df6a059b-62ab-41d7-b586-b7ee9c36f4d5.png)
- 이전의 foo와 같게, 1. SC 생성 및 초기화, 2. 변수 객체화, 3. this value 결정이 순차적 실행

  ![ec_19](https://user-images.githubusercontent.com/60285506/150705375-e7632af1-72a1-4548-a3e1-99a0b7157df1.png)

- `console.log(x+y+z);의 실행결과 예측해보기`
