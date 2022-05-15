---
description: 웹 프레임워크의 핵심은 컴포넌트간의 데이터 공유죠!
---

# 03. Props

## Prop 정의

부모 컴포넌트에서 자식 컴포넌트로 데이터를 넘겨줄 때, 아래와 같이 prop을 이용합니다.

```html
<script>
  import Nested from './Nested.svelte';
</script>

<Nested answer={42}/>
```
자식 컴포넌트에서 넘겨받은 prop을 그냥 쓸 수 있는 건 아니에요. *export*를 이용해 해당 prop을 정의해줘야 합니다.

{% tabs %}
{% tab title="Nested.svelte" %}
```html
<script>
  export let answer;
</script>

<p>The answer is {answer}</p>
```
{% endtab %}
{% tab title="결과" %}
`The answer is 42`
{% endtab %}
{% endtabs %}

> 왜 export로 했을까... 넘나 헷갈린다... (그치만 튜토리얼에서 곧 익숙해질거라고 했으니 믿어봅쉬당)

### Prop에 Default 값 주기

부모로부터 값을 넘겨받지 않은 경우에도 default 값을 할당할 수 있습니다.

{% tabs %}
{% tab title="App.svelte" %}
```html
<script>
  import Nested from './Nested.svelte';
</script>

<Nested />
```
{% endtab %}
{% tab title="Nested.svelte" %}
```html
<script>
  export let answer = 21;
</script>

<p>The answer is {answer}</p>
```
{% endtab %}
{% tab title="결과" %}
`The answer is 21`
{% endtab %}
{% endtabs %}

### 객체 Props

Props가 객체라면 아래와 같이 spread로 넘겨줄 수도 있습니다.

{% tabs %}
{% tab title="App.svelte" %}
```html
<script>
  import Info from './Info.svelte';

  const pkg = {
    name: 'svelte',
    version: 3,
    speed: 'blazing',
    website: 'https://svelte.dev'
  };
</script>

<Info name={pkg.name} version={pkg.version} speed={pkg.speed} website={pkg.website}/>
<Info {...pkg}/>
```
{% endtab %}
{% tab title="Info.svelte" %}
```html
<script>
  export let name;
  export let version;
  export let speed;
  export let website;
</script>

<p>
  The <code>{name}</code> package is {speed} fast.
  Download version {version} from <a href="https://www.npmjs.com/package/{name}">npm</a>
  and <a href={website}>learn more here</a>
</p>
```
{% endtab %}
{% tab title="결과" %}
`The svelte package is blazing fast. Download version 3 from npm and learn more here`

`The svelte package is blazing fast. Download version 3 from npm and learn more here`
{% endtab %}
{% endtabs %}

{% hint style="info" %}
자식 컴포넌트에서 하나하나 export 해주지 않고, `$$props`로 바로 이용할 수도 있습니다. Svelte가 최적화 하기 어려워 권장하는 방법은 아니지만, 드물게 유용히 사용될 수 있습니다.
```javascript
let {name, speed, version, website} = $$props;
```
{% endhint %}