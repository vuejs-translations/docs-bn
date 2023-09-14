# \<script setup> {#script-setup}

`<script setup>` হল সিঙ্গেল-ফাইল কম্পোনেন্টস (SFCs) এর মধ্যে কম্পোজিশনের API ব্যবহার করার জন্য একটি কম্পাইল-টাইম সিনট্যাকটিক সুগার। আপনি যদি SFCs এবং Composition API উভয়ই ব্যবহার করেন তবে এটি রিকোমেন্ডেড সিনট্যাক্স। এটি সাধারণ `<script>` সিনট্যাক্সের তুলনায় অনেক সুবিধা প্রদান করে:

- লেস বয়লারপ্লেট সহ আরও সংক্ষিপ্ত কোড
- পিউর টাইপস্ক্রিপ্ট ব্যবহার করে প্রপস এবং নির্গত ইভেন্ট ডিক্লেয়ার করার ক্ষমতা
- ভাল রানটাইম পারফরম্যান্স (টেমপ্লেটটি একই সুযোগে একটি রেন্ডার ফাংশনে কম্পাইল্ড হয়, একটি মধ্যবর্তী প্রক্সি ছাড়াই)
- আরও ভাল আইডিই টাইপ-ইনফারেন্স পারফরম্যান্স (কোড থেকে টাইপগুলি বের করতে ল্যাঙ্গুয়েজ সার্ভারের জন্য কম কাজ)

## Basic Syntax {#basic-syntax}

সিনট্যাক্সে opt-in করতে, `<script>` ব্লকে `setup` অ্যাট্রিবিউট যোগ করুন:

```vue
<script setup>
console.log('hello script setup')
</script>
```

ভিতরের কোডটি কম্পোনেন্টের `setup()` ফাংশনের কন্টেন্ট হিসেবে কম্পাইল করা হয়। এর মানে হল যে স্বাভাবিক `<script>` থেকে ভিন্ন, যেটি শুধুমাত্র একবার এক্সিকিউট হয় যখন কম্পোনেন্টটি প্রথম ইম্পোর্ট করা হয়, `<script setup>`-এর ভিতরের কোডটি **প্রতিবার কম্পোনেন্টের একটি ইন্সট্যান্স তৈরি করার সময় এক্সিকিউট করবে**।

### Top-level bindings are exposed to template {#top-level-bindings-are-exposed-to-template}

`<script setup>` ব্যবহার করার সময়, `<script setup>`-এর ভিতরে ডিক্লেয়ারড যেকোনো টপ-লেভেল বাইন্ডিং (ভেরিয়েবল, ফাংশন ডিক্লেয়ার এবং ইম্পোর্ট সহ) সরাসরি টেমপ্লেটে ব্যবহারযোগ্য:

```vue
<script setup>
// variable
const msg = 'Hello!'

// functions
function log() {
  console.log(msg)
}
</script>

<template>
  <button @click="log">{{ msg }}</button>
</template>
```

ইম্পোর্ট একই ধারায় উন্মুক্ত করা হয়। এর মানে হল আপনি টেমপ্লেট এক্সপ্রেশনে একটি ইম্পোর্ট করা সহায়ক ফাংশন সরাসরি ব্যবহার করতে পারেন এটিকে `methods` অপশনের মাধ্যমে প্রকাশ না করেই:

```vue
<script setup>
import { capitalize } from './helpers'
</script>

<template>
  <div>{{ capitalize('hello') }}</div>
</template>
```

## Reactivity {#reactivity}

[Reactivity APIs](./reactivity-core) ব্যবহার করে রিয়েক্টিভ স্টেট স্পষ্টভাবে তৈরি করা প্রয়োজন। একটি `setup()` ফাংশন থেকে রিটার্নড ভ্যালুগুলির অনুরূপ, টেমপ্লেটগুলিতে উল্লেখ করা হলে রেফগুলি স্বয়ংক্রিয়ভাবে unwrapped হয়:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

## Using Components {#using-components}

`<script setup>` এর স্কোপ ভ্যালুগুলি সরাসরি কাস্টম কম্পোনেন্টে ট্যাগ নেইম হিসাবে ব্যবহার করা যেতে পারে:

```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <MyComponent />
</template>
```

মনে করুন `MyComponent` একটি ভ্যারিয়েবল হিসাবে উল্লেখ করা হচ্ছে । আপনি যদি JSX ব্যবহার করে থাকেন, তাহলে মেন্টাল মডেল এখানে একই রকম। kebab-case সমতুল্য `<my-component>` টেমপ্লেটেও কাজ করে - তবে PascalCase কম্পোনেন্ট ট্যাগগুলি দৃঢ়ভাবে সামঞ্জস্যের জন্য রিকোমেন্ড করা হয়। এটি নেটিভ কাস্টম কম্পোনেন্ট থেকে পার্থক্য করতে সাহায্য করে।

### Dynamic Components {#dynamic-components}

যেহেতু কম্পোনেন্টগুলি স্ট্রিং কীগুলির অধীনে রেজিস্ট্রাড না হয়ে ভেরিয়েবল হিসাবে উল্লেখ করা হয়েছে, তাই `<script setup>`-এর ভিতরে ডায়নামিক কম্পোনেন্টগুলি ব্যবহার করার সময় আমাদের ডাইনামিক `:is` বাইন্ডিং ব্যবহার করা উচিত:

```vue
<script setup>
import Foo from './Foo.vue'
import Bar from './Bar.vue'
</script>

<template>
  <component :is="Foo" />
  <component :is="someCondition ? Foo : Bar" />
</template>
```

লক্ষ্য করুন কিভাবে কম্পোনেন্টগুলিকে একটি টারনারি এক্সপ্রেশনে ভেরিয়েবল হিসাবে ব্যবহার করা যেতে পারে।

### Recursive Components {#recursive-components}

একটি এসএফসি তার ফাইলের নাম দিয়ে অন্তর্নিহিতভাবে নিজেকে রেফার করতে পারে। যেমন `FooBar.vue` নামের একটি ফাইল তার টেমপ্লেটে নিজেকে `<FooBar/>` হিসেবে রেফার করতে পারে।

নোট করুন এটি ইম্পোর্টেড কম্পোনেন্টের তুলনায় কম অগ্রাধিকার রয়েছে। আপনার যদি একটি নেইমড ইম্পোর্ট থাকে যা কম্পোনেন্টটির অনুমিত নামের সাথে কনফ্লিক্ট করে, আপনি ইম্পোর্টের নাম দিতে পারেন:
```js
import { FooBar as FooBarChild } from './components'
```

### Namespaced Components {#namespaced-components}

আপনি অবজেক্টের প্রোপার্টির অধীনে নেস্ট করা কোম্পোনেন্টেগুলিকে উল্লেখ করতে `<Foo.Bar>` এর মতো ডট সহ কম্পোনেন্ট ট্যাগ ব্যবহার করতে পারেন। আপনি যখন একটি ফাইল থেকে একাধিক কম্পোনেন্ট ইম্পোর্ট করেন তখন এটি দরকারী:

```vue
<script setup>
import * as Form from './form-components'
</script>

<template>
  <Form.Input>
    <Form.Label>label</Form.Label>
  </Form.Input>
</template>
```

## Using Custom Directives {#using-custom-directives}

গ্লোবালি রেজিস্ট্রাড কাস্টম ডিরেক্টিভগুলো স্বাভাবিক হিসাবে কাজ করে। লোকাল কাস্টম কে `<script setup>`-এর সাথে স্পষ্টভাবে রেজিস্ট্রাড হওয়ার প্রয়োজন নেই, তবে তাদের অবশ্যই নেইমিং স্কিম `vNameOfDirective` অনুসরণ করতে হবে:

```vue
<script setup>
const vMyDirective = {
  beforeMount: (el) => {
    // do something with the element
  }
}
</script>
<template>
  <h1 v-my-directive>This is a Heading</h1>
</template>
```

