# Component Instance {#component-instance}

:::info
এই পৃষ্ঠাটি অন্তর্নির্মিত বৈশিষ্ট্য এবং কম্পোনেন্ট পাবলিক ইনস্ট্যান্সে উন্মোচিত পদ্ধতিগুলি নথিভুক্ত করে, যেমন `this`।

এই পৃষ্ঠায় তালিকাভুক্ত সমস্ত বৈশিষ্ট্য শুধুমাত্র পঠনযোগ্য (`$data`-এ নেস্টেড বৈশিষ্ট্যগুলি ছাড়া)।
:::

## $data {#data}

অবজেক্টটি [`data`](./options-state#data) বিকল্প থেকে ফিরে এসেছে, কম্পোনেন্ট দ্বারা প্রতিক্রিয়াশীল করা হয়েছে। কম্পোনেন্ট ইনস্ট্যান্স প্রক্সি তার ডেটা অবজেক্টের বৈশিষ্ট্যগুলিতে অ্যাক্সেস করে।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $data: object
  }
  ```

## $props {#props}

কম্পোনেন্টস বর্তমান, সমাধান করা প্রপস প্রতিনিধিত্বকারী একটি অবজেক্ট।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $props: object
  }
  ```

- ** বিস্তারিত**

  শুধুমাত্র [`props`](./options-state#props) বিকল্পের মাধ্যমে ঘোষিত প্রপস অন্তর্ভুক্ত করা হবে। কম্পোনেন্ট ইনস্ট্যান্স প্রক্সি এর প্রপস অবজেক্টের বৈশিষ্ট্যগুলিতে অ্যাক্সেস করে।

## $el {#el}

রুট DOM নোড যা কম্পোনেন্ট ইনস্ট্যান্স পরিচালনা করছে।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $el: Node | undefined
  }
  ```

- ** বিস্তারিত**

  কম্পোনেন্টটি [মাউন্ট করা](./options-lifecycle#mounted) না হওয়া পর্যন্ত `$el` `অসংজ্ঞায়িত` থাকবে।

  - একটি একক মূল কম্পোনেন্ট সহ কম্পোনেন্টগুলির জন্য, `$el` সেই কম্পোনেন্টটিকে নির্দেশ করবে৷
  - পাঠ্য রুট সহ কম্পোনেন্টগুলির জন্য, `$el` পাঠ্য নোডের দিকে নির্দেশ করবে।
  - একাধিক রুট নোড সহ কম্পোনেন্টগুলির জন্য, `$el` হবে স্থানধারক DOM নোড যা Vue ব্যবহার করে DOM-এ কম্পোনেন্টস অবস্থানের ট্র্যাক রাখতে (একটি পাঠ্য নোড, বা SSR হাইড্রেশন মোডে একটি মন্তব্য নোড)।

  :::tip
  সামঞ্জস্যের জন্য, `$el` এর উপর নির্ভর না করে কম্পোনেন্টগুলিতে সরাসরি অ্যাক্সেসের জন্য [টেমপ্লেট রেফস](/guide/essentials/template-refs) ব্যবহার করার পরামর্শ দেওয়া হয়।
  :::

## $options {#options}

বর্তমান কম্পোনেন্ট ইনস্ট্যান্স ইনস্ট্যান্ট করার জন্য ব্যবহৃত সমাধানকৃত কম্পোনেন্ট অপশন।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $options: ComponentOptions
  }
  ```

- ** বিস্তারিত**

  `$options` অবজেক্ট বর্তমান কম্পোনেন্টস জন্য সমাধান করা বিকল্পগুলিকে প্রকাশ করে এবং এই সম্ভাব্য উত্সগুলির একত্রিত ফলাফল:

  - Global mixins
  - Component `extends` base
  - Component mixins

  এটি সাধারণত কাস্টম কম্পোনেন্ট বিকল্প সমর্থন করতে ব্যবহৃত হয়:

  ```js
  const app = createApp({
    customOption: 'foo',
    created() {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

- **আরো দেখুন** [`app.config.optionMergeStrategies`](/api/application#app-config-optionmergestrategies)

## $parent {#parent}

প্যারেন্ট ইনস্ট্যান্স, যদি বর্তমান ইনস্ট্যান্সে একটি থাকে। এটি রুট উদাহরণের জন্যই `null` হবে।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $parent: ComponentPublicInstance | null
  }
  ```

