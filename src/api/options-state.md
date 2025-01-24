# Options: State {#options-state}

## data {#data}

একটি ফাংশন যা কম্পোনেন্ট ইন্সট্যান্সের জন্য ইনিশিয়াল রিয়েক্টিভ স্টেইট রিটার্ন করে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    data?(
      this: ComponentPublicInstance,
      vm: ComponentPublicInstance
    ): object
  }
  ```

- **বিস্তারিত**

  ফাংশনটি একটি প্লেইন জাভাস্ক্রিপ্ট অবজেক্ট রিটার্ন করবে বলে আশা করা হচ্ছে, যা Vue দ্বারা রিয়েক্টিভ করা হবে। ইন্সট্যান্স তৈরি হওয়ার পরে, রিয়েক্টিভ ডেটা অবজেক্টটি `this.$data` হিসাবে অ্যাক্সেস করা যেতে পারে। কম্পোনেন্ট ইনস্ট্যান্স ডেটা অবজেক্টে পাওয়া সমস্ত প্রোপার্টিকেও প্রক্সি করে, তাই `this.a` হবে `this.$data.a` এর সমতুল্য।

  সমস্ত টপ-লেভেলের ডেটা প্রোপার্টি অবশ্যই রিটার্নড ডেটা অবজেক্টে অন্তর্ভুক্ত করতে হবে। `this.$data`-এ নতুন প্রপার্টি যোগ করা সম্ভব, কিন্তু এটা **বাঞ্ছনীয় নয়**। যদি একটি প্রপার্টির কাঙ্খিত ভ্যালু এখনও এভ্যাইলএবল না হয় তাহলে একটি এম্পটি ভ্যালু যেমন `undefined` বা `null` একটি প্লেইসহোল্ডার হিসেবে অন্তর্ভুক্ত করা উচিত যাতে Vue জানে যে প্রোপার্টিটি বিদ্যমান।

  যে প্রপার্টিগুলি `_` বা `$` দিয়ে শুরু হয় সেগুলি কম্পোনেন্ট ইন্সট্যান্সে প্রক্সি করা হবে না কারণ সেগুলি Vue-এর ইন্টারন্যাল প্রোপার্টি এবং API মেথডের সাথে কনফিলিক্ট হতে পারে। আপনাকে সেগুলিকে `this.$data._property` হিসেবে অ্যাক্সেস করতে হবে।

  ব্রাউজার API অবজেক্ট এবং প্রোটোটাইপ প্রোপার্টির মতো অবজেক্টকে তাদের নিজস্ব স্টেইটফুল আচরণের সাথে রিটার্ন দেওয়ার জন্য **বাঞ্ছনীয় নয়**। রিটার্নড অবজেক্টটি আদর্শভাবে একটি প্লেইন অবজেক্ট হওয়া উচিত যা শুধুমাত্র কম্পোনেন্টটির স্টেইটকে রিপ্রেজেন্ট করে।

- **উদাহরন**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    created() {
      console.log(this.a) // 1
      console.log(this.$data) // { a: 1 }
    }
  }
  ```

 মনে রাখবেন যে আপনি যদি `data` প্রপার্টির সাথে একটি অ্যারো ফাংশন ব্যবহার করেন, তাহলে `this` কম্পোনেন্টটির ইন্সট্যান্স হবে না, তবে আপনি এখনও ফাংশনের প্রথম আরগুমেন্ট হিসাবে ইন্সট্যান্সটি অ্যাক্সেস করতে পারেন:

  ```js
  data: (vm) => ({ a: vm.myProp })
  ```

- **আরো দেখুন** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## props {#props}