আপনি যদি অন্য কোথাও থেকে একটি ডিরেক্টিভ ইম্পোর্ট করে থাকেন, তাহলে প্রয়োজনীয় নেইমিং স্কিমের সাথে মানানসই করার জন্য এটির নাম পরিবর্তন করা যেতে পারে:

```vue
<script setup>
import { myDirective as vMyDirective } from './MyDirective.js'
</script>
```

## defineProps() & defineEmits() {#defineprops-defineemits}

সম্পূর্ণ প্রকারের অনুমান সমর্থন সহ `props` এবং `emits` এর মতো অপশন ডিক্লেয়ার করতে, আমরা `defineProps` এবং `defineEmits` এপিআই ব্যবহার করতে পারি, যা `<script setup>`-এর মধ্যে অটোমেটিক্লি অ্যাভাইলএবল হবে:

```vue
<script setup>
const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
// setup code
</script>
```

- `defineProps` এবং `defineEmits` হল **কম্পাইলার ম্যাক্রো** শুধুমাত্র `<script setup>`-এর মধ্যে ব্যবহারযোগ্য। সেগুলি ইম্পোর্ট করার      প্রয়োজন নেই এবং `<script setup>` প্রক্রিয়া করা হলে কম্পাইল করা হয়।

- `defineProps` `props` অপশনের মতো একই ভ্যালু গ্রহণ করে, যখন `defineEmits` `emits` অপশনের মতো একই ভ্যালু গ্রহণ করে।

- `defineProps` এবং `defineEmits` পাস করা অপশনগুলির উপর ভিত্তি করে সঠিক টাইপের ইনফারেন্স প্রদান করে।

- `defineProps` এবং `defineEmits`-এ পাস করা অপশনগুলি সেটআপের বাইরে মডিউল স্কোপে হোইস্টেড করা হবে। অতএব, অপশনগুলি সেটআপ সুযোগে ডিক্লেয়াড লোকাল ভেরিয়েবলের উল্লেখ করতে পারে না। এটি করার ফলে একটি কম্পাইল এরর হবে। যাইহোক, it _can_ reference  ইম্পোর্ট করা বাইন্ডিং যেহেতু তারা মডিউল স্কোপ রয়েছে।

### Type-only props/emit declarations<sup class="vt-badge ts" /> {#type-only-props-emit-declarations}

'defineProps' বা 'defineEmits'-এ একটি আক্ষরিক টাইপ আর্গুমেন্ট পাস করে পিউর-টাইপ সিনট্যাক্স ব্যবহার করে প্রপস এবং ইমিট ডিক্লেয়ার করা যেতে পারে:

```ts
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+: alternative, more succinct syntax
const emit = defineEmits<{
  change: [id: number] // named tuple syntax
  update: [value: string]
}>()
```

- `defineProps` বা `defineEmits` শুধুমাত্র রানটাইম ডিক্লেয়ার বা টাইপ ডিক্লেয়ার ব্যবহার করতে পারে। একই সময়ে উভয় ব্যবহার করার ফলে একটি কম্পাইল এরর হবে।

- টাইপ ডিক্লেয়ারেশন ব্যবহার করার সময়, ডবল ডিক্লেয়ারেশনের প্রয়োজনীয়তা দূর করতে এবং সঠিক রানটাইম বেহ্যিভিয়ার নিশ্চিত করতে স্ট্যাটিক বিশ্লেষণ থেকে সমতুল্য রানটাইম ঘোষণা স্বয়ংক্রিয়ভাবে তৈরি হয়।

  - ডেভ মোডে, কম্পাইলার টাইপগুলি থেকে সংশ্লিষ্ট রানটাইম ভ্যালিডেশন ইনফার করার চেষ্টা করবে। উদাহরণস্বরূপ এখানে `foo: String` ইনফারড করা হয়েছে `foo: string` প্রকার থেকে। যদি টাইপটি একটি ইম্পোর্ট করা টাইপের রেফারেন্স হয়, তাহলে ইনফারড করা ফলাফল হবে `foo: null` (`any` টাইপের সমান) যেহেতু কম্পাইলারের কাছে এক্সটারনাল ফাইলের তথ্য নেই।

  - প্রোড মোডে, কম্পাইলার বান্ডেলের সাইজ কমাতে অ্যারে ফরম্যাট ডিক্লারেইশন তৈরি করবে (এখানে প্রপগুলি `['foo', 'বার']` এ কম্পাইল করা হবে)