## $root {#root}

The root component instance of the current component tree. If the current instance has no parents this value will be itself.

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $root: ComponentPublicInstance
  }
  ```

## $slots {#slots}

An object representing the [slots](/guide/components/slots) passed by the parent component.

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $slots: { [name: string]: Slot }
  }

  type Slot = (...args: any[]) => VNode[]
  ```

- ** বিস্তারিত**

  সাধারণত ম্যানুয়ালি [রেন্ডার ফাংশন](/guide/extras/render-function) লেখার সময় ব্যবহৃত হয়, তবে স্লট উপস্থিত আছে কিনা তা সনাক্ত করতেও ব্যবহার করা যেতে পারে।

  প্রতিটি স্লট `this.$slots`-এ একটি ফাংশন হিসেবে উন্মোচিত হয় যা সেই স্লটের নামের সাথে সম্পর্কিত কী-এর অধীনে ভনোডের একটি অ্যারে প্রদান করে। ডিফল্ট স্লটটি `this.$slots.default` হিসেবে প্রকাশ করা হয়েছে।

  যদি একটি স্লট একটি [স্কোপড স্লট](/guide/components/slots#scoped-slots) হয়, তাহলে স্লট ফাংশনে পাস করা আর্গুমেন্টগুলি স্লট প্রপস হিসাবে স্লটে উপলব্ধ থাকে।

- **আরো দেখুন** [Render Functions - Rendering Slots](/guide/extras/render-function#rendering-slots)

## $refs {#refs}

DOM কম্পোনেন্ট এবং কম্পোনেন্ট উদাহরণের একটি অবজেক্ট, এর মাধ্যমে নিবন্ধিত৷ [template refs](/guide/essentials/template-refs).

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $refs: { [name: string]: Element | ComponentPublicInstance | null }
  }
  ```

- **আরো দেখুন**

  - [Template refs](/guide/essentials/template-refs)
  - [Special Attributes - ref](./built-in-special-attributes.md#ref)

## $attrs {#attrs}

একটি অবজেক্ট যা কম্পোনেন্টস ফল-থ্রু বৈশিষ্ট্য ধারণ করে।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $attrs: object
  }
  ```

- ** বিস্তারিত**

  [Fallthrough Attributes](/guide/components/attrs) হল অ্যাট্রিবিউট এবং ইভেন্ট হ্যান্ডলার যা প্যারেন্ট কম্পোনেন্ট দ্বারা পাস করা হয়, কিন্তু সন্তানের দ্বারা প্রপ বা নির্গত ইভেন্ট হিসাবে ঘোষণা করা হয় না।

  ডিফল্টরূপে, শুধুমাত্র একটি রুট কম্পোনেন্ট থাকলে `$attrs`-এর সবকিছু স্বয়ংক্রিয়ভাবে কম্পোনেন্টের রুট এলিমেন্টে উত্তরাধিকার সূত্রে প্রাপ্ত হবে। এই আচরণটি অক্ষম করা হয় যদি কম্পোনেন্টটির একাধিক রুট নোড থাকে এবং [`inheritAttrs`](./options-misc#inheritattrs) বিকল্পের সাথে স্পষ্টভাবে নিষ্ক্রিয় করা যেতে পারে।

- **আরো দেখুন**

  - [Fallthrough Attributes](/guide/components/attrs)

## $watch() {#watch}

পর্যবেক্ষক তৈরির জন্য অপরিহার্য API।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $watch(
      source: string | (() => any),
      callback: WatchCallback,
      options?: WatchOptions
    ): StopHandle
  }

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  interface WatchOptions {
    immediate?: boolean // default: false
    deep?: boolean // default: false
    flush?: 'pre' | 'post' | 'sync' // default: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }

  type StopHandle = () => void
  ```

- ** বিস্তারিত**

  প্রথম যুক্তি হল ঘড়ির উৎস। এটি একটি কম্পোনেন্ট প্রপার্টি নামের স্ট্রিং, একটি সাধারণ ডট-ডিলিমিটেড পাথ স্ট্রিং বা একটি [গেটার ফাংশন](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description)।

  দ্বিতীয় যুক্তি হল কলব্যাক ফাংশন। কলব্যাক নতুন মান এবং প্রেক্ষিত উৎসের পুরানো মান গ্রহণ করে।

  - **`immediate`**: trigger the callback immediately on watcher creation. Old value will be `undefined` on the first call.
  - **`deep`**: force deep traversal of the source if it is an object, so that the callback fires on deep mutations. See [Deep Watchers](/guide/essentials/watchers#deep-watchers).
  - **`flush`**: adjust the callback's flush timing. See [Callback Flush Timing](/guide/essentials/watchers#callback-flush-timing) and [`watchEffect()`](/api/reactivity-core#watcheffect).
  - **`onTrack / onTrigger`**: debug the watcher's dependencies. See [Watcher Debugging](/guide/extras/reactivity-in-depth#watcher-debugging).

- **Example**

  Watch a property name:

  ```js
  this.$watch('a', (newVal, oldVal) => {})
  ```

  Watch a dot-delimited path:

  ```js
  this.$watch('a.b', (newVal, oldVal) => {})
  ```

  Using getter for more complex expressions:

  ```js
  this.$watch(
    // every time the expression `this.a + this.b` yields
    // a different result, the handler will be called.
    // It's as if we were watching a computed property
    // without defining the computed property itself.
    () => this.a + this.b,
    (newVal, oldVal) => {}
  )
  ```

  Stopping the watcher:

  ```js
  const unwatch = this.$watch('a', cb)

  // later...
  unwatch()
  ```

- **আরো দেখুন**
  - [Options - `watch`](/api/options-state#watch)
  - [Guide - Watchers](/guide/essentials/watchers)

## $emit() {#emit}

বর্তমান উদাহরণে একটি কাস্টম ইভেন্ট ট্রিগার করুন। যেকোন অতিরিক্ত আর্গুমেন্ট শ্রোতার কলব্যাক ফাংশনে পাঠানো হবে।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $emit(event: string, ...args: any[]): void
  }
  ```

- **Example**

  ```js
  export default {
    created() {
      // only event
      this.$emit('foo')
      // with additional arguments
      this.$emit('bar', 1, 2, 3)
    }
  }
  ```

- **আরো দেখুন**

  - [Component - Events](/guide/components/events)
  - [`emits` option](./options-state#emits)

## $forceUpdate() {#forceupdate}

Force the component instance to re-render.

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $forceUpdate(): void
  }
  ```

- ** বিস্তারিত**

  Vue-এর সম্পূর্ণ স্বয়ংক্রিয় প্রতিক্রিয়া সিস্টেমের কারণে এটি খুব কমই প্রয়োজন। আপনি যখন উন্নত প্রতিক্রিয়াশীল API ব্যবহার করে স্পষ্টভাবে অ-প্রতিক্রিয়াশীল কম্পোনেন্ট অবস্থা তৈরি করেছেন তখনই আপনার এটির প্রয়োজন হতে পারে।

## $nextTick() {#nexttick}

গ্লোবাল [`nextTick()`](./general#nexttick) এর ইনস্ট্যান্স-বাউন্ড সংস্করণ।

- **টাইপ**

  ```ts
  interface ComponentPublicInstance {
    $nextTick(callback?: (this: ComponentPublicInstance) => void): Promise<void>
  }
  ```

- ** বিস্তারিত**

  `nextTick()` এর বৈশ্বিক সংস্করণ থেকে একমাত্র পার্থক্য হল যে কলব্যাকটি `this.$nextTick()`-এ পাস করা হয়েছে তার `this` প্রসঙ্গ বর্তমান কম্পোনেন্ট উদাহরণের সাথে আবদ্ধ থাকবে।

- **আরো দেখুন** [`nextTick()`](./general#nexttick)
