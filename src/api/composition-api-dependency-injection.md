# Composition API: <br>Dependency Injection {#composition-api-dependency-injection}

## provide() {#provide}

একটি মান প্রদান করে যা বংশধর উপাদান দ্বারা ইনজেকশন করা যেতে পারে।

- **Type**

  ```ts
  function provide<T>(key: InjectionKey<T> | string, value: T): void
  ```

- **Details**

  `provide()` দুটি আর্গুমেন্ট নেয়: কী, যা একটি স্ট্রিং বা প্রতীক হতে পারে এবং যে মানটি inject করা হবে।

  TypeScript ব্যবহার করার সময়, কীটি `InjectionKey` হিসেবে কাস্ট করা একটি প্রতীক হতে পারে - একটি Vue প্রদত্ত ইউটিলিটি টাইপ যা `Symbol` প্রসারিত করে, যা `provide()` এবং `inject()` এর মধ্যে মান প্রকার সিঙ্ক করতে ব্যবহার করা যেতে পারে।

  লাইফসাইকেল হুক রেজিস্ট্রেশন এপিআই-এর মতো, একটি উপাদানের `setup()` পর্বের সময় অবশ্যই `provide()` সিঙ্ক্রোনাসভাবে কল করতে হবে।

- **Example**

  ```vue
  <script setup>
  import { ref, provide } from 'vue'
  import { fooSymbol } from './injectionSymbols'

  // provide static value
  provide('foo', 'bar')

  // provide reactive value
  const count = ref(0)
  provide('count', count)

  // provide with Symbol keys
  provide(fooSymbol, count)
  </script>
  ```

- **See also**
  - [Guide - Provide / Inject](/guide/components/provide-inject)
  - [Guide - Typing Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />

## inject() {#inject}

একটি ancestor উপাদান বা অ্যাপ্লিকেশন (`app.provide()` এর মাধ্যমে) দ্বারা প্রদত্ত একটি মান ইনজেক্ট করে।

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

  পূর্ববর্তী `provide()` উদাহরণে দেখানো হিসাবে একটি অভিভাবক উপাদান মান প্রদান করেছে:

  ```vue
  <script setup>
  import { inject } from 'vue'
  import { fooSymbol } from './injectionSymbols'

  // inject static value without default
  const foo = inject('foo')

  // inject reactive value
  const count = inject('count')

  // inject with Symbol keys
  const foo2 = inject(fooSymbol)

  // inject with default value
  const bar = inject('foo', 'default value')

  // inject with function default value
  const fn = inject('function', () => {})

  // inject with default value factory
  const baz = inject('factory', () => new ExpensiveObject(), true)
  </script>
  ```

- **See also**
  - [Guide - Provide / Inject](/guide/components/provide-inject)
  - [Guide - Typing Provide / Inject](/guide/typescript/composition-api#typing-provide-inject) <sup class="vt-badge ts" />