একটি কম্পোনেন্টের প্রপস ডিক্লার করুন।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    props?: ArrayPropsOptions | ObjectPropsOptions
  }

  type ArrayPropsOptions = string[]

  type ObjectPropsOptions = { [key: string]: Prop }

  type Prop<T = any> = PropOptions<T> | PropType<T> | null

  interface PropOptions<T> {
    type?: PropType<T>
    required?: boolean
    default?: T | ((rawProps: object) => T)
    validator?: (value: unknown, rawProps: object) => boolean
  }

  type PropType<T> = { new (): T } | { new (): T }[]
  ```

  > টাইপগুলি পঠনযোগ্যতার জন্য সহজ করা হয়েছে ।

- **বিস্তারিত**

  Vue-তে, সমস্ত কম্পোনেন্ট প্রপস স্পষ্টভাবে ডিক্লার করা প্রয়োজন। কম্পোনেন্ট প্রপস দুটি আকারে ডিক্লার করা যেতে পারে:

  - সিম্পল ফর্ম স্ট্রিংগুলির একটি অ্যারে ব্যবহার করে ।
  - একটি অবজেক্ট ব্যবহার করে সম্পূর্ণ ফর্ম যেখানে প্রতিটি স্টেইট কী হল প্রপের নাম, এবং ভ্যালু হল প্রপের ধরন (একটি কনস্ট্রাক্টর ফাংশন) বা অ্যাডভ্যান্সড অপশন ।

  অবজেক্ট-ভিত্তিক সিনট্যাক্সের সাথে, প্রতিটি প্রপ নিম্নলিখিত অপশনগুলিকে আরও ডিফাইন করতে পারে:

  - **`type`**: নিম্নলিখিত নেটিভ কনস্ট্রাক্টর হতে পারে: `String`, `Number`, `Boolean`, `Array`, `Object`, `Date`, `Function`, `Symbol` কোনো কাস্টম কনস্ট্রাক্টর ফাংশন বা সেগুলির একটি অ্যারে। ডেভেলপমেন্ট মোডে, Vue একটি প্রপের ভ্যালু ডিক্লারড টাইপের সাথে মেলে কিনা তা পরীক্ষা করবে এবং এটি না হলে একটি ওয়ার্নিং থ্রো করবে। আরো বিস্তারিত জানার জন্য [Prop Validation](/guide/components/props#prop-validation) দেখুন।

  এছাড়াও মনে রাখবেন যে  `Boolean` টাইপের একটি প্রপ ডেভেলপমেন্ট এবং প্রোডাকশন উভয় ক্ষেত্রেই এর ভ্যালু কাস্টিং বিহেইভিয়ারকে প্রভাবিত করে। আরো বিস্তারিত জানার জন্য [Boolean Casting](/guide/components/props#boolean-casting) দেখুন।

  - **`default`**: প্রপের জন্য একটি ডিফল্ট ভ্যালু নির্দিষ্ট করে যখন এটি প্যারেন্ট দ্বারা পাস করা হয় না বা `undefined` ভ্যালু থাকে৷ অবজেক্ট বা অ্যারে ডিফল্ট একটি ফাক্টরি ফাংশন ব্যবহার করে রিটার্ন দিতে হবে। ফ্যাক্টরি ফাংশন আর্গুমেন্ট হিসাবে র-প্রপস অবজেক্ট রিসিভ করে।

  - **`required`**: প্রপ প্রয়োজন হলে ডিফাইন করে। একটি নন-প্রোডাকশন ইনভারনমেন্ট, একটি কনসোল ওয়ার্নিং থ্রো করা হবে যদি এই ভ্যালু সত্য হয় এবং প্রপ পাস না হয়।

  - **`validator`**: কাস্টম ভ্যালিডেটর ফাংশন যা প্রপ ভ্যালু এবং প্রপস অবজেক্টকে আর্গুমেন্ট হিসেবে নেয়। ডেভেলপমেন্ট মোডে, যদি এই ফাংশনটি একটি মিথ্যা মান প্রদান করে (অর্থাৎ বৈধতা ব্যর্থ হয়) তাহলে একটি কনসোল সতর্কতা পাঠানো হবে।

- **উদাহরন**

  সিম্পল ডিক্লারেশন:

  ```js
  export default {
    props: ['size', 'myMessage']
  }
  ```

  ভ্যালিডেইশন সহ অবজেক্ট ডিক্লারেশন:

  ```js
  export default {
    props: {
      // type check
      height: Number,
      // type check plus other validations
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: (value) => {
          return value >= 0
        }
      }
    }
  }
  ```

- **আরো দেখুন**
  - [Guide - Props](/guide/components/props)
  - [Guide - Typing Component Props](/guide/typescript/options-api#typing-component-props) <sup class="vt-badge ts" />

## computed {#computed}

 কম্পোনেন্ট ইনস্ট্যান্সে এক্সপোজড করা কম্পিউটেড করা প্রপার্টিগুলো ডিক্লার করুন।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    computed?: {
      [key: string]: ComputedGetter<any> | WritableComputedOptions<any>
    }
  }

  type ComputedGetter<T> = (
    this: ComponentPublicInstance,
    vm: ComponentPublicInstance
  ) => T

  type ComputedSetter<T> = (
    this: ComponentPublicInstance,
    value: T
  ) => void

  type WritableComputedOptions<T> = {
    get: ComputedGetter<T>
    set: ComputedSetter<T>
  }
  ```

