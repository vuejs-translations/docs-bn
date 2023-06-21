# Built-in Special Elements {#built-in-special-elements}

:::info Components নয়
`<component>`, `<slot>` এবং `<template>` হল কম্পোনেন্ট-এর মতো বৈশিষ্ট্য এবং টেমপ্লেট সিনট্যাক্সের অংশ। এগুলি সত্য উপাদান নয় এবং টেমপ্লেট সংকলনের সময় কম্পাইল করা হয়। যেমন, তারা প্রচলিতভাবে টেমপ্লেটে ছোট হাতের অক্ষর দিয়ে লেখা হয়।
:::

## `<component>` {#component}

গতিশীল উপাদান বা উপাদান রেন্ডার করার জন্য একটি "meta component"।

- **Props**

  ```ts
  interface DynamicComponentProps {
    is: string | Component
  }
  ```

- ** বিস্তারিত**

  রেন্ডার করার জন্য প্রকৃত উপাদান `is` প্রপ দ্বারা নির্ধারিত হয়।

  - যখন `is` একটি স্ট্রিং হয়, এটি হয় একটি HTML ট্যাগ নাম বা কোনো উপাদানের নিবন্ধিত নাম হতে পারে।

  - বিকল্পভাবে, `is` একটি উপাদানের সংজ্ঞার সাথে সরাসরি আবদ্ধ হতে পারে।

- **উদাহরণ**

  নিবন্ধিত নাম দ্বারা উপাদান রেন্ডারিং (Options API):

  ```vue
  <script>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: { Foo, Bar },
    data() {
      return {
        view: 'Foo'
      }
    }
  }
  </script>

  <template>
    <component :is="view" />
  </template>
  ```

  সংজ্ঞা অনুসারে কম্পোনেন্ট রেন্ডারিং (Composition API with `<script setup>`):

  ```vue
  <script setup>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'
  </script>

  <template>
    <component :is="Math.random() > 0.5 ? Foo : Bar" />
  </template>
  ```

  Rendering HTML elements:

  ```vue-html
  <component :is="href ? 'a' : 'span'"></component>
  ```

  [বিল্ট-ইন কম্পোনেন্ট](./built-in-components) সবই `is`-এ পাস করা যেতে পারে, কিন্তু আপনি যদি সেগুলি নাম দিয়ে পাস করতে চান তাহলে আপনাকে অবশ্যই সেগুলি নিবন্ধন করতে হবে৷ উদাহরণ স্বরূপ:

  ```vue
  <script>
  import { Transition, TransitionGroup } from 'vue'

  export default {
    components: {
      Transition,
      TransitionGroup
    }
  }
  </script>

  <template>
    <component :is="isGroup ? 'TransitionGroup' : 'Transition'">
      ...
    </component>
  </template>
  ```

  আপনি যদি কম্পোনেন্টটিকে এর নামের পরিবর্তে `is`-এ পাস করেন তাহলে নিবন্ধনের প্রয়োজন নেই, যেমন `<script setup>`-এ।

  যদি `v-model` একটি `<component>` ট্যাগে ব্যবহার করা হয়, তাহলে টেমপ্লেট কম্পাইলার এটিকে একটি `modelValue` প্রপ এবং `update:modelValue` ইভেন্ট লিসেনারে প্রসারিত করবে, অনেকটা অন্য কোনো উপাদানের মতো। যাইহোক, এটি নেটিভ HTML উপাদানের সাথে সামঞ্জস্যপূর্ণ হবে না, যেমন `<input>` বা `<select>`। ফলস্বরূপ, গতিশীলভাবে তৈরি স্থানীয় উপাদানের সাথে `v-model` ব্যবহার করা কাজ করবে না:

  ```vue
  <script setup>
  import { ref } from 'vue'

  const tag = ref('input')
  const username = ref('')
  </script>

  <template>
    <!-- This won't work as 'input' is a native HTML element -->
    <component :is="tag" v-model="username" />
  </template>
  ```

 অনুশীলনে, এই এজ কেসটি সাধারণ নয় কারণ নেটিভ ফর্ম ক্ষেত্রগুলি সাধারণত বাস্তব অ্যাপ্লিকেশনগুলিতে উপাদানগুলিতে মোড়ানো থাকে। আপনি যদি সরাসরি একটি নেটিভ এলিমেন্ট ব্যবহার করতে চান তাহলে আপনি `v-model` কে একটি অ্যাট্রিবিউট এবং ইভেন্টে ম্যানুয়ালি বিভক্ত করতে পারেন।

