---
title: "007의 성능을 개선"
date: 2021-01-19 +0800
categories: [Javascript, mini-project]
tags: [translate, load]
---

# 1. 개선 방법

그럼 무언가가 바뀔때 가장 좋은 방법은, 수정하고 나서 operation 과정중에 composition만 하면 되게끔 하는 방법이다. 그러나 top,left를 바꾸면 layout부터 다시 시작해야한다. css property 중에 수정하고 나서 operation 과정중에 어디서 부터 수정해야하는지 알려주는 사이트가 있다.

[효율적인 css를 위한 웹사이트](https://csstriggers.com/)

들어가보면 top, left는 operation과정을 모두 거쳐야한다는 것을 확인할 수 있다. 그럼 효율적으로 위치를 수정하는 property가 뭐가 있을까? 바로 `transform property` 이다. composition만 하면 된다는 것을 확인 할 수 있다. 그럼 자바스크립트를 수정해보자.

```javascript
document.addEventListener("mousemove", (event) => {
  const xCoor = event.pageX;
  const yCoor = event.pageY;
  target.style.transform = `translate(${xCoor}px,${yCoor}px)`;
  coord.style.transform = `translate(${xCoor}px,${yCoor}px)`;
  cross.style.transform = `translate(${xCoor}px,0)`;
  horizontal.style.transform = `translate(0,${yCoor}px)`;
  coord.innerHTML = `${xCoor}px, ${yCoor}px`;
});
```

요렇게 바꾸면 된다. 근데 target이 맞지 않다.

![target](https://yeonghunko.github.io/assets/img/vanila_practice/target_miss.png){:class="img-fluid"}

곰곰히 생각해보면, target의 가로길이의 반만큼 왼쪽으로 가고 반만큼 위로 올라가면 커서에 위치하는 것을 알 수 있다. 그럼 생각할 수 있는방법이, `getBoundingClientRect` 함수를 이용해서 target의 width,height를 알아내고 나누기 2를 하는 방법이다. 그리고 그 반으로 나눈 값을 target.transform 에서 빼면 된다.

```javascript
const targetRect = target.getBoundingClientRect();
const targetRectHalfWidth = targetRect.width / 2;
const targetRectHalfHeight = targetRect.height / 2;
target.style.transform = `translate(${xCoor - targetRectHalfWidth}px,${
  yCoor - targetRectHalfHeight
}px)`;
```

그러나 문제가 발생할 수 있다. `getBoundingClientRect`을 출력했을때 height/width의 값이 0 일 수 있다. 이유는 html이 rendering되기 전에 js가 fetching되어진 경우이다. 그렇다면 html이 다 rendering 된 이후에 target의 크기를 받아오면 되므로 event추가할때 `load`라는 키워드를 사용하면 된다.

```javascript
window.addEventListener("load", () => {
  const target = document.querySelector("img");
  const targetRect = target.getBoundingClientRect();
  const targetRectHalfWidth = targetRect.width / 2;
  const targetRectHalfHeight = targetRect.height / 2;

  document.addEventListener("mousemove", (event) => {
    const xCoor = event.pageX;
    const yCoor = event.pageY;
    target.style.transform = `translate(${xCoor}px,${yCoor}px)`;
    coord.style.transform = `translate(${xCoor}px,${yCoor}px)`;
    cross.style.transform = `translate(${xCoor}px,0)`;
    horizontal.style.transform = `translate(0,${yCoor}px)`;
    coord.innerHTML = `${xCoor}px, ${yCoor}px`;
  });
});
```

그럼 html이 다 `load`되고 나서 중괄호안에 있는 코드를 실행하게 된다. 그렇게 되면, target의 height/width 값을 받아올 수 있게 되면서 target이 커서에 위치하게 된다.

# 2. 성능이 향상되었다는 증거

그럼 실제로 성능이 향상되었는지 어떻게 알 수 있을까? 바로 개발자 도구에 performance 탭에 들어가면 된다. 우선 top/left를 썼을때 performance를 살펴보고 tranform을 사용했을때 성능이 얼마나 향상되었을지 비교해보자.

![bad](https://yeonghunko.github.io/assets/img/vanila_practice/bad.png){:class="img-fluid"}

우선 perfomance에 들어가서 녹화버튼을 누르고 동작한다음 종료를 누르면 이렇게 녹화했을당시에 얼마나 잘 돌아가고 있었는지에 대한 모든 정보를 볼 수 있다. 기본적으로는 1초당 60개의 프레임이 지나가야한다. 그럼 1개의 프레임당 11.67ms 정도의 시간이 걸리면 그정도 나온다. 근데 보면 프레임 한개당 16ms, 20ms 정도의 시간이 걸린걸 확인할 수 있다.

그래서 빨간색으로 문제가 있다고 알려주고 클릭하면 그 문제의 원인이 무엇이고 어디서 발생했는지 까지 매우 친절하게 알려준다.

그럼 transform을 사용하면 어떻게 될까?

![good](https://yeonghunko.github.io/assets/img/vanila_practice/good.png){:class="img-fluid"}

빨간색이 없어지고 초록색깔의 향연을 볼 수 있다. 프레임이 향상된것이다. 이렇게 성능을 향상하는 개발, 그리고 성능을 측정할 수 있는 방법까지 살펴보았다. 진짜 유용한 팁을 알게되어서 매우 기분이 좋다.
