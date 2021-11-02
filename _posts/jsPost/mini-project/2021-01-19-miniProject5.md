---
title: "좌표찾아 007"
date: 2021-01-19 +0800
categories: [Javascript, mini-project]
tags: [rendering]
---

# 1. 좌표찾아 007을 통해 알아보는 좋은 코드작성 법

이 프로젝트를 통해 브라우저의 좌표에 대해 알아보고 그 좌표를 화면에 나타내보도록 하겠다. 더 나아가 브라우저가 이 코드를 조금더 덜 부담스럽게 받아들 일 수 있는 방법까지 알아보고 실제로 그걸 확인할 수 있는 방법도 설명하고자 한다.

```html
<body>
  <div class="target">
    <img src="target.png" alt="img" />
  </div>
  <div class="innerCircle"></div>
  <div class="cross"></div>
  <div class="horizontal"></div>
</body>
```

요렇게 target그림 , 좌표가 나오는 공간, 세로축, 가로축 이 들어갈 tag를 만들었다. 그럼 css를 만들어보자.

```css
body {
  background-color: black;
  margin: 0;
}

img {
  position: absolute;
}

.coord {
  width: 20vmin;
  color: beige;
  position: absolute;
  margin: 20px;
}
section {
  position: absolute;
}

.cross {
  height: 100vh;
  width: 1px;
  background-color: beige;
  position: fixed;
}

.horizontal {
  height: 1px;
  width: 100vw;
  background-color: beige;
  position: fixed;
}
```

마우스가 움직일때 마다 위치가 변해야 하므로 `position:absolute` 가 동일하게 들어있다는 것을 염두해두자. 그럼이제 자바스크립트를 작성해야한다. 또한 target이 커서로 부터 약간 미세하게 벗어나있는데 body margin 때문에 그런것이니 이것을 0으로 만들어주면 깔끔하게 커서와 target이 일치되는 것을 볼 수 있다.

```javascript
const coord = document.querySelector(".coord");
const cross = document.querySelector(".cross");
const horizontal = document.querySelector(".horizontal");
const target = document.querySelector("img");

document.addEventListener("mousemove", (event) => {
  const xCoor = event.pageX;
  const yCoor = event.pageY;
  target.style.top = xCoor + "px";
  target.style.left = yCoor + "px";
  cross.style.top = xCoor + "px";
  horizontal.style.left = yCoor + "px";
  coord.style.top = xCoor + "px";
  coord.style.left = yCoor + "px";
  coord.innerHTML = `${xCoor}px, ${yCoor}px`;
});
```

그럼 이렇게 잘 나오는 것을 확인할 수 가 있다.

![target](https://yeonghunko.github.io/assets/img/vanila_practice/target.png){:class="img-fluid"}

그러나 top , left를 쓰면 성능이 저하된다. 그 이유를 알려면 먼저 서버가 브라우저에게 웹 정보를 전달하는 과정을 알아야한다.

(rendering 하는 과정)

**1.** request/reponse: 브라우저가 서버에게 html 파일을 요청. 가장 먼저 html을 받아오고 그 파일안에서 링크된 필요한 파일들 (css/js)을 받아온다
{: .notice--success}

**2.** loading: 받아온 파일들을 로딩
{: .notice--success}

**3.** scripting: html 파일을 읽어서 DOM요소로 변환한다(DOM 트리 생성), 그리고 CSS 스타일을 모두 계산해서 CSSOM 트리 생성
{: .notice--success}

**4.** rendering: DOM + CSSOM 트리를 브라우저에 표기될 요소들만 선별해서 Render Tree를 생성 (html header는 표기하지 않음)
{: .notice--success}

(여기서부터는 operation 하는 과정)

**5.** layout: Render Tree의 각 요소들의 위치, 크기, 나타날 순서 등을 고려하여 브라우저에 배치하는 과정
{: .notice--success}

**6.** painting: layout을 통해 계산된 모든 정보를 bitmap의 형태로 조각조각 떨어져 있는데 그러한 정보를 모아서 실제 뿌려주는 단계.
{: .notice--success}

**7.** composition: 뿌려주고나서 다듬는 단계
{: .notice--success}

그런데 top과 left를 사용하면 layout단계에서 node들이 바뀌어 버린다. 그렇다면 target이 없어지면 그와 관련된 다른 모든 node들의 layout도 계산해서 바뀌어 버리기때문에 엉망이 되고 많다. 쉽게말해, 유지보수도 힘들어지고, 프레임도 떨어진다. 다음글에서 해결방법을 설명하겠다.
