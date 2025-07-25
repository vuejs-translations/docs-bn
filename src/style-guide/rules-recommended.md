# অগ্রাধিকার C নিয়ম: প্রস্তাবিত {#priority-c-rules-recommended}

::: warning Note
এই Vue.js স্টাইল গাইড পুরানো এবং পর্যালোচনা করা প্রয়োজন৷ আপনার কোন প্রশ্ন বা পরামর্শ থাকলে, অনুগ্রহ করে [একটি সমস্যা খুলুন](https://github.com/vuejs/docs/issues/new)।
:::

যেখানে একাধিক, সমানভাবে ভাল বিকল্প বিদ্যমান, সেখানে ধারাবাহিকতা নিশ্চিত করার জন্য একটি নির্বিচারে পছন্দ করা যেতে পারে। এই নিয়মগুলিতে, আমরা প্রতিটি গ্রহণযোগ্য বিকল্প বর্ণনা করি এবং একটি ডিফল্ট পছন্দের পরামর্শ দিই। তার মানে আপনি আপনার নিজের কোডবেসে একটি ভিন্ন পছন্দ করতে নির্দ্বিধায় পারেন, যতক্ষণ না আপনি সামঞ্জস্যপূর্ণ এবং একটি ভাল কারণ আছে। যদিও একটি ভাল কারণ আছে দয়া করে! কমিউনিটি স্ট্যান্ডার্ডের সাথে খাপ খাইয়ে, আপনি করবেন:

1. আপনার মস্তিস্ককে প্রশিক্ষিত করুন যাতে আপনি যে সম্প্রদায়ের কোডের মুখোমুখি হন তার বেশিরভাগই আরও সহজে পার্স করুন৷
2. পরিবর্তন ছাড়াই বেশিরভাগ কমিউনিটি কোড উদাহরণ কপি এবং পেস্ট করতে সক্ষম হন
3. প্রায়শই দেখুন নতুন নিয়োগকারীরা ইতিমধ্যেই আপনার পছন্দের কোডিং স্টাইলতে অভ্যস্ত, অন্তত Vue এর ক্ষেত্রে

## কম্পোনেন্ট/ইনস্ট্যান্স অপশন অর্ডার {#component-instance-options-order}

** কম্পোনেন্ট/ইনস্ট্যান্স বিকল্পগুলি ধারাবাহিকভাবে অর্ডার করা উচিত।**

এটি হল ডিফল্ট অর্ডার যা আমরা কম্পোনেন্ট বিকল্পগুলির জন্য সুপারিশ করি। এগুলি বিভাগগুলিতে বিভক্ত, তাই আপনি জানতে পারবেন কোথায় প্লাগইনগুলি থেকে নতুন বৈশিষ্ট্য যুক্ত করতে হবে৷

1. **Global Awareness** (কম্পোনেন্টস বাইরে জ্ঞানের প্রয়োজন)

   - `name`

2. **Template Compiler Options** (টেমপ্লেট কম্পাইল করার পদ্ধতি পরিবর্তন করে)

   - `compilerOptions`

3. **Template Dependencies** (টেমপ্লেটে ব্যবহৃত সম্পদ)

   - `components`
   - `directives`

4. **Composition** (অপশনের মধ্যে বৈশিষ্ট্য মার্জ করে)

   - `extends`
   - `mixins`
   - `provide`/`inject`

5. **Interface** ( কম্পোনেন্টের ইন্টারফেস)

   - `inheritAttrs`
   - `props`
   - `emits`

6. **Composition API** (কম্পোজিশন এপিআই ব্যবহার করার জন্য এন্ট্রি পয়েন্ট)

   - `setup`

7. **Local State** (স্থানীয় প্রতিক্রিয়াশীল বৈশিষ্ট্য)

   - `data`
   - `computed`

8. **Events** (প্রতিক্রিয়াশীল ইভেন্ট দ্বারা ট্রিগার কলব্যাক)

   - `watch`
   - Lifecycle Events (যে ক্রমে বলা হয়)
     - `beforeCreate`
     - `created`
     - `beforeMount`
     - `mounted`
     - `beforeUpdate`
     - `updated`
     - `activated`
     - `deactivated`
     - `beforeUnmount`
     - `unmounted`
     - `errorCaptured`
     - `renderTracked`
     - `renderTriggered`

9. **Non-Reactive Properties** (প্রতিক্রিয়াশীলতা সিস্টেমের থেকে স্বাধীন উদাহরণ বৈশিষ্ট্য)

   - `methods`

10. **Rendering** ( কম্পোনেন্ট আউটপুটের ডিক্লেয়ার বিবরণ)
    - `template`/`render`

## কম্পোনেন্ট বৈশিষ্ট্য ক্রম {#element-attribute-order}

**কম্পোনেন্টস গুণাবলী (কম্পোনেন্ট সহ) ধারাবাহিকভাবে অর্ডার করা উচিত।**

এটি হল ডিফল্ট অর্ডার যা আমরা কম্পোনেন্ট বিকল্পগুলির জন্য সুপারিশ করি। এগুলি বিভাগগুলিতে বিভক্ত, তাই আপনি জানতে পারবেন কোথায় কাস্টম বৈশিষ্ট্য এবং নির্দেশাবলী যোগ করতে হবে৷

1. **Definition** (কম্পোনেন্ট বিকল্প প্রদান করে)

   - `is`

2. **List Rendering** (cএকই কম্পোনেন্টের একাধিক বৈচিত্র তৈরি করে)

   - `v-for`

3. **Conditionals** (কম্পোনেন্টটি রেন্ডার/দেখানো হয়েছে কিনা)

   - `v-if`
   - `v-else-if`
   - `v-else`
   - `v-show`
   - `v-cloak`

4. **Render Modifiers** (কম্পোনেন্ট রেন্ডার উপায় পরিবর্তন)

   - `v-pre`
   - `v-once`

5. **Global Awareness** (কম্পোনেন্ট অতিক্রম জ্ঞান প্রয়োজন)

   - `id`

6. **Unique Attributes** (বৈশিষ্ট্য যে অনন্য মান প্রয়োজন)

   - `ref`
   - `key`

7. **Two-Way Binding** (বাঁধাই এবং ঘটনা একত্রিত করা)

   - `v-model`

8. **Other Attributes** (সমস্ত অনির্দিষ্ট আবদ্ধ এবং আনবাউন্ড বৈশিষ্ট্য)

9. **Events** ( কম্পোনেন্ট ইভেন্ট শ্রোতা)

   - `v-on`

10. **Content** (কম্পোনেন্টস বিষয়অবজেক্ট ওভাররাইড করে)

    - `v-html`
    - `v-text`

## কম্পোনেন্ট/ইনস্ট্যান্স বিকল্পে খালি লাইন {#empty-lines-in-component-instance-options}

**আপনি মাল্টি-লাইন বৈশিষ্ট্যগুলির মধ্যে একটি খালি লাইন যোগ করতে চাইতে পারেন, বিশেষ করে যদি বিকল্পগুলি স্ক্রোল না করে আপনার স্ক্রিনে আর ফিট না হয়৷**

যখন কম্পোনেন্টগুলি সঙ্কুচিত বা পড়তে অসুবিধা হতে শুরু করে, বহু-লাইন বৈশিষ্ট্যগুলির মধ্যে স্পেস যোগ করা তাদের আবার স্কিম করা সহজ করে তোলে। কিছু এডিটরে, যেমন Vim, এই ধরনের ফরম্যাটিং বিকল্পগুলি তাদের কীবোর্ড দিয়ে নেভিগেট করা আরও সহজ করে তুলতে পারে।

<div class="options-api">

<div class="style-example style-example-bad">
<h3>Bad</h3>

```js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue() {
    // ...
  },

  inputClasses() {
    // ...
  }
}
```

</div>

<div class="style-example style-example-good">
<h3>Good</h3>

```js
// No spaces are also fine, as long as the component
// is still easy to read and navigate.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue() {
    // ...
  },
  inputClasses() {
    // ...
  }
}
```

</div>

</div>

<div class="composition-api">

<div class="style-example style-example-bad">
<h3>Bad</h3>

```js
defineProps({
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
})
const formattedValue = computed(() => {
  // ...
})
const inputClasses = computed(() => {
  // ...
})
```

</div>

<div class="style-example style-example-good">
<h3>Good</h3>

```js
defineProps({
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
})

const formattedValue = computed(() => {
  // ...
})

const inputClasses = computed(() => {
  // ...
})
```

</div>

</div>

## Single-file component top-level element order {#single-file-component-top-level-element-order}

**[একক-ফাইল কম্পোনেন্ট]](/guide/scaling-up/sfc) সর্বদা `<script>`, `<টেমপ্লেট>`, এবং `<style>` ট্যাগগুলিকে ধারাবাহিকভাবে, `<style>` শেষের সাথে অর্ডার করতে হবে, কারণ অন্য দুটির মধ্যে অন্তত একটি সর্বদা প্রয়োজনীয়।**

<div class="style-example style-example-bad">
<h3>Bad</h3>

```vue-html [ComponentX.vue]
<style>/* ... */</style>
<script>/* ... */</script>
<template>...</template>
```

```vue-html [ComponentA.vue]
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

```vue-html [ComponentB.vue]
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

</div>

<div class="style-example style-example-good">
<h3>Good</h3>

```vue-html [ComponentA.vue]
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

```vue-html [ComponentB.vue]
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

or

```vue-html  [ComponentA.vue]
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

```vue-html [ComponentB.vue]
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```

</div>