- সংস্করণ 3.2 এবং নীচে, `defineProps()`-এর জন্য জেনেরিক টাইপ প্যারামিটার একটি লিটারাল টাইপ বা লোকাল ইন্টারফেসের একটি রেফারেন্সের মধ্যে সীমাবদ্ধ ছিল।

  এই সীমাবদ্ধতা 3.3 এ সমাধান করা হয়েছে। Vue-এর সর্বশেষ সংস্করণটি টাইপ প্যারামিটার পজিশনে ইম্পোর্ট করা রেফারেন্সিং এবং জটিল টাইপের একটি সীমিত সেট সমর্থন করে। যাইহোক, যেহেতু রানটাইম কনভারশেন ধরনটি এখনও AST-ভিত্তিক, কিছু কমপ্লেক্স টাইপের প্রকৃত টাইপ অ্যানালাইসিস প্রয়োজন, যেমন কন্ডিশনাল টাইপ, সমর্থিত নয়। আপনি একটি সিঙ্গেল প্রপের ধরণের জন্য শর্তসাপেক্ষ টাইপগুলি ব্যবহার করতে পারেন, তবে সম্পূর্ণ প্রপস অবজেক্ট নয়।

### Default props values when using type declaration {#default-props-values-when-using-type-declaration}

টাইপ-অনলি `defineProps` ডিক্লারেশন একটি এরর হল যে এটিতে প্রপসের জন্য ডিফল্ট ভ্যালু প্রদান করার কোনো উপায় নেই। এই সমস্যাটি সমাধান করার জন্য, একটি 'withDefaults' কম্পাইলার ম্যাক্রোও প্রদান করা হয়েছে:

```ts
export interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```

এটি সমতুল্য রানটাইম প্রপস `default` অপশনের কম্পাইল করা হবে। উপরন্তু, `withDefaults` হেল্পার ডিফল্ট ভ্যালুগুলির জন্য টাইপ চেক প্রদান করে এবং নিশ্চিত করে যে প্রত্যাবর্তিত `props` টাইপটিতে ডিফল্ট ভ্যালু ডিক্লারড বৈশিষ্ট্যগুলির জন্য অপশনাল ফ্ল্যাগগুলি রিমুভ করা হয়েছে।

## defineExpose() {#defineexpose}

`<script setup>` ব্যবহার করা কম্পোনেন্টগুলি **ডিফল্টরূপে বন্ধ থাকে** - যেমন কম্পোনেন্টের সর্বজনীন উদাহরণ, যা টেমপ্লেট রেফ বা `$parent` চেইনের মাধ্যমে পুনরুদ্ধার করা হয়, ডিক্লারড কোনো বাইন্ডিং **কে প্রকাশ করবে না** `<script setup>` এর ভিতরে।

একটি `<script setup>` কম্পোনেন্টে প্রোপার্টিগুলি স্পষ্টভাবে প্রকাশ করতে, `defineExpose` কম্পাইলার ম্যাক্রো ব্যবহার করুন:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

যখন একটি প্যারেন্ট টেমপ্লেট রেফের মাধ্যমে এই কম্পোনেন্টটির একটি ইন্সট্যান্স পান, তখন পুনরুদ্ধারকৃত ইন্সট্যান্স `{ a: number, b: number }` আকারের হবে (রেফগুলি স্বয়ংক্রিয়ভাবে স্বাভাবিক উদাহরণের মতোই unwrapped হয়)।

## defineOptions() {#defineoptions}

This macro can be used to declare component options directly inside `<script setup>` without having to use a separate `<script>` block:

```vue
<script setup>
defineOptions({
  inheritAttrs: false,
  customOptions: {
    /* ... */
  }
})
</script>
```

