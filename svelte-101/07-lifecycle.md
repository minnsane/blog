---
description: Svelte의 요람에서 무덤까지
---

모든 컴포넌트는 '생성'되며 시작되어 '소멸'되며 끝나는 생애주기를 갖고 있습니다. 생애 주기의 특정 시점에 접근 하고 싶다면 아래의 함수들을 알아두시면 됩니다.

|함수|시점|
|--|--|
|`onMount`|컴포넌트가 생성된 직후|
|`onDestroy`|컴포넌트가 소멸되기 직전|
|`beforeUpdate`|컴포넌트의 상태가 업데이트되기 직전|
|`afterUpdate`|컴포넌트의 상태가 업데이트된 직후|
|`tick`|컴포넌트의 상태가 업데이트된 후 DOM에 반영되기까지 대기|


# onMount

가장 자주 사용하게 될 `onMount`는 컴포넌트가 DOM에 최초 렌더된 직후 실행됩니다. 

```html
<script>
  import { onMount } from 'svelte';

  let photos = [];

  onMount(async () => {
    const res = await fetch(`/tutorial/api/album`);
    photos = await res.json();
  });
</script>
```

{% hint style="tip" %}
SSR(서버 사이드 렌더링) 때문에 `<script>`의 상단이 아닌 `onMount`에서 `fetch`하기를 추천합니다. SSR일 때, 라이프사이클 함수들은 `onDestroy` 없이는 실행되지 않기 때문에 컴포넌트가 생성된 후 lazy-load 되어야 하는 데이터를 미리 fetching하는 오류를 피할 수 있게 됩니다.
{% endhint %}

라이프사이클 함수들은 컴포넌트가 초기화될 때 실행되어야 컴포넌트의 인스턴스로 바인딩됩니다. `setTimeout`같은 함수 내에서 사용하지 않도록 주의하세요.

`onMount`의 콜백함수가 다른함수를 return한다면, 해당 함수는 컴포넌트가 소멸될 때 호출됩니다.

# onDestroy

컴포넌트가 소멸될 때 코드를 실행하고 싶다면, `onDestroy`를 이용하세요. 

예를 들어, 컴포넌트가 생성될 때 `setInterval` 함수를 추가했다면, `onDestroy` 함수에서 해당 인터벌을 `clearInterval`해주면 메모리릭을 예방할 수 있죠.

```javascript
import { onDestroy } from 'svelte';

let counter = 0;
const interval = setInterval(() => counter += 1, 1000);

onDestroy(() => clearInterval(interval));
```

컴포넌트의 라이프사이클 함수들이 초기화 **시점**에 실행되어야 하는 규칙이 있는 반면, **어디서** 불리는지는 상관이 없습니다. 원한다면 *util.js* 같은 함수로 인터벌 로직을 빼줄 수도 있죠.

{% tabs %}
{% tab title="util.js" %}
```javascript
import { onDestroy } from 'svelte';

export function onInterval(callback, milliseconds) {
	const interval = setInterval(callback, milliseconds);

	onDestroy(() => {
		clearInterval(interval);
	});
}
```
{% endtab %}
{% tab title="App.svelte" %}
```html
<script>
	import { onInterval } from './utils.js';

	let counter = 0;
	onInterval(() => counter += 1, 1000);
</script>
```
{% endtab %}
{% endtabs %}

# beforeUpdate와 afterUpdate

`beforeUpdate`함수는 DOM이 업데이트 되기 직전 실행됩니다. `afterUpdate`는 그 반대함수로, DOM이 데이터와 싱크된 직후 실행되죠.

이 두 함수를 이용하면, 상태 변화만으로는 해결할 수 없는, element의 스크롤 포지션 업데이트와 같은 일들을 해결하는데 유용합니다.

```javascript
let div;
let autoscroll;

beforeUpdate(() => {
	autoscroll = div && (div.offsetHeight + div.scrollTop) > (div.scrollHeight - 20);
});

afterUpdate(() => {
	if (autoscroll) div.scrollTo(0, div.scrollHeight);
});
```

# tick

다른 라이프사이클 함수와 달리, `tick`함수는 언제든 실행할 수 있습니다. `tick`함수는 어느 pending중인 상태던 DOM에 반영될 때(pending 중인 상태가 없다면 즉시) resolve되는 `promise`를 반환합니다.

Svelte에 컴포넌트 상태를 업데이트한다고, 즉시 DOM에 반영되는 것은 아닙니다. 대신, 따로 또 업데이트 해야 할 상태가 해당 컴포넌트나 다른 컴포넌트에 있지는 않을까 하여 다음 microtask까지 대기하죠. 이렇게 해야 불필요한 작업을 줄이고, 브라우저가 더 효율적으로 일괄작업을 할 수 있죠.

```html
<script>
  let state;
  $: stateText = `state : ${state}`;

  async function updateState(newState) {
    state = newState;
    console.log(stateText); // state : 이전 상태
    await tick();
    console.log(stateText); // state : 새로운 상태(newState)
  }

</script>
```

