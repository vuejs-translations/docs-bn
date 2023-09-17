# SFC Syntax Specification {#sfc-syntax-specification}

## Overview {#overview}

একটি Vue সিঙ্গেল-ফাইল কম্পোনেন্ট (SFC), প্রচলিতভাবে `*.vue` ফাইল এক্সটেনশন ব্যবহার করে, একটি কাস্টম ফাইল ফরম্যাট যা একটি Vue কম্পোনেন্ট বর্ণনা করার জন্য একটি HTML-এর মতো সিনট্যাক্স ব্যবহার করে। একটি Vue SFC এইচটিএমএল এর সাথে সিনট্যাক্টিভাবে সামঞ্জস্যপূর্ণ।

প্রতিটি `*.vue` ফাইলে তিন ধরনের টপ-লেভেলের ভাষা ব্লক থাকে: `<template>`, `<script>`, এবং `<style>`, এবং ঐচ্ছিকভাবে অতিরিক্ত কাস্টম ব্লক:

```vue
<template>
  <div class="example">{{ msg }}</div>
</template>

<script>
export default {
  data() {
    return {
      msg: 'Hello world!'
    }
  }
}
</script>

<style>
.example {
  color: red;
}
</style>

<custom1>
  This could be e.g. documentation for the component.
</custom1>
```

## Language Blocks {#language-blocks}

### `<template>` {#template}

- প্রতিটি `*.vue` ফাইলে সর্বাধিক একটি টপ-লেভেলেরর `<template>` ব্লক থাকতে পারে।

- কন্টেন্ট এক্সট্রাক্ট করা হবে এবং `@vue/compiler-dom`-এ পাস করা হবে, জাভাস্ক্রিপ্ট রেন্ডার ফাংশনে প্রি-কম্পাইলড করা হবে এবং এক্সপোর্টেড কম্পোনেন্টেরর সাথে এর `render` অপশন হিসেবে সংযুক্ত করা হবে।

### `<script>` {#script}

- প্রতিটি `*.vue` ফাইলে সর্বাধিক একটি `<script>` ব্লক থাকতে পারে ([`<script setup>`](/api/sfc-script-setup) ব্যতীত)।

- স্ক্রিপ্টটি একটি ES মডিউল হিসাবে এক্সিকিউট করা হয়।

