---
description: Svelte가 뭔지 알아보자
---

# 01. Introduction

## Svelte는 뭐가 다를까?

React, Vue, Angular.. 세상에 이렇게 많은 웹 프레임워크(라이브러리)가 있는데 왜 우리는 Svelte까지 알아야 하나?

왜냐면...

* 런타임이 아닌 빌드할 때 JavaScript로 변환하기 때문에 프레임워크 추상화가 실행 퍼포먼스에 영향을 미치지 않는다.
* 따라서, 앱이 처음 로딩할 때 지연이 없다.
* 이미 존재하는 JS 코드에 일부만 적용도 가능하다! -> 프레임워크 의존성이 낮다.

그리고 무엇보다.. 2019년부터 만족스러운 프레임워크 랭킹 2위 아래로 내려간적이 없다고 하니깐여...?! ([링크](https://2021.stateofjs.com/en-US/libraries/front-end-frameworks))

## Component

Svelte 어플리케이션은 컴포넌트로 이루어져 있습니다.

Svelte 컴포넌트는요...

* 재사용이 가능하고,
* HTML, CSS, JavaScript를 한데 뭉쳐놓은
* .svelte 파일입니

### 데이터 추가하기

여기 세상에 인사하는 컴포넌트가 하나 있습니다.

```html
<h1>Hello world!</h1>
```

특정 name에게 인사하도록 데이터를 바인딩 해 주겠습니다.

script에서 정의한 변수를 중괄호({})에 야\~무지게 넣어줄게요!

{% tabs %}
{% tab title="코드" %}
```html
<script>
    let name = 'minnsane';
</script>

<h1>Hello {name}!</h1>
```
{% endtab %}

{% tab title="Second Tab" %}
`Hello minnsane!`
{% endtab %}
{% endtabs %}

중괄호 안에는 js 함수를 써서 좀 더 다양하게 인사할 수도 있어요.

{% tabs %}
{% tab title="코드" %}
```html
<script>
  let name = 'minnsane';
</script>

<h1>Hello {name.toUpperCase()}!</h1>
<h1>Hello {name.slice(0, 3)}!</h1>
```
{% endtab %}

{% tab title="결과" %}
`Hello MINNSANE!`

`Hello min!`
{% endtab %}
{% endtabs %}

### 동적 Attribute

텍스트 추가하듯, element attribute도 중괄호에 아래와 같이 추가할 수 있어요.

```html
<img src={src}>
```

이 때 Svelte는 웹 접근성을 위해 alt를 추가하라고 경고를 할 거에요!

> <mark style="color:orange;">A11y:  element should have an alt attribute</mark>

웹 접근성은 항상 갖추긴 쉽지 않지만, 가능한 한 따르며 더 많은 사람/기계가 이해할 수 있는 어플리케이션을 만들어 봅시당..!!

```html
<img src={src} alt="Minnsane's blog">
<img src={src} alt="{name}'s blog"> //name 변수를 동적으로 할당도 가능!
```

#### 짧게 쓰기

attribute의 속성명과 할당 할 변수명이 같다면 다음과 같이 짧게 쓰기도 가능합니다.

```html
<img {src} alt="src is src">
```

### 스타일

HTML 파일과 같이, \<style> 태그를 추가할 수 있어요.

다만, 컴포넌트 파일 내부의 style 태그 내용은 **해당 컴포넌트에만 적용**됩니다!

(하위에 같은 태그를 가진 컴포넌트가 있더라도 영향이 가지 않음)

{% tabs %}
{% tab title="코드" %}
```html
<p>This is a paragraph.</p>

<style>
  p {
    color: red;
  }
</style>
```
{% endtab %}

{% tab title="결과" %}
<mark style="color:red;">`This is a paragraph.`</mark>
{% endtab %}
{% endtabs %}

### HTML 태그

보통 string은 text로 그냥 보여지지만, \<br>이나 \<strong> 같은 HTML 태그를 컴포넌트에 적용하고 싶다면, **@html**을 이용하세요!

{% tabs %}
{% tab title="코드" %}
```html
<script>
  let string = `this string contains <br>some <strong>HTML!!!</strong>`;
  let htmlString = `this string contains <br>some <strong>HTML!!!</strong>`;
</script>

<p>{string}</p>
<p>{@html htmlString}</p>
```
{% endtab %}

{% tab title="결과" %}
`this string contains <br>some <strong>HTML!!!</strong>`

`this string contains`\
`some`` `**`HTML!!!`**
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Svelte는 해당 string을 DOM에 삽입하기 전에 보안안정성을 확인하지 않습니. 위 기능을 이용하기 전에 XSS 공격에 취약하지 않은지 직접 확인해야 합니다.
{% endhint %}

### 컴포넌트 사용하기

Svelte에서 만들어진 컴포넌트를 사용하는 것은 아\~주 쉽습니다. 우리가 다 아는 문법인 new를 통해 import하면 됩니다! 컴파일러가 컴포넌트들을 JS 클래스로 변환해주거든요.

```javascript
import App from './App.svelte';

const app = new App({
  target: document.body
});
```

> 컴포넌트는 Upper Case로 시작하는 것이 컨벤션입니다.