- **বিস্তারিত**

  অপশনটি এমন একটি অবজেক্টকে গ্রহণ করে যেখানে কী টি কম্পিউটেড প্রপার্টির নেইম, এবং ভ্যালুটি হয় একটি কম্পিউটেড গেটার, অথবা `get` এবং `set` মেথড সহ একটি অবজেক্ট (লিখনযোগ্য কম্পিউটেড প্রপার্টিগুলোর জন্য)।

  সমস্ত getters এবং setters তাদের `this` কনটেক্সট স্বয়ংক্রিয়ভাবে কম্পোনেন্ট ইনস্ট্যান্সের সাথে আবদ্ধ থাকে।

  মনে রাখবেন যে আপনি যদি একটি কম্পিউটেড প্রপার্টির সাথে একটি অ্যারো ফাংশন ব্যবহার করেন, তাহলে `this` কম্পোনেন্টটির ইন্সট্যান্সের দিকে নির্দেশ করবে না, তবে আপনি এখনও ফাংশনের প্রথম আরগুমেন্ট হিসাবে ইন্সট্যান্সটি অ্যাক্সেস করতে পারেন:

  ```js
  export default {
    computed: {
      aDouble: (vm) => vm.a * 2
    }
  }
  ```

- **উদাহরন**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    computed: {
      // readonly
      aDouble() {
        return this.a * 2
      },
      // writable
      aPlus: {
        get() {
          return this.a + 1
        },
        set(v) {
          this.a = v - 1
        }
      }
    },
    created() {
      console.log(this.aDouble) // => 2
      console.log(this.aPlus) // => 2

      this.aPlus = 3
      console.log(this.a) // => 2
      console.log(this.aDouble) // => 4
    }
  }
  ```

- **আরো দেখুন**
  - [Guide - Computed Properties](/guide/essentials/computed)
  - [Guide - Typing Computed Properties](/guide/typescript/options-api#typing-computed-properties) <sup class="vt-badge ts" />

## methods {#methods}

 কম্পোনেন্টটির ইন্সট্যান্সে মিক্সড করার মেথড ঘোষণা করুন।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    methods?: {
      [key: string]: (this: ComponentPublicInstance, ...args: any[]) => any
    }
  }
  ```

- **বিস্তারিত**

  ডিক্লারড মেথডগুলি সরাসরি কম্পোনেন্ট ইনস্ট্যান্সে অ্যাক্সেস করা যেতে পারে বা টেমপ্লেট এক্সপ্রেশনে ব্যবহার করা যেতে পারে। সমস্ত মেথডগুলোতে তাদের `this` কনটেক্সট স্বয়ংক্রিয়ভাবে কম্পোনেন্ট ইনস্ট্যান্সের সাথে আবদ্ধ থাকে, এমনকি চারপাশে পাস করার পরেও।

  মেথড ঘোষণা করার সময় অ্যারো ফাংশন ব্যবহার করা এড়িয়ে চলুন, কারণ তারা `this` এর মাধ্যমে কম্পোনেন্ট ইন্সট্যান্সে অ্যাক্সেস পাবে না।

- **উদাহরন**

  ```js
  export default {
    data() {
      return { a: 1 }
    },
    methods: {
      plus() {
        this.a++
      }
    },
    created() {
      this.plus()
      console.log(this.a) // => 2
    }
  }
  ```

- **আরো দেখুন** [Event Handling](/guide/essentials/event-handling)

## watch {#watch}

