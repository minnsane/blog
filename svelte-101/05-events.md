---
description: 이벤트를 좋아하시나요?
---

# 05. Events

## DOM 이벤트

DOM 이벤트는 이전에 말했듯이, on 디렉티브를 사용하면 됩니다.

`on:`_`event`_`={`_`function`_`}`

### Inline 핸들러

DOM 이벤트에 함수를 연결시켜줘도 좋지만, inline으로 핸들러를 달아줄 수도 있어요!

```html
<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
	The mouse position is {m.x} x {m.y}
</div>
```

Svelte의 inline 핸들러는 두가지 특징이 있어요.
* inline 핸들러를 감싸는 quote("")는 optional입니다. 그치만 붙여주면 syntax highlighting에 도움이 됩니다!
* 몇몇 프레임웍은 성능상 문제로 inline 핸들러를 지양하라고 하지만, Svelte는 문제 없슴다!!! 항상 최고를 제공합니다!!

### Event 수식어

이벤트 핸들러에 수식어를 붙여서 핸들러의 수행을 제어할 수 있어요. 아래처럼 체이닝도 가능합니다.

`on:`_`event`_`|`_`modifier`_`={`_`function`_`}`<br>
`on:`_`event`_`|`_`modifier1`_`|`_`modifier2...`_`={`_`function`_`}`

|수식어|설명|
|-----|-----|
|`preventDefault`|핸들러 함수 수행 전에 `event.preventDefault()`를 호출합니다.|
|`stopPropagation`|핸들러 함수 수행 전에 `event.stopPropagation()`를 호출해 이벤트가 다음 요소로 전파되는것을 막습니다.|
|`passive`|터치/휠 이벤트의 스크롤 이벤트를 강화합니다. (Svelte는 가능하면 이 기능을 default로 켜놓습니다.)|
|`nonpassive`|`passive`를 확실하게 false로 설정합니다.|
|`capture`|핸들러를 버블링 단계가 아닌 캡쳐링 단계에서 수행합니다.|
|`once`|핸들러를 한 번 수행한 뒤 삭제합니다.|
|`self`|`event.target`이 자기자신일 때만 핸들러를 수행합니다.|
|`trusted`|`event.isTrusted`가 true일때만 핸들러를 수행합니다. (예: user action에 의해 이벤트가 발생했을 때)|

## 컴포넌트 Event
컴포넌트도 이벤트를 보낼 수 있어요. dispatcher event를 만들어 주면 됩니다.

`dispatch(`_`eventName`_`, `_`eventDetail`_`)`

```javascript
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  function sayHello() {
    dispatch('message', {
      text: 'Hello!'
    })
  }
```

이벤트를 받는 쪽은 아래와 같이 핸들러를 달아주면 됩니다.

`on:`_`eventName`_`={`_`function`_`}`

{% hint style="tip" %}
`createEventDispatcher`는 반드시 컴포넌트가 초기화될 때 생성되어야 합니다. *setTimeout*같은 나중에 수행되는 함수 안에서는 생성될 수 없어요!
{% endhint %}

### Event 포워딩

DOM 이벤트와 달리, 컴포넌트 이벤트는 버블링이 일어나지 않습니다. 중첩구조 깊숙히 안에 있는 컴포넌트의 이벤트를 듣고 싶다면, 중간에 끼어있는 컴포넌트들이 이벤트를 전달해줘야 해요.

```
GrandParent
⎿_ Parent
   ⎿_ Child
```

컴포넌트 계층 구도가 아래와 같고, *Child*에서 dispatch하는 이벤트를 *GrandParent*에서 listen하고 있을 때, *Parent*는 해당 이벤트를 전달하는 역할을 해야 합니다.

`on:`_`eventName`_

{% tabs %}
{% tab title="GrandParent" %}
```html
<Parent on:event={handler} />
```
{% endtab %}
{% tab title="Parent" %}
```html
<Child on:event />
```
{% endtab %}
{% tab title="Child" %}
```javascript
import { createEventDispatcher } from 'svelte';

const dispatch = createEventDispatcher();

function dispatcher() {
  dispatch('event', {
    text: 'Hello!'
  })
}
```
{% endtab %}
{% endtabs %}

{% hint style="tip" %}
이벤트 포워딩은 DOM 이벤트에서도 가능합니다!

{% tabs %}
{% tab title="App" %}
```html
<CustomButton on:click={handler} />
```
{% endtab %}
{% tab title="CustomButton" %}
```html
<button on:click>Click</button>
```
{% endtab %}
{% endtabs %}
{% endhint %}