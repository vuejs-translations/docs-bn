---
footer: false
---

# ভূমিকা {#introduction}

:::info আপনি Vue 3 এর জন্য ডকুমেন্টেশন পড়ছেন!

- Vue 2 সমর্থন **৩১ ডিসেম্বর, ২০২৩**-এ শেষ হয়েছে। [Vue 2 EOL](https://v2.vuejs.org/eol/) সম্পর্কে আরও জানুন।
- Vue 2 থেকে আপগ্রেড করছেন? [মাইগ্রেশন গাইড](https://v3-migration.vuejs.org/) দেখুন।
  :::

<style src="@theme/styles/vue-mastery.css"></style>
<div class="vue-mastery-link">
  <a href="https://www.vuemastery.com/courses/" target="_blank">
    <div class="banner-wrapper">
      <img class="banner" alt="Vue Mastery banner" width="96px" height="56px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vuemastery-graphical-link-96x56.png" />
    </div>
    <p class="description">ভিডিও টিউটোরিয়াল চালু সহ Vue শিখুন <span>VueMastery.com</span></p>
    <div class="logo-wrapper">
        <img alt="Vue Mastery Logo" width="25px" src="https://storage.googleapis.com/vue-mastery.appspot.com/flamelink/media/vue-mastery-logo.png" />
    </div>
  </a>
</div>

## Vue কি? {#what-is-vue}

Vue (উচ্চারিত /vjuː/, যেমন **view**) হল একটি JavaScript framework ব্যবহারকারী ইন্টারফেস তৈরির জন্য। এটি স্ট্যান্ডার্ড HTML, CSS, and JavaScript এর উপরে তৈরি করা এবং একটি ডিক্লেয়ার এবং কম্পোনেন্ট-ভিত্তিক প্রোগ্রামিং মডেল যা আপনাকে দক্ষতার সাথে ব্যবহারকারীর সহজ বা জটিল ইন্টারফেস তৈরি করতে সহায়তা করে।

এখানে একটি উদাহরণ:

<div class="options-api">

```js
import { createApp } from 'vue'

createApp({
  data() {
    return {
      count: 0
    }
  }
}).mount('#app')
```

</div>
<div class="composition-api">

```js
import { createApp, ref } from 'vue'

createApp({
  setup() {
    return {
      count: ref(0)
    }
  }
}).mount('#app')
```

</div>

```vue-html
<div id="app">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>
```

**ফলাফল**

<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<div class="demo">
  <button @click="count++">
    Count is: {{ count }}
  </button>
</div>

উপরের উদাহরণটি Vue এর দুটি মূল বৈশিষ্ট্য প্রদর্শন করে:

- **Declarative Rendering (ডিক্লেয়ার উপস্থাপন)**: Vue একটি টেমপ্লেট সিনট্যাক্স সহ স্ট্যান্ডার্ড HTML ব্যবহার করে যা আমাদের জাভাস্ক্রিপ্ট স্টেট এর উপর ভিত্তি করে HTML আউটপুটকে ডিক্লেয়ারভাবে বর্ণনা করতে দেয়।

- **Reactivity (প্রতিক্রিয়াশীলতা)**: Vue স্বয়ংক্রিয়ভাবে জাভাস্ক্রিপ্টের স্টেট এর পরিবর্তনগুলি ট্র্যাক করে এবং পরিবর্তনগুলি ঘটলে দক্ষতার সাথে DOM আপডেট করে৷

আপনার ইতিমধ্যে প্রশ্ন থাকতে পারে - চিন্তা করবেন না। আমরা বাকি ডকুমেন্টেশনে প্রতিটি সামান্য বিষয়ও বিস্তারিতভাবে আলোচনা করব। আপাতত, অনুগ্রহ করে পড়তে থাকুন যাতে আপনি Vue কিভাবে কাজ করে সে সম্পর্কে সর্বোচ্চ ধারণা পেতে পারেন।

:::tip পূর্বশর্ত
বাকি ডকুমেন্টেশন এইচটিএমএল, সিএসএস এবং জাভাস্ক্রিপ্টের সাথে প্রাথমিক পরিচিতি অনুমান করে। আপনি যদি ফ্রন্টএন্ড ডেভেলপমেন্টে সম্পূর্ণ নতুন হয়ে থাকেন, তাহলে আপনার প্রথম ধাপ হিসেবে একটি ফ্রেমওয়ার্কের ব্যবহার করা সেরা ধারণা নাও হতে পারে - মূল বিষয়গুলো ধরুন এবং তারপরে ফিরে আসুন ফ্রেমওয়ার্কে! আপনি [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript), [HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML) এবং [CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps) এর জন্য এই ওভারভিউ দিয়ে আপনার জ্ঞানের স্তর পরীক্ষা করতে পারেন প্রয়োজন হলে। অন্যান্য ফ্রেমওয়ার্কের সাথে পূর্বের অভিজ্ঞতা সাহায্য করে, কিন্তু প্রয়োজন হয় না।
:::

## একটি প্রগতিশীল ফ্রেমওয়ার্ক {#the-progressive-framework}

Vue হল একটি ফ্রেমওয়ার্ক এবং ইকোসিস্টেম যা ফ্রন্টএন্ড ডেভেলপমেন্টে প্রয়োজনীয় বেশিরভাগ সাধারণ বৈশিষ্ট্যগুলিকে কভার করে। কিন্তু ওয়েব অত্যন্ত বৈচিত্র্যময় - আমরা ওয়েবে যে জিনিসগুলি তৈরি করি তা আকার এবং স্কেলে ব্যাপকভাবে পরিবর্তিত হতে পারে। এটি মাথায় রেখে, Vue নমনীয় এবং ক্রমবর্ধমানভাবে গ্রহণযোগ্য হওয়ার জন্য ডিজাইন করা হয়েছে। আপনার ব্যবহারের ক্ষেত্রে নির্ভর করে, Vue বিভিন্ন উপায়ে ব্যবহার করা যেতে পারে:

- বিল্ড স্টেপ ছাড়াই স্ট্যাটিক এইচটিএমএল উন্নত করা
- যেকোন পৃষ্ঠায় ওয়েব কম্পোনেন্ট হিসেবে এম্বেড করা
- একক পৃষ্ঠার অ্যাপ্লিকেশন (SPA)
- ফুলস্ট্যাক / সার্ভার-সাইড রেন্ডারিং (SSR)
- জ্যামস্ট্যাক / স্ট্যাটিক সাইট জেনারেশন (SSG)
- ডেস্কটপ, মোবাইল, ওয়েবজিএল, এমনকি টার্মিনালকে টার্গেট করা

আপনি যদি এই বিষয় দেখে আতঙ্কিত হন তাহলে একদমই চিন্তা করবেন না! টিউটোরিয়াল এবং গাইডের জন্য শুধুমাত্র মৌলিক HTML এবং JavaScript জ্ঞানের প্রয়োজন, এবং আপনি এইগুলির মধ্যে একজন বিশেষজ্ঞ না হয়েও অনুসরণ করতে সক্ষম হবেন।

আপনি যদি একজন অভিজ্ঞ ডেভেলপার হন এবং কীভাবে আপনার স্ট্যাকের মধ্যে Vue কে সর্বোত্তমভাবে ব্যবহার করা যায় সে বিষয়ে আগ্রহী হন, অথবা আপনি এই শর্তাবলীর অর্থ কী তা নিয়ে কৌতূহলী হন, তাহলে আমরা সেগুলিকে [Vue ব্যবহার করার উপায়](/guide/extras/ways-of-using-vue) এ আরও বিশদে আলোচনা করি।

ফ্লেক্সিবল সত্ত্বেও, Vue কীভাবে কাজ করে সে সম্পর্কে মূল জ্ঞান এই সমস্ত ব্যবহারের ক্ষেত্রে ভাগ করা হয়। এমনকি আপনি যদি এখন মাত্র একজন শিক্ষানবিস হয়ে থাকেন, তবে ভবিষ্যতে আরও উচ্চাভিলাষী লক্ষ্যগুলি মোকাবেলা করার জন্য আপনার বেড়ে ওঠার সাথে সাথে অর্জিত জ্ঞান কার্যকর থাকবে। আপনি যদি একজন অভিজ্ঞ হন, তাহলে একই উৎপাদনশীলতা বজায় রেখে আপনি যে সমস্যার সমাধান করার চেষ্টা করছেন তার উপর ভিত্তি করে Vue-এর সুবিধা নেওয়ার সর্বোত্তম উপায় বেছে নিতে পারেন। এই কারণেই আমরা Vue কে "প্রগ্রেসিভ ফ্রেমওয়ার্ক" বলি: এটি এমন একটি কাঠামো যা আপনার সাথে বৃদ্ধি পেতে পারে এবং আপনার প্রয়োজনের সাথে খাপ খাইয়ে নিতে পারে৷

## একক ফাইল কম্পোনেন্ট {#single-file-components}

বেশিরভাগ বিল্ড-টুল-সক্ষম Vue প্রোজেক্টে, আমরা **একক-ফাইল কম্পোনেন্ট** নামে একটি HTML-এর মতো ফাইল ফরম্যাট ব্যবহার করে Vue কম্পোনেন্টগুলি লিখি (যা `*.vue` ফাইল নামেও পরিচিত, সংক্ষেপে **SFC**)। একটি Vue SFC, নাম অনুসারে, একটি একক ফাইলে কম্পোনেন্টের যুক্তি (জাভাস্ক্রিপ্ট), টেমপ্লেট (এইচটিএমএল) এবং স্টাইল (সিএসএস) অন্তর্ভুক্ত করে। এখানে পূর্ববর্তী উদাহরণ, SFC বিন্যাসে লেখা:

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

</div>

SFC হল Vue-এর একটি সংজ্ঞায়িত বৈশিষ্ট্য এবং একজন কোডারের Vue কম্পোনেন্টগুলি লেখার জন্য প্রস্তাবিত উপায় **যদি** আপনার ব্যবহারের ক্ষেত্রে একটি বিল্ড সেটআপ দরকার পরে। আপনি এর ডেডিকেটেড বিভাগে [কীভাবে এবং কেন SFC](/guide/scaling-up/sfc) সম্পর্কে আরও বিস্তারিতভাবে জানতে পারেন - কিন্তু আপাতত, শুধু জেনে রাখুন যে Vue আপনার জন্য সমস্ত বিল্ড টুল সেটআপ পরিচালনা করবে।

## এপিআই ধরন (API Styles) {#api-styles}

Vue কম্পোনেন্ট দুটি ভিন্ন API ধরনে ব্যবহার হতে পারে: **Options API** এবং **Composition API**.

### অপশনস এপিআই (Options API) {#options-api}

Options API এর সাহায্যে, আমরা একটি কম্পোনেন্টের যুক্তি সংজ্ঞায়িত করি যাতে অপশনের একটি অবজেক্ট যেমন `data`, `methods`, এবং `mounted` ব্যবহার করে। কম্পোনেন্টগুলির দ্বারা সংজ্ঞায়িত বৈশিষ্ট্যগুলি `this` ফাংশনে উন্মুক্ত করা হয়, যা কম্পোনেন্ট উদাহরণের দিকে নির্দেশ করে।

```vue
<script>
export default {
  // data() থেকে প্রত্যাবর্তিত বৈশিষ্ট্যগুলি প্রতিক্রিয়াশীল অবস্থায় পরিণত হয়
  // এবং `this`-তে প্রকাশ করা হবে।
  data() {
    return {
      count: 0
    }
  },

  // মেথড হলো সেই ফাংশন যা স্থিতি পরিবর্তন করে এবং আপডেটগুলি ট্রিগার করে
  // এটি টেমপ্লেটে ইভেন্ট হ্যান্ডলার হিসেবে বাইন্ড করা যেতে পারে
  methods: {
    increment() {
      this.count++
    }
  },

  // লাইফসাইকেল হুকগুলিকে বিভিন্ন পর্যায়ে ডাকা হয়
  // একটি কম্পোনেন্টের লাইফসাইকেলের।
  // যখন কম্পোনেন্ট মাউন্ট করা হয় তখন এই ফাংশনটি কল করা হবে।
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNptkMFqxCAQhl9lkB522ZL0HNKlpa/Qo4e1ZpLIGhUdl5bgu9es2eSyIMio833zO7NP56pbRNawNkivHJ25wV9nPUGHvYiaYOYGoK7Bo5CkbgiBBOFy2AkSh2N5APmeojePCkDaaKiBt1KnZUuv3Ky0PppMsyYAjYJgigu0oEGYDsirYUAP0WULhqVrQhptF5qHQhnpcUJD+wyQaSpUd/Xp9NysVY/yT2qE0dprIS/vsds5Mg9mNVbaDofL94jZpUgJXUKBCvAy76ZUXY53CTd5tfX2k7kgnJzOCXIF0P5EImvgQ2olr++cbRE4O3+t6JxvXj0ptXVpye1tvbFY+ge/NJZt)

### কম্পোজিশন এপিআই (Composition API) {#composition-api}

Composition API-এর সাহায্যে, আমরা import করা API ফাংশন ব্যবহার করে একটি কম্পোনেন্টের যুক্তি সংজ্ঞায়িত করি। SFC-তে, Composition API সাধারণত [`<script setup>`](/api/sfc-script-setup) এর সাথে ব্যবহার করা হয়। `setup` অ্যাট্রিবিউট হল একটি ইঙ্গিত যা Vue কে কম্পাইল-টাইম ট্রান্সফর্ম করে যা আমাদের কম বয়লারপ্লেটের সাথে Composition API ব্যবহার করতে দেয়। উদাহরণ স্বরূপ, `<script setup>`-এ imports এবং শীর্ষ-স্তরের ভেরিয়েবল/ফাংশন সরাসরি টেমপ্লেটে ব্যবহারযোগ্য।

এখানে একই কম্পোনেন্ট, ঠিক একই টেমপ্লেট সহ, কিন্তু Composition API এবং `<script setup>` ব্যবহার করে এর পরিবর্তে:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// প্রতিক্রিয়াশীল অবস্থা
const count = ref(0)

// যে ফাংশনগুলি স্থিতি পরিবর্তন করে এবং আপডেটগুলি ট্রিগার করে
function increment() {
  count.value++
}

// লাইফসাইকেলের হুক
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNpNkMFqwzAQRH9lMYU4pNg9Bye09NxbjzrEVda2iLwS0spQjP69a+yYHnRYad7MaOfiw/tqSliciybqYDxDRE7+qsiM3gWGGQJ2r+DoyyVivEOGLrgRDkIdFCmqa1G0ms2EELllVKQdRQa9AHBZ+PLtuEm7RCKVd+ChZRjTQqwctHQHDqbvMUDyd7mKip4AGNIBRyQujzArgtW/mlqb8HRSlLcEazrUv9oiDM49xGGvXgp5uT5his5iZV1f3r4HFHvDprVbaxPhZf4XkKub/CDLaep1T7IhGRhHb6WoTADNT2KWpu/aGv24qGKvrIrr5+Z7hnneQnJu6hURvKl3ryL/ARrVkuI=)

### কোনটি বেছে নেবেন? {#which-to-choose}

উভয় API ধরনই সাধারণভাবে ব্যবহারের ক্ষেত্রে সম্পূর্ণরূপে কভার করতে সক্ষম। এগুলি ঠিক একই অন্তর্নিহিত সিস্টেম দ্বারা চালিত বিভিন্ন ইন্টারফেস। Options API আসলে Composition API এর উপরেই প্রয়োগ করা হয়! Vue সম্পর্কে মৌলিক ধারণা এবং জ্ঞান দুটি ধরনে ভাগ করা যায়।

Options API মূলত " কম্পোনেন্ট ইন্সট্যান্স" এর ধারণাকে কেন্দ্র করে (উদাহরণে দেখানো হয়েছে `this`) তৈরি, যা সাধারণত অবজেক্ট ওরিয়েন্টেড প্রোগ্রামিং (OOP) ব্যাকগ্রাউন্ড থেকে আগত ব্যবহারকারীদের জন্য একটি শ্রেণি-ভিত্তিক মানসিক মডেলের সাথে আরও ভালোভাবে সারিবদ্ধ করে। এটি আরও শিক্ষানবিস-বান্ধব কারণ এটি রিঅ্যাকটিভিটির বিশদ ঢেকে রাখে এবং options group-এর মাধ্যমে কোড সংগঠিত রাখে।

Composition API মূলত একটি ফাংশন স্কোপে সরাসরি প্রতিক্রিয়াশীল স্টেট ভেরিয়েবল ঘোষণা করে এবং জটিলতা সামলাতে একসাথে একাধিক ফাংশন থেকে স্টেট রচনা করাকে কেন্দ্র করে তৈরি। এটি আরও ফ্রি-ফর্ম এবং এটি কার্যকরভাবে ব্যবহার করার জন্য Vue-তে প্রতিক্রিয়া কীভাবে কাজ করে তা বোঝা প্রয়োজন। বিনিময়ে, এর ফ্লেক্সিবল যুক্তি সংগঠন এবং পুনঃব্যবহারের জন্য আরও শক্তিশালী প্যাটার্ন প্রদান করে।

আপনি দুটি ধরন এর মধ্যে তুলনা এবং Composition API-এর সম্ভাব্য সুবিধাগুলি সম্পর্কে আরও জানতে পারেন৷ [Composition API এর প্রায়শই জিজ্ঞাসিত প্রশ্ন](/guide/extras/composition-api-faq).

আপনি যদি Vue-তে নতুন হন, তাহলে এখানে আমাদের সাধারণ সুপারিশ রয়েছে:

- শেখার উদ্দেশ্যে, এমন স্টাইল নিয়ে ব্যবহার করুন যা আপনার কাছে বুঝতে সহজ মনে হয়। আবার, বেশিরভাগ মূল ধারণাই দুটি ধরন এর মধ্যে একই ভাবে কাজ করে। আপনি সবসময়ই পরবর্তীতে স্টাইল ব্যবহার করতে পারেন।

- উৎপাদনে ব্যবহারের জন্য:

  - আপনি যদি বিল্ড টুল ব্যবহার না করেন, অথবা কম-জটিল পরিস্থিতিতে প্রাথমিকভাবে Vue ব্যবহার করার পরিকল্পনা করেন, তাহলে Options API-এর সাথে যান, যেমন প্রগতিশীল বর্ধিতকরণ.

  - আপনি যদি Vue এর সাথে সম্পূর্ণ অ্যাপ্লিকেশন তৈরি করার পরিকল্পনা করেন তবে Composition API + একক-ফাইল কম্পোনেন্টগুলির সাথে যান৷

শেখার পর্যায়ে আপনাকে শুধুমাত্র একটি স্টাইলে প্রতিশ্রুতিবদ্ধ করতে হবে না। বাকি ডকুমেন্টেশনগুলি যেখানে প্রযোজ্য সেখানে উভয় স্টাইলে কোড নমুনা প্রদান করবে এবং আপনি বাম সাইডবারের শীর্ষে **API Preference switches** ব্যবহার করে যে কোনো সময় তাদের মধ্যে পরিবর্তন করতে পারেন।

## এখনও প্রশ্ন আছে? {#still-got-questions}

এখানে দেখুন [প্রায়শই জিজ্ঞাসিত প্রশ্ন](/about/faq).

## আপনার শেখার পথ বেছে নিন {#pick-your-learning-path}

বিভিন্ন ডেভেলপারদের বিভিন্ন শেখার স্টাইল রয়েছে। আপনার পছন্দ অনুসারে একটি শেখার পথ নির্দ্বিধায় বেছে নিন - যদিও আমরা সম্ভব হলে সমস্ত বিষয়অবজেক্টতে যাওয়ার পরামর্শ দিই!

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">টিউটোরিয়াল ব্যবহার করে দেখুন</p>
    <p class="next-steps-caption">যারা হাতে-কলমে শিখতে পছন্দ করেন তাদের জন্য।</p>
  </a>
  <a class="vt-box" href="/guide/quick-start.html">
    <p class="next-steps-link">গাইড পড়ুন</p>
    <p class="next-steps-caption">গাইড আপনাকে সম্পূর্ণ বিশদে ফ্রেমওয়ার্কের প্রতিটি দিক দিয়ে চলে।</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">উদাহরণ দেখুন</p>
    <p class="next-steps-caption">মূল বৈশিষ্ট্য এবং সাধারণ UI কার্যগুলির উদাহরণগুলি অন্বেষণ করুন৷</p>
  </a>
</div>