ওয়াচ কলব্যাককে ডিক্লার করুন ডেটা পরিবর্তনের জন্য আহ্বান করা হবে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    watch?: {
      [key: string]: WatchOptionItem | WatchOptionItem[]
    }
  }

  type WatchOptionItem = string | WatchCallback | ObjectWatchOptionItem

  type WatchCallback<T> = (
    value: T,
    oldValue: T,
    onCleanup: (cleanupFn: () => void) => void
  ) => void

  type ObjectWatchOptionItem = {
    handler: WatchCallback | string
    immediate?: boolean // default: false
    deep?: boolean // default: false
    flush?: 'pre' | 'post' | 'sync' // default: 'pre'
    onTrack?: (event: DebuggerEvent) => void
    onTrigger?: (event: DebuggerEvent) => void
  }
  ```

  > টাইপগুলি পঠনযোগ্যতার জন্য সহজ করা হয়েছে ।

- **বিস্তারিত**

  `watch` অপশনটি এমন একটি অবজেক্ট প্রত্যাশা করে যেখানে কী গুলি হল রিয়েক্টিভ কম্পোনেন্ট ইন্সট্যান্স প্রপার্টিগুলি দেখার জন্য (যেমন বৈশিষ্ট্যগুলি `data` বা `computed` এর মাধ্যমে ডিক্লারড) — এবং ভ্যালুগুলি সংশ্লিষ্ট কলব্যাক। কলব্যাক নতুন ভ্যালু এবং প্রেক্ষিত উৎসের পুরানো ভ্যালু গ্রহণ করে।

  একটি রুট-লেভেল প্রপার্টি ছাড়াও, কী টি একটি সাধারণ ডট-ডিলিমিটেড পাথও হতে পারে, যেমন `a.b.c` মনে রাখবেন যে এই ব্যবহার জটিল এক্সপ্রেশনগুলিকে **সাপোর্ট করে না** - শুধুমাত্র ডট-ডিলিমিটেড পাথ সমর্থিত। আপনি যদি জটিল ডেটা সোর্সগুলি দেখতে চান তবে এর পরিবর্তে ইম্পারেটিভ [`$watch()`](/api/component-instance#watch) API ব্যবহার করুন।

  ভ্যালুটি একটি মেথডের নামের একটি স্ট্রিংও হতে পারে (`method` এর মাধ্যমে ডিক্লার করা হয়েছে), বা এডিশনাল অপশন রয়েছে এমন একটি অবজেক্ট। অবজেক্ট সিনট্যাক্স ব্যবহার করার সময়, `handler` ক্ষেত্রের অধীনে কলব্যাক ডিক্লার করা উচিত। এডিশনাল অপশন অন্তর্ভুক্ত:

  - **`immediate`**: ওয়াচার তৈরিতে অবিলম্বে কলব্যাক ট্রিগার করুন। প্রথম কলে পুরানো ভ্যালু `undefined` হবে।
  - **`deep`**: সোর্সের ডীপ ট্র্যাভার্সাল ফোর্স করে যদি এটি কোনো অবজেক্ট বা অ্যারে হয়, যাতে কলব্যাক ডীপ মিউটেশনের উপর সক্রিয় হয়। [Deep Watchers](/guide/essentials/watchers#deep-watchers) দেখুন।
  - **`flush`**: কলব্যাকের ফ্লাশের সময় সামঞ্জস্য করুন। [Callback Flush Timing](/guide/essentials/watchers#callback-flush-timing) এবং [`watchEffect()`](/api/reactivity-core#watcheffect) দেখুন।
  - **`onTrack / onTrigger`**: ওয়াচারের ডিপেন্ডেন্সিসমূহ ডিবাগ করুন। [Watcher Debugging](/guide/extras/reactivity-in-depth#watcher-debugging) দেখুন।

  ওয়াচ কলব্যাক ডিক্লার করার সময় অ্যারো ফাংশন ব্যবহার করা এড়িয়ে চলুন কারণ তারা `this` এর মাধ্যমে কম্পোনেন্ট ইন্সট্যান্সে অ্যাক্সেস পাবে না।

- **উদাহরন**

  ```js
  export default {
    data() {
      return {
        a: 1,
        b: 2,
        c: {
          d: 4
        },
        e: 5,
        f: 6
      }
    },
    watch: {
      // watching top-level property
      a(val, oldVal) {
        console.log(`new: ${val}, old: ${oldVal}`)
      },
      // string method name
      b: 'someMethod',
      // the callback will be called whenever any of the watched object properties change regardless of their nested depth
      c: {
        handler(val, oldVal) {
          console.log('c changed')
        },
        deep: true
      },
      // watching a single nested property:
      'c.d': function (val, oldVal) {
        // do something
      },
      // the callback will be called immediately after the start of the observation
      e: {
        handler(val, oldVal) {
          console.log('e changed')
        },
        immediate: true
      },
      // you can pass array of callbacks, they will be called one-by-one
      f: [
        'handle1',
        function handle2(val, oldVal) {
          console.log('handle2 triggered')
        },
        {
          handler: function handle3(val, oldVal) {
            console.log('handle3 triggered')
          }
          /* ... */
        }
      ]
    },
    methods: {
      someMethod() {
        console.log('b changed')
      },
      handle1() {
        console.log('handle 1 triggered')
      }
    },
    created() {
      this.a = 3 // => new: 3, old: 1
    }
  }
  ```

- **আরো দেখুন** [Watchers](/guide/essentials/watchers)

## emits {#emits}

 কম্পোনেন্ট দ্বারা কাস্টম ইভেন্ট ইমিটেড ডিক্লার করুন।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    emits?: ArrayEmitsOptions | ObjectEmitsOptions
  }

  type ArrayEmitsOptions = string[]

  type ObjectEmitsOptions = { [key: string]: EmitValidator | null }

  type EmitValidator = (...args: unknown[]) => boolean
  ```

