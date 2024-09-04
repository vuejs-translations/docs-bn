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

### Reactive Props Destructure <sup class="vt-badge" data-text="3.5+" /> {#reactive-props-destructure}

In Vue 3.5 and above, variables destructured from the return value of `defineProps` are reactive. Vue's compiler automatically prepends `props.` when code in the same `<script setup>` block accesses variables destructured from `defineProps`:

```ts
const { foo } = defineProps(['foo'])

watchEffect(() => {
  // runs only once before 3.5
  // re-runs when the "foo" prop changes in 3.5+
  console.log(foo)
})
```

The above is compiled to the following equivalent:

```js {5}
const props = defineProps(['foo'])

watchEffect(() => {
  // `foo` transformed to `props.foo` by the compiler
  console.log(props.foo)
})
```

In addition, you can use JavaScript's native default value syntax to declare default values for the props. This is particularly useful when using the type-based props declaration:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const { msg = 'hello', labels = ['one', 'two'] } = defineProps<Props>()
```

### Default props values when using type declaration <sup class="vt-badge ts" /> {#default-props-values-when-using-type-declaration}

In 3.5 and above, default values can be naturally declared when using Reactive Props Destructure. But in 3.4 and below, Reactive Props Destructure is not enabled by default. In order to declare props default values with type-based declaration, the `withDefaults` compiler macro is needed:

```ts
interface Props {
  msg?: string
  labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
  msg: 'hello',
  labels: () => ['one', 'two']
})
```

এটি সমতুল্য রানটাইম প্রপস `default` অপশনের কম্পাইল করা হবে। উপরন্তু, `withDefaults` হেল্পার ডিফল্ট ভ্যালুগুলির জন্য টাইপ চেক প্রদান করে এবং নিশ্চিত করে যে প্রত্যাবর্তিত `props` টাইপটিতে ডিফল্ট ভ্যালু ডিক্লারড বৈশিষ্ট্যগুলির জন্য অপশনাল ফ্ল্যাগগুলি রিমুভ করা হয়েছে।

:::info
Note that default values for mutable reference types (like arrays or objects) should be wrapped in functions when using `withDefaults` to avoid accidental modification and external side effects. This ensures each component instance gets its own copy of the default value. This is **not** necessary when using default values with destructure.
:::

## defineModel() {#definemodel}

- Only available in 3.4+

এই ম্যাক্রোটি একটি দ্বি-মুখী বাইন্ডিং প্রপ ঘোষণা করতে ব্যবহার করা যেতে পারে যা মূল কম্পোনেন্ট থেকে `v-model` এর মাধ্যমে ব্যবহার করা যেতে পারে। উদাহরণ ব্যবহার [কম্পোনেন্ট `v-model`](/guide/components/v-model) গাইডেও আলোচনা করা হয়েছে।

হুডের নিচে, এই ম্যাক্রো একটি মডেল প্রপ এবং একটি সংশ্লিষ্ট মান আপডেট ইভেন্ট ঘোষণা করে। যদি প্রথম আর্গুমেন্টটি একটি আক্ষরিক স্ট্রিং হয় তবে এটি প্রপ নাম হিসাবে ব্যবহার করা হবে; অন্যথায় প্রপ নামটি ডিফল্ট হবে `"modelValue"`। উভয় ক্ষেত্রেই, আপনি একটি অতিরিক্ত অবজেক্টও পাস করতে পারেন যা প্রপের বিকল্পগুলি এবং মডেল রেফের মান রূপান্তর বিকল্পগুলি অন্তর্ভুক্ত করতে পারে।

```js
// declares "modelValue" prop, consumed by parent via v-model
const model = defineModel()
// OR: declares "modelValue" prop with options
const model = defineModel({ type: String })

// emits "update:modelValue" when mutated
model.value = 'hello'

// declares "count" prop, consumed by parent via v-model:count
const count = defineModel('count')
// OR: declares "count" prop with options
const count = defineModel('count', { type: Number, default: 0 })

function inc() {
  // emits "update:count" when mutated
  count.value++
}
```

:::warning
যদি আপনার কাছে `defineModel` প্রপের জন্য একটি `default` মান থাকে এবং আপনি এই প্রপের জন্য অভিভাবক কম্পোনেন্ট থেকে কোনো মান প্রদান না করেন, তাহলে এটি পিতামাতা এবং শিশু কম্পোনেন্টগুলির মধ্যে একটি ডি-সিঙ্ক্রোনাইজেশন সৃষ্টি করতে পারে। নীচের উদাহরণে, পিতামাতার `myRef` অসংজ্ঞায়িত, কিন্তু সন্তানের `model` হল 1:

```js
// child component:
const model = defineModel({ default: 1 })

// parent component:
const myRef = ref()
```

```html
<Child v-model="myRef"></Child>
```

:::

### Modifiers and Transformers {#modifiers-and-transformers}

`v-model` নির্দেশের সাথে ব্যবহৃত মডিফায়ার অ্যাক্সেস করতে, আমরা `defineModel()` এর রিটার্ন মানকে এভাবে ধ্বংস করতে পারি:

```js
const [modelValue, modelModifiers] = defineModel()

