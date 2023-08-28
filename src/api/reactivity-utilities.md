# Reactivity API: Utilities {#reactivity-api-utilities}

## isRef() {#isref}

একটি রেফ অবজেক্ট একটি ভ্যালু কিনা পরিক্ষা করা । 

- **প্রকার**

  ```ts
  function isRef<T>(r: Ref<T> | unknown): r is Ref<T>
  ```

  খেয়াল রাখবেন রিটার্ন টাইপ হল একটি [type predicate](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates), যার মানে `isRef` টাইপ গার্ড হিসেবে ব্যবহার করা যেতে পারে :

  ```ts
  let foo: unknown
  if (isRef(foo)) {
    // foo's type is narrowed to Ref<unknown>
    foo.value
  }
  ```

## unref() {#unref}

আর্গুমেন্টটি রেফ হলে ইনার ভ্যালু রিটার্ন করে, অন্যথায় আর্গুমেন্ট নিজেই রিটার্ন করে। এটি  একটি সুগার ফাংশন এর জন্য `val = isRef(val) ? val.value : val` ।

- **প্রকার**

  ```ts
  function unref<T>(ref: T | Ref<T>): T
  ```

- **উদাহরন**

  ```ts
  function useFoo(x: number | Ref<number>) {
    const unwrapped = unref(x)
    // unwrapped is guaranteed to be number now
  }
  ```

## toRef() {#toref}

ভ্যালু/রেফ/গেটারকে রেফসে (3.3+) নরমালাইজ করতে ব্যবহার করা যেতে পারে।

একটি সোর্স রিয়েক্টিভ অবজেক্টের একটি প্রপার্টির জন্য একটি রেফ তৈরি করতেও ব্যবহার করা যেতে পারে। তৈরি করা রেফ এর সোর্স প্রপার্টির সাথে সিঙ্ক করা হয়েছে: সোর্স প্রপার্টি পরিবর্তন করলে রেফ আপডেট হবে এবং এর বিপরীত হবে।

- **প্রকার**

  ```ts
  // normalization signature (3.3+)
  function toRef<T>(
    value: T
  ): T extends () => infer R
    ? Readonly<Ref<R>>
    : T extends Ref
    ? T
    : Ref<UnwrapRef<T>>

  // object property signature
  function toRef<T extends object, K extends keyof T>(
    object: T,
    key: K,
    defaultValue?: T[K]
  ): ToRef<T[K]>

  type ToRef<T> = T extends Ref ? T : Ref<T>
  ```

