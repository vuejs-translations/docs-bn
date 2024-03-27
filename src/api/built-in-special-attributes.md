# Built-in Special Attributes {#built-in-special-attributes}

## key {#key}

`key` বিশেষ বৈশিষ্ট্যটি প্রাথমিকভাবে Vue-এর ভার্চুয়াল DOM অ্যালগরিদমের জন্য একটি ইঙ্গিত হিসাবে ব্যবহৃত হয় যাতে পুরানো তালিকার বিপরীতে নোডের নতুন তালিকার পার্থক্য করার সময় vnodes সনাক্ত করা হয়।

- **Expects:** `number | string | symbol`

- ** বিস্তারিত**

  কী ব্যতীত, Vue একটি অ্যালগরিদম ব্যবহার করে যা কম্পোনেন্টস গতিবিধি কমিয়ে দেয় এবং যতটা সম্ভব একই ধরণের কম্পোনেন্টস প্যাচ/পুনঃব্যবহার করার চেষ্টা করে। কীগুলির সাহায্যে, এটি কীগুলির ক্রম পরিবর্তনের উপর ভিত্তি করে কম্পোনেন্টগুলিকে পুনর্বিন্যাস করবে এবং কীগুলির সাথে যে কম্পোনেন্টগুলি আর উপস্থিত থাকবে না সেগুলি সর্বদা সরানো/ধ্বংস করা হবে৷

  একই অভিভাবকের সন্তানদের অবশ্যই **অনন্য কী** থাকতে হবে। ডুপ্লিকেট কী রেন্ডার ত্রুটির কারণ হবে।

  সবচেয়ে সাধারণ ব্যবহারের ক্ষেত্রে `v-for` এর সাথে মিলিত হয়:

  ```vue-html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  এটি পুনঃব্যবহারের পরিবর্তে একটি কম্পোনেন্ট/ কম্পোনেন্টকে জোর করে প্রতিস্থাপন করতেও ব্যবহার করা যেতে পারে। আপনি যখন চান তখন এটি কার্যকর হতে পারে:

  - সঠিকভাবে একটি কম্পোনেন্টস লাইফসাইকেল হুক ট্রিগার
  - ট্রিগার ট্রানজিশন

  উদাহরণ স্বরূপ:

  ```vue-html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  যখন `text` পরিবর্তিত হয়, `<span>` সর্বদা প্যাচের পরিবর্তে প্রতিস্থাপিত হবে, তাই একটি রূপান্তর ট্রিগার হবে।

- **আরো দেখুন** [Guide - List Rendering - Maintaining State with `key`](/guide/essentials/list#maintaining-state-with-key)

## ref {#ref}

Denotes a [template ref](/guide/essentials/template-refs).

- **Expects:** `string | Function`

- ** বিস্তারিত**

  একটি কম্পোনেন্ট বা একটি  চাইল্ড কম্পোনেন্টস একটি রেফারেন্স নিবন্ধন করতে `ref` ব্যবহার করা হয়।

  অপশন এপিআই-এ, রেফারেন্সটি কম্পোনেন্টের `this.$refs` অবজেক্টের অধীনে নিবন্ধিত হবে:

  ```vue-html
  <!-- stored as this.$refs.p -->
  <p ref="p">hello</p>
  ```

  কম্পোজিশন এপিআই-তে, রেফারেন্সটি মিলিত নামের সাথে একটি রেফারেন্সে সংরক্ষণ করা হবে:

  ```vue
  <script setup>
  import { ref } from 'vue'

  const p = ref()
  </script>

  <template>
    <p ref="p">hello</p>
  </template>
  ```

  একটি সাধারণ DOM কম্পোনেন্টে ব্যবহার করা হলে, রেফারেন্সটি সেই কম্পোনেন্ট হবে; একটি  চাইল্ড কম্পোনেন্ট ব্যবহার করা হলে, রেফারেন্স  চাইল্ড কম্পোনেন্ট উদাহরণ হবে.

  বিকল্পভাবে `ref` একটি ফাংশন মান গ্রহণ করতে পারে যা রেফারেন্স কোথায় সংরক্ষণ করতে হবে তার উপর সম্পূর্ণ নিয়ন্ত্রণ প্রদান করে:

  ```vue-html
  <ChildComponent :ref="(el) => child = el" />
  ```

  রেফ রেজিস্ট্রেশনের সময় সম্পর্কে একটি গুরুত্বপূর্ণ নোট: যেহেতু রেন্ডার ফাংশনের ফলে রেফগুলি নিজেই তৈরি হয়, সেগুলি অ্যাক্সেস করার আগে আপনাকে কম্পোনেন্টটি মাউন্ট করা পর্যন্ত অপেক্ষা করতে হবে।

  `this.$refs`ও অ-প্রতিক্রিয়াশীল, তাই আপনার ডেটা-বাইন্ডিংয়ের জন্য টেমপ্লেটগুলিতে এটি ব্যবহার করার চেষ্টা করা উচিত নয়।

- **আরো দেখুন**
  - [Guide - Template Refs](/guide/essentials/template-refs)
  - [Guide - Typing Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />
  - [Guide - Typing Component Template Refs](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

## is {#is}

Used for binding [dynamic components](/guide/essentials/component-basics#dynamic-components).

- **Expects:** `string | Component`

- **Usage on native elements** <sup class="vt-badge">3.1+</sup>

  যখন একটি নেটিভ HTML এলিমেন্টে `is` অ্যাট্রিবিউট ব্যবহার করা হয়, তখন এটিকে [কাস্টমাইজ করা বিল্ট-ইন এলিমেন্ট](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example) হিসেবে ব্যাখ্যা করা হবে, যা একটি নেটিভ ওয়েব প্ল্যাটফর্ম বৈশিষ্ট্য।

  যাইহোক, একটি ব্যবহারের ক্ষেত্রে রয়েছে যেখানে আপনাকে একটি Vue কম্পোনেন্ট দিয়ে একটি নেটিভ কম্পোনেন্ট প্রতিস্থাপন করতে Vue-এর প্রয়োজন হতে পারে, যেমনটি [in-DOM Template Parsing Caveats](/guide/essentials/component-basics#in-dom-template-parsing-caveats) এ ব্যাখ্যা করা হয়েছে। আপনি `is` অ্যাট্রিবিউটের মানকে `vue:` দিয়ে প্রিফিক্স করতে পারেন যাতে Vue কম্পোনেন্টটিকে Vue কম্পোনেন্ট হিসেবে রেন্ডার করবে:

  ```vue-html
  <table>
    <tr is="vue:my-row-component"></tr>
  </table>
  ```

- **আরো দেখুন**

  - [Built-in Special Element - `<component>`](/api/built-in-special-elements#component)
  - [Dynamic Components](/guide/essentials/component-basics#dynamic-components)
