---
outline: deep
---

<script setup>
import SpreadSheet from './demos/SpreadSheet.vue'
</script>

# Reactivity in Depth {#reactivity-in-depth}

Vue-এর সবচেয়ে স্বতন্ত্র বৈশিষ্ট্যগুলির মধ্যে একটি হল অবাধ প্রতিক্রিয়াশীলতা ব্যবস্থা। কম্পোনেন্ট স্টেট প্রতিক্রিয়াশীল জাভাস্ক্রিপ্ট অবজেক্ট নিয়ে গঠিত। আপনি যখন তাদের পরিবর্তন করেন, Vue আপডেট হয়। এটি রাষ্ট্র পরিচালনাকে সহজ এবং স্বজ্ঞাত করে তোলে, তবে কিছু সাধারণ গোটচা এড়াতে এটি কীভাবে কাজ করে তা বোঝাও গুরুত্বপূর্ণ। এই বিভাগে, আমরা Vue-এর প্রতিক্রিয়াশীলতা সিস্টেমের নিম্ন-স্তরের কিছু বিবরণে খনন করতে যাচ্ছি।

## What is Reactivity? {#what-is-reactivity}

এই শব্দটি আজকাল প্রোগ্রামিংয়ে বেশ কিছুটা আসে, কিন্তু লোকেরা যখন এটি বলে তখন তার অর্থ কী? প্রতিক্রিয়াশীলতা একটি প্রোগ্রামিং দৃষ্টান্ত যা আমাদেরকে ডিক্লেয়ার পদ্ধতিতে পরিবর্তনের সাথে সামঞ্জস্য করতে দেয়। ক্যানোনিকাল উদাহরণ যা লোকেরা সাধারণত দেখায়, কারণ এটি একটি দুর্দান্ত, একটি এক্সেল স্প্রেডশীট:

<SpreadSheet />

এখানে সেল A2 কে `= A0 + A1` এর একটি সূত্রের মাধ্যমে সংজ্ঞায়িত করা হয়েছে (আপনি সূত্রটি দেখতে বা সম্পাদনা করতে A2 এ ক্লিক করতে পারেন), তাই স্প্রেডশীট আমাদের দেয় 3. সেখানে কোনো আশ্চর্যের কিছু নেই। কিন্তু যদি আপনি A0 বা A1 আপডেট করেন, আপনি লক্ষ্য করবেন যে A2 স্বয়ংক্রিয়ভাবে আপডেট হয়।

জাভাস্ক্রিপ্ট সাধারণত এই মত কাজ করে না। আমরা যদি জাভাস্ক্রিপ্টে তুলনামূলক কিছু লিখি:

```js
let A0 = 1
let A1 = 2
let A2 = A0 + A1

console.log(A2) // 3

A0 = 2
console.log(A2) // Still 3
```

যখন আমরা `A0` পরিবর্তন করি, `A2` স্বয়ংক্রিয়ভাবে পরিবর্তিত হয় না।

তাই কিভাবে আমরা জাভাস্ক্রিপ্ট এই কাজ করতে হবে? প্রথমে, যে কোডটি `A2` আপডেট করে সেটি পুনরায় চালানোর জন্য, আসুন এটিকে একটি ফাংশনে মোড়ানো যাক:

```js
let A2

function update() {
  A2 = A0 + A1
}
```

তারপরে, আমাদের কয়েকটি পদ সংজ্ঞায়িত করতে হবে:

- `update()` ফাংশনটি একটি **পার্শ্ব প্রভাব**, বা সংক্ষেপে **প্রভাব** তৈরি করে, কারণ এটি প্রোগ্রামের অবস্থা পরিবর্তন করে।

- `A0` এবং `A1`কে প্রভাবের **নির্ভরতা** হিসেবে বিবেচনা করা হয়, কারণ তাদের মান প্রভাবটি সম্পাদন করতে ব্যবহৃত হয়। প্রভাবটিকে এর নির্ভরশীলতার জন্য **গ্রাহক** বলা হয়।

আমাদের যা দরকার তা হল একটি ম্যাজিক ফাংশন যা `update()` (**প্রভাব**) যখনই `A0` বা `A1` (**নির্ভরতা**) পরিবর্তন করতে পারে:

```js
whenDepsChange(update)
```

এই `whenDepsChange()` ফাংশনের নিম্নলিখিত কাজগুলি রয়েছে:

1. একটি পরিবর্তনশীল পড়া হয় যখন ট্র্যাক. যেমন `A0 + A1` অভিব্যক্তিটির মূল্যায়ন করার সময়, `A0` এবং `A1` উভয়ই পড়া হয়।

2. বর্তমানে চলমান প্রভাব থাকা অবস্থায় যদি একটি ভেরিয়েবল পড়া হয়, তাহলে সেই প্রভাবটিকে সেই ভেরিয়েবলের গ্রাহক করুন। যেমন কারণ `A0` এবং `A1` পড়া হয় যখন `update()` চালানো হচ্ছে, `update()` প্রথম কলের পর `A0` এবং `A1` উভয়েরই গ্রাহক হয়ে যায়।

3. সনাক্ত করুন যখন একটি পরিবর্তনশীল পরিবর্তন করা হয়। যেমন যখন `A0` একটি নতুন মান বরাদ্দ করা হয়, তখন এর সমস্ত গ্রাহক প্রভাব পুনরায় চালানোর জন্য অবহিত করুন।

