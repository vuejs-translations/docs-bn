# Composition API: <br>Dependency Injection {#composition-api-dependency-injection}

## provide() {#provide}

একটি মান প্রদান করে যা বংশধর কম্পোনেন্ট দ্বারা ইনজেকশন করা যেতে পারে।

- **Type**

  ```ts
  function provide<T>(key: InjectionKey<T> | string, value: T): void
  ```

- **Details**

  `provide()` দুটি আর্গুমেন্ট নেয়: কী, যা একটি স্ট্রিং বা প্রতীক হতে পারে এবং যে মানটি inject করা হবে।

  TypeScript ব্যবহার করার সময়, কীটি `InjectionKey` হিসেবে কাস্ট করা একটি প্রতীক হতে পারে - একটি Vue প্রদত্ত ইউটিলিটি টাইপ যা `Symbol` প্রসারিত করে, যা `provide()` এবং `inject()` এর মধ্যে মান প্রকার সিঙ্ক করতে ব্যবহার করা যেতে পারে।

  লাইফসাইকেল হুক রেজিস্ট্রেশন এপিআই-এর মতো, একটি কম্পোনেন্টস `setup()` পর্বের সময় অবশ্যই `provide()` সিঙ্ক্রোনাসভাবে কল করতে হবে।

- **Example**

  ```vue
  <script setup>
  import { ref, provide } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // provide static value
  provide('path', '/project/')

  // provide reactive value
  const count = ref(0)
  provide('count', count)

  // provide with Symbol keys
  provide(countSymbol, count)
  </script>
  ```

- **See also**
  - [Guide - Provide / Inject](/guide/components/provide-inject)
  - [Guide - Typing Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## inject() {#inject}

একটি ancestor কম্পোনেন্ট বা অ্যাপ্লিকেশন (`app.provide()` এর মাধ্যমে) দ্বারা প্রদত্ত একটি মান ইনজেক্ট করে।

- **Type**

  ```ts
  // without default value
  function inject<T>(key: InjectionKey<T> | string): T | undefined

  // with default value
  function inject<T>(key: InjectionKey<T> | string, defaultValue: T): T

  // with factory
  function inject<T>(
    key: InjectionKey<T> | string,
    defaultValue: () => T,
    treatDefaultAsFactory: true
  ): T
  ```

- **Details**

  প্রথম যুক্তি হল ইনজেকশন কী। Vue একটি ম্যাচিং কী সহ একটি প্রদত্ত মান সনাক্ত করতে প্যারেন্ট চেইনে চলে যাবে। যদি প্যারেন্ট চেইনের একাধিক কম্পোনেন্ট একই কী প্রদান করে, তাহলে ইনজেকশনের কম্পোনেন্টের সবচেয়ে কাছের একটি চেইনের উপরের অংশটিকে "ছায়া" করবে। ম্যাচিং কী সহ কোনো মান পাওয়া না গেলে, ডিফল্ট মান প্রদান না করা পর্যন্ত `inject()` `undefined` প্রদান করে।

  দ্বিতীয় যুক্তিটি ঐচ্ছিক এবং এটি ডিফল্ট মান যা ব্যবহার করা হবে যখন কোনো মিল পাওয়া যায় নি।

  দ্বিতীয় যুক্তিটি একটি ফ্যাক্টরি ফাংশনও হতে পারে যা তৈরি করা ব্যয়বহুল মান প্রদান করে। এই ক্ষেত্রে, 'সত্য'কে অবশ্যই তৃতীয় আর্গুমেন্ট হিসাবে পাস করতে হবে যে ফাংশনটিকে মানের পরিবর্তে ফ্যাক্টরি হিসাবে ব্যবহার করা উচিত।

  লাইফসাইকেল হুক রেজিস্ট্রেশন API-এর মতোই, একটি কম্পোনেন্টের 'সেটআপ()' পর্বের সময় `inject()`কে অবশ্যই সিঙ্ক্রোনাসভাবে কল করতে হবে।

  TypeScript ব্যবহার করার সময়, কীটি `InjectionKey`-এর প্রকার হতে পারে - একটি Vue-প্রদত্ত ইউটিলিটি টাইপ যা `Symbol` প্রসারিত করে, যা `provide()` এবং `inject()` এর মধ্যে মান প্রকার সিঙ্ক করতে ব্যবহার করা যেতে পারে।

- **Example**

  পূর্ববর্তী `provide()` উদাহরণে দেখানো হিসাবে একটি অভিভাবক কম্পোনেন্ট মান প্রদান করেছে:

  ```vue
  <script setup>
  import { inject } from 'vue'
  import { countSymbol } from './injectionSymbols'

  // inject static value without default
  const path = inject('path')

  // inject reactive value
  const count = inject('count')

  // inject with Symbol keys
  const count2 = inject(countSymbol)

  // inject with default value
  const bar = inject('path', '/default-path')

  // inject with function default value
  const fn = inject('function', () => {})

  // inject with default value factory
  const baz = inject('factory', () => new ExpensiveObject(), true)
  </script>
  ```
  
- **See also**
  - [Guide - Provide / Inject](/guide/components/provide-inject)
  - [Guide - Typing Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## hasInjectionContext() {#has-injection-context}

- Only supported in 3.3+

ভুল জায়গায় (যেমন `setup()` এর বাইরে) কোনো সতর্কতা ছাড়াই যদি [inject()](#inject) ব্যবহার করা যায় তাহলে সত্য দেখায়। এই পদ্ধতিটি এমন লাইব্রেরিদের দ্বারা ব্যবহার করার জন্য ডিজাইন করা হয়েছে যেগুলি শেষ ব্যবহারকারীকে সতর্ক না করেই অভ্যন্তরীণভাবে `ইনজেক্ট()` ব্যবহার করতে চায়।

- **Type**

  ```ts
  function hasInjectionContext(): boolean
  ```
