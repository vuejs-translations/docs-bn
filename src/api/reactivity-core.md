# Reactivity API: Core {#reactivity-api-core}

:::info আরোও দেখুন
রিঅ্যাকটিভিটি এপিআইগুলিকে আরও ভালভাবে বোঝার জন্য, গাইডের নিম্নলিখিত অধ্যায়গুলি পড়ার পরামর্শ দেওয়া হলো:

- [Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals) (with the API preference set to Composition API)
- [Reactivity in Depth](/guide/extras/reactivity-in-depth)
  :::

## ref() {#ref}

একটি ইনার ভ্যালু নেয় এবং একটি রিয়েক্টিভ এবং পরিবর্তনযোগ্য রেফ অবজেক্ট প্রদান করে, যার একটি সিঙ্গেল প্রপার্টি `.value` আছে যা ইনার ভ্যালুকে নির্দেশ করে।

- **প্রকার**

  ```ts
  function ref<T>(value: T): Ref<UnwrapRef<T>>

  interface Ref<T> {
    value: T
  }
  ```

- **বিস্তারিত**

  রেফ অবজেক্টটি পরিবর্তনযোগ্য - যেমন আপনি `.value`-এ নতুন ভ্যালু নির্ধারণ করতে পারেন। এটি রিয়েক্টিভও - যেমন `.value`-তে যে কোনো রিড অপারেশন ট্র্যাক করা হয় এবং রাইট অপারেশন অ্যাসোসিয়েটিভ ইফেক্টগুলিকে ট্রিগার করবে৷

  যদি একটি অবজেক্টকে রেফের ভ্যালু হিসাবে অ্যাসাইন করা হয়, তবে অবজেক্টটিকে [reactive()](#reactive) দিয়ে ডীপলি রিয়েক্টিভ করা হয়। এর মানে হল যদি অবজেক্টটিতে নেস্টেড রেফ থাকে তবে সেগুলি ডীপলি মোড়ানো হবে।

  ডীপ কনভার্সন এড়াতে, পরিবর্তে [`shallowRef()`](./reactivity-advanced#shallowref) ব্যবহার করুন।

- **উদাহরন**

  ```js
  const count = ref(0)
  console.log(count.value) // 0

  count.value++
  console.log(count.value) // 1
  ```

- **আরোও দেখুন**
  - [Guide - Reactivity Fundamentals with `ref()`](/guide/essentials/reactivity-fundamentals#ref)
  - [Guide - Typing `ref()`](/guide/typescript/composition-api#typing-ref) <sup class="vt-badge ts" />

## computed() {#computed}

একটি গেটার ফাংশন নেয় এবং গেটার থেকে রিটার্ন ভ্যালুরর জন্য একটি পঠনযোগ্য রিয়েক্টিভ [ref](#ref) অবজেক্ট প্রদান করে। এটি একটি লিখনযোগ্য রেফ অবজেক্ট তৈরি করতে `get` এবং `set` ফাংশন সহ একটি অবজেক্টও নিতে পারে।

- **প্রকার**

  ```ts
  // read-only
  function computed<T>(
    getter: () => T,
    // see "Computed Debugging" link below
    debuggerOptions?: DebuggerOptions
  ): Readonly<Ref<Readonly<T>>>

  // writable
  function computed<T>(
    options: {
      get: () => T
      set: (value: T) => void
    },
    debuggerOptions?: DebuggerOptions
  ): Ref<T>
  ```

- **উদাহরন**

 একটি রিডঅনলি কম্পিউটেড রেফ তৈরি করা হচ্ছে:

  ```js
  const count = ref(1)
  const plusOne = computed(() => count.value + 1)

  console.log(plusOne.value) // 2

  plusOne.value++ // error
  ```

  একটি রাইটঅনলি কম্পিউটেড রেফ তৈরি করা হচ্ছে:

  ```js
  const count = ref(1)
  const plusOne = computed({
    get: () => count.value + 1,
    set: (val) => {
      count.value = val - 1
    }
  })

  plusOne.value = 1
  console.log(count.value) // 0
  ```

  ডিবাগিং:

  ```js
  const plusOne = computed(() => count.value + 1, {
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

- **আরোও দেখুন**
  - [Guide - Computed Properties](/guide/essentials/computed)
  - [Guide - Computed Debugging](/guide/extras/reactivity-in-depth#computed-debugging)
  - [Guide - Typing `computed()`](/guide/typescript/composition-api#typing-computed) <sup class="vt-badge ts" />

## reactive() {#reactive}

অবজেক্টের একটি রিয়েক্টিভ প্রক্সি প্রদান করে।

- **প্রকার**

  ```ts
  function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
  ```

- **বিস্তারিত**

  রিয়েক্টিভ রূপান্তর হল "ডীপ": এটি সমস্ত নেস্টেড প্রপার্টিগুলোকে প্রভাবিত করে। একটি রিয়েক্টিভ অবজেক্ট রিয়েক্টিভতা বজায় রাখার সময় [refs](#ref) যে কোনো প্রপার্টিকে ডীপলি  খুলে দেয়।

  এটিও উল্লেখ করা উচিত যে যখন রেফটিকে একটি রিয়েক্টিভ অ্যারের একটি উপাদান বা `Map` এর মতো একটি নেটিভ কালেকশন টাইপ হিসাবে অ্যাক্সেস করা হয় তখন কোনও রেফ আনর্যাপিং করা হয় না।

  ডীপ কনভার্সন এড়াতে এবং শুধুমাত্র রুট লেভেল রিয়েক্টিভিটি বজায় রাখতে, পরিবর্তে [shallowReactive()](./reactivity-advanced#shallowreactive) ব্যবহার করুন।

  রিটার্ন অবজেক্ট এবং এর নেস্টেড অবজেক্টগুলি [ES Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) দিয়ে মোড়ানো হয় এবং **not** এর সমান অরিজিনাল অবজেক্ট। রিয়েক্টিভ প্রক্সির সাথে একচেটিয়াভাবে কাজ করার এবং অরিজিনাল অবজেক্টের উপর নির্ভর করা এড়াতে রিকোমেন্ড করা হয়।

- **উদাহরন**

  একটি রিয়েক্টিভ অবজেক্ট তৈরি করা:

  ```js
  const obj = reactive({ count: 0 })
  obj.count++
  ```

  Ref unwrapping:

  ```ts
  const count = ref(1)
  const obj = reactive({ count })

  // ref will be unwrapped
  console.log(obj.count === count.value) // true

  // it will update `obj.count`
  count.value++
  console.log(count.value) // 2
  console.log(obj.count) // 2

  // it will also update `count` ref
  obj.count++
  console.log(obj.count) // 3
  console.log(count.value) // 3
  ```

  মনে রাখবেন যে রেফগুলি অ্যারে বা কালেকশন ইলিমেন্ট হিসাবে অ্যাক্সেস করার সময় **not** unwrapped হয় না:

  ```js
  const books = reactive([ref('Vue 3 Guide')])
  // need .value here
  console.log(books[0].value)

  const map = reactive(new Map([['count', ref(0)]]))
  // need .value here
  console.log(map.get('count').value)
  ```

  একটি `রিয়েক্টিভ` প্রপার্টিতে একটি [ref](#ref) অ্যাসাইন করার সময়, সেই রেফটিও অটোমেটিকলি unwrapped হবে:

  ```ts
  const count = ref(1)
  const obj = reactive({})

  obj.count = count

  console.log(obj.count) // 1
  console.log(obj.count === count.value) // true
  ```

- **আরোও দেখুন**
  - [Guide - Reactivity Fundamentals](/guide/essentials/reactivity-fundamentals)
  - [Guide - Typing `reactive()`](/guide/typescript/composition-api#typing-reactive) <sup class="vt-badge ts" />

## readonly() {#readonly}

একটি অবজেক্ট (রিয়েক্টিভ বা প্লেইন) বা একটি [ref](#ref) নেয় এবং অরিজিনাল একটি রিডুঅনলি প্রক্সি রিটার্ন দেয়।

- **প্রকার**

  ```ts
  function readonly<T extends object>(
    target: T
  ): DeepReadonly<UnwrapNestedRefs<T>>
  ```

- **বিস্তারিত**

 একটি রিডঅনলি প্রক্সি হচ্ছে ডীপ: যে কোনো নেস্টেড প্রপার্টি অ্যাক্সেস করা হবে রিডঅনলি। এটিতে `reactive()` এর মতো একই রেফ-আনর্যাপিং আচরণ রয়েছে, ব্যতীত মোড়ানো মানগুলিও রিডঅনলি হবে।

  ডীপ কনভার্সন এড়াতে, পরিবর্তে [shallowReadonly()](./reactivity-advanced#shallowreadonly) ব্যবহার করুন।

- **উদাহরন**

  ```js
  const original = reactive({ count: 0 })

  const copy = readonly(original)

  watchEffect(() => {
    // works for reactivity tracking
    console.log(copy.count)
  })

  // mutating original will trigger watchers relying on the copy
  original.count++

  // mutating the copy will fail and result in a warning
  copy.count++ // warning!
  ```

## watchEffect() {#watcheffect}

রিয়েক্টিভভাবে তার ডিপেন্ডেন্সিগুলো ট্র্যাক করার সময় অবিলম্বে একটি ফাংশন রান হয় এবং যখনই ডিপেন্ডেন্সিগুলো পরিবর্তন করা হয় তখন এটি পুনরায় রান হয়।

- **প্রকার**

  ```ts
  function watchEffect(
    effect: (onCleanup: OnCleanup) => void,
    options?: WatchEffectOptions
  ): StopHandle

  type OnCleanup = (cleanupFn: () => void) => void

  interface WatchEffectOptions {
    flush?: 'pre' | 'post' | 'sync' // default: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }

  type StopHandle = () => void
  ```

- **বিস্তারিত**

  প্রথম আর্গুমেন্ট হচ্ছে ইফেক্ট ফাংশন রান করা। ইফেক্ট ফাংশন একটি ফাংশন গ্রহণ করে যা একটি ক্লিনআপ কলব্যাক রেজিস্ট্রার করতে ব্যবহার করা যেতে পারে। পরের বার ইফেক্টটি পুনরায় রান করার ঠিক আগে ক্লিনআপ কলব্যাক কল করা হবে, এবং ইনভ্যালিড সাইড ইফেক্টগুলি ক্লিনআপ করতে ব্যবহার করা যেতে পারে, যেমন একটি পেন্ডিং অ্যাসিঙ্ক রিকোয়েস্ট (নীচের উদাহরণ দেখুন)।

  দ্বিতীয় আরগুমেন্ট হল একটি অপশনাল অপশন অবজেক্ট যা ইফেক্টের ফ্লাশ টাইমিং সামঞ্জস্য করতে বা ইফেক্টের ডিপেন্ডেন্সিগুলো ডিবাগ করতে ব্যবহার করা যেতে পারে।

  ডিফল্টভাবে, কম্পোনেন্ট রেন্ডারিংয়ের ঠিক আগে ওয়াচার্স চলবে। `flush: 'post'` সেট করা কম্পোনেন্ট রেন্ডারিং না হওয়া পর্যন্ত ওয়াচারকে পিছিয়ে দিবে। আরও তথ্যের জন্য [Callback Flush Timing](/guide/essentials/watchers#callback-flush-timing) দেখুন। বিরল ক্ষেত্রে, রিয়েক্টিভ ডিপেন্ডেন্সির পরিবর্তিত হলে অবিলম্বে একটি ওয়াচার ট্রিগার করার প্রয়োজন হতে পারে, যেমন একটি ক্যাশে ইনভ্যালিড করতে। এটি `flush: 'sync` ব্যবহার করে অর্জন করা যেতে পারে। যাইহোক, এই সেটিংটি সতর্কতার সাথে ব্যবহার করা উচিত, কারণ এটি কর্মক্ষমতা এবং ডেটা সামঞ্জস্য নিয়ে সমস্যা হতে পারে যদি একই সময়ে একাধিক প্রপার্টি আপডেট করা হয়।

  রিটার্ন ভ্যালু হল একটি হ্যান্ডেল ফাংশন যেটিকে আবার রান করা থেকে ইফেক্ট বন্ধ করতে বলা যেতে পারে।

- **উদাহরন**

  ```js
  const count = ref(0)

  watchEffect(() => console.log(count.value))
  // -> logs 0

  count.value++
  // -> logs 1
  ```

  Side effect cleanup:

  ```js
  watchEffect(async (onCleanup) => {
    const { response, cancel } = doAsyncWork(id.value)
    // `cancel` will be called if `id` changes
    // so that previous pending request will be cancelled
    // if not yet completed
    onCleanup(cancel)
    data.value = await response
  })
  ```

  Stopping the watcher:

  ```js
  const stop = watchEffect(() => {})

  // when the watcher is no longer needed:
  stop()
  ```

  অপশন:

  ```js
  watchEffect(() => {}, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

- **আরোও দেখুন**
  - [Guide - Watchers](/guide/essentials/watchers#watcheffect)
  - [Guide - Watcher Debugging](/guide/extras/reactivity-in-depth#watcher-debugging)

## watchPostEffect() {#watchposteffect}

Alias of [`watchEffect()`](#watcheffect) with `flush: 'post'` option.

## watchSyncEffect() {#watchsynceffect}

Alias of [`watchEffect()`](#watcheffect) with `flush: 'sync'` option.

## watch() {#watch}

এক বা একাধিক রিয়েক্টিভ ডেটা সোর্স ওয়াচ করে এবং সোর্স পরিবর্তন হলে একটি কলব্যাক ফাংশন কল করে৷

- **প্রকার**

  ```ts
  // watching single source
  function watch<T>(
    source: WatchSource<T>,
    callback: WatchCallback<T>,
    options?: WatchOptions
  ): StopHandle

  // watching multiple sources
  function watch<T>(
    sources: WatchSource<T>[],
    callback: WatchCallback<T[]>,
    options?: WatchOptions
  ): StopHandle

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type WatchSource<T> =
    | Ref<T> // ref
    | (() => T) // getter
    | T extends object
    ? T
    : never // reactive object

  interface WatchOptions extends WatchEffectOptions {
    immediate?: boolean // default: false
    deep?: boolean // default: false
    flush?: 'pre' | 'post' | 'sync' // default: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }
  ```

  > টাইপগুলি রিডঅ্যাবিলিটির জন্য সিমপ্লিফাইড।

- **বিস্তারিত**

  `watch()` ডিফল্টরূপে lazy - অর্থাৎ কলব্যাক শুধুমাত্র তখনই কল করা হয় যখন ওয়াচ সোর্স পরিবর্তন হয়।

  প্রথম আরগুমেন্ট হল ওয়াচার্স-এর **source**। সোর্স নিম্নলিখিতগুলির মধ্যে একটি হতে পারে:

  - একটি গেটার ফাংশন যা একটি ভ্যালু প্রদান করে
  - একটি রেফ
  - একটি রিয়েক্টিভ অবজেক্ট
  - ...অথবা উপরের একটি অ্যারে.

  দ্বিতীয় আরগুমেন্ট হল কলব্যাক যা সোর্স পরিবর্তন হলে কল করা হবে। কলব্যাক তিনটি আর্গুমেন্ট পায়: নিউ ভ্যালু, ওল্ড ভ্যালু এবং একটি সাইড ইফেক্ট ক্লিনআপ কলব্যাক রেজিস্ট্রারের জন্য একটি ফাংশন। পরের বার ইফেক্টি পুনরায় চালানোর ঠিক আগে ক্লিনআপ কলব্যাক কল করা হবে, এবং ইনভ্যালিড সাইড ইফেক্টগুলি ক্লিন আপ করতে ব্যবহার করা যেতে পারে, যেমন একটি পেন্ডিং অ্যাসিঙ্ক রিকোয়েস্ট।

  একাধিক সোর্স ওয়াচ সময়, কলব্যাক সোর্স অ্যারের সাথে সম্পর্কিত নতুন/পুরনো ভ্যালুগুলো ধারণকারী দুটি অ্যারে পায়।

  তৃতীয় অপশনাল আরগুমেন্ট হল একটি অপশন অবজেক্ট যা নিম্নলিখিত অপশনগুলোকে সমর্থন করে:

  - **`immediate`**: ওয়াচার তৈরিতে অবিলম্বে কলব্যাক ট্রিগার করুন। প্রথম কলে পুরানো ভ্যালু 'undefined' হবে।
  - **`deep`**: সোর্সের ডীপ ট্র্যাভার্সাল ফোর্স করে যদি এটি কোনো অবজেক্ট হয়, যাতে কলব্যাক ডীপ মিউটেশনের উপর  ফায়ারস করে। [Deep Watchers](/guide/essentials/watchers#deep-watchers) দেখুন।
  - **`flush`**: কলব্যাকের ফ্লাশের সময় সামঞ্জস্য করুন। [Callback Flush Timing](/guide/essentials/watchers#callback-flush-timing) এবং [`watchEffect()`](/api/reactivity-core#watcheffect) দেখুন।
  - **`onTrack / onTrigger`**: ওয়াচার-এর ডিপেন্ডেন্সি ডিবাগ করুন। [Watcher Debugging](/guide/extras/reactivity-in-depth#watcher-debugging) দেখুন।

  [`watchEffect()`](#watcheffect) এর তুলনায়, `watch()` আমাদের অ্যালাউ করে:

  - লেইজিলি সাইড ইফেক্ট পারফর্ম করে;
  - কোন স্টেটে ওয়াচার পুনরায় চালানোর জন্য ট্রিগার করা উচিত সে সম্পর্কে আরও সুনির্দিষ্ট হন;
  - ওয়াচার স্টেটের আগের এবং বর্তমান ভ্যালু উভয়ই অ্যাক্সেস করুন।

- **উদাহরন**

  একটি গেটারকে ওয়াচিং করা হচ্ছে:

  ```js
  const state = reactive({ count: 0 })
  watch(
    () => state.count,
    (count, prevCount) => {
      /* ... */
    }
  )
  ```

  একটি রেফ-কে ওয়াচিং করা হচ্ছে:

  ```js
  const count = ref(0)
  watch(count, (count, prevCount) => {
    /* ... */
  })
  ```

  একাধিক সোর্স ওয়াচ করার সময়, কলব্যাক সোর্স অ্যারের সাথে সম্পর্কিত নতুন / পুরানো ভ্যালু ধারণকারী অ্যারেগুলি গ্রহণ করে:

  ```js
  watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
    /* ... */
  })
  ```

  গেটার সোর্স ব্যবহার করার সময়, গেটারস রিটার্ন ভ্যালু পরিবর্তিত হলে ওয়াচার শুধুমাত্র ফায়ার হয়। আপনি যদি ডীপ মিউটেশনেও কলব্যাক চালু করতে চান, তাহলে আপনাকে স্পষ্টভাবে ওয়াচারকে `{ deep: true }` দিয়ে ডীপ মোডে ফোর্স করতে হবে। ডীপ মোডে নোট করুন, নতুন ভ্যালু এবং পুরানো একই অবজেক্ট হবে যদি কলব্যাকটি একটি ডীপ মিউটেশন দ্বারা ট্রিগার হয়:

  ```js
  const state = reactive({ count: 0 })
  watch(
    () => state,
    (newValue, oldValue) => {
      // newValue === oldValue
    },
    { deep: true }
  )
  ```

  একটি রিয়েক্টিভ অবজেক্ট সরাসরি ওয়াচ করার সময়, ওয়াচার স্বয়ংক্রিয়ভাবে ডীপ মোডে থাকে:

  ```js
  const state = reactive({ count: 0 })
  watch(state, () => {
    /* triggers on deep mutation to state */
  })
  ```

  `watch()` একই ফ্লাশ টাইমিং এবং ডিবাগিং অপশনগুলি [`watchEffect()`](#watcheffect) এর সাথে শেয়ার করে:

  ```js
  watch(source, callback, {
    flush: 'post',
    onTrack(e) {
      debugger
    },
    onTrigger(e) {
      debugger
    }
  })
  ```

  ওয়াচারকে বন্ধ করা হচ্ছে:

  ```js
  const stop = watch(source, callback)

  // when the watcher is no longer needed:
  stop()
  ```

  সাইড ইফেক্ট ক্লিনআপ:

  ```js
  watch(id, async (newId, oldId, onCleanup) => {
    const { response, cancel } = doAsyncWork(newId)
    // `cancel` will be called if `id` changes, cancelling
    // the previous request if it hasn't completed yet
    onCleanup(cancel)
    data.value = await response
  })
  ```

- **আরোও দেখুন**

  - [Guide - Watchers](/guide/essentials/watchers)
  - [Guide - Watcher Debugging](/guide/extras/reactivity-in-depth#watcher-debugging)
