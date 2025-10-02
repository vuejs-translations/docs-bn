---
footer: false
---

<script setup>
import { VTCodeGroup, VTCodeGroupTab } from '@vue/theme'
</script>

# দ্রুত শুরু করার নির্দেশাবলী {#quick-start}

## Vue অনলাইন চেষ্টা করুন {#try-vue-online}

- দ্রুত Vue ব্যবহার করতে, আপনি এটি সরাসরি আমাদের এখানে চেষ্টা করতে পারেন [প্লেগ্রাউন্ড](https://play.vuejs.org/#eNo9jcEKwjAMhl/lt5fpQYfXUQfefAMvvRQbddC1pUuHUPrudg4HIcmXjyRZXEM4zYlEJ+T0iEPgXjn6BB8Zhp46WUZWDjCa9f6w9kAkTtH9CRinV4fmRtZ63H20Ztesqiylphqy3R5UYBqD1UyVAPk+9zkvV1CKbCv9poMLiTEfR2/IXpSoXomqZLtti/IFwVtA9A==)।

- আপনি যদি কোনো বিল্ড স্টেপ ছাড়াই একটি প্লেইন HTML সেটআপ পছন্দ করেন, তাহলে আপনি এটি [জেএসফিডল](https://jsfiddle.net/yyx990803/2ke1ab0z/) আপনার শুরুর পয়েন্ট হিসেবে ব্যবহার করতে পারেন।

- আপনি যদি ইতিমধ্যেই Node.js এবং বিল্ড টুলের ধারণার সাথে পরিচিত হন, তাহলে আপনি [স্ট্যাকব্লিটজ](https://vite.new/vue) এর মাধ্যমে আপনার ব্রাউজারের মধ্যেই একটি সম্পূর্ণ বিল্ড সেটআপ করার চেষ্টা করতে পারেন।

- প্রস্তাবিত সেটআপের একটি ওয়াকথ্রু পেতে, এই ইন্টারেক্টিভ [Scrimba](http://scrimba.com/links/vue-quickstart) টিউটোরিয়ালটি দেখুন যা আপনাকে দেখায় যে কীভাবে আপনার প্রথম Vue অ্যাপটি চালাবেন, সম্পাদনা করবেন এবং স্থাপন করবেন।

## একটি Vue অ্যাপটি তৈরি করুন {#creating-a-vue-application}

:::tip পূর্বশর্ত

- কমান্ড লাইনের সাথে পরিচিতি
- [Node.js](https://nodejs.org/) সংস্করণ `^20.19.0 || >=22.12.0` ইনস্টল করুন
  :::

এই বিভাগে আমরা আপনার local মেশিনে কীভাবে একটি Vue [একক পৃষ্ঠা অ্যাপ্লিকেশন](/guide/extras/ways-of-using-vue#single-page-application-spa) স্ক্যাফল্ড করবেন তার সাথে পরিচয় করিয়ে দিব। তৈরি করা প্রোজেক্টটি [Vite](https://vitejs.dev) এর উপর ভিত্তি করে একটি বিল্ড সেটআপ ব্যবহার করবে এবং আমাদেরকে Vue [Single-File Components](/guide/scaling-up/sfc) (SFCs) ব্যবহার করার অনুমতি দিবে।

নিশ্চিত করুন যে আপনার কাছে [Node.js](https://nodejs.org/) এর একটি আপ-টু-ডেট সংস্করণ ইনস্টল করা আছে এবং আপনার বর্তমান কার্যকারী ডিরেক্টরিটি যেখানে আপনি একটি প্রকল্প তৈরি করতে চান। আপনার কমান্ড লাইনে নিম্নলিখিত কমান্ডটি চালান (`$` চিহ্ন ছাড়া):

::: code-group

```sh [npm]
$ npm create vue@latest
```

```sh [pnpm]
$ pnpm create vue@latest
```

```sh [yarn]
# For Yarn (v1+)
$ yarn create vue

# For Yarn Modern (v2+)
$ yarn create vue@latest
  
# For Yarn ^v4.11
$ yarn dlx create-vue@latest
```

```sh [bun]
$ bun create vue@latest
```
:::

এই কমান্ডটি [create-vue](https://github.com/vuejs/create-vue), অফিসিয়াল Vue প্রজেক্ট স্ক্যাফোল্ডিং টুল ইনস্টল এবং কার্যকর করবে। টাইপস্ক্রিপ্ট এবং টেস্টিং সাপোর্টের মতো কয়েকটি ঐচ্ছিক বৈশিষ্ট্যের জন্য আপনাকে প্রম্পট দেওয়া হবে:

<div class="language-sh"><pre><code><span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Project name: <span style="color:#888;">… <span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add TypeScript? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add JSX Support? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue Router for Single Page Application development? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Pinia for state management? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vitest for Unit testing? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add an End-to-End Testing Solution? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Cypress / Nightwatch / Playwright</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add ESLint for code quality? <span style="color:#888;">… No / <span style="color:#89DDFF;text-decoration:underline">Yes</span></span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Prettier for code formatting? <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span style="color:var(--vt-c-green);">✔</span> <span style="color:#A6ACCD;">Add Vue DevTools 7 extension for debugging? (experimental) <span style="color:#888;">… <span style="color:#89DDFF;text-decoration:underline">No</span> / Yes</span></span>
<span></span>
<span style="color:#A6ACCD;">Scaffolding project in ./<span style="color:#89DDFF;">&lt;</span><span style="color:#888;">your-project-name</span><span style="color:#89DDFF;">&gt;</span>...</span>
<span style="color:#A6ACCD;">Done.</span></code></pre></div>

আপনি যদি একটি বিকল্প সম্পর্কে অনিশ্চিত হন, তাহলে এখনকার জন্য এন্টার বাটন টিপে শুধু `No` নির্বাচন করুন। প্রকল্পটি তৈরি হয়ে গেলে, আরও ডিপেন্ডেন্সি ইনস্টল করতে এবং ডেভ সার্ভার শুরু করতে নির্দেশাবলী অনুসরণ করুন:

::: code-group

```sh-vue [npm]
$ cd {{'<your-project-name>'}}
$ npm install
$ npm run dev
```

```sh-vue [pnpm]
$ cd {{'<your-project-name>'}}
$ pnpm install
$ pnpm run dev
```

```sh-vue [yarn]
$ cd {{'<your-project-name>'}}
$ yarn
$ yarn dev
```

```sh-vue [bun]
$ cd {{'<your-project-name>'}}
$ bun install
$ bun run dev
```

:::


আপনার প্রথম Vue প্রকল্পটি এখন চালু হয়ে যাওয়া উচিত! মনে রাখবেন যে উৎপন্ন প্রকল্পের উদাহরণ হিসেবে তৈরি হওয়া কম্পোনেন্টগুলি [Options API](/guide/introduction#options)-এর পরিবর্তে [Composition API](/guide/introduction#composition-api) এবং `<script setup>` ব্যবহার করে লেখা হয়েছে। এখানে কিছু অতিরিক্ত টিপস আছে:

- প্রস্তাবিত IDE সেটআপ হল [ভিজ্যুয়াল স্টুডিও কোড](https://code.visualstudio.com/) + [Vue - অফিসিয়াল এক্সটেনশন](https://marketplace.visualstudio.com/items?itemName=Vue.volar)। আপনি যদি অন্য এডিটর ব্যবহার করেন তবে [IDE সমর্থন বিভাগ](/guide/scaling-up/tooling#ide-support) দেখুন।
- ব্যাকএন্ড ফ্রেমওয়ার্কের সাথে ইন্টিগ্রেশন সহ আরও টুলিং বিশদ [টুলিং গাইড](/guide/scaling-up/tooling) এ আলোচনা করা হয়েছে।
- অন্তর্নিহিত বিল্ড টুল Vite সম্পর্কে আরও জানতে, [Vite docs](https://vitejs.dev) দেখুন।
- আপনি যদি টাইপস্ক্রিপ্ট ব্যবহার করতে চান তবে [টাইপস্ক্রিপ্ট ব্যবহার নির্দেশিকা](typescript/overview) দেখুন।

আপনি যখন আপনার অ্যাপটিকে প্রোডাকশনে পাঠানোর জন্য প্রস্তুত হন, তখন নিম্নলিখিতটি চালান:

::: code-group

```sh [npm]
$ npm run build
```

```sh [pnpm]
$ pnpm run build
```

```sh [yarn]
$ yarn build
```

```sh [bun]
$ bun run build
```

:::


এটি প্রোজেক্টের `./dist` ডিরেক্টরিতে আপনার অ্যাপের একটি প্রোডাকশন-রেডি বিল্ড তৈরি করবে। আপনার অ্যাপকে প্রোডাকশনে পাঠানোর বিষয়ে আরও জানতে [প্রোডাকশন ডিপ্লয়মেন্ট গাইড](/guide/best-practices/production-deployment) দেখুন।

[পরবর্তী ধাপ >](#next-steps)

## CDN থেকে Vue ব্যবহার করা {#using-vue-from-cdn}

আপনি একটি স্ক্রিপ্ট ট্যাগের মাধ্যমে সরাসরি CDN থেকে Vue ব্যবহার করতে পারেন:

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
```

এখানে আমরা [unpkg](https://unpkg.com/) ব্যবহার করছি, কিন্তু আপনি যে কোনো CDN ব্যবহার করতে পারেন যা npm প্যাকেজ পরিবেশন করে, উদাহরণস্বরূপ [jsdelivr](https://www.jsdelivr.com/package/npm/vue) অথবা [cdnjs](https://cdnjs.com/libraries/vue)। অবশ্যই, আপনি এই ফাইলটি ডাউনলোড করতে পারেন এবং এটি নিজে ব্যবহার করতে পারেন।

একটি CDN থেকে Vue ব্যবহার করার সময়, কোন "বিল্ড স্টেপ" জড়িত থাকে না। এটি সেটআপটিকে অনেক সহজ করে তোলে এবং স্ট্যাটিক HTML বাড়ানো বা ব্যাকএন্ড ফ্রেমওয়ার্কের সাথে ব্যবহার করার জন্য উপযুক্ত। যাইহোক, আপনি একক-ফাইল কম্পোনেন্ট (SFC) সিনট্যাক্স ব্যবহার করতে পারবেন না।

### গ্লোবাল বিল্ড ব্যবহার করে {#using-the-global-build}

উপরের লিঙ্কটি Vue-এর _global build_ লোড করে, যেখানে সমস্ত টপ-লেভেল এপিআইগুলো গ্লোবাল `Vue` অবজেক্টের বৈশিষ্ট্য হিসেবে উন্মুক্ত হয়। এখানে গ্লোবাল বিল্ড ব্যবহার করে একটি সম্পূর্ণ উদাহরণ রয়েছে:

<div class="options-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp } = Vue

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/QWJwJLp)

</div>

<div class="composition-api">

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue

  createApp({
    setup() {
      const message = ref('Hello vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/eYQpQEG)

:::tip
গাইডের সাথে সংযোজিত বেশিরভাগ উদাহরণগুলো `<script setup>` সিনট্যাক্স সম্বলিত, যার জন্য বিল্ড টুলস অবশ্যই প্রয়োজনীয়। আপনি যদি বিল্ডের ধাপ ছাড়াই Composition API ব্যবহার করতে চান তাহলে [`setup()` option](/api/composition-api-setup)-এর ব্যবহার দেখুন।
:::

</div>

### ES মডিউল বিল্ড ব্যবহার করে {#using-the-es-module-build}

বাকি ডকুমেন্টেশন জুড়ে, আমরা প্রাথমিকভাবে [ইএস মডিউল](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) সিনট্যাক্স ব্যবহার করব। বেশিরভাগ আধুনিক ব্রাউজার এখন সাধারণত ইএস মডিউলগুলিকে সমর্থন করে, তাই আমরা একটি CDN থেকে Vue ব্যবহার করতে পারি নেটিভ ইএস মডিউলগুলির মাধ্যমে:

<div class="options-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

</div>

<div class="composition-api">

```html{3,4}
<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'https://unpkg.com/vue@3/dist/vue.esm-browser.js'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

</div>

লক্ষ্য করুন যে আমরা `<script type="module">` ব্যবহার করছি, এবং imported CDN URL এর পরিবর্তে Vue-এর **ইএস মডিউল বিল্ড**-এর দিকে নির্দেশ করছে।

<div class="options-api">

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/VwVYVZO)

</div>
<div class="composition-api">

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/MWzazEv)

</div>

### ইম্পোর্ট ম্যাপস ব্যবহার করে {#enabling-import-maps}

উপরের উদাহরণে, আমরা সম্পূর্ণ CDN URL থেকে import করছি, কিন্তু বাকি ডকুমেন্টেশনে আপনি এইরকম কোড দেখতে পাবেন:

```js
import { createApp } from 'vue'
```

আমরা ব্রাউজারকে শেখাতে পারি কোথায় `vue` ইম্পোর্ট ব্যবহার করে সনাক্ত করতে হয় [Import Maps](https://caniuse.com/import-maps):

<div class="options-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/wvQKQyM)

</div>

<div class="composition-api">

```html{1-7,12}
<script type="importmap">
  {
    "imports": {
      "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
    }
  }
</script>

<div id="app">{{ message }}</div>

<script type="module">
  import { createApp, ref } from 'vue'

  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return {
        message
      }
    }
  }).mount('#app')
</script>
```

[CodePen Demo >](https://codepen.io/vuejs-examples/pen/YzRyRYM)

</div>

আপনি import maps এর অন্যান্য নির্ভরতার জন্য এন্ট্রি যোগ করতে পারেন - তবে নিশ্চিত করুন যে তারা লাইব্রেরির ইএস মডিউল সংস্করণের দিকে নির্দেশ করে যা আপনি ব্যবহার করতে চান।

:::tip ইম্পোর্ট ম্যাপস (Import Maps) ব্রাউজার সমর্থন
ইম্পোর্ট ম্যাপস একটি অপেক্ষাকৃত নতুন ব্রাউজার বৈশিষ্ট্য। একটি ব্রাউজার এর [সমর্থন পরিসর](https://caniuse.com/import-maps) এর মধ্যে ব্যবহারযোগ্য কিনা নিশ্চিত করুন। বিশেষ করে, এটি শুধুমাত্র Safari 16.4+ এ সমর্থিত।
:::

:::warning প্রোডাকশন এ ব্যবহার এর নোট
এখন পর্যন্ত উদাহরণগুলি হল Vue-এর ডেভেলপমেন্ট বিল্ড ব্যবহার করা - আপনি যদি প্রোডাকশনে CDN থেকে Vue ব্যবহার করতে চান, তবে [Production Deployment Guide](/guide/best-practices/production-deployment#without-build-tools) দেখে নিন।

যদিও বিল্ড সিস্টেম ছাড়াই Vue ব্যবহার করা সম্ভব, বিবেচনা করার জন্য একটি বিকল্প পদ্ধতি হল [`vuejs/petite-vue`](https://github.com/vuejs/petite-vue) ব্যবহার করা যা প্রেক্ষাপটের সাথে আরও ভালভাবে মানানসই হতে পারে [`jquery/jquery`](https://github.com/jquery/jquery) (অতীতে) বা [`alpinejs/alpine`](https://github.com/alpinejs/alpine) (বর্তমানে ) পরিবর্তে ব্যবহার করা যেতে পারে।
:::

### মডিউলগুলি বিভক্ত করা {#splitting-up-the-modules}

আমরা গাইডের আরও গভীরে ডুব দেওয়ার সাথে সাথে আমাদের কোড আলাদা জাভাস্ক্রিপ্ট ফাইলগুলিতে বিভক্ত করতে হতে পারে যাতে সেগুলি পরিচালনা করা সহজ হয়। উদাহরণ স্বরূপ:

```html [index.html]
<div id="app"></div>

<script type="module">
  import { createApp } from 'vue'
  import MyComponent from './my-component.js'

  createApp(MyComponent).mount('#app')
</script>
```

<div class="options-api">

```js [my-component.js]
export default {
  data() {
    return { count: 0 }
  },
  template: `<div>Count is: {{ count }}</div>`
}
```

</div>
<div class="composition-api">

```js [my-component.js]
import { ref } from 'vue'
export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `<div>Count is: {{ count }}</div>`
}
```

</div>

আপনি যদি সরাসরি আপনার ব্রাউজারে উপরের `index.html` খোলেন, তাহলে আপনি একটি এরর দেখতে পাবেন কারণ ES মডিউলগুলি `file://` প্রোটোকলের উপর কাজ করতে পারে না, যেটি লোকাল ফাইল খোলার জন্য ব্রাউজার ব্যবহৃত প্রোটোকল।

নিরাপত্তাজনিত কারণে, ES মডিউলগুলি শুধুমাত্র `http://` প্রোটোকলের উপর কাজ করতে পারে, যা ওয়েবে পৃষ্ঠা খোলার সময় ব্রাউজার ব্যবহার করে। আমাদের লোকাল মেশিনে ইএস মডিউলগুলি কাজ করার জন্য, আমাদের একটি local HTTP সার্ভারের সাথে `http://` প্রোটোকলের উপর `index.html` পরিবেশন করতে হবে।

একটি local HTTP সার্ভার শুরু করতে, প্রথমে নিশ্চিত করুন যে আপনি [Node.js](https://nodejs.org/en/) ইনস্টল করেছেন, তারপরে আপনার HTML ফাইলটি যেখানে একই ডিরেক্টরিতে কমান্ড লাইন থেকে `npx serve` চালান। আপনি অন্য কোনো HTTP সার্ভারও ব্যবহার করতে পারেন যা সঠিক MIME প্রকারের সাথে স্ট্যাটিক ফাইল পরিবেশন করতে পারে।

আপনি হয়তো লক্ষ্য করেছেন যে আমদানি করা কম্পোনেন্টস টেমপ্লেটটি জাভাস্ক্রিপ্ট স্ট্রিং হিসাবে ইনলাইন করা হয়েছে। আপনি যদি VS কোড ব্যবহার করেন, তাহলে আপনি [es6-string-html](https://marketplace.visualstudio.com/items?itemName=Tobermory.es6-string-html) এক্সটেনশনটি ইনস্টল করতে পারেন এবং একটি মন্তব্য সহ `/*html*/` স্ট্রিংগুলি প্রিফিক্স করতে পারেন তাদের জন্য সিনট্যাক্স হাইলাইটিং পান।

## Next Steps {#next-steps}

আপনি যদি [ভূমিকা](/guide/introduction) এড়িয়ে যান, তাহলে বাকি ডকুমেন্টেশনে যাওয়ার আগে আমরা দৃঢ়ভাবে এটি পড়ার পরামর্শ দিচ্ছি।

<div class="vt-box-container next-steps">
  <a class="vt-box" href="/guide/essentials/application.html">
    <p class="next-steps-link">গাইডের সাথে চালিয়ে যান</p>
    <p class="next-steps-caption">গাইড আপনাকে সম্পূর্ণ বিশদে ফ্রেমওয়ার্কের প্রতিটি দিক দিয়ে নিয়ে যাবে।</p>
  </a>
  <a class="vt-box" href="/tutorial/">
    <p class="next-steps-link">টিউটোরিয়াল ব্যবহার করে দেখুন</p>
    <p class="next-steps-caption">যারা হাতে-কলমে শিখতে পছন্দ করেন তাদের জন্য।</p>
  </a>
  <a class="vt-box" href="/examples/">
    <p class="next-steps-link">উদাহরণ দেখুন</p>
    <p class="next-steps-caption">মূল বৈশিষ্ট্য এবং সাধারণ UI কার্যগুলির উদাহরণগুলি অন্বেষণ করুন৷</p>
  </a>
</div>