// corresponds to v-model.trim
if (modelModifiers.trim) {
  // ...
}
```

যখন একটি সংশোধক উপস্থিত থাকে, তখন আমাদের সম্ভবত মানটি প্যারেন্টের সাথে পড়ার বা সিঙ্ক করার সময় রূপান্তর করতে হবে। আমরা `get` এবং `set` ট্রান্সফরমার বিকল্পগুলি ব্যবহার করে এটি অর্জন করতে পারি:

```js
const [modelValue, modelModifiers] = defineModel({
  // get() omitted as it is not needed here
  set(value) {
    // if the .trim modifier is used, return trimmed value
    if (modelModifiers.trim) {
      return value.trim()
    }
    // otherwise, return the value as-is
    return value
  }
})
```

### Usage with TypeScript <sup class="vt-badge ts" /> {#usage-with-typescript}

`defineProps` এবং `defineEmits` এর মতো, `defineModel` মডেলের মান এবং মডিফায়ারের ধরন নির্দিষ্ট করতে টাইপ আর্গুমেন্টও পেতে পারে:

```ts
const modelValue = defineModel<string>()
//    ^? Ref<string | undefined>

// default model with options, required removes possible undefined values
const modelValue = defineModel<string>({ required: true })
//    ^? Ref<string>

const [modelValue, modifiers] = defineModel<string, 'trim' | 'uppercase'>()
//                 ^? Record<'trim' | 'uppercase', true | undefined>
```

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

- Only supported in 3.3+

এই ম্যাক্রোটি একটি পৃথক `<script>` ব্লক ব্যবহার না করে সরাসরি `<script setup>` এর ভিতরে কম্পোনেন্ট অপশন ডিক্লেয়ার করতে ব্যবহার করা যেতে পারে:

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

- This is a macro. The options will be hoisted to module scope and cannot access local variables in `<script setup>` that are not literal constants.

## defineSlots()<sup class="vt-badge ts"/> {#defineslots}

- Only supported in 3.3+

This macro can be used to provide type hints to IDEs for slot name and props type checking.

`defineSlots()` শুধুমাত্র একটি টাইপ প্যারামিটার রিসিভ করে এবং কোনো রানটাইম আর্গুমেন্ট নেই। টাইপ প্যারামিটার একটি টাইপ লিটারাল হওয়া উচিত যেখানে প্রোপার্টি কী-টি স্লটের নাম এবং ভ্যালুর টাইপটি স্লট ফাংশন। ফাংশনের প্রথম আর্গুমেন্ট হল প্রপস যা স্লট পাওয়ার আশা করে এবং এর টাইপটি টেমপ্লেটের স্লট প্রপসের জন্য ব্যবহার করা হবে। রিটার্নের টাইপটি বর্তমানে উপেক্ষা করা হয়েছে এবং এটি `any` হতে পারে, তবে আমরা ভবিষ্যতে স্লট সামগ্রী চেক করার জন্য এটির সুবিধা নিতে পারি।

এটি `slots` অবজেক্টও রিটার্ন দেয়, যা সেটআপ প্রসঙ্গে প্রকাশিত `slots` অবজেক্টের সমতুল্য অথবা `useSlots()` দ্বারা রিটার্নড হয়।

```vue
<script setup lang="ts">
const slots = defineSlots<{
  default(props: { msg: string }): any
}>()
</script>
```

## `useSlots()` & `useAttrs()` {#useslots-useattrs}

`<script setup>`-এর ভিতরে `slots` এবং `attrs`-এর ব্যবহার তুলনামূলকভাবে বিরল হওয়া উচিত, কারণ আপনি টেমপ্লেটে `$slots` এবং `$attrs` হিসেবে সরাসরি অ্যাক্সেস করতে পারবেন। বিরল ক্ষেত্রে যেখানে আপনার প্রয়োজন হয়, যথাক্রমে `useSlots` এবং `useAttrs` হেল্পার ব্যবহার করুন:

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()
</script>
```

`useSlots` এবং `useAttrs` হল প্রকৃত রানটাইম ফাংশন যা `setupContext.slots` এবং `setupContext.attrs` এর সমতুল্য রিটার্ন করে। এগুলি সাধারণ কম্পোজিশন API ফাংশনেও ব্যবহার করা যেতে পারে।

## Usage alongside normal `<script>` {#usage-alongside-normal-script}

`<script setup>` সাধারণ `<script>` এর সাথে ব্যবহার করা যেতে পারে। একটি নরমাল `<script>` প্রয়োজন হতে পারে যেই ক্ষেত্রে আমাদের প্রয়োজন:

- যে অপশনগুলি `<script setup>`-এ প্রকাশ করা যায় না সেগুলি ডিক্লেয়ার করুন, উদাহরণস্বরূপ `inheritAttrs` বা প্লাগইনগুলির মাধ্যমে এনাবল করা কাস্টম অপশনগুলি (3.3-এ [`defineOptions`](/api/sfc-script-setup#defineoptions দ্বারা প্রতিস্থাপিত হতে পারে) +)।
- নেইমড এক্সপোর্ট ডিক্লেয়ার করা।
- সাইড ইফেক্ট চালান বা এমন অবজেক্ট তৈরি করুন যা শুধুমাত্র একবার এক্সিকিউট করা উচিত।

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

একই কম্পোনেন্টে `<script setup>` এবং `<script>` একত্রিত করার জন্য সাপোর্টেড, উপরে বর্ণিত পরিস্থিতিতে সীমাবদ্ধ। বিশেষভাবে:

- **NOT** অপশনগুলির জন্য একটি পৃথক `<script>` সেকশন ব্যবহার করবেন না যা `<script setup>` ব্যবহার করে ইতিমধ্যেই ডিফাইনড করা যেতে পারে, যেমন `props` এবং `emits`।
- `<script setup>`-এর মধ্যে তৈরি ভেরিয়েবলগুলিকে কম্পোনেন্ট ইন্সট্যান্সে প্রপার্টি হিসেবে যোগ করা হয় না, যা অপশন এপিআই থেকে অ্যাক্সেসযোগ্য করে তোলে। এইভাবে Mixing API গুলিকে দৃঢ়ভাবে নিরুৎসাহিত করা হয়৷

আপনি যদি সাপোর্টেড নয় এমন পরিস্থিতিতে নিজেকে খুঁজে পান তাহলে `<script setup>` ব্যবহার করার পরিবর্তে আপনার একটি স্পষ্ট [`setup()`](/api/composition-api-setup) ফাংশনে স্যুইচ করার কথা বিবেচনা করা উচিত।

## Top-level `await` {#top-level-await}

টপ-লেভেল `await` ব্যবহার করা যেতে পারে `<script setup>` এর মধ্যে। ফলস্বরূপ কোডটি `async setup()` হিসাবে কম্পাইল করা হবে:

```vue
<script setup>
const post = await fetch(`/api/post/1`).then((r) => r.json())
</script>
```

উপরন্তু, প্রতীক্ষিত এক্সপ্রেশনটি স্বয়ংক্রিয়ভাবে একটি ফরম্যাটে সংকলিত হবে যা `await` এর পরে কারেন্ট কম্পোনেন্ট উদাহরণের কনটেক্স সংরক্ষণ করে।

:::warning নোট
`async setup()` এর সাথে `Suspense` ব্যবহার করতে হবে, যা এখনও পরীক্ষামূলক বৈশিষ্ট্য। আমরা পরবর্তী রিলিজে এটি চূড়ান্ত করার এবং ডকুমেন্ট করার পরিকল্পনা করছি। তবে, আপনি যদি এখনই জানতে আগ্রহী হন, তাহলে আপনি এর [পরীক্ষা](https://github.com/vuejs/core/blob/main/packages/runtime-core/__tests__/components/Suspense.spec.ts) দেখতে পারেন। 
:::

## Generics <sup class="vt-badge ts" /> {#generics}

জেনেরিক টাইপ প্যারামিটারগুলি `<script>` ট্যাগে `generic` অ্যাট্রিবিউট ব্যবহার করে ডিক্লেয়ার করা যেতে পারে:

```vue
<script setup lang="ts" generic="T">
defineProps<{
  items: T[]
  selected: T
}>()
</script>
```

টাইপস্ক্রিপ্টে `<...>`-এর মধ্যে প্যারামিটার লিস্টের মতোই `generic`-এর ভ্যালু ঠিক একইভাবে কাজ করে। উদাহরণস্বরূপ, আপনি একাধিক প্যরামিটার, `extends` সীমাবদ্ধতা, ডিফল্ট টাইপস এবং রেফারেন্স ইম্পোর্টেড করা টাইপগুলি ব্যবহার করতে পারেন:

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

একটি `রেফ`-এ জেনেরিক কম্পোনেন্টের রেফারেন্স ব্যবহার করার জন্য আপনাকে [`vue-component-type-helpers`](https://www.npmjs.com/package/vue-component-type-helpers) লাইব্রেরি `InstanceType` হিসাবে কাজ করবে না।

```vue
<script
  setup
  lang="ts"
>
import componentWithoutGenerics from '../component-without-generics.vue';
import genericComponent from '../generic-component.vue';

import type { ComponentExposed } from 'vue-component-type-helpers';

// Works for a component without generics
ref<InstanceType<typeof componentWithoutGenerics>>();

ref<ComponentExposed<typeof genericComponent>>();
```

## Restrictions {#restrictions}

- মডিউল এক্সিকিউশন শব্দার্থবিদ্যার পার্থক্যের কারণে, `<script setup>` এর ভিতরের কোড একটি SFC-এর প্রসঙ্গে নির্ভর করে। বাহ্যিক `.js` বা `.ts` ফাইলে সরানো হলে, এটি ডেভেলপার এবং টুল উভয়ের জন্যই বিভ্রান্তির কারণ হতে পারে। অতএব, **`<script setup>`** `src` অ্যাট্রিবিউটের সাথে ব্যবহার করা যাবে না।
- `<script setup>` ইন-ডোম রুট কম্পোনেন্ট টেমপ্লেট সমর্থন করে না।([সম্পর্কিত আলোচনা](https://github.com/vuejs/core/issues/8391))
