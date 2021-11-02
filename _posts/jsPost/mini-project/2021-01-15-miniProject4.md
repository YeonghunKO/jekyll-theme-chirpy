---
title: "쇼핑몰 미니게임 강화!"
date: 2021-01-15 +0800
categories: [Javascript, mini-project]
tags: [delegation, data-set]
---

# 1. displayList 함수 구현

items을 출력할 함수를 만들어 보자

```javascript
// update the list with given items
function displayList(items) {
  const list = document.querySelector(".list");
  list.innerHTML = items.map((item) => displayHTML(item)).join("");
  // list태그안에 있는 내용을 innerHTML을 통해서 업데이트 가능
}
// map은 key-value가 있는 object를 loop할때 사용할 수 있구나.
// join함수가 없으면 li 뒤에 , 가 따라 붙어서 쓸데없는 공백이 생겨버린다.

// create list element
function displayHtml(item) {
  // 아까 샘플로 만들었던 html list를 붙여넣고 수정하자
  return `
   <li class="item ${item.type} ${item.color}">
    <img src="${item.image}" alt="${item.type}" class="thumnail" />
    <span class="description">${item.gender}, ${item.size}</span>
  </li>`;
}
// createElement/ append 를 통해서 태그를 만들어도 되지만 이렇게 직관적으로 string template를 통해 return 해줘도 상관없다. 오히려 이게 더 보기 쉬울 수 있다
```

요런식으로 string template를 사용해 만들면 더 보기 깔끔하고 쉽다.

# 2. setClickEvent 함수 구현

여기서 아까 buttons를 section으로 감쌌던 이유가 나온다. 바로 `이벤트 위임`이라는 것 때문인데, 부모태그에 event를 설정하면 자식태그들도 그 부모태그의 이벤트를 위임받기 때문에 `querySelectorAll('.btn')` 사용할 필요가 없다.(참고로 querySelectorAll은 array-like를 리턴하기 때문에 forEach를 사용할 수 있다)

```javascript
// set click function
function setClickEvent(items) {
  const buttons = document.querySelector(".button");
  const logo = document.querySelector(".logo");
  buttons.addEventListener("click", (event) => onButton(event, items));
  logo.addEventListener("click", () => displayList(items));
}
```

여기서 onButton을 만들기 위해서 button에 dataset을 설정할 필요가 있다. dataset은 내가 임의로 그 button의 data속성을 설정해서 내가 선택한 button을 명확하게 가져올 수 있다.

```html
<section class="button">
  <button class="btn">
    <img
      src="./img/blue_p.png"
      alt="pants"
      class="btnImg"
      data-key="type"
      data-value="pants"
    />
  </button>
  <button class="btn">
    <img
      src="./img/blue_s.png"
      alt="pants"
      class="btnImg"
      data-key="type"
      data-value="skirt"
    />
  </button>
  <button class="btn">
    <img
      src="./img/blue_t.png"
      alt="pants"
      class="btnImg"
      data-key="type"
      data-value="tshirt"
    />
  </button>
  <button class="btn blueB" data-key="color" data-value="blue">blue</button>
  <button class="btn pinkB" data-key="color" data-value="pink">pink</button>
  <button class="btn yellowB" data-key="color" data-value="yellow">
    yellow
  </button>
</section>
```

요런식으로 각 button에 datakey / datavalue를 달아주면 그 button의 속성을 가져올 수 있다. 그러고 onButton을 만들자.

```javascript
function onButton(event, items) {
  const data = event.target.dataset;
  const key = data.key;
  const value = data.value;
  if (key === null || value === null) {
    return;
  }
  // 예를 들어 blue 버튼을 선택했다고 하면 item[color] === blue 가 속해있는 data만 filter해서 displayList에 보내게 된다
  displayList(
    items.filter((item) => {
      return item[key] === value;
    })
  );

  const list = document.querySelectroAll(".item");
}
```

그런데 이렇게 하면 문제가 발생한다. 버튼을 매번 클릭할때마다 list가 업데이트된다는 것이다. 그럼 시간도 에너지도 많이 든다. 따라서 기존에 있는 list에서 클릭한 값에 해당되는 list에다가 display = none 해주는게 더 바람직하다.

```javascript
const list = document.querySelectorAll(".item");
list.forEach((item) => {
  if (item.classList[1] === value || item.classList[2] === value) {
    item.classList.remove("non-visible");
  } else {
    item.classList.add("non-visible");
  }
});
```

(css에는 non-visible 클래스에 display = none을 추가해주었다) 이런식으로 말이다.
그리고 list 중에서 class에 선택한 버튼의 value에 해당되는 게 있다면 표시 아니면 표시하지 않는 로직을 짰다.(string template를 보면 클래스에 color와 type 을 추가했었다) 그럼 이로써 다 만들었다.

# 3. 느낀 점

**1.** 필요할것 같은 함수의 이름을 일단 적고나서 함수의 기능을 구현해라.
만들고 나서 적용하지 말고. 우선 가이드라인이 있어야하기 때문. 큰그림을 먼저그려놓고 세부적으로 안에 작은 그림을 채워넣는거다
{: .notice--success}

**2.** 일단 html을 정적으로 만드는것 부터 시작하라.
가이드라인이 되는것도 있지만 일이 탄력받는데 도화선이 되는 목적이 가장 크다.
우선 뭔가 진전이 있어야 할 마음이 생기기 때문. 그리고 코드 작성하면서 머리도 빨리빨리 돌아가고
{: .notice--success}

**3.** 도저히 풀리지 않으면 끙끙 앓지말고 질문해라.
힌트를 봐라! 안그럼 엄청난 고통이 느껴지면서 포기하게 될 수 도 있다.
{: .notice--success}

# 4. 새로 배운 문법

**1.** ...(spread syntax)
예를 들어, 가져온 json 데이터를 여러개의 list로 변환한 뒤에 container에 append 해주려고 한다.
이때 forEach를 사용해서 하나하나 append 해줘도 되지만, iterable elements 이므로 `...` 를 사용해도 된다. 아래처럼 말이다.

```javascript
loadItems().then(items => {
const elements = items.map(createElement);
const container = document.querySelector('.items');
// 아래 코드를
elements.forEach(item => {
container.append(item);
});
// 요렇게 깔끔하게 바꿀수 있다
container.append(...elements);
```

아래 링크를 클릭하면 결과물을 확인할 수 있다.

[mini-shopping-game](https://yeonghunko.github.io/mini_shopping_game/)
