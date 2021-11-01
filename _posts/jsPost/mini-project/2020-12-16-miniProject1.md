---
title: "그 유명한 계산기 한 번 만들어보기"
date: 2020-12-16 +0800
categories: [Javascript, mini-project]
tags: [eval]
---

# 계산기 만들기.

챌린지 과제중에 계산기 만들기 과제가 있었다. 숫자를 누를때마다 화면에 출력되어야 하고 연산자를 누를때마다 계산되어야 하며 그 결과 값이 바로바로 화면에 출력되어야한다.

js로 계산기 만드는 방법은 쉽게 구글링 할 수 있었지만 오로지 내 스스로의 능력으로 만들어보고 싶었다. 예상대로 고통스러웠지만 결국에는 해내는 나를 보면서 참 자랑스러웠다. 우선 html 부터 만들어주자.

```html
<body>
  <form>
    <div>
      <span class="big"></span>
      <span class="small"></span>
    </div>
    <button value="undo" id="undo">C</button>
    <button value="1" id="one">1</button>
    <button value="2" id="two">2</button>
    <button value="3" id="three">3</button>
    <button value="4" id="four">4</button>
    <button value="5" id="five">5</button>
    <button value="6" id="six">6</button>
    <button value="7" id="seven">7</button>
    <button value="8" id="eight">8</button>
    <button value="9" id="nine">9</button>
    <button value="0" id="zero">0</button>
    <button value="=" id="equal">=</button>
    <button value="+" id="plus">+</button>
    <button value="-" id="minus">-</button>
    <button value="*" id="multiple">*</button>
    <button value="/" id="divide">/</button>
  </form>
  <script src="src/index.js"></script>
</body>
```

그리고 각각의 버튼을 클릭했을때 값이 입력되는걸로 대충 로직을 짰다. 로직 순서는 이렇게 생각했다.

1. 입력값을 한곳에 모으기(i.e 1112 ) 리스트를 쓰튼 localstrage를 쓰든. 그리고 모은 값을 가져와서 span에 display하기.
2. 연산자를 클릭하면 모은값을 가져다가 연산자 함수에 pass 해서 계산 하고 display한다음, return으로 저장. result 를 리스트안에 저장하지 말고 span값을 가져다가 바로 계산해서 display하면 된다.
3. = 을 누르면 최종값을 가져다가 span에 display함.
4. c 누르면 값이 없어지고 return으로 함수 종료.

```javascript
const form = document.querySelector("form"),
  span = form.querySelector(".big"),
  spanSmall = form.querySelector(".small"),
  undo = document.getElementById("undo"),
  one = document.getElementById("one"),
  two = document.getElementById("two"),
  three = document.getElementById("three"),
  four = document.getElementById("four"),
  five = document.getElementById("five"),
  six = document.getElementById("six"),
  seven = document.getElementById("seven"),
  eight = document.getElementById("eight"),
  nine = document.getElementById("nine"),
  zero = document.getElementById("zero"),
  equal = document.getElementById("equal"),
  plus = document.getElementById("plus"),
  minus = document.getElementById("minus"),
  multiple = document.getElementById("multiple"),
  divide = document.getElementById("divide");

let spanContainer = [];
let smallContainer = [];

undo.addEventListener("click", undoFunc);
one.addEventListener("click", display);
two.addEventListener("click", display);
three.addEventListener("click", display);
four.addEventListener("click", display);
five.addEventListener("click", display);
six.addEventListener("click", display);
seven.addEventListener("click", display);
eight.addEventListener("click", display);
nine.addEventListener("click", display);
zero.addEventListener("click", display);
equal.addEventListener("click", equalFunc);
plus.addEventListener("click", plusFunc);
minus.addEventListener("click", minusFunc);
multiple.addEventListener("click", mutipleFunc);
divide.addEventListener("click", divideFunc);
// 우선 이렇게 대략적인 틀을 만들었다. 그러고 함수를 추가하면 된다.
```

## 1. 각 버튼에 함수 추가

우선 숫자를 누를때마다 실행되는 함수부터 만들어주자.

```javascript
function display(event) {
  event.preventDefault();
  let value = event.target.value;
  spanContainer.push(value);
  smallContainer.push(value);
  //입력하는 족족 array에 넣어준다.
  var spanResult = "";
  var spanSmallResult = "";
  for (var i = 0; i < spanContainer.length; i++) {
    spanResult += spanContainer[i];
  }
  span.innerHTML = spanResult;
  for (var i = 0; i < smallContainer.length; i++) {
    spanSmallResult += smallContainer[i];
  }
  spanSmall.innerHTML = spanSmallResult;
  // 그리고 array에 넣은 값을 for 문을 통해서 이어붙인다음 화면에 띄어준다.
}
```

## 2. 연산자 함수 만들기

여기서 참 고생했다. 연산자를 클릭할때마다 결과값이 나와야하는데 그 로직을 어떻게 짜야할지 머리가 터질 지경이었다. 처음엔 for 문을 써서 하려고 했는데 실패했다. 그렇게 검색하다가 우연히 eval() 이란 함수를 발견했다. 적혀있는데로 수식을 계산한다음 결과를 내놓는 함수였다. 치트키같은 함수다.

```javascript
// + 연산자 함수만 일단 써봤다. 나머지 연산자도 다 같기 때문에.
function plusFunc(event) {
  event.preventDefault();
  const plusVal = spanSmall.innerHTML;
  try {
    const plusResult = eval(plusVal);
    //spansmall에 숫자와 연산자가 이미 적혀있는데 그것을 그대로 불러온것이다. 그 수식을 eval에 pass 했다.
    console.log(plusResult);
    span.innerHTML = plusResult;
    // 그리고 결과값을 span에 띄어주고
    spanContainer = [];
    // main display는 빈칸으로 만들어준다.
  } catch (error) {
    //pass
  }

  let val = event.target.value;
  spanSmall.innerHTML = spanSmall.innerHTML + val;
  // 그리고 수식을 이어붙여 계산 display 에 띄어준다.
  smallContainer.push(val);
  //display 함수를 위해 smallcontainer에 입력된수식을 push한다.
}
```

## 3. undo/equal 함수 만들기

undo/equal 함수는 쉽다. equal은 smallspan 값을 계산하기만 하면 되고 undo는 모든 값을 초기화 하면 된다.

```javascript
function equalFunc(evnet) {
  evnet.preventDefault();
  const finalVal = spanSmall.innerHTML;
  const finalResult = eval(finalVal);
  span.innerHTML = finalResult;
}

function undoFunc(event) {
  event.preventDefault();
  span.innerHTML = 0;
  spanSmall.innerHTML = 0;
  spanContainer = [];
  smallContainer = [];
}
```

[계산기 결과물](https://chlmz.csb.app/)