- **উদাহরন**

  Normalization signature (3.3+):

  ```js
  // returns existing refs as-is
  toRef(existingRef)

  // creates a readonly ref that calls the getter on .value access
  toRef(() => props.foo)

  // creates normal refs from non-function values
  // equivalent to ref(1)
  toRef(1)
  ```

  Object property signature:

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // a two-way ref that syncs with the original property
  const fooRef = toRef(state, 'foo')

  // mutating the ref updates the original
  fooRef.value++
  console.log(state.foo) // 2

  // mutating the original also updates the ref
  state.foo++
  console.log(fooRef.value) // 3
  ```

  Note this is different from:

  ```js
  const fooRef = ref(state.foo)
  ```

  উপরের রেফটি `state.foo`-এর সাথে **not** সিঙ্ক করা হয়নি, কারণ `ref()` একটি প্লেইন নম্বর ভ্যালু রিসিভ করে।

  `toRef()` ইউজফুল যখন আপনি একটি কম্পোজেবল ফাংশনে একটি প্রপের রেফ পাস করতে চান:

  ```vue
  <script setup>
  import { toRef } from 'vue'

  const props = defineProps(/* ... */)

  // convert `props.foo` into a ref, then pass into
  // a composable
  useSomeFeature(toRef(props, 'foo'))

  // getter syntax - recommended in 3.3+
  useSomeFeature(toRef(() => props.foo))
  </script>
  ```

  যখন `toRef` কম্পোনেন্ট প্রপসের সাথে ব্যবহার করা হয়, তখনও প্রপগুলিকে পরিবর্তন করার জন্য সাধারণ বিধিনিষেধ প্রযোজ্য। রেফের জন্য একটি নতুন ভ্যালু অ্যাসাইন করার চেষ্টা করা সরাসরি প্রপ পরিবর্তন করার চেষ্টা করার সমতুল্য এবং অনুমোদিত নয়। এই পরিস্থিতিতে আপনি পরিবর্তে `get` এবং `set` এর সাথে [`computed`](./reactivity-core#computed) ব্যবহার করার কথা বিবেচনা করতে পারেন। আরও তথ্যের জন্য  [using `v-model` with components](/guide/components/v-model) নির্দেশিকা দেখুন।

  অবজেক্ট প্রপার্টি সিগনেচার ব্যবহার করার সময়, `toRef()` একটি ব্যবহারযোগ্য রেফ প্রদান করবে এমনকি যদি সোর্স প্রপার্টি বর্তমানে বিদ্যমান না থাকে। এটি অপশনাল প্রপার্টিগুলির সাথে কাজ করা সম্ভব করে, যা [`toRefs`](#torefs) দ্বারা বাছাই করা হবে না।

## toValue() <sup class="vt-badge" data-text="3.3+" /> {#tovalue}

ভ্যালুস/রেফ/গেটারকে ভ্যালুকে স্বাভাবিক করে তোলে। এটি [unref()](#unref) এর অনুরূপ, ব্যতীত এটি গেটারকেও স্বাভাবিক করে। যদি আর্গুমেন্টটি একটি গেটার হয়, তবে এটি কল করা হবে এবং এর রিটার্ন ভ্যালু রিটার্ন দেওয়া হবে।

এটি একটি আরগুমেন্টকে স্বাভাবিক করার জন্য [Composables](/guide/reusability/composables.html) এ ব্যবহার করা যেতে পারে যা হয় একটি ভ্যালু, একটি রেফ বা একটি গেটার হতে পারে।

- **প্রকার**

  ```ts
  function toValue<T>(source: T | Ref<T> | (() => T)): T
  ```

- **উদাহরন**

  ```js
  toValue(1) //       --> 1
  toValue(ref(1)) //  --> 1
  toValue(() => 1) // --> 1
  ```

  Normalizing arguments in composables:

  ```ts
  import type { MaybeRefOrGetter } from 'vue'

  function useFeature(id: MaybeRefOrGetter<number>) {
    watch(() => toValue(id), id => {
      // react to id changes
    })
  }

  // this composable supports any of the following:
  useFeature(1)
  useFeature(ref(1))
  useFeature(() => 1)
  ```

## toRefs() {#torefs}

একটি রিয়েক্টিভ অবজেক্টকে একটি প্লেইন অবজেক্টে রূপান্তরিত করে যেখানে ফলস্বরূপ অবজেক্টের প্রতিটি প্রপার্টি মূল অবজেক্টের সংশ্লিষ্ট প্রপার্টির দিকে নির্দেশ করে। প্রতিটি পৃথক রেফ [`toRef()`](#toref) ব্যবহার করে তৈরি করা হয়।

- **প্রকার**

  ```ts
  function toRefs<T extends object>(
    object: T
  ): {
    [K in keyof T]: ToRef<T[K]>
  }

  type ToRef = T extends Ref ? T : Ref<T>
  ```

- **উদাহরন**

  ```js
  const state = reactive({
    foo: 1,
    bar: 2
  })

  const stateAsRefs = toRefs(state)
  /*
  Type of stateAsRefs: {
    foo: Ref<number>,
    bar: Ref<number>
  }
  */

  // The ref and the original property is "linked"
  state.foo++
  console.log(stateAsRefs.foo.value) // 2

  stateAsRefs.foo.value++
  console.log(state.foo) // 3
  ```

  একটি কম্পোসেবল ফাংশন থেকে একটি রিয়েক্টিভ অবজেক্ট রিটার্ন দেওয়ার সময় `toRefs` দরকারী যাতে কনজিউমিং কম্পোনেন্ট রিয়েক্টিভতা না হারিয়ে রিটার্ন আসা অবজেক্টকে ডিস্ট্রাকচার/স্প্রেড করতে পারে:

  ```js
  function useFeatureX() {
    const state = reactive({
      foo: 1,
      bar: 2
    })

    // ...logic operating on state

    // convert to refs when returning
    return toRefs(state)
  }

  // can destructure without losing reactivity
  const { foo, bar } = useFeatureX()
  ```

  `toRefs` শুধুমাত্র সেই প্রপার্টিগুলির জন্য refs তৈরি করবে যা কলের সময় সোর্স অবজেক্টে গণনাযোগ্য। এখনও বিদ্যমান নাও থাকতে পারে এমন একটি প্রপার্টির জন্য একটি রেফ তৈরি করতে, পরিবর্তে [`toRef`](#toref) ব্যবহার করুন।

## isProxy() {#isproxy}

একটি অবজেক্ট [`reactive()`](./reactivity-core#reactive), [`readonly()`](./reactivity-core#readonly), [`shallowReactive()`] দ্বারা তৈরি প্রক্সি কিনা তা পরীক্ষা করে। (./reactivity-advanced#shallowreactive) বা [`shallowReadonly()`](./reactivity-advanced#shallowreadonly)।

- **প্রকার**

  ```ts
  function isProxy(value: unknown): boolean
  ```

## isReactive() {#isreactive}

একটি অবজেক্ট [`reactive()`](./reactivity-core#reactive) বা [`shallowReactive()`](./reactivity-advanced#shallowreactive) দ্বারা তৈরি প্রক্সি কিনা তা পরীক্ষা করা ।

- **প্রকার**

  ```ts
  function isReactive(value: unknown): boolean
  ```

## isReadonly() {#isreadonly}

পাস করা ভ্যালুটি একটি রিডঅনলি অবজেক্ট কিনা তা পরীক্ষা করে। একটি রিডঅনলি অবজেক্টের প্রপার্টি পরিবর্তন হতে পারে, কিন্তু  পাস করা অবজেক্টের মাধ্যমে তারা সরাসরি অ্যাসাইন হবে না।

[`readonly()`](./reactivity-core#readonly) এবং [`shallowReadonly()`](./reactivity-advanced#shallowreadonly) দ্বারা সৃষ্ট প্রক্সি উভয়ই শুধুমাত্র রিডঅনলি হিসাবে বিবেচিত হয়, যেমন একটি [`computed()`](./reactivity-core#computed) রেফ একটি `set` ফাংশন ছাড়াই।

- **প্রকার**

  ```ts
  function isReadonly(value: unknown): boolean
  ```
