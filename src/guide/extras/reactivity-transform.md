# Reactivity Transform {#reactivity-transform}

:::danger অপ্রচলিত পরীক্ষামূলক বৈশিষ্ট্য
রিঅ্যাকটিভিটি ট্রান্সফর্ম একটি পরীক্ষামূলক বৈশিষ্ট্য ছিল এবং এটিকে অবমূল্যায়ন করা হয়েছে। অনুগ্রহ করে [এখানে যুক্তি](https://github.com/vuejs/rfcs/discussions/369#discussioncomment-5059028) সম্পর্কে পড়ুন।

এটি অবশেষে ভবিষ্যত ছোটো রিলিজে Vue কোর থেকে সরানো হবে।

- এটি থেকে দূরে সরে যেতে, এই [কমান্ড লাইন টুল](https://github.com/edison1105/drop-reactivity-transform) দেখুন যা প্রক্রিয়াটিকে স্বয়ংক্রিয় করতে পারে।
- আপনি যদি এখনও এটি ব্যবহার করতে চান তবে এটি এখন [Vue Macros](https://vue-macros.sxzz.moe/features/reactivity-transform.html) প্লাগইনের মাধ্যমে উপলব্ধ৷
:::

:::tip রচনা-এপিআই-নির্দিষ্ট
রিঅ্যাকটিভিটি ট্রান্সফর্ম একটি কম্পোজিশন-এপিআই-নির্দিষ্ট বৈশিষ্ট্য এবং এর জন্য একটি বিল্ড স্টেপ প্রয়োজন।
:::

## Refs vs. Reactive Variables {#refs-vs-reactive-variables}

কম্পোজিশন এপিআই প্রবর্তনের পর থেকে, প্রাথমিক অমীমাংসিত প্রশ্নগুলির মধ্যে একটি হল refs বনাম প্রতিক্রিয়াশীল অবজেক্টর ব্যবহার। প্রতিক্রিয়াশীল অবজেক্টগুলিকে ধ্বংস করার সময় প্রতিক্রিয়াশীলতা হারানো সহজ, যখন refs ব্যবহার করার সময় সর্বত্র `.value` ব্যবহার করা কষ্টকর হতে পারে। এছাড়াও, টাইপ সিস্টেম ব্যবহার না করলে `.value` মিস করা সহজ।

[ভিউ রিঅ্যাকটিভিটি ট্রান্সফর্ম](https://github.com/vuejs/core/tree/main/packages/reactivity-transform) হল একটি কম্পাইল-টাইম ট্রান্সফর্ম যা আমাদের এইরকম কোড লিখতে দেয়:

```vue
<script setup>
let count = $ref(0)

console.log(count)

function increment() {
  count++
}
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

এখানে `$ref()` পদ্ধতিটি একটি **কম্পাইল-টাইম ম্যাক্রো**: এটি একটি বাস্তব পদ্ধতি নয় যা রানটাইমে কল করা হবে। পরিবর্তে, Vue কম্পাইলার এটিকে একটি ইঙ্গিত হিসাবে ব্যবহার করে ফলস্বরূপ `count` ভেরিয়েবলকে একটি **প্রতিক্রিয়াশীল ভেরিয়েবল হিসেবে ব্যবহার করে।**

প্রতিক্রিয়াশীল ভেরিয়েবলগুলিকে অ্যাক্সেস করা যেতে পারে এবং সাধারণ ভেরিয়েবলের মতোই পুনরায় বরাদ্দ করা যেতে পারে, তবে এই ক্রিয়াকলাপগুলিকে `.value` দিয়ে refs-এ কম্পাইল করা হয়। উদাহরণস্বরূপ, উপরের কম্পোনেন্টটির `<script>` অংশটি এতে কম্পাইল করা হয়েছে:

```js{5,8}
import { ref } from 'vue'

let count = ref(0)

console.log(count.value)

function increment() {
  count.value++
}
```

প্রতিটি রিঅ্যাকটিভিটি এপিআই যা রেফ প্রদান করে একটি `$`-প্রিফিক্সড ম্যাক্রো সমতুল্য থাকবে। এই APIগুলির মধ্যে রয়েছে:

- [`ref`](/api/reactivity-core#ref) -> `$ref`
- [`computed`](/api/reactivity-core#computed) -> `$computed`
- [`shallowRef`](/api/reactivity-advanced#shallowref) -> `$shallowRef`
- [`customRef`](/api/reactivity-advanced#customref) -> `$customRef`
- [`toRef`](/api/reactivity-utilities#toref) -> `$toRef`

এই ম্যাক্রোগুলি বিশ্বব্যাপী উপলব্ধ এবং রিঅ্যাক্টিভিটি ট্রান্সফর্ম সক্ষম হলে আমদানি করার প্রয়োজন নেই, তবে আপনি যদি আরও স্পষ্ট হতে চান তবে আপনি ঐচ্ছিকভাবে `vue/macros` থেকে আমদানি করতে পারেন:

```js
import { $ref } from 'vue/macros'

let count = $ref(0)
```

## Destructuring with `$()` {#destructuring-with}

একটি কম্পোজিশন ফাংশনের জন্য refs-এর একটি অবজেক্ট ফেরত দেওয়া এবং এই refs পুনরুদ্ধার করতে destructuring ব্যবহার করা সাধারণ। এই উদ্দেশ্যে, রিঅ্যাকটিভিটি ট্রান্সফর্ম **`$()`** ম্যাক্রো প্রদান করে:

```js
import { useMouse } from '@vueuse/core'

const { x, y } = $(useMouse())

console.log(x, y)
```

Compiled output:

```js
import { toRef } from 'vue'
import { useMouse } from '@vueuse/core'

const __temp = useMouse(),
  x = toRef(__temp, 'x'),
  y = toRef(__temp, 'y')

console.log(x.value, y.value)
```

মনে রাখবেন যদি `x` ইতিমধ্যেই একটি রেফ হয়, `toRef(__temp, 'x')` এটিকে যেমন আছে তেমনই ফিরিয়ে দিবে এবং কোনো অতিরিক্ত রেফ তৈরি করা হবে না। যদি একটি ধ্বংসকৃত মান একটি রেফ (যেমন একটি ফাংশন) না হয় তবে এটি এখনও কাজ করবে - মানটি একটি রেফের মধ্যে মোড়ানো হবে যাতে বাকি কোডটি প্রত্যাশিত হিসাবে কাজ করে।

`$()` ডিস্ট্রাকচার রিএক্টিভ অবজেক্ট **এবং** রেফ সমন্বিত প্লেইন অবজেক্ট উভয়েই কাজ করে।

## Convert Existing Refs to Reactive Variables with `$()` {#convert-existing-refs-to-reactive-variables-with}

কিছু ক্ষেত্রে আমাদের মোড়ানো ফাংশন থাকতে পারে যা রেফগুলিও দেয়। যাইহোক, Vue কম্পাইলার সময়ের আগে জানতে সক্ষম হবে না যে একটি ফাংশন একটি রেফ ফিরিয়ে দিতে যাচ্ছে। এই ধরনের ক্ষেত্রে, `$()` ম্যাক্রো যেকোনো বিদ্যমান রেফকে প্রতিক্রিয়াশীল ভেরিয়েবলে রূপান্তর করতে ব্যবহার করা যেতে পারে:

```js
function myCreateRef() {
  return ref(0)
}

let count = $(myCreateRef())
```

## Reactive Props Destructure {#reactive-props-destructure}

`<script setup>`-এ বর্তমান `defineProps()` ব্যবহারের সাথে দুটি ব্যথার পয়েন্ট আছে:

1. `.value` এর মতো, প্রতিক্রিয়াশীলতা ধরে রাখতে আপনাকে সর্বদা `props.x` হিসেবে প্রপস অ্যাক্সেস করতে হবে। এর মানে হল আপনি `defineProps`কে ধ্বংস করতে পারবেন না কারণ এর ফলে ধ্বংস হওয়া ভেরিয়েবলগুলো প্রতিক্রিয়াশীল নয় এবং আপডেট হবে না।

2. [Type-only props declaration](/api/sfc-script-setup#type-only-props-emit-declarations) ব্যবহার করার সময়, প্রপসের জন্য ডিফল্ট মান ঘোষণা করার কোন সহজ উপায় নেই। আমরা এই সঠিক উদ্দেশ্যে `withDefaults()` API প্রবর্তন করেছি, কিন্তু এটি এখনও ব্যবহার করা কঠিন।

আমরা একটি কম্পাইল-টাইম ট্রান্সফর্ম প্রয়োগ করে এই সমস্যাগুলিকে সমাধান করতে পারি যখন `defineProps` destructuring এর সাথে ব্যবহার করা হয়, যা আমরা আগে `$()` এর সাথে দেখেছি:

```html
<script setup lang="ts">
  interface Props {
    msg: string
    count?: number
    foo?: string
  }

  const {
    msg,
    // default value just works
    count = 1,
    // local aliasing also just works
    // here we are aliasing `props.foo` to `bar`
    foo: bar
  } = defineProps<Props>()

  watchEffect(() => {
    // will log whenever the props change
    console.log(msg, count, bar)
  })
</script>
```

উপরেরটি নিম্নলিখিত রানটাইম ঘোষণার সমতুল্য কম্পাইল করা হবে:

```js
export default {
  props: {
    msg: { type: String, required: true },
    count: { type: Number, default: 1 },
    foo: String
  },
  setup(props) {
    watchEffect(() => {
      console.log(props.msg, props.count, props.foo)
    })
  }
}
```

## Retaining Reactivity Across Function Boundaries {#retaining-reactivity-across-function-boundaries}

প্রতিক্রিয়াশীল ভেরিয়েবলগুলি আমাদেরকে সর্বত্র `.value` ব্যবহার করার থেকে মুক্তি দেয়, যখন আমরা প্রতিক্রিয়াশীল ভেরিয়েবলগুলিকে ফাংশনের সীমানা অতিক্রম করি তখন এটি "প্রতিক্রিয়াশীলতা ক্ষতি" এর একটি সমস্যা তৈরি করে। এটি দুটি ক্ষেত্রে ঘটতে পারে:

### Passing into function as argument {#passing-into-function-as-argument}

একটি ফাংশন দেওয়া যা একটি যুক্তি হিসাবে একটি রেফ আশা করে, যেমন:

```ts
function trackChange(x: Ref<number>) {
  watch(x, (x) => {
    console.log('x changed!')
  })
}

let count = $ref(0)
trackChange(count) // doesn't work!
```

উপরের কেসটি প্রত্যাশিত হিসাবে কাজ করবে না কারণ এটি এতে কম্পাইল করে:

```ts
let count = ref(0)
trackChange(count.value)
```

এখানে `count.value` একটি সংখ্যা হিসাবে পাস করা হয়েছে, যেখানে `trackChange` একটি প্রকৃত রেফারেন্স আশা করে। এটি পাস করার আগে `$$()` দিয়ে `count` মোড়ানোর মাধ্যমে এটি ঠিক করা যেতে পারে:

```diff
let count = $ref(0)
- trackChange(count)
+ trackChange($$(count))
```

উপরেরটি এতে কম্পাইল করে:

```js
import { ref } from 'vue'

let count = ref(0)
trackChange(count)
```

আমরা দেখতে পাচ্ছি, `$$()` হল একটি ম্যাক্রো যা একটি **এস্কেপ ইঙ্গিত** হিসেবে কাজ করে: `$$()` এর ভিতরের প্রতিক্রিয়াশীল ভেরিয়েবল `.value` যুক্ত হবে না।

### Returning inside function scope {#returning-inside-function-scope}

প্রতিক্রিয়াশীল ভেরিয়েবলগুলি সরাসরি ফিরে আসা অভিব্যক্তিতে ব্যবহার করা হলে প্রতিক্রিয়াশীলতাও হারিয়ে যেতে পারে:

```ts
function useMouse() {
  let x = $ref(0)
  let y = $ref(0)

  // listen to mousemove...

  // doesn't work!
  return {
    x,
    y
  }
}
```

উপরের রিটার্ন স্টেটমেন্ট এতে কম্পাইল করে:

```ts
return {
  x: x.value,
  y: y.value
}
```

রিঅ্যাকটিভিটি ধরে রাখার জন্য, আমাদের আসল রেফ ফেরত দেওয়া উচিত, রিটার্নের সময়ে বর্তমান মান নয়।

আবার, আমরা এটি ঠিক করতে `$$()` ব্যবহার করতে পারি। এই ক্ষেত্রে, `$$()` সরাসরি প্রত্যাবর্তিত অবজেক্টতে ব্যবহার করা যেতে পারে - `$$()` কলের ভিতরের প্রতিক্রিয়াশীল ভেরিয়েবলের যেকোনো রেফারেন্স তাদের অন্তর্নিহিত রেফারেন্সের রেফারেন্স ধরে রাখবে:

```ts
function useMouse() {
  let x = $ref(0)
  let y = $ref(0)

  // listen to mousemove...

  // fixed
  return $$({
    x,
    y
  })
}
```

### Using `$$()` on destructured props {#using-on-destructured-props}

`$$()` ধ্বংসকৃত প্রপসগুলিতে কাজ করে কারণ সেগুলিও প্রতিক্রিয়াশীল ভেরিয়েবল। কম্পাইলার দক্ষতার জন্য এটিকে `toRef` দিয়ে রূপান্তর করবে:

```ts
const { count } = defineProps<{ count: number }>()

passAsRef($$(count))
```

compiles to:

```js
setup(props) {
  const __props_count = toRef(props, 'count')
  passAsRef(__props_count)
}
```

## TypeScript Integration <sup class="vt-badge ts" /> {#typescript-integration}

Vue এই ম্যাক্রোগুলির জন্য টাইপিং প্রদান করে (বিশ্বব্যাপী উপলব্ধ) এবং সমস্ত প্রকার প্রত্যাশিত হিসাবে কাজ করবে। স্ট্যান্ডার্ড টাইপস্ক্রিপ্ট শব্দার্থবিদ্যার সাথে কোন অসঙ্গতি নেই, তাই সিনট্যাক্স সমস্ত বিদ্যমান টুলিংয়ের সাথে কাজ করবে।

এর মানে ম্যাক্রো যেকোন ফাইলে কাজ করতে পারে যেখানে বৈধ JS/TS অনুমোদিত - শুধু Vue SFC-এর ভিতরে নয়।

যেহেতু ম্যাক্রোগুলি বিশ্বব্যাপী উপলব্ধ, তাদের প্রকারগুলি স্পষ্টভাবে উল্লেখ করা প্রয়োজন (যেমন একটি `env.d.ts` ফাইলে):

```ts
/// <reference types="vue/macros-global" />
```

`vue/macros` থেকে স্পষ্টভাবে ম্যাক্রো আমদানি করার সময়, টাইপটি গ্লোবাল ঘোষণা না করেই কাজ করবে।

## Explicit Opt-in {#explicit-opt-in}

:::warning
নিম্নলিখিত শুধুমাত্র Vue সংস্করণ 3.3 এবং নীচের পর্যন্ত প্রযোজ্য। মূল সমর্থন 3.4 এবং তার উপরে সরানো হবে। আপনি যদি ট্রান্সফর্ম ব্যবহার চালিয়ে যেতে চান, তাহলে অনুগ্রহ করে পরিবর্তে [Vue Macros](https://vue-macros.sxzz.moe/features/reactivity-transform.html)-এ মাইগ্রেট করুন।
:::

### Vite {#vite}

- এর জন্য `@vitejs/plugin-vue@>=2.0.0` প্রয়োজন
- SFC এবং js(x)/ts(x) ফাইলগুলিতে প্রযোজ্য৷ ট্রান্সফর্ম প্রয়োগ করার আগে ফাইলগুলিতে একটি দ্রুত ব্যবহার পরীক্ষা করা হয় তাই ম্যাক্রো ব্যবহার না করা ফাইলগুলির জন্য কোনও পারফরম্যান্স খরচ হওয়া উচিত নয়।
- দ্রষ্টব্য `reactivityTransform` এখন `script.refSugar` হিসেবে নেস্ট করার পরিবর্তে একটি প্লাগইন রুট-লেভেল বিকল্প, কারণ এটি শুধুমাত্র SFC-কে প্রভাবিত করে না।

```js
// vite.config.js
export default {
  plugins: [
    vue({
      reactivityTransform: true
    })
  ]
}
```

### `vue-cli` {#vue-cli}

- Currently only affects SFCs
- Requires `vue-loader@>=17.0.0`

```js
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => {
        return {
          ...options,
          reactivityTransform: true
        }
      })
  }
}
```

### Plain `webpack` + `vue-loader` {#plain-webpack-vue-loader}

- Currently only affects SFCs
- Requires `vue-loader@>=17.0.0`

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: {
          reactivityTransform: true
        }
      }
    ]
  }
}
```