- **বিস্তারিত**

  ইমিটেড ইভেন্ট দুটি ফর্মস ডিক্লার করা যেতে পারে:

  - সহজ ফর্ম ব্যবহার করে স্ট্রিংগুলির একটি অ্যারে। 
  - একটি অবজেক্ট ব্যবহার করে সম্পূর্ণ ফর্ম যেখানে প্রতিটি প্রপার্টি কী ইভেন্টের নাম, এবং ভ্যালুটি হয় `null` বা একটি ভ্যালিডেটর ফাংশন।

  ভ্যালিডেশন ফাংশন কম্পোনেন্টটির `$emit` কলে পাস করা অ্যাডিশনাল আর্গুমেন্টগুলি পাবে। উদাহরণস্বরূপ, যদি `this.$emit('foo', 1)` বলা হয়, তাহলে `foo` এর জন্য সংশ্লিষ্ট যাচাইকারী আর্গুমেন্ট `1` পাবে। ইভেন্ট আর্গুমেন্ট বৈধ কিনা তা নির্দেশ করতে ভ্যালিডেটর ফাংশন একটি বুলিয়ান রিটার্ন করবে।

  মনে রাখবেন যে `emits` অপশনটি প্রভাবিত করে কোন ইভেন্ট লিসেনারকে কম্পোনেন্ট ইভেন্ট লিসেনারস হিসাবে বিবেচনা করা হয়, নেটিভ DOM ইভেন্ট লিসেনারস এর পরিবর্তে। ডিক্লারড ইভেন্টেস লিসেনারস কম্পোনেন্টের `$attrs` অবজেক্ট থেকে রিমুভ করা হবে, তাই সেগুলিকে কম্পোনেন্টের রুট এলিমেন্টে পাস করা হবে না। আরও বিস্তারিত জানার জন্য [Fallthrough Attributes](/guide/components/attrs) দেখুন।

- **উদাহরন**

  Array syntax:

  ```js
  export default {
    emits: ['check'],
    created() {
      this.$emit('check')
    }
  }
  ```

  Object syntax:

  ```js
  export default {
    emits: {
      // no validation
      click: null,

      // with validation
      submit: (payload) => {
        if (payload.email && payload.password) {
          return true
        } else {
          console.warn(`Invalid submit event payload!`)
          return false
        }
      }
    }
  }
  ```

- **আরো দেখুন**
  - [Guide - Fallthrough Attributes](/guide/components/attrs)
  - [Guide - Typing Component Emits](/guide/typescript/options-api#typing-component-emits) <sup class="vt-badge ts" />

## expose {#expose}

টেমপ্লেট রেফের মাধ্যমে প্যারেন্টের দ্বারা কম্পোনেন্টের ইন্সট্যান্স অ্যাক্সেস করা হলে এক্সপোজড করা পাব্লিক প্রপার্টিগুলো ডিক্লার করুন।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    expose?: string[]
  }
  ```

- **বিস্তারিত**

  ডিফল্টভাবে, `$parent`, `$root`, বা টেমপ্লেট রেফের মাধ্যমে অ্যাক্সেস করার সময় একটি কম্পোনেন্ট ইনস্ট্যান্স প্যারেন্টের কাছে সমস্ত ইন্সট্যান্স প্রপার্টিগুলো প্রকাশ করে। এটি অবাঞ্ছিত হতে পারে, যেহেতু একটি কম্পোনেন্টের সম্ভবত ইন্টারন্যাল স্টেইট বা মেথড রয়েছে যা টাইট কাপলিং এড়াতে প্রাইভেট রাখা উচিত।

  `expose` অপশনটি প্রপার্টির নামের স্ট্রিংগুলির একটি লিস্ট এক্সপেক্ট করে। যখন `expose` ব্যবহার করা হয়, শুধুমাত্র সুস্পষ্টভাবে তালিকাভুক্ত প্রপার্টিগুলো কম্পোনেন্টের পাব্লিক ইন্সট্যান্সে এক্সপোজড করা হবে।

  `expose` শুধুমাত্র ইউজার-ডিফাইনড প্রপার্টিগুলোকে প্রভাবিত করে - এটি বিল্ট-ইন  কম্পোনেন্ট ইন্সট্যান্স প্রপার্টিগুলোকে ফিল্টার করে না।

- **উদাহরন**

  ```js
  export default {
    // only `publicMethod` will be available on the public instance
    expose: ['publicMethod'],
    methods: {
      publicMethod() {
        // ...
      },
      privateMethod() {
        // ...
      }
    }
  }
  ```