- Only supported in 3.3+.
- This is a macro. The options will be hoisted to module scope and cannot access local variables in `<script setup>` that are not literal constants.

## defineSlots()<sup class="vt-badge ts"/> {#defineslots}

This macro can be used to provide type hints to IDEs for slot name and props type checking.

`defineSlots()` only accepts a type parameter and no runtime arguments. The type parameter should be a type literal where the property key is the slot name, and the value type is the slot function. The first argument of the function is the props the slot expects to receive, and its type will be used for slot props in the template. The return type is currently ignored and can be `any`, but we may leverage it for slot content checking in the future.

It also returns the `slots` object, which is equivalent to the `slots` object exposed on the setup context or returned by `useSlots()`.

```vue
<script setup lang="ts">
const slots = defineSlots<{
  default(props: { msg: string }): any
}>()
</script>
```

- Only supported in 3.3+.

## `useSlots()` & `useAttrs()` {#useslots-useattrs}

Usage of `slots` and `attrs` inside `<script setup>` should be relatively rare, since you can access them directly as `$slots` and `$attrs` in the template. In the rare case where you do need them, use the `useSlots` and `useAttrs` helpers respectively:

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```

`useSlots` and `useAttrs` are actual runtime functions that return the equivalent of `setupContext.slots` and `setupContext.attrs`. They can be used in normal composition API functions as well.

## Usage alongside normal `<script>` {#usage-alongside-normal-script}

`<script setup>` can be used alongside normal `<script>`. A normal `<script>` may be needed in cases where we need to:

- Declare options that cannot be expressed in `<script setup>`, for example `inheritAttrs` or custom options enabled via plugins (Can be replaced by [`defineOptions`](/api/sfc-script-setup#defineoptions) in 3.3+).
- Declaring named exports.
- Run side effects or create objects that should only execute once.

```vue
<script>
// normal <script>, executed in module scope (only once)
runSideEffectOnce()

// declare additional options
export default {
  inheritAttrs: false,
  customOptions: {}
}
</script>

<script setup>
// executed in setup() scope (for each instance)
</script>
```

Support for combining `<script setup>` and `<script>` in the same component is limited to the scenarios described above. Specifically:

- Do **NOT** use a separate `<script>` section for options that can already be defined using `<script setup>`, such as `props` and `emits`.
- Variables created inside `<script setup>` are not added as properties to the component instance, making them inaccessible from the Options API. Mixing APIs in this way is strongly discouraged.

If you find yourself in one of the scenarios that is not supported then you should consider switching to an explicit [`setup()`](/api/composition-api-setup) function, instead of using `<script setup>`.

## Top-level `await` {#top-level-await}

Top-level `await` can be used inside `<script setup>`. The resulting code will be compiled as `async setup()`:

```vue
<script setup>
const post = await fetch(`/api/post/1`).then((r) => r.json())
</script>
```

In addition, the awaited expression will be automatically compiled in a format that preserves the current component instance context after the `await`.

:::warning Note
`async setup()` must be used in combination with `Suspense`, which is currently still an experimental feature. We plan to finalize and document it in a future release - but if you are curious now, you can refer to its [tests](https://github.com/vuejs/core/blob/main/packages/runtime-core/__tests__/components/Suspense.spec.ts) to see how it works.
:::

## Generics <sup class="vt-badge ts" /> {#generics}

Generic type parameters can be declared using the `generic` attribute on the `<script>` tag:

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
  selected: T
}>()
</script>
```

The value of `generic` works exactly the same as the parameter list between `<...>` in TypeScript. For example, you can use multiple parameters, `extends` constraints, default types, and reference imported types:

```vue
<script
  setup
  lang="ts"
  generic="T extends string | number, U extends Item"
>
import type { Item } from './types'
defineProps<{
  id: T
  list: U[]
}>()
</script>
```

## Restrictions {#restrictions}

Due to the difference in module execution semantics, code inside `<script setup>` relies on the context of an SFC. When moved into external `.js` or `.ts` files, it may lead to confusion for both developers and tools. Therefore, **`<script setup>`** cannot be used with the `src` attribute.