- **ডিফল্ট এক্সপোর্ট** একটি Vue কম্পোনেন্ট অপশন অবজেক্ট হওয়া উচিত, হয় একটি প্লেইন অবজেক্ট হিসাবে বা [defineComponent](/api/general#definecomponent) এর রিটার্ন ভ্যালু হিসাবে।

### `<script setup>` {#script-setup}

- প্রতিটি `*.vue` ফাইলে সর্বাধিক একটি `<script setup>` ব্লক থাকতে পারে (সাধারণ `<script>` ব্যতীত)।

- স্ক্রিপ্টটি প্রি-প্রসেস করা হয় এবং কম্পোনেন্টের `setup()` ফাংশন হিসেবে ব্যবহার করা হয়, যার মানে এটি **কম্পোনেন্টের প্রতিটি ইনস্ট্যান্সের জন্য** কার্যকর করা হবে। `<script setup>`-এ টপ-লেভেলের বাইন্ডিংগুলি স্বয়ংক্রিয়ভাবে টেমপ্লেটে এক্সপোজড হয়৷ আরও বিশদ বিবরণের জন্য, [`<script setup>` এ ডেডিকেটেড ডকুমেন্টেশন দেখুন](/api/sfc-script-setup)।

### `<style>` {#style}

- একটি একক `*.vue` ফাইলে একাধিক `<style>` ট্যাগ থাকতে পারে।

- একটি `<style>` ট্যাগে `scoped` বা `module` অ্যাট্রিবিউট থাকতে পারে (আরো বিশদ বিবরণের জন্য [SFC স্টাইল ফিচারস](/api/sfc-css-features) দেখুন) কারন্ট কম্পোনেন্টে স্টাইলগুলিকে এনক্যাপসুলেট করতে হেল্প করে। বিভিন্ন এনক্যাপসুলেশন মোড সহ একাধিক `<style>` ট্যাগ একই কম্পোনেন্টে মিশ্রিত করা যেতে পারে।

### Custom Blocks {#custom-blocks}

যেকোনো প্রজেক্ট-স্পেসিফিক প্রয়োজনের জন্য একটি `*.vue` ফাইলে অতিরিক্ত কাস্টম ব্লক অন্তর্ভুক্ত করা যেতে পারে, যেমন একটি `<docs>` ব্লক। কাস্টম ব্লকের কিছু রিয়েল-ওয়াল্ড উদাহরণ অন্তর্ভুক্ত:

- [Gridsome: `<page-query>`](https://gridsome.org/docs/querying-data/)
- [vite-plugin-vue-gql: `<gql>`](https://github.com/wheatjs/vite-plugin-vue-gql)
- [vue-i18n: `<i18n>`](https://github.com/intlify/bundle-tools/tree/main/packages/vite-plugin-vue-i18n#i18n-custom-block)

কাস্টম ব্লকগুলি পরিচালনা করা টুলিংয়ের উপর নির্ভর করবে - আপনি যদি নিজের কাস্টম ব্লক ইন্টিগ্রেশন তৈরি করতে চান তবে আরও জানতে [SFC কাস্টম ব্লক ইন্টিগ্রেশন টুলিং বিভাগ](/guide/scaling-up/tooling#sfc-custom-block-integrations) দেখুন বিস্তারিত

## Automatic Name Inference {#automatic-name-inference}

নিম্নলিখিত ক্ষেত্রে একটি SFC অটোমেটিকলি তার **filename** থেকে কম্পোনেন্টটির নাম অনুমান করে:

- ডেভ সতর্কতা ফরম্যাট
- DevTools পরিদর্শন
- রিকারসিভ স্ব-রেফারেন্স, যেমন `FooBar.vue` নামের একটি ফাইল তার টেমপ্লেটে নিজেকে `<FooBar/>` হিসেবে উল্লেখ করতে পারে। এটি স্পষ্টভাবে রেজিস্ট্রাড/ইমপোর্টেড করা কম্পোনেন্টগুলির তুলনায় কম অগ্রাধিকার রয়েছে৷

## Pre-Processors {#pre-processors}

ব্লকগুলি `lang` অ্যাট্রিবিউট ব্যবহার করে প্রি-প্রসেসর ভাষা ঘোষণা করতে পারে। সবচেয়ে সাধারণ ক্ষেত্রে `<script>` ব্লকের জন্য TypeScript ব্যবহার করা হচ্ছে:

```vue-html
<script lang="ts">
  // use TypeScript
</script>
```

`lang` যেকোনো ব্লকে প্রয়োগ করা যেতে পারে - উদাহরণস্বরূপ আমরা `<style>` ব্যবহার করতে পারি [Sass](https://sass-lang.com/) এর সাথে এবং `<template>` [Pug](https:/) এর সাথে /pugjs.org/api/getting-started.html):

```vue-html
<template lang="pug">
p {{ msg }}
</template>

<style lang="scss">
  $primary-color: #333;
  body {
    color: $primary-color;
  }
</style>
```

নোট করুন যে বিভিন্ন প্রি-প্রসেসরের সাথে একীকরণ টুলচেইনের দ্বারা পৃথক হতে পারে। উদাহরণের জন্য সংশ্লিষ্ট ডকুমেন্টেশন দেখুন:

- [Vite](https://vitejs.dev/guide/features.html#css-pre-processors)
- [Vue CLI](https://cli.vuejs.org/guide/css.html#pre-processors)
- [webpack + vue-loader](https://vue-loader.vuejs.org/guide/pre-processors.html#using-pre-processors)

## `src` Imports {#src-imports}

আপনি যদি আপনার `*.vue` কম্পোনেন্টগুলিকে একাধিক ফাইলে বিভক্ত করতে পছন্দ করেন, আপনি একটি ল্যঙ্গুয়েজ ব্লকের জন্য একটি বহিরাগত ফাইল ইম্পোর্ট করতে `src` বৈশিষ্ট্য ব্যবহার করতে পারেন:

```vue
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

সতর্ক থাকুন যে `src` ইম্পোর্টগুলি ওয়েবপ্যাক মডিউল অনুরোধের মতো একই পথ রেজোলিউশনের নিয়ম অনুসরণ করে, যার অর্থ:

- রিলিটিভ পাথগুলি `./` দিয়ে শুরু করতে হবে৷
- আপনি npm নির্ভরতা থেকে রিসোর্স ইম্পোর্ট করতে পারেন:

```vue
<!-- import a file from the installed "todomvc-app-css" npm package -->
<style src="todomvc-app-css/index.css" />
```

`src` ইম্পোর্ট কাস্টম ব্লকের সাথেও কাজ করে, যেমন:

```vue
<unit-test src="./unit-test.js">
</unit-test>
```

## Comments {#comments}

প্রতিটি ব্লকের ভিতরে আপনি যে ল্যাঙ্গুয়েজ ব্যবহার করা হচ্ছে তার কমেন্ট সিনট্যাক্স ব্যবহার করবেন (HTML, CSS, JavaScript, Pug, ইত্যাদি)। টপ-লেভেলের কমেন্টের জন্য, HTML কমেন্ট সিনট্যাক্স ব্যবহার করুন: `<!-- কমেন্ট কন্টেন্টস এখানে -->`
