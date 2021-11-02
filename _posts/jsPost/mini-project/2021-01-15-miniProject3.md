---
title: "쇼핑몰 미니게임 시작!"
date: 2021-01-15 +0800
categories: [Javascript, mini-project]
tags: [JSON]
---

# 1. 쇼핑몰 미니게임 만들기

우연히 엘리님의 강의를 듣게되었는데 사소하게 보이지만 정말 중요하다고 생각했던것을 알려주고 계셨고 관심이 있어서 강의를 하나 들었다. 간단한 미니게임을 만드는 과제를 받았다. 색깔을 누를때마다 거기에 해당하는 색깔대로 list가 출력되도록 하는 웹앱이어서 쉽게 만들 수 있다고 생각했다. 그러나 막상시작하려니 뭘 할지 막막했다. 내가 기본기가 아직 많이 약하다는것을 발견했다. 게다가 엘리님이 코딩리뷰까지 해주신다고 하니 이건 꼭 수강해서 배워야겠다고 생각했다.

우선 프로젝트의 기능은 버튼이 있고 그 버튼(바지,치마,색깔)을 클릭할때 마다 그 버튼에 해당되는 속성만 필터링되서 출력되게 하는 프로젝트였다. vanila JS 로 만들어야 했다. 그리고 필요한 자료는 img폴더안에 들어있었다. 일단 내가 쓴 코드는 모르고 다 지워버렸다. 대충 설명하자면 HTML에서 버튼은 IMG와 DIV로 구현을 했고 JS에서는 각 리스트의 이름을 ARRAY 안에 담은 다음 for문으로 array안에 있는 이름들을 img에 대입하여 list를 만들었다. 그러나 버튼을 구현하는 과정에서 막혔고 결국 이틀을 고민끝에 엘리님 코딩을 보았다.

엘리님은 data.json을 따로 만들어서 data폴더에 보관하셨고 그 data를 fetch를 통해 동적으로 전달받아서 html에 출력하셨다. 정말 깔끔하고 보기 쉽고 효율적인 방법이었다. 그래서 나도 data.json을 만들어서 불러들이려고 했으나 http형태가 아니면 전달받을 수 없다고 에러가 떴다. 알아보니깐 html 파일을 클릭해서 열면 로컬서버가 아닌 html을 그대로 보여주는 것 밖에 안되기 때문에 http 형태로 출력되지 않기 때문이었다.

따라서 node.js 를 설치하고 `http-server 파일경로`를 명령창에 입력한다음 localhost를 만들어서 실행해야만 json파일을 받아 올 수 있었다. 그럼 본격적으로 만들어 보자. 로직의 순서는 아래와 같다.

**1.** data.json을 불러온다.
{: .notice--success}

**2.** 불러온 json을 display 함수에 넣고 알아서 가공한다음 browser에 전달한다.
{: .notice--success}

**3.** setclick 함수에 json을 pass 하고 조건에 따라 filter 되도록 한다.
{: .notice--success}

# 2. data.json과 html만들기

먼저 data폴더 안에 data.json을 만들자. data를 js안에 보관하면 안된다. js는 비지니스 코드만 보관하고 data는 따로 보관해야 모든면에서 편하다.(가독성,유지보수, 메모리)

```json
{
  "items": [
    {
      "type": "tshirt",
      "gender": "female",
      "size": "large",
      "color": "pink",
      "image": "./img/pink_t.png"
    },
    {
      "type": "pants",
      "gender": "male",
      "size": "small",
      "color": "blue",
      "image": "./img/blue_p.png"
    },
    {
      "type": "pants",
      "gender": "male",
      "size": "large",
      "color": "yellow",
      "image": "./img/yellow_p.png"
    },
    {
      "type": "skirt",
      "gender": "male",
      "size": "large",
      "color": "yellow",
      "image": "./img/yellow_s.png"
    },
    {
      "type": "skirt",
      "gender": "female",
      "size": "small",
      "color": "blue",
      "image": "./img/blue_s.png"
    }
    .........
  ]
}
```

그래서 나중에 fetch로 받아와서 array처럼 사용하면 된다. 그럼 html을 만들어보자

우선 button을 만들고 그 button들을 section안에 담았다(이유는 나중에 나온다) 그리고 ul을 만든다음 대충 어떻게 표시되나 볼려고 임의로 list를 만들었다.

```html
<body>
  <img src="./img/logo.png" alt="logo" class="logo" />
  <section class="button">
    <button class="btn">
      <img src="./img/blue_p.png" alt="pants" class="btnImg" />
    </button>
    <button class="btn">
      <img src="./img/blue_s.png" alt="pants" class="btnImg" />
    </button>
    <button class="btn">
      <img src="./img/blue_t.png" alt="pants" class="btnImg" />
    </button>
    <button class="btn blueB">blue</button>
    <button class="btn pinkB">pink</button>
    <button class="btn yellowB">yellow</button>
  </section>
  <ul class="list">
    <li class="item">
      <img src="./img/yellow_p.png" alt="pants" class="thumnail" />
      <span class="description">male, large</span>
    </li>
  </ul>
</body>
```

css같은경우 배울점만 설명하겠다. 바로 아래와 같이 변수를 설정하는 것이었다. 그럼 일일이 숫자와 unit을 입력하지 않아도 된다. 코드가 길어지면 이렇게 간편화하는게 얼마나 큰 차이를 낳는지 느껴지게 될것이다. 여기만 바꾸면 되니깐 말이다.(또한 주석을 달아주는 것도 매우 중요하다. 그리고 핵심만 깔끔하게 적어주자)

```css
:root {
  /* color */
  --color-black: #3f454d;
  --color-white: #ffffff;
  --color-blue: #3b88c3;
  --color-yellow: #fbbe28;
  --color-pink: #fd7f84;
  --color-light-grey: #dfdfdf;
  --animation-duration: 300ms;

  /* space */
  --base--space: 1vmin;
}
```

# 3. JS 시작!

우선 JSON을 불러오고 그다음 내가 필요한 기능을 대략적으로 파악하여 어떤 함수를 만들지 적어보자. 다시말해, 큰그림을 그리고 세부적인것을 채우는 방식이다. 함수의 이름을 먼저 적고 기능은 나중에 구현하자는 말이다.

```javascript
// fetching data from data folder
function loadJson() {
  return fetch("./data/data.json")
    .then((response) => response.json())
    .then((json) => json.items);
}

// main board
loadJson()
  .then((item) => {
    displayList(item);
    setClickEvent(item);
  })
  .catch((err) => console.log(err));
```

지금 보면 가져온 data를 displayList에 보내줘서 일단 출력한다.
그리고 button을 클릭했을때 해당 item이 출력되도록 한다.
에러가 있으면 에러를 출력한다.

처럼 우선 해야할 것들을 적음으로써 가이드라인을 명확하게 제시한다. 그럼 각 함수의 기능은 다음 글에서 구현해보자
