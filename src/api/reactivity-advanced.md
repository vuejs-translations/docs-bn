# Reactivity API: Advanced {#reactivity-api-advanced}

## shallowRef() {#shallowref}

শ্যালো ভার্সনের [`ref()`](./reactivity-core#ref).

- **প্রকার**

  ```ts
  function shallowRef<T>(value: T): ShallowRef<T>

  interface ShallowRef<T> {
    value: T
  }
  ```

- **বিস্তারিত**

  `ref()`-এ, একটি শ্যালো রেফের অভ্যন্তরীণ ভ্যালু সংরক্ষণ করা হয় এবং S-2 প্রকাশ করা হয় এবং এটিকে ডাবল-রিয়েক্টিভ করা হবে না। শুধুমাত্র t `.value` রিয়েক্টিভলি 2 অ্যাক্সেস করে।

  `shallowRef()` সাধারণত বড় ডেটা স্ট্রাকচারের পারফরম্যান্স অপ্টিমাইজেশান বা এক্সটারনাল স্টেট ম্যানেজমেন্ট সিস্টেমের সাথে ইন্ট্রিগ্রেশনের জন্য ব্যবহৃত হয়।

- **উদাহরন**

  ```js
  const state = shallowRef({ count: 1 })

  // does NOT trigger change
  state.value.count = 2

  // does trigger change
  state.value = { count: 2 }
  ```

- **আরও দেখুন**
  - [Guide - Reduce Reactivity Overhead for Large Immutable Structures](/guide/best-practices/performance#reduce-reactivity-overhead-for-large-immutable-structures)
  - [Guide - Integration with External State Systems](/guide/extras/reactivity-in-depth#integration-with-external-state-systems)

## triggerRef() {#triggerref}

ফোর্স  ট্রিগার ইফেক্টস যা একটি [shallow ref](#shallowref) এর উপর নির্ভর করে। এটি সাধারণত একটি শ্যালো রেফের ইনার ভ্যালু ডীপ মিউটেশন করার পরে ব্যবহৃত হয়।

- **প্রকার**

  ```ts
  function triggerRef(ref: ShallowRef): void
  ```

- **উদাহরন**

  ```js
  const shallow = shallowRef({
    greet: 'Hello, world'
  })

  // Logs "Hello, world" once for the first run-through
  watchEffect(() => {
    console.log(shallow.value.greet)
  })

  // This won't trigger the effect because the ref is shallow
  shallow.value.greet = 'Hello, universe'

  // Logs "Hello, universe"
  triggerRef(shallow)
  ```

## customRef() {#customref}

ডিফেন্ডেন্সি ট্র্যাকিং এবং আপডেট ট্রিগারিং এর উপর স্পষ্ট নিয়ন্ত্রণ সহ একটি কাস্টমাইজড রেফ তৈরি করে।

- **প্রকার**

  ```ts
  function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

  type CustomRefFactory<T> = (
    track: () => void,
    trigger: () => void
  ) => {
    get: () => T
    set: (value: T) => void
  }
  ```

- **বিস্তারিত**

  `customRef()` একটি ফ্যাক্টরি ফাংশন আশা করে, যেটি আর্গুমেন্ট হিসেবে `track` এবং `trigger` ফাংশন গ্রহণ করে এবং `get` এবং `set` মেথড সহ একটি অবজেক্ট রিটার্ন দেয়।

  সাধারণভাবে, `track()` কে `get()` এর ভিতরে কল করা  এবং `trigger()`কে `set()` এর মধ্যে কল করা উচিত। যাইহোক, কখন তাদের কল করা উচিত, বা আদৌ কল করা উচিত কিনা তার উপর আপনার সম্পূর্ণ নিয়ন্ত্রণ রয়েছে।

- **উদাহরন**

  একটি ডিবাউন্সড রেফ তৈরি করে যাতে সর্বশেষ সেট কলের পরে একটি নির্দিষ্ট সময়সীমার পরে ভ্যালু আপডেট করে:

  ```js
  import { customRef } from 'vue'

  export function useDebouncedRef(value, delay = 200) {
    let timeout
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          clearTimeout(timeout)
          timeout = setTimeout(() => {
            value = newValue
            trigger()
          }, delay)
        }
      }
    })
  }
  ```

  কম্পোনেন্টে ব্যবহার:

  ```vue
  <script setup>
  import { useDebouncedRef } from './debouncedRef'
  const text = useDebouncedRef('hello')
  </script>

  <template>
    <input v-model="text" />
  </template>
  ```

  [চেষ্টা করুন](https://play.vuejs.org/#eNplUkFugzAQ/MqKC1SiIekxIpEq9QVV1BMXCguhBdsyaxqE/PcuGAhNfYGd3Z0ZDwzeq1K7zqB39OI205UiaJGMOieiapTUBAOYFt/wUxqRYf6OBVgotGzA30X5Bt59tX4iMilaAsIbwelxMfCvWNfSD+Gw3++fEhFHTpLFuCBsVJ0ScgUQjw6Az+VatY5PiroHo3IeaeHANlkrh7Qg1NBL43cILUmlMAfqVSXK40QUOSYmHAZHZO0KVkIZgu65kTnWp8Qb+4kHEXfjaDXkhd7DTTmuNZ7MsGyzDYbz5CgSgbdppOBFqqT4l0eX1gZDYOm057heOBQYRl81coZVg9LQWGr+IlrchYKAdJp9h0C6KkvUT3A6u8V1dq4ASqRgZnVnWg04/QWYNyYzC2rD5Y3/hkDgz8fY/cOT1ZjqizMZzGY3rDPC12KGZYyd3J26M8ny1KKx7c3X25q1c1wrZN3L9LCMWs/+AmeG6xI=)

  :::warning সতর্কতার সাথে ব্যবহার করুন
  CustomRef ব্যবহার করার সময়, এর গেটারের রিটার্ন মান সম্পর্কে আমাদের সতর্ক হওয়া উচিত, বিশেষ করে যখন গেটার চালানোর সময় নতুন অবজেক্ট ডেটাটাইপ তৈরি করা হয়। এটি পিতামাতা এবং শিশু উপাদানগুলির মধ্যে সম্পর্ককে প্রভাবিত করে, যেখানে এই ধরনের একটি কাস্টম রেফ একটি প্রপ হিসাবে পাস করা হয়েছে।

  প্যারেন্ট কম্পোনেন্টের রেন্ডার ফাংশন একটি ভিন্ন প্রতিক্রিয়াশীল অবস্থায় পরিবর্তনের মাধ্যমে ট্রিগার হতে পারে। রিরেন্ডারের সময়, আমাদের CustomRef-এর মান পুনঃমূল্যায়ন করা হয়, একটি চাইল্ড কম্পোনেন্টের প্রপ হিসাবে একটি নতুন অবজেক্ট ডেটাটাইপ ফিরিয়ে দেয়। এই প্রপটিকে চাইল্ড কম্পোনেন্টে এর শেষ মানের সাথে তুলনা করা হয় এবং যেহেতু সেগুলি আলাদা, কাস্টমরেফের প্রতিক্রিয়াশীল নির্ভরতা চাইল্ড কম্পোনেন্টে ট্রিগার হয়। ইতিমধ্যে, প্যারেন্ট কম্পোনেন্টে প্রতিক্রিয়াশীল নির্ভরতাগুলি চলে না কারণ কাস্টমরেফের সেটারকে কল করা হয়নি, এবং এর নির্ভরতাগুলি ফলস্বরূপ ট্রিগার করা হয়নি।

  [চেষ্টা করুন](https://play.vuejs.org/#eNqFVEtP3DAQ/itTS9Vm1ZCt1J6WBZUiDvTQIsoNcwiOkzU4tmU7+9Aq/71jO1mCWuhlN/PyfPP45kAujCk2HSdLsnLMCuPBcd+Zc6pEa7T1cADWOa/bW17nYMPPtvRsDT3UVrcww+DZ0flStybpKSkWQQqPU0IVVUwr58FYvdvDWXgpu6ek1pqSHL0fS0vJw/z0xbN1jUPHY/Ys87Zkzzl4K5qG2zmcnUN2oAqg4T6bQ/wENKNXNk+CxWKsSlmLTSk7XlhedYxnWclYDiK+MkQCoK4wnVtnIiBJuuEJNA2qPof7hzkEoc8DXgg9yzYTBBFgNr4xyY4FbaK2p6qfI0iqFgtgulOe27HyQRy69Dk1JXY9C03JIeQ6wg4xWvJCqFpnlNytOcyC2wzYulQNr0Ao+Mhw0KnTTEttl/CIaIJiMz8NGBHFtYetVrPwa58/IL48Zag4N0ssquNYLYBoW16J0vOkC3VQtVqk7cG9QcHz1kj0QAlgVYkNMFk6d0bJ1pbGYKUkmtD42HmvFfi94WhOEiXwjUnBnlEz9OLTJwy5qCo44D4O7en71SIFjI/F9VuG4jEy/GHQKq5hQrJAKOc4uNVighBF5/cygS0GgOMoK+HQb7+EWvLdMM7weVIJy5kXWi0Rj+xaNRhLKRp1IvB9hxYegA6WJ1xkUe9PcF4e9a+suA3YwYiC5MQ79KlFUzw5rZCZEUtoRWuE5PaXCXmxtuWIkpJSSr39EXXHQcWYNWfP/9A/uV3QUXJjueN2E1ZhtPnSIqGS+er3T77D76Ox1VUn0fsd4y3HfewCxuT2vVMVwp74RbTX8WQI1dy5qx12xI1Fpa1K5AreeEHCCN8q/QXul+LrSC3s4nh93jltkVPDIYt5KJkcIKStCReo4rVQ/CZI6dyEzToCCJu7hAtry/1QH/qXncQB400KJwqPxZHxEyona0xS/E3rt1m9Ld1rZl+uhaxecRtP3EjtgddCyimtXyj9H/Ii3eId7uOGTkyk/wOEbQ9h)

  :::

## shallowReactive() {#shallowreactive}

শ্যালো ভার্সনের [`reactive()`](./reactivity-core#reactive).

- **টাইপ**

  ```ts
  function shallowReactive<T extends object>(target: T): T
  ```

- **বিস্তারিত**

  `reactive()` এর বিপরীতে, কোনো ডীপ ভার্সন নেই: শুধুমাত্র রুট-লেভেলের প্রপার্টিগুলি একটি শ্যালো রিয়েক্টিভ অবজেক্টের জন্য রিয়েক্টিভ। প্রপার্টির ভ্যালু-গুলি যেমন আছে সেভাবে সংরক্ষিত এবং প্রকাশ করা হয় - এর মানে রেফ ভ্যালুর সহ প্রপার্টিগুলি স্বয়ংক্রিয়ভাবে **not** হবে না।

  :::warning সতর্কতার সাথে ব্যবহার করুন
  শ্যালো ডেটা স্ট্রাকচার শুধুমাত্র একটি কম্পোনেন্টে রুট লেভেল স্টেটের জন্য ব্যবহার করা উচিত। একটি ডীপ রিয়েক্টিভ অবজেক্টের ভিতরে নেস্টিং এড়িয়ে চলুন কারণ এটি অসঙ্গত রিয়েক্টিভ আচরণ সহ একটি ট্রি তৈরি করে যা বোঝা এবং ডিবাগ করা কঠিন হতে পারে।
  :::

- **উদাহরন**

  ```js
  const state = shallowReactive({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // mutating state's own properties is reactive
  state.foo++

  // ...but does not convert nested objects
  isReactive(state.nested) // false

  // NOT reactive
  state.nested.bar++
  ```

## shallowReadonly() {#shallowreadonly}

শ্যালো ভার্সনের [`readonly()`](./reactivity-core#readonly).

- **প্রকার**

  ```ts
  function shallowReadonly<T extends object>(target: T): Readonly<T>
  ```

- **বিস্তারিত**

  'readonly()' এর বিপরীতে, কোন ডীপ কনভার্সন নেই: শুধুমাত্র রুট-লেভেল প্রপার্টিগুলি কেবল পঠনযোগ্য। প্রপার্টির ভ্যালুগুলি যেমন আছে সেভাবে সংরক্ষিত এবং প্রকাশ করা হয় - এর মানে রেফ ভ্যালু সহ প্রপার্টিগুলি স্বয়ংক্রিয়ভাবে **not** হবে না।


  :::warning সতর্কতার সাথে ব্যবহার করুন
  শ্যালো ডেটা স্ট্রাকচার শুধুমাত্র একটি কম্পোনেন্টে রুট লেভেল স্টেটের জন্য ব্যবহার করা উচিত। একটি ডীপ রিয়েক্টিভ অবজেক্টের ভিতরে নেস্টিং এড়িয়ে চলুন কারণ এটি অসঙ্গত রিয়েক্টিভ আচরণ সহ একটি ট্রি তৈরি করে যা বোঝা এবং ডিবাগ করা কঠিন হতে পারে।
  :::

- **উদাহরন**

  ```js
  const state = shallowReadonly({
    foo: 1,
    nested: {
      bar: 2
    }
  })

  // mutating state's own properties will fail
  state.foo++

  // ...but works on nested objects
  isReadonly(state.nested) // false

  // works
  state.nested.bar++
  ```

## toRaw() {#toraw}

Vue-তৈরি করা প্রক্সির Raw, অরিজিনাল অবজেক্ট রিটার্ন দেয়।

- **প্রকার**

  ```ts
  function toRaw<T>(proxy: T): T
  ```

- **বিস্তারিত**

  `toRaw()` [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](#shallowreactive) দ্বারা তৈরি প্রক্সি থেকে অরিজিনাল অবজেক্টটি রিটার্ন দিতে পারে।  বা [`shallowReadonly()`](#shallowreadonly)

  এটি একটি এস্কেপ হ্যাচ যা অস্থায়ীভাবে প্রক্সি অ্যাক্সেস / ট্র্যাকিং ওভারহেড বা পরিবর্তনগুলি ট্রিগার না করে লেখার জন্য অস্থায়ীভাবে পড়তে ব্যবহার করা যেতে পারে। আসল অবজেক্টের একটা অবিরাম রেফারেন্স রাখা **not** বাঞ্ছনীয়। সতর্কতার সাথে ব্যবহার করুন.

- **উদাহরন**

  ```js
  const foo = {}
  const reactiveFoo = reactive(foo)

  console.log(toRaw(reactiveFoo) === foo) // true
  ```

## markRaw() {#markraw}

একটি অবজেক্টকে চিহ্নিত করে যাতে এটি কখনই প্রক্সিতে রূপান্তরিত না হয়। অবজেক্টটি নিজেই রিটার্ন দেয়।

- **প্রকার**

  ```ts
  function markRaw<T extends object>(value: T): T
  ```

- **উদাহরন**

  ```js
  const foo = markRaw({})
  console.log(isReactive(reactive(foo))) // false

  // also works when nested inside other reactive objects
  const bar = reactive({ foo })
  console.log(isReactive(bar.foo)) // false
  ```

  :::warning সতর্কতার সাথে ব্যবহার করুন
  `markRaw()` এবং শ্যালো API যেমন `shallowReactive()` আপনাকে ডিফল্ট ডীপ reactive/readonly রূপান্তর থেকে বেছে বেছে অপ্ট-আউট করতে এবং আপনার স্টেট গ্রাফে raw, নন-প্রক্সিড অবজেক্ট এম্বেড করার অনুমতি দেয়। এগুলি বিভিন্ন কারণে ব্যবহার করা যেতে পারে:

  - কিছু ভ্যালু সহজভাবে রিয়েক্টিভ করা উচিত নয়, উদাহরণস্বরূপ একটি জটিল থার্ড-পার্টি  ক্লাস ইনস্ট্যান্স, বা একটি Vue কম্পোনেন্ট অবজেক্ট।

  - অপরিবর্তনীয় ডেটা উৎস সহ বড় লিস্ট রেন্ডার করার সময় প্রক্সি কনভার্সন স্কীপ করা পারফরমেন্স উন্নতি করতে পারে।

  এগুলিকে অ্যাডভান্সড হিসাবে বিবেচনা করা হয় কারণ raw অপ্ট-আউট শুধুমাত্র রুট লেভেলে থাকে, তাই আপনি যদি একটি নেস্টেড, নন-মার্কড raw অবজেক্টকে একটি রিয়েক্টিভ অবজেক্টে সেট করেন এবং তারপরে এটিকে আবার অ্যাক্সেস করেন, আপনি প্রক্সি সংস্করণটি ফিরে পাবেন। এর ফলে **identify hazards** হতে পারে - যেমন একটি অপারেশন করা যা অবজেক্টের পরিচয়ের উপর নির্ভর করে কিন্তু একই অবজেক্টের raw এবং প্রক্সি ভার্সন উভয়ই ব্যবহার করে:

  ```js
  const foo = markRaw({
    nested: {}
  })

  const bar = reactive({
    // although `foo` is marked as raw, foo.nested is not.
    nested: foo.nested
  })

  console.log(foo.nested === bar.nested) // false
  ```

  আইডেন্টিফাই হ্যাজার্ড সাধারণত রেয়ার। যাইহোক, নিরাপদে পরিচয়ের ঝুঁকি এড়ানোর সময় এই APIগুলি সঠিকভাবে ব্যবহার করার জন্য রিয়েক্টিভিটি সিস্টেম কীভাবে কাজ করে তার একটি দৃঢ় বোঝার প্রয়োজন।

  :::

## effectScope() {#effectscope}

একটি ইফেক্ট স্কোপ অবজেক্ট তৈরি করে যা এটির মধ্যে তৈরি রিয়েক্টিভ ইফেক্টগুলি (i.e. computed and watchers) ক্যাপচার করতে পারে যাতে এই ইফেক্টগুলি একসাথে নিষ্পত্তি করা যায়। এই API-এর বিস্তারিত ব্যবহারের ক্ষেত্রে, অনুগ্রহ করে এর সংশ্লিষ্ট [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0041-reactivity-effect-scope.md) এর সাথে পরামর্শ করুন।

- **প্রকার**

  ```ts
  function effectScope(detached?: boolean): EffectScope

  interface EffectScope {
    run<T>(fn: () => T): T | undefined // undefined if scope is inactive
    stop(): void
  }
  ```

- **উদাহরন**

  ```js
  const scope = effectScope()

  scope.run(() => {
    const doubled = computed(() => counter.value * 2)

    watch(doubled, () => console.log(doubled.value))

    watchEffect(() => console.log('Count: ', doubled.value))
  })

  // to dispose all effects in the scope
  scope.stop()
  ```

## getCurrentScope() {#getcurrentscope}

কারেন্ট এক্টিভ [effect scope](#effectcope) রিটার্ন করে যদি একটি থাকে।

- **প্রকার**

  ```ts
  function getCurrentScope(): EffectScope | undefined
  ```

## onScopeDispose() {#onscopedispose}

কারেন্ট অ্যাক্টিভ [effect scope](#effectcope) এ একটি নিষ্পত্তি কলব্যাক রেজিস্ট্রার করে। অ্যাসোসিয়েটিভ ইফেক্ট সুযোগ বন্ধ হয়ে গেলে কলব্যাক ডাকা হবে।

এই  পুনঃব্যবহারযোগ্য কম্পোজিশন ফাংশনে `onUnmounted`-এর একটি নন- কম্পোনেন্ট-কাপল রিপ্লেসমেন্ট হিসাবে ব্যবহার করা যেতে পারে, যেহেতু প্রতিটি Vue কম্পোনেন্টের `setup()` ফাংশনও একটি ইফেক্ট স্কোপে ডাকা হয়।

A warning will be thrown if this function is called without an active effect scope. In 3.5+, this warning can be suppressed by passing `true` as the second argument.

- **Type**

  ```ts
  function onScopeDispose(fn: () => void, failSilently?: boolean): void
  ```