- **আরো দেখুন** [Dynamic Components](/guide/essentials/component-basics#dynamic-components)

## `<slot>` {#slot}

টেমপ্লেটগুলিতে স্লট সামগ্রী আউটলেটগুলিকে বোঝায়।

- **Props**

  ```ts
  interface SlotProps {
    /**
     * Any props passed to <slot> to passed as arguments
     * for scoped slots
     */
    [key: string]: any
    /**
     * Reserved for specifying slot name.
     */
    name?: string
  }
  ```

- ** বিস্তারিত**

  `<slot>` উপাদানটি একটি স্লটের নাম নির্দিষ্ট করতে `name` বৈশিষ্ট্য ব্যবহার করতে পারে। যখন কোনো `name` নির্দিষ্ট করা না থাকে, এটি ডিফল্ট স্লট রেন্ডার করবে। স্লট উপাদানে পাস করা অতিরিক্ত বৈশিষ্ট্যগুলি প্যারেন্টে সংজ্ঞায়িত স্কোপড স্লটে স্লট প্রপ হিসাবে পাস করা হবে।

  উপাদানটি নিজেই এর মিলিত স্লট সামগ্রী দ্বারা প্রতিস্থাপিত হবে।

  `<slot>` Vue টেমপ্লেটের উপাদানগুলি জাভাস্ক্রিপ্টে কম্পাইল করা হয়েছে, তাই সেগুলিকে বিভ্রান্ত করা যাবে না [native `<slot>` elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/slot).

- **আরো দেখুন** [Component - Slots](/guide/components/slots)

## `<template>` {#template}

`<template>` ট্যাগটি প্লেসহোল্ডার হিসেবে ব্যবহার করা হয় যখন আমরা DOM-এ কোনো উপাদান রেন্ডার না করে বিল্ট-ইন নির্দেশিকা ব্যবহার করতে চাই।

- ** বিস্তারিত**

  `<template>` এর জন্য বিশেষ হ্যান্ডলিং শুধুমাত্র তখনই ট্রিগার হয় যদি এটি এই নির্দেশাবলীর একটির সাথে ব্যবহার করা হয়:

  - `v-if`, `v-else-if`, or `v-else`
  - `v-for`
  - `v-slot`

  যদি এই নির্দেশাবলীর কোনোটিই উপস্থিত না থাকে তবে এটি একটি [নেটিভ `<template>` উপাদান](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) হিসেবে রেন্ডার করা হবে।

  একটি `<template>` একটি `v-for` সহ একটি [`কী` বৈশিষ্ট্য](/api/built-in-special-attributes#key) থাকতে পারে। অন্যান্য সমস্ত বৈশিষ্ট্য এবং নির্দেশ বাতিল করা হবে, কারণ সেগুলি সংশ্লিষ্ট উপাদান ছাড়া অর্থপূর্ণ নয়৷

  একক-ফাইল উপাদানগুলি সম্পূর্ণ টেমপ্লেটটি মোড়ানোর জন্য একটি [শীর্ষ-স্তরের `<template>` ট্যাগ](/api/sfc-spec#language-blocks) ব্যবহার করে। সেই ব্যবহার উপরে বর্ণিত `<template>` ব্যবহার থেকে আলাদা। সেই শীর্ষ-স্তরের ট্যাগটি নিজেই টেমপ্লেটের অংশ নয় এবং টেমপ্লেট সিনট্যাক্স সমর্থন করে না, যেমন নির্দেশাবলী।

- **আরো দেখুন**
  - [Guide - `v-if` on `<template>`](/guide/essentials/conditional#v-if-on-template)
  - [Guide - `v-for` on `<template>`](/guide/essentials/list#v-for-on-template)
  - [Guide - Named slots](/guide/components/slots#named-slots)
