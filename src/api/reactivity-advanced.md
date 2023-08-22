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

  [Try it in the Playground](https://play.vuejs.org/#eNplUkFugzAQ/MqKC1SiIekxIpEq9QVV1BMXCguhBdsyaxqE/PcuGAhNfYGd3Z0ZDwzeq1K7zqB39OI205UiaJGMOieiapTUBAOYFt/wUxqRYf6OBVgotGzA30X5Bt59tX4iMilaAsIbwelxMfCvWNfSD+Gw3++fEhFHTpLFuCBsVJ0ScgUQjw6Az+VatY5PiroHo3IeaeHANlkrh7Qg1NBL43cILUmlMAfqVSXK40QUOSYmHAZHZO0KVkIZgu65kTnWp8Qb+4kHEXfjaDXkhd7DTTmuNZ7MsGyzDYbz5CgSgbdppOBFqqT4l0eX1gZDYOm057heOBQYRl81coZVg9LQWGr+IlrchYKAdJp9h0C6KkvUT3A6u8V1dq4ASqRgZnVnWg04/QWYNyYzC2rD5Y3/hkDgz8fY/cOT1ZjqizMZzGY3rDPC12KGZYyd3J26M8ny1KKx7c3X25q1c1wrZN3L9LCMWs/+AmeG6xI=)

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

Returns the raw, original object of a Vue-created proxy.

- **Type**

  ```ts
  function toRaw<T>(proxy: T): T
  ```

- **Details**

  `toRaw()` can return the original object from proxies created by [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`](#shallowreactive) or [`shallowReadonly()`](#shallowreadonly).

  This is an escape hatch that can be used to temporarily read without incurring proxy access / tracking overhead or write without triggering changes. It is **not** recommended to hold a persistent reference to the original object. Use with caution.

- **Example**

  ```js
  const foo = {}
  const reactiveFoo = reactive(foo)

  console.log(toRaw(reactiveFoo) === foo) // true
  ```

## markRaw() {#markraw}

Marks an object so that it will never be converted to a proxy. Returns the object itself.

- **Type**

  ```ts
  function markRaw<T extends object>(value: T): T
  ```

- **Example**

  ```js
  const foo = markRaw({})
  console.log(isReactive(reactive(foo))) // false

  // also works when nested inside other reactive objects
  const bar = reactive({ foo })
  console.log(isReactive(bar.foo)) // false
  ```

  :::warning Use with Caution
  `markRaw()` and shallow APIs such as `shallowReactive()` allow you to selectively opt-out of the default deep reactive/readonly conversion and embed raw, non-proxied objects in your state graph. They can be used for various reasons:

  - Some values simply should not be made reactive, for example a complex 3rd party class instance, or a Vue component object.

  - Skipping proxy conversion can provide performance improvements when rendering large lists with immutable data sources.

  They are considered advanced because the raw opt-out is only at the root level, so if you set a nested, non-marked raw object into a reactive object and then access it again, you get the proxied version back. This can lead to **identity hazards** - i.e. performing an operation that relies on object identity but using both the raw and the proxied version of the same object:

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

  Identity hazards are in general rare. However, to properly utilize these APIs while safely avoiding identity hazards requires a solid understanding of how the reactivity system works.

  :::

## effectScope() {#effectscope}

Creates an effect scope object which can capture the reactive effects (i.e. computed and watchers) created within it so that these effects can be disposed together. For detailed use cases of this API, please consult its corresponding [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0041-reactivity-effect-scope.md).

- **Type**

  ```ts
  function effectScope(detached?: boolean): EffectScope

  interface EffectScope {
    run<T>(fn: () => T): T | undefined // undefined if scope is inactive
    stop(): void
  }
  ```

- **Example**

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

Returns the current active [effect scope](#effectscope) if there is one.

- **Type**

  ```ts
  function getCurrentScope(): EffectScope | undefined
  ```

## onScopeDispose() {#onscopedispose}

Registers a dispose callback on the current active [effect scope](#effectscope). The callback will be invoked when the associated effect scope is stopped.

This method can be used as a non-component-coupled replacement of `onUnmounted` in reusable composition functions, since each Vue component's `setup()` function is also invoked in an effect scope.

- **Type**

  ```ts
  function onScopeDispose(fn: () => void): void
  ```