## How Reactivity Works in Vue {#how-reactivity-works-in-vue}

আমরা সত্যিই উদাহরণের মত স্থানীয় ভেরিয়েবলের পড়া এবং লেখার ট্র্যাক করতে পারি না। ভ্যানিলা জাভাস্ক্রিপ্টে এটি করার জন্য কোন ব্যবস্থা নেই। যদিও আমরা **কি করতে পারি** তা হল **অবজেক্ট বৈশিষ্ট্য** এর পড়া এবং লেখাকে আটকানো।

জাভাস্ক্রিপ্টে সম্পত্তি অ্যাক্সেস আটকানোর দুটি উপায় রয়েছে: [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description) / [setters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set#description) এবং [Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy). Vue 2 ব্রাউজার সমর্থন সীমাবদ্ধতার কারণে একচেটিয়াভাবে গেটার/সেটার ব্যবহার করেছে। Vue 3 তে, প্রতিক্রিয়াশীল বস্তুর জন্য প্রক্সি ব্যবহার করা হয় এবং রেফের জন্য গেটার/সেটার ব্যবহার করা হয়। এখানে কিছু ছদ্ম-কোড রয়েছে যা তারা কীভাবে কাজ করে তা ব্যাখ্যা করে:

```js{4,9,17,22}
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      track(target, key)
      return target[key]
    },
    set(target, key, value) {
      target[key] = value
      trigger(target, key)
    }
  })
}

function ref(value) {
  const refObject = {
    get value() {
      track(refObject, 'value')
      return value
    },
    set value(newValue) {
      value = newValue
      trigger(refObject, 'value')
    }
  }
  return refObject
}
```

:::tip
এখানে এবং নীচের কোড স্নিপেটগুলি মূল ধারণাগুলিকে সম্ভাব্য সহজতম আকারে ব্যাখ্যা করার জন্য বোঝানো হয়েছে, তাই অনেকগুলি বিবরণ বাদ দেওয়া হয়েছে এবং প্রান্তের ক্ষেত্রে উপেক্ষা করা হয়েছে৷
:::

এটি কয়েকটি [প্রতিক্রিয়াশীল অবজেক্টর সীমাবদ্ধতা](/guide/essentials/reactivity-fundamentals#limitations-of-reactive) ব্যাখ্যা করে যা আমরা মৌলিক বিভাগে আলোচনা করেছি:

- যখন আপনি একটি স্থানীয় ভেরিয়েবলে একটি প্রতিক্রিয়াশীল অবজেক্টর কম্পিউটেড প্রপার্টি বরাদ্দ বা ধ্বংস করেন, তখন সেই ভেরিয়েবলে অ্যাক্সেস করা বা বরাদ্দ করা অ-প্রতিক্রিয়াশীল কারণ এটি আর উত্স অবজেক্টে গেট/সেট প্রক্সি ট্র্যাপগুলিকে ট্রিগার করে না। মনে রাখবেন এই "সংযোগ বিচ্ছিন্ন" শুধুমাত্র পরিবর্তনশীল বাইন্ডিংকে প্রভাবিত করে - যদি ভেরিয়েবলটি একটি অ-আদিম মানকে নির্দেশ করে যেমন একটি অবজেক্ট, তাহলে অবজেক্টটিকে মিউট করা এখনও প্রতিক্রিয়াশীল হবে।

- `reactive()` থেকে ফিরে আসা প্রক্সি, যদিও আসলটির মতোই আচরণ করে, যদি আমরা `===` অপারেটর ব্যবহার করে আসলটির সাথে তুলনা করি তবে এর একটি আলাদা পরিচয় রয়েছে।

`track()` এর ভিতরে, আমরা বর্তমানে কোনো চলমান প্রভাব আছে কিনা তা পরীক্ষা করি। যদি একটি থাকে, আমরা ট্র্যাক করা কম্পিউটেড প্রপার্টির জন্য গ্রাহক প্রভাব (একটি সেটে সংরক্ষিত) সন্ধান করি এবং সেটটিতে প্রভাব যুক্ত করি:

```js
// This will be set right before an effect is about
// to be run. We'll deal with this later.
let activeEffect

function track(target, key) {
  if (activeEffect) {
    const effects = getSubscribersForProperty(target, key)
    effects.add(activeEffect)
  }
}
```

প্রভাব সাবস্ক্রিপশন একটি বিশ্বব্যাপী `WeakMap<target, Map<key, Set<effect>>>` ডেটা কাঠামোতে সংরক্ষণ করা হয়। যদি কোনও কম্পিউটেড প্রপার্টির জন্য কোনও সদস্যতা প্রভাব সেট না পাওয়া যায় (প্রথমবারের জন্য ট্র্যাক করা হয়েছে), এটি তৈরি করা হবে। সংক্ষেপে `getSubscribersForProperty()` ফাংশন এটিই করে। সরলতার জন্য, আমরা এর বিবরণ এড়িয়ে যাব।

`trigger()` এর ভিতরে, আমরা আবার কম্পিউটেড প্রপার্টির জন্য গ্রাহক প্রভাবগুলি সন্ধান করি। কিন্তু এবার আমরা পরিবর্তে তাদের আহ্বান জানাই:

```js
function trigger(target, key) {
  const effects = getSubscribersForProperty(target, key)
  effects.forEach((effect) => effect())
}
```

এখন `whenDepsChange()` ফাংশনে ফিরে বৃত্তাকার করা যাক:

```js
function whenDepsChange(update) {
  const effect = () => {
    activeEffect = effect
    update()
    activeEffect = null
  }
  effect()
}
```

এটি একটি ইফেক্টে raw `update` ফাংশনকে মোড়ানো করে যা প্রকৃত আপডেট চালানোর আগে বর্তমান সক্রিয় প্রভাব হিসেবে নিজেকে সেট করে। এটি বর্তমান সক্রিয় প্রভাব সনাক্ত করতে আপডেটের সময় `track()` কলগুলিকে সক্ষম করে৷

এই মুহুর্তে, আমরা একটি প্রভাব তৈরি করেছি যা স্বয়ংক্রিয়ভাবে তার নির্ভরতাগুলিকে ট্র্যাক করে, এবং যখনই কোনও নির্ভরতা পরিবর্তিত হয় তখন পুনরায় চালানো হয়। আমরা একে **প্রতিক্রিয়াশীল প্রভাব** বলি।

Vue একটি API প্রদান করে যা আপনাকে প্রতিক্রিয়াশীল প্রভাব তৈরি করতে দেয়: [`watchEffect()`](/api/reactivity-core#watcheffect)। প্রকৃতপক্ষে, আপনি লক্ষ্য করেছেন যে এটি উদাহরণে জাদুকর `whenDepsChange()` এর মতোই কাজ করে। আমরা এখন আসল Vue API ব্যবহার করে মূল উদাহরণটি পুনরায় কাজ করতে পারি:

```js
import { ref, watchEffect } from 'vue'

const A0 = ref(0)
const A1 = ref(1)
const A2 = ref()

watchEffect(() => {
  // tracks A0 and A1
  A2.value = A0.value + A1.value
})

// triggers the effect
A0.value = 2
```

একটি রেফ পরিবর্তন করার জন্য একটি প্রতিক্রিয়াশীল প্রভাব ব্যবহার করা সবচেয়ে আকর্ষণীয় ব্যবহারের ক্ষেত্রে নয় - আসলে, একটি গণনা করা কম্পিউটেড প্রপার্টি ব্যবহার করা এটিকে আরও ডিক্লেয়ার করে তোলে:

```js
import { ref, computed } from 'vue'

const A0 = ref(0)
const A1 = ref(1)
const A2 = computed(() => A0.value + A1.value)

A0.value = 2
```

অভ্যন্তরীণভাবে, `computed` একটি প্রতিক্রিয়াশীল প্রভাব ব্যবহার করে এর অবৈধকরণ এবং পুনরায় গণনা পরিচালনা করে।

তাই একটি সাধারণ এবং দরকারী প্রতিক্রিয়াশীল প্রভাব একটি উদাহরণ কি? আচ্ছা, DOM আপডেট করা হচ্ছে! আমরা এই মত সহজ "প্রতিক্রিয়াশীল রেন্ডারিং" বাস্তবায়ন করতে পারি:

```js
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  document.body.innerHTML = `Count is: ${count.value}`
})

// updates the DOM
count.value++
```

আসলে, এটি একটি Vue কম্পোনেন্ট কীভাবে স্টেট এবং DOM-কে সিঙ্কে রাখে তার খুব কাছাকাছি - প্রতিটি কম্পোনেন্ট ইনস্ট্যান্স DOM রেন্ডার এবং আপডেট করার জন্য একটি প্রতিক্রিয়াশীল প্রভাব তৈরি করে। অবশ্যই, Vue কম্পোনেন্টগুলি `innerHTML` এর চেয়ে DOM আপডেট করার জন্য অনেক বেশি কার্যকর উপায় ব্যবহার করে। এটি [Rendering Mechanism](./rendering-mechanism) এ আলোচনা করা হয়েছে।

<div class="options-api">

`ref()`, `computed()` এবং `watchEffect()` এপিআইগুলি কম্পোজিশন এপিআই-এর অংশ। আপনি যদি এখন পর্যন্ত শুধুমাত্র Vue-এর সাথে Options API ব্যবহার করে থাকেন, তাহলে আপনি লক্ষ্য করবেন যে Vue-এর প্রতিক্রিয়াশীলতা সিস্টেম হুডের অধীনে কীভাবে কাজ করে তার কাছাকাছি Composition API। প্রকৃতপক্ষে, Vue 3-এ Options API কম্পোজিশন API-এর উপরে প্রয়োগ করা হয়েছে। কম্পোনেন্ট ইন্সট্যান্সে সমস্ত কম্পিউটেড প্রপার্টি অ্যাক্সেস (`this`) প্রতিক্রিয়াশীলতা ট্র্যাকিংয়ের জন্য গেটার/সেটারকে ট্রিগার করে এবং `watch` এবং `computed` এর মতো বিকল্পগুলি তাদের কম্পোজিশন এপিআই সমতুল্যকে অভ্যন্তরীণভাবে আহ্বান করে।

</div>

## Runtime vs. Compile-time Reactivity {#runtime-vs-compile-time-reactivity}

Vue-এর রিঅ্যাকটিভিটি সিস্টেম প্রাথমিকভাবে রানটাইম-ভিত্তিক: কোডটি সরাসরি ব্রাউজারে চলার সময় ট্র্যাকিং এবং ট্রিগারিং করা হয়। রানটাইম রিঅ্যাকটিভিটির সুবিধা হল যে এটি একটি বিল্ড স্টেপ ছাড়াই কাজ করতে পারে এবং কম এজ কেস আছে। অন্যদিকে, এটি জাভাস্ক্রিপ্টের সিনট্যাক্স সীমাবদ্ধতা দ্বারা এটিকে সীমাবদ্ধ করে তোলে, যার ফলে Vue refs-এর মতো মান ধারকগুলির প্রয়োজন হয়।

কিছু ফ্রেমওয়ার্ক, যেমন [Svelte](https://svelte.dev/), সংকলনের সময় প্রতিক্রিয়াশীলতা প্রয়োগ করে এই ধরনের সীমাবদ্ধতা কাটিয়ে উঠতে বেছে নেয়। এটি প্রতিক্রিয়াশীলতা অনুকরণ করার জন্য কোডটিকে বিশ্লেষণ করে এবং রূপান্তর করে। সংকলন ধাপটি ফ্রেমওয়ার্কটিকে জাভাস্ক্রিপ্টের শব্দার্থবিদ্যাকে পরিবর্তন করার অনুমতি দেয় - উদাহরণস্বরূপ, অন্তর্নিহিতভাবে কোড ইনজেক্ট করা যা নির্ভরতা বিশ্লেষণ করে এবং স্থানীয়ভাবে সংজ্ঞায়িত ভেরিয়েবলের অ্যাক্সেসের চারপাশে প্রভাব ট্রিগার করে। নেতিবাচক দিক হল যে এই ধরনের রূপান্তরগুলির জন্য একটি বিল্ড স্টেপ প্রয়োজন, এবং জাভাস্ক্রিপ্ট শব্দার্থবিদ্যা পরিবর্তন করা মূলত একটি ভাষা তৈরি করছে যা জাভাস্ক্রিপ্টের মতো দেখতে কিন্তু অন্য কিছুতে কম্পাইল করে।

Vue টিম [Reactivity Transform](/guide/extras/reactivity-transform) নামক একটি পরীক্ষামূলক বৈশিষ্ট্যের মাধ্যমে এই দিকটি অন্বেষণ করেছে, কিন্তু শেষ পর্যন্ত আমরা সিদ্ধান্ত নিয়েছি যে এটি প্রকল্পের জন্য উপযুক্ত হবে না [যুক্তির কারণ এখানে](https://github.com/vuejs/rfcs/discussions/369#discussioncomment-5059028)।

## Reactivity Debugging {#reactivity-debugging}

এটি দুর্দান্ত যে Vue-এর প্রতিক্রিয়াশীলতা সিস্টেম স্বয়ংক্রিয়ভাবে নির্ভরতাগুলিকে ট্র্যাক করে, তবে কিছু ক্ষেত্রে আমরা ঠিক কী ট্র্যাক করা হচ্ছে বা কোন কম্পোনেন্টটিকে পুনরায় রেন্ডার করার কারণ হতে পারে তা বের করতে চাই।

### Component Debugging Hooks {#component-debugging-hooks}

কোন কম্পোনেন্টের রেন্ডারের সময় কোন নির্ভরতা ব্যবহার করা হয় এবং কোন নির্ভরতা <span class="options-api">`renderTracked`</span><span class="composition-api">`onRenderTracked` ব্যবহার করে আপডেট ট্রিগার করছে তা আমরা ডিবাগ করতে পারি </span> এবং <span class="options-api">`renderTriggered`</span><span class="composition-api">`onRenderTriggered`</span> লাইফসাইকেল হুক। উভয় হুকই একটি ডিবাগার ইভেন্ট পাবে যা প্রশ্নে নির্ভরশীলতার তথ্য ধারণ করে। ইন্টারেক্টিভভাবে নির্ভরতা পরিদর্শন করতে কলব্যাকগুলিতে একটি `ডিবাগার` বিবৃতি রাখার পরামর্শ দেওয়া হয়:

<div class="composition-api">

```vue
<script setup>
import { onRenderTracked, onRenderTriggered } from 'vue'

onRenderTracked((event) => {
  debugger
})

onRenderTriggered((event) => {
  debugger
})
</script>
```

</div>
<div class="options-api">

```js
export default {
  renderTracked(event) {
    debugger
  },
  renderTriggered(event) {
    debugger
  }
}
```

</div>

:::tip
 কম্পোনেন্ট ডিবাগ হুক শুধুমাত্র উন্নয়ন মোডে কাজ করে।
:::

ডিবাগ ইভেন্ট অবজেক্টের নিম্নলিখিত প্রকার রয়েছে:

<span id="debugger-event"></span>

```ts
type DebuggerEvent = {
  effect: ReactiveEffect
  target: object
  type:
    | TrackOpTypes /* 'get' | 'has' | 'iterate' */
    | TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
  key: any
  newValue?: any
  oldValue?: any
  oldTarget?: Map<any, any> | Set<any>
}
```

### Computed Debugging {#computed-debugging}

<!-- TODO options API equivalent -->

আমরা `onTrack` এবং `onTrigger` কলব্যাকের সাথে একটি দ্বিতীয় বিকল্প অবজেক্টকে `computed()` পাস করে গণনা করা বৈশিষ্ট্যগুলি ডিবাগ করতে পারি:

- 'onTrack' বলা হবে যখন একটি প্রতিক্রিয়াশীল কম্পিউটেড প্রপার্টি বা রেফ নির্ভরতা হিসাবে ট্র্যাক করা হয়।
- 'onTrigger' কল করা হবে যখন প্রহরী কলব্যাক নির্ভরতার মিউটেশন দ্বারা ট্রিগার হয়।

উভয় কলব্যাকই কম্পোনেন্ট ডিবাগ হুক হিসাবে [same format](#debugger-event) ডিবাগার ইভেন্টগুলি গ্রহণ করবে:

```js
const plusOne = computed(() => count.value + 1, {
  onTrack(e) {
    // triggered when count.value is tracked as a dependency
    debugger
  },
  onTrigger(e) {
    // triggered when count.value is mutated
    debugger
  }
})

// access plusOne, should trigger onTrack
console.log(plusOne.value)

// mutate count.value, should trigger onTrigger
count.value++
```

:::tip
`onTrack` এবং `onTrigger` গণনা করা বিকল্পগুলি শুধুমাত্র ডেভেলপমেন্ট মোডে কাজ করে।
:::

### Watcher Debugging {#watcher-debugging}

<!-- TODO options API equivalent -->

`computed()` এর মতো, পর্যবেক্ষকরা `onTrack` এবং `onTrigger` বিকল্পগুলিকেও সমর্থন করে:

```js
watch(source, callback, {
  onTrack(e) {
    debugger
  },
  onTrigger(e) {
    debugger
  }
})

watchEffect(callback, {
  onTrack(e) {
    debugger
  },
  onTrigger(e) {
    debugger
  }
})
```

:::tip
`onTrack` এবং `onTrigger` প্রহরী বিকল্পগুলি শুধুমাত্র ডেভেলপমেন্ট মোডে কাজ করে।
:::

## Integration with External State Systems {#integration-with-external-state-systems}

Vue এর প্রতিক্রিয়াশীলতা সিস্টেম গভীরভাবে প্লেইন জাভাস্ক্রিপ্ট অবজেক্টকে প্রতিক্রিয়াশীল প্রক্সিতে রূপান্তর করে কাজ করে। বাহ্যিক স্টেট ম্যানেজমেন্ট সিস্টেমের সাথে ব্যবহার করার সময় গভীর রূপান্তরটি অপ্রয়োজনীয় বা কখনও কখনও অবাঞ্ছিত হতে পারে (যেমন যদি একটি বাহ্যিক সমাধানও প্রক্সি ব্যবহার করে)।

বাহ্যিক স্টেট ম্যানেজমেন্ট সলিউশনের সাথে Vue-এর রিঅ্যাকটিভিটি সিস্টেমকে ব্যবহার করার সাধারণ ধারণা হল বাহ্যিক অবস্থাকে একটি [`shallowRef`](/api/reactivity-advanced#shallowref) এ রাখা। একটি অগভীর রেফ শুধুমাত্র তখনই প্রতিক্রিয়াশীল হয় যখন এর `.value` বৈশিষ্ট্য অ্যাক্সেস করা হয় - ভিতরের মানটি অক্ষত থাকে। যখন বাহ্যিক অবস্থা পরিবর্তিত হয়, আপডেটগুলি ট্রিগার করতে রেফ মান প্রতিস্থাপন করুন।

### Immutable Data {#immutable-data}

আপনি যদি একটি পূর্বাবস্থায় ফেরান/পুনরায় করুন বৈশিষ্ট্যটি প্রয়োগ করেন, আপনি সম্ভবত প্রতিটি ব্যবহারকারীর সম্পাদনায় অ্যাপ্লিকেশনটির অবস্থার একটি স্ন্যাপশট নিতে চান। যাইহোক, স্টেট ট্রি বড় হলে Vue-এর মিউটেবল রিঅ্যাকটিভিটি সিস্টেম এর জন্য সবচেয়ে উপযুক্ত নয়, কারণ প্রতিটি আপডেটে পুরো স্টেট অবজেক্টকে সিরিয়াল করা CPU এবং মেমরি ব্যয় উভয়ের ক্ষেত্রেই ব্যয়বহুল হতে পারে।

[অপরিবর্তনীয় ডেটা স্ট্রাকচার](https://en.wikipedia.org/wiki/Persistent_data_structure) স্টেট অবজেক্টকে কখনো মিউট না করে এর সমাধান করুন - পরিবর্তে, এটি নতুন অবজেক্ট তৈরি করে যা পুরানোগুলির সাথে একই, অপরিবর্তিত অংশগুলি ভাগ করে। জাভাস্ক্রিপ্টে অপরিবর্তনীয় ডেটা ব্যবহার করার বিভিন্ন উপায় রয়েছে, তবে আমরা Vue-এর সাথে [Immer](https://immerjs.github.io/immer/) ব্যবহার করার পরামর্শ দিই কারণ এটি আপনাকে আরও অর্গোনমিক, পরিবর্তনযোগ্য সিনট্যাক্স রেখে অপরিবর্তনীয় ডেটা ব্যবহার করতে দেয় .

আমরা একটি সাধারণ কম্পোজেবলের মাধ্যমে ইমারকে Vue এর সাথে ব্যবহার করতে পারি:

```js
import { produce } from 'immer'
import { shallowRef } from 'vue'

export function useImmer(baseState) {
  const state = shallowRef(baseState)
  const update = (updater) => {
    state.value = produce(state.value, updater)
  }

  return [state, update]
}
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9VMFu2zAM/RXNl6ZAYnfoTlnSdRt66DBsQ7vtEuXg2YyjRpYEUU5TBPn3UZLtuE1RH2KLfCIfycfsk8/GpNsGkmkyw8IK4xiCa8wVV6I22jq2Zw3CbV2DZQe2srpmZ2km/PmMK8a4KrRCxxbCQY1j1pgyd3DrD0s27++OFh689z/0OOEkTBlPvkNuFfvbAE/Gra/UilzOko0Mh2A+ufcHwd9ij8KtWUjwMsAqlxgjcLU854qrVaMKJ7RiTleVDBRHQpWwO4/xB8xHoRg2v+oyh/MioJepT0ClvTsxhnSUi1LOsthN6iMdCGgkBacTY7NGhjd9ScG2k5W2c56M9rG6ceBPdbOWm1AxO0/a+uiZFjJHpFv7Fj10XhdSFBtyntTJkzaxf/ZtQnYguoFNJkUkmAWGs2xAm47onqT/jPWHxjjYuUkJhba57+yUSaFg4tZWN9X6Y9eIcC8ZJ1FQkzo36QNqRZILQXjroAqnXb+9LQzVD3vtnMFpljXKbKq00HWU3/X7i/QivcxKgS5aUglVXjxNAGvK8KnWZSNJWa0KDoGChzmk3L28jSVcQX1o1d1puwfgOpdSP97BqsfQxhCCK9gFTC+tXu7/coR7R71rxRWXBL2FpHOMOAAeYVGJhBvFL3s+kGKIkW5zSfKfd+RHA2u3gzZEpML9y9JS06YtAq5DLFmOMWXsjkM6rET1YjzUcSMk2J/G1/h8TKGOb8HmV7bdQbqzhmLziv0Bd3Govywg2O1x8Umvua3ARffN/Q/S1sDZDfMN5x2glo3nGGFfGlUS7QEusL0NcxWq+o03OwcKu6Ke/+fwhIb89Y3Sj3Qv0w+9xg7/AWfvyMs=)

### State Machines {#state-machines}

[State Machine](https://en.wikipedia.org/wiki/Finite-state_machine) হল একটি মডেল যেখানে একটি অ্যাপ্লিকেশন থাকতে পারে এবং এটি একটি রাজ্য থেকে অন্য রাজ্যে স্থানান্তর করতে পারে এমন সমস্ত সম্ভাব্য স্থিতির বর্ণনা দেয়৷ যদিও এটি সাধারণ কম্পোনেন্টগুলির জন্য ওভারকিল হতে পারে, এটি জটিল অবস্থার প্রবাহকে আরও শক্তিশালী এবং পরিচালনাযোগ্য করতে সাহায্য করতে পারে।

জাভাস্ক্রিপ্টের সবচেয়ে জনপ্রিয় স্টেট মেশিন বাস্তবায়নের একটি হল [XState](https://xstate.js.org/)। এখানে একটি সংমিশ্রণযোগ্য যা এটির সাথে সংহত করে:

```js
import { createMachine, interpret } from 'xstate'
import { shallowRef } from 'vue'

export function useMachine(options) {
  const machine = createMachine(options)
  const state = shallowRef(machine.initialState)
  const service = interpret(machine)
    .onTransition((newState) => (state.value = newState))
    .start()
  const send = (event) => service.send(event)

  return [state, send]
}
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp1U81unDAQfpWRL7DSFqqqUiXEJumhyqVVpDa3ugcKZtcJjC1syEqId8/YBu/uIRcEM9/P/DGz71pn0yhYwUpTD1JbMMKO+o6j7LUaLMwwGvGrqk8SBSzQDqqHJMv7EMleTMIRgGOt0Fj4a2xlxZ5EsPkHhytuOjucbApIrDoeO5HsfQCllVVHUYlVbeW0xr2OKcCzHCwkKQAK3fP56fHx5w/irSyqbfFMgA+h0cKBHZYey45jmYfeqWv6sKLXHbnTF0D5f7RWITzUnaxfD5y5ztIkSCY7zjwKYJ5DyVlf2fokTMrZ5sbZDu6Bs6e25QwK94b0svgKyjwYkEyZR2e2Z2H8n/pK04wV0oL8KEjWJwxncTicnb23C3F2slabIs9H1K/HrFZ9HrIPX7Mv37LPuTC5xEacSfa+V83YEW+bBfleFkuW8QbqQZDEuso9rcOKQQ/CxosIHnQLkWJOVdept9+ijSA6NEJwFGePaUekAdFwr65EaRcxu9BbOKq1JDqnmzIi9oL0RRDu4p1u/ayH9schrhlimGTtOLGnjeJRAJnC56FCQ3SFaYriLWjA4Q7SsPOp6kYnEXMbldKDTW/ssCFgKiaB1kusBWT+rkLYjQiAKhkHvP2j3IqWd5iMQ+M=)

### RxJS {#rxjs}

[RxJS](https://rxjs.dev/) হল অ্যাসিঙ্ক্রোনাস ইভেন্ট স্ট্রিমগুলির সাথে কাজ করার জন্য একটি লাইব্রেরি। [VueUse](https://vueuse.org/) লাইব্রেরি Vue-এর প্রতিক্রিয়াশীলতার সাথে RxJS স্ট্রিমগুলিকে সংযুক্ত করার জন্য [`@vueuse/rxjs`](https://vueuse.org/rxjs/readme.html) অ্যাড-অন প্রদান করে পদ্ধতি.

## Connection to Signals {#connection-to-signals}

বেশ কিছু অন্যান্য ফ্রেমওয়ার্ক "সিগন্যাল" শব্দের অধীনে Vue এর কম্পোজিশন এপিআই থেকে রেফের অনুরূপ প্রতিক্রিয়াশীল আদিম প্রবর্তন করেছে:

- [Solid Signals](https://www.solidjs.com/docs/latest/api#createsignal)
- [Angular Signals](https://angular.io/guide/signals)
- [Preact Signals](https://preactjs.com/guide/v10/signals/)
- [Qwik Signals](https://qwik.builder.io/docs/components/state/#usesignal)

মৌলিকভাবে, সংকেত Vue refs হিসাবে একই ধরনের প্রতিক্রিয়াশীল আদিম। এটি একটি মান ধারক যা অ্যাক্সেসের উপর নির্ভরতা ট্র্যাকিং প্রদান করে এবং মিউটেশনে পার্শ্ব-প্রতিক্রিয়া ট্রিগার করে। এই প্রতিক্রিয়াশীলতা-আদি-ভিত্তিক দৃষ্টান্তটি ফ্রন্টএন্ড বিশ্বে একটি বিশেষ নতুন ধারণা নয়: এর মতো বাস্তবায়নের সময়কার [নকআউট অবজারভেবলস](https://knockoutjs.com/documentation/observables.html) এবং [মেটিওর ট্র্যাকার](https://docs.meteor.com/api/tracker.html) এক দশকেরও বেশি আগে থেকে। Vue Options API এবং React স্টেট ম্যানেজমেন্ট লাইব্রেরি [MobX](https://mobx.js.org/) একই নীতির উপর ভিত্তি করে, কিন্তু অবজেক্টের বৈশিষ্ট্যগুলির পিছনে আদিম বিষয়গুলি লুকিয়ে রাখে।

যদিও সিগন্যাল হিসেবে যোগ্যতা অর্জনের জন্য কোনো কিছুর জন্য প্রয়োজনীয় বৈশিষ্ট্য নয়, আজ ধারণাটি প্রায়ই রেন্ডারিং মডেলের পাশাপাশি আলোচনা করা হয় যেখানে সূক্ষ্ম-দানাদার সদস্যতার মাধ্যমে আপডেট করা হয়। ভার্চুয়াল DOM ব্যবহারের কারণে, Vue বর্তমানে [অনুরূপ অপটিমাইজেশন অর্জনের জন্য কম্পাইলারদের উপর নির্ভর করে](/guide/extras/rendering-mechanism#compiler-informed-virtual-dom)। যাইহোক, আমরা [Vapor Mode](https://github.com/vuejs/core-vapor) নামে একটি নতুন সলিড-অনুপ্রাণিত সংকলন কৌশলও অন্বেষণ করছি, যা ভার্চুয়াল DOM-এর উপর নির্ভর করে না এবং Vue-এর বিল্ট-এর আরও সুবিধা নেয়। প্রতিক্রিয়া সিস্টেমে।

### API Design Trade-Offs {#api-design-trade-offs}

Preact এবং Qwik-এর সিগন্যালগুলির নকশা Vue এর [shallowRef](/api/reactivity-advanced#shallowref) এর সাথে খুব মিল: তিনটিই `.value` বৈশিষ্ট্যের মাধ্যমে একটি পরিবর্তনযোগ্য ইন্টারফেস প্রদান করে। আমরা সলিড এবং কৌণিক সংকেতের উপর আলোচনায় ফোকাস করব।

#### Solid Signals {#solid-signals}

সলিডের `createSignal()` API ডিজাইন পঠন/লেখা বিচ্ছিন্নতার উপর জোর দেয়। সিগন্যালগুলি শুধুমাত্র পঠনযোগ্য গেটার এবং একটি পৃথক সেটার হিসাবে প্রকাশ করা হয়:

```js
const [count, setCount] = createSignal(0)

count() // access the value
setCount(1) // update the value
```

লক্ষ্য করুন কীভাবে সেটার ছাড়াই `count` সংকেতটি পাস করা যেতে পারে। এটি নিশ্চিত করে যে রাষ্ট্রকে কখনই পরিবর্তিত করা যাবে না যদি না সেটারকেও স্পষ্টভাবে প্রকাশ করা হয়। এই নিরাপত্তা গ্যারান্টিটি আরও ভারবোস সিনট্যাক্সকে ন্যায্যতা দেয় কিনা তা প্রকল্পের প্রয়োজনীয়তা এবং ব্যক্তিগত স্বাদের সাপেক্ষে হতে পারে - তবে আপনি যদি এই API স্টাইলটি পছন্দ করেন তবে আপনি সহজেই এটিকে Vue-তে প্রতিলিপি করতে পারেন:

```js
import { shallowRef, triggerRef } from 'vue'

export function createSignal(value, options) {
  const r = shallowRef(value)
  const get = () => r.value
  const set = (v) => {
    r.value = typeof v === 'function' ? v(r.value) : v
    if (options?.equals === false) triggerRef(r)
  }
  return [get, set]
}
```

[চেষ্টা করুন](https://play.vuejs.org/#eNpdUk1TgzAQ/Ss7uQAjgr12oNXxH+ix9IAYaDQkMV/qMPx3N6G0Uy9Msu/tvn2PTORJqcI7SrakMp1myoKh1qldI9iopLYwQadpa+krG0TLYYZeyxGSojSSs/d7E8vFh0ka0YhOCmPh0EknbB4mPYfTEeqbIelD1oiqXPRQCS+WjoojAW8A1Wmzm1A39KYZzHNVYiUib85aKeCx46z7rBuySqQe6h14uINN1pDIBWACVUcqbGwtl17EqvIiR3LyzwcmcXFuTi3n8vuF9jlYzYaBajxfMsDcomv6E/m9E51luN2NV99yR3OQKkAmgykss+SkMZerxMLEZFZ4oBYJGAA600VEryAaD6CPaJwJKwnr9ldR2WMedV1Dsi6WwB58emZlsAV/zqmH9LzfvqBfruUmNvZ4QN7VearjenP4aHwmWsABt4x/+tiImcx/z27Jqw==)

#### Angular Signals {#angular-signals}

কৌণিক নোংরা-পরীক্ষাকে অগ্রাহ্য করে এবং একটি রিঅ্যাকটিভিটি আদিম এর নিজস্ব বাস্তবায়ন প্রবর্তন করে কিছু মৌলিক পরিবর্তনের মধ্য দিয়ে যাচ্ছে। কৌণিক সংকেত API এই মত দেখায়:

```js
const count = signal(0)

count() // access the value
count.set(1) // set new value
count.update((v) => v + 1) // update based on previous value
```

আবার, আমরা সহজেই Vue-তে API কে প্রতিলিপি করতে পারি:

```js
import { shallowRef } from 'vue'

export function signal(initialValue) {
  const r = shallowRef(initialValue)
  const s = () => r.value
  s.set = (value) => {
    r.value = value
  }
  s.update = (updater) => {
    r.value = updater(r.value)
  }
  return s
}
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9Ul1v0zAU/SuWX9ZCSRh7m9IKGHuAB0AD8WQJZclt6s2xLX+ESlH+O9d2krbr1Df7nnPu17k9/aR11nmgt7SwleHaEQvO6w2TvNXKONITyxtZihWpVKu9g5oMZGtUS66yvJSNF6V5lyjZk71ikslKSeuQ7qUj61G+eL+cgFr5RwGITAkXiyVZb5IAn2/IB+QWeeoHO8GPg1aL0gH+CCl215u7mJ3bW9L3s3IYihyxifMlFRpJqewL1qN3TknysRK8el4zGjNlXtdYa9GFrjryllwvGY18QrisDLQgXZTnSX8pF64zzD7pDWDghbbI5/Hoip7tFL05eLErhVD/HmB75Edpyd8zc9DUaAbso3TrZeU4tjfawSV3vBR/SuFhSfrQUXLHBMvmKqe8A8siK7lmsi5gAbJhWARiIGD9hM7BIfHSgjGaHljzlDyGF2MEPQs6g5dpcAIm8Xs+2XxODTgUn0xVYdJ5RxPhKOd4gdMsA/rgLEq3vEEHlEQPYrbgaqu5APNDh6KWUTyuZC2jcWvfYswZD6spXu2gen4l/mT3Icboz3AWpgNGZ8yVBttM8P2v77DH9wy2qvYC2RfAB7BK+NBjon32ssa2j3ix26/xsrhsftv7vQNpp6FCo4E5RD6jeE93F0Y/tHuT3URd2OLwHyXleRY=)

Vue refs-এর তুলনায়, সলিড এবং অ্যাঙ্গুলারের গেটার-ভিত্তিক API স্টাইল Vue কম্পোনেন্টগুলিতে ব্যবহার করার সময় কিছু আকর্ষণীয় ট্রেড-অফ প্রদান করে:

- `()` `.value`-এর চেয়ে সামান্য কম ভার্বস, কিন্তু মান আপডেট করা আরও ভার্বস।
- কোন রেফ-আনর্যাপিং নেই: অ্যাক্সেসের মান সবসময় `()` প্রয়োজন। এটি মান অ্যাক্সেসকে সর্বত্র সামঞ্জস্যপূর্ণ করে তোলে। এর মানে হল আপনি কম্পোনেন্ট প্রপস হিসাবে raw সংকেত পাস করতে পারেন।

এই API স্টাইল আপনার জন্য উপযুক্ত কিনা তা কিছুটা বিষয়ভিত্তিক। এখানে আমাদের লক্ষ্য হল এই বিভিন্ন API ডিজাইনের মধ্যে অন্তর্নিহিত সাদৃশ্য এবং ট্রেড-অফ প্রদর্শন করা। আমরা এটাও দেখাতে চাই যে Vue নমনীয়: আপনি আসলে বিদ্যমান API-এ লক নন। এটি প্রয়োজন হলে, আপনি আরও নির্দিষ্ট প্রয়োজন অনুসারে আপনার নিজস্ব প্রতিক্রিয়াশীল আদিম API তৈরি করতে পারেন।
