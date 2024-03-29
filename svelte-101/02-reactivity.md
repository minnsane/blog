---
description: Svelte는 상태 변화에 따른 반응성이 아주 뛰어납니다.
---

# 02. Reactivity

## 상태 변화

### 이벤트 바인딩

`on:`_`event`_`={`_`function`_`}`

이벤트 함수는 아래 코드와 같이 바인딩 해주면 됩니다. 이 때, count 상태가 변화함에 따라 svelte는 DOM을 자동으로 업데이트 합니다.

```html
<script>
  let count = 0;

  function handleClick() {
    count++;
  }
</script>

<button on:click={handleClick}>
  count
</button>
```

### 상태 변화 감지

어떤 상태는 다른 상태에 영향을 받기도 하죠. 예를 들어, fullName은 firstName과 lastName에 영향을 받습니다. firstName, lastName의 상태가 변화할 때 fullName의 상태도 업데이트 하고 싶다면, 아래와 같이 선언해주면 됩니다.

`$: `_`computedState`_` = ...`

```javascript
let firstName = 'John';
let lastName = 'Doe';

$: fullName = `${firstName} ${lastName}`;
```

#### 함수에 적용하기

아래의 코드는 name이 변경될 때 마다 콘솔에 로그를 찍습니다.

```javascript
$: console.log(`Hello, ${name}`);
```

#### if문에 적용하기

if문에 적용해서 조건에 따라 다른 동작을 하게 할 수도 있죠!

```javascript
$: if(name === 'Tony') {
  console.log('You are banned');
}
```

### 배열

Svelte의 상태변화는 할당에 의해 일어나기 때문에 push나 splice같은 배열 함수들은 동작하지 않습니다. 배열이나 객체의 상태 변화를 감지시키려면 아래와 같이 써줘야 합니다.

* 통째로 재할당하기

```javascript
array = [...array, newElement];
```

* 배열/객체의 프로퍼티에 직접 접근하기

```javascript
// good
obj.foo = 'bar';

// bad : 아래와 같은 객체 프로퍼티 사용은 변화를 감지하지 못합니다.
const {foo} = obj;
foo = 'bar';
```
