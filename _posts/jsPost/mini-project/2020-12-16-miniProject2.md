---
title: "좀 더 세련된 계산기 코드(shout out to Nico💪)"
date: 2020-12-16 +0800
categories: [Javascript, mini-project]
tags: [advanced calulator]
---

# 좀더 세련된 계산기 코드

고생고생해서 계산기를 완성했고 다행히 제시간내에 제출했다. 그리고 다음날 니코의 정답이 나왔는데 역시나 굉장히 깔끔하고 참신하며 예술적이기 까지 했다. 우선 html 부터 보자.

```html
<body>
  <span class="js-result result">0</span>
  <span class="js-reset reset">C</span>
  <span class="js-number">7</span>
  <span class="js-number">8</span>
  <span class="js-number">9</span>
  <span class="js-operation operation">+</span>
  <span class="js-number">4</span>
  <span class="js-number">5</span>
  <span class="js-number">6</span>
  <span class="js-operation operation">-</span>
  <span class="js-number">1</span>
  <span class="js-number">2</span>
  <span class="js-number">3</span>
  <span class="js-operation operation">*</span>
  <span class="js-number zero">0</span>
  <span class="js-equals">=</span>
  <span class="js-operation operation">/</span>
  <script src="src/index.js"></script>
</body>
```

span만 사용했고 class로 번호는 번호끼리 연산자는 연산자끼리 묶었다.

# 계산기 로직

array와 queryselectorAll을 통해서 같은 class를 semi array로 만들 수 있었고 그걸 foreach로 풀어낸다.

로직 순서를 설명해보면

**1. 웹브라우저를 처음 켜고 숫자를 최초로 입력하면 firstDone이 true가 되면서 입력되는 족족 값이 쌓이고 firstValue에 할당**
{: .notice--success}
**2. 그다음 연산자를 누르면 secondDone이 없기 때문에 아무일도 일어나지 않음 그리고 그 연산자는 currentOpearation에 할당됨**
{: .notice--success}
**3. 그리고 다시 숫자를 누르면 그 숫자는 secondValue에 할당되고 secondDone은 true가 됨.**
{: .notice--success}
**4. 그 뒤에 연산자를 다시 클릭하면 calculate함수가 실행됨**
{: .notice--success}
**5. calculate 함수 안에 doOperation 함수에 의해서 "fisrtValue 연산자 secondValue" 의 수식이 만들어지고 계산이 됨.**
{: .notice--success}
**6. 결과값이 창에 띄어지며, 동시에 firstValue에 할당이 되고 secondDone 은 false가 되면 secondValue는 ""로 바뀜.**
{: .notice--success}
**7. 그 뒤에 다시 연산자를 클릭하면 4번의 순서로 돌아가면서 반복된다.**
{: .notice--success}

어떻게 이런 로직을 짤 수 가 있는지 진짜 참신하다. 정말 대단하다는 말 밖에 안나온다. 진짜 기막힌 로직을 배운것 같아서 매우 기분 좋다.

```javascript
const result = document.querySelector(".js-result");
const reset = document.querySelector(".js-reset");
const equals = document.querySelector(".js-equals");
const numbers = Array.from(document.querySelectorAll(".js-number"));
const operations = Array.from(document.querySelectorAll(".js-operation"));

let firstValue = "",
  firstDone,
  secondValue = "",
  secondDone,
  currentOperation;

numbers.forEach(function (number) {
  number.addEventListener("click", handleNumberClick);
});
operations.forEach(function (operation) {
  operation.addEventListener("click", handleOperationClick);
});
reset.addEventListener("click", handleReset);
equals.addEventListener("click", handleEqualsClick);
```

이렇게 틀을 만들어주고 함수를 만들자.

# 함수 만들기

```javascript
function doOperation() {
  console.log(firstValue, secondValue);
  console.log(typeof firstValue, typeof secondValue);
  const intValueA = parseInt(firstValue, 10);
  const intValueB = parseInt(secondValue, 10);
  switch (currentOperation) {
    // 첫번째 숫자를 입력후 연산자를 클릭했을시 그 연산자는 currentOperation 전연변수에 저장됨. 그 저장된걸 꺼내씀
    case "+":
      return intValueA + intValueB;
    case "-":
      return intValueA - intValueB;
    case "/":
      return intValueA / intValueB;
    case "*":
      return intValueA * intValueB;
    default:
      return;
    // switch 함수에 의해 깔끔하게 계산됨
    // 다시 calculate함수로 돌아감
  }
}

function handleNumberClick(e) {
  const clickedNum = e.target.innerText;
  if (!firstDone) {
    firstValue = firstValue + clickedNum;
    result.innerHTML = firstValue;
    // 최초 숫자 입력시 활성화
  } else {
    secondValue = secondValue + clickedNum;
    result.innerHTML = secondValue;
    secondDone = true;
    console.log(secondValue);
    // 그 이후론 줄곧 second 쪽만 활성화됨
  }
}

function calculate() {
  const operation = doOperation();
  // 본격적인 계산이 들어감
  console.log(firstDone);
  result.innerHTML = operation;
  // 결과값이 화면에 띄어짐
  firstValue = operation;
  secondDone = false;
  secondValue = "";
  // second 가 비활성화 초기화됨
}

function handleOperationClick(e) {
  const clickedOperation = e.target.innerText;
  if (!firstDone) {
    console.log(firstDone);
    firstDone = true;
    //최초 계산시 활성화
  }
  if (firstDone && secondDone) {
    calculate();
    // 연산자를 입력하고 두번째 숫자를 입력한 그다음 연산자를 클릭했을때 활성화됨
  }
  currentOperation = clickedOperation;
  // 첫번째 숫자를 입력하고 연산자를 클릭했을시 활성화
}
```

# equal/reset 함수 만들기

간단하다. equal함수는 계산하면되고 reset함수는 모든것을 초기화/비활성화 시킨다.

```javascript
function handleReset() {
  firstValue = "";
  secondValue = "";
  firstDone = false;
  secondDone = false;
  currentOperation = null;
  result.innerHTML = "0";
}

function handleEqualsClick() {
  if (firstDone && secondDone) {
    calculate();
  }
}
```

정말 다시 생각해봐도 기분좋을 정도로 예술적이다. 또 배웠다! Thanks a bunch Nico!
