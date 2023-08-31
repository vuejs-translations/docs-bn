# Options: Composition {#options-composition}

## provide {#provide}

প্রোভাইড ভ্যালু যা ডিসেনডেন্ট কম্পোনেন্ট দ্বারা ইনজেকশন করা যেতে পারে । 

- **প্রকার**

  ```ts
  interface ComponentOptions {
    provide?: object | ((this: ComponentPublicInstance) => object)
  }
  ```

- **বিস্তারিত**

  `provide` এবং [`inject`](#inject) একসাথে ব্যবহার করা হয় একটি অ্যানচেস্টর কম্পোনেন্টকে তার সমস্ত ডিসেনডেন্টদের জন্য ডিপেনডেন্সি ইনজেক্টর হিসাবে সারভ করার অনুমতি দেওয়ার জন্য, কম্পোনেন্ট হায়ারার্কি যতটা ডীপ হোক না কেন, যতক্ষণ তারা একই প্যারন্ট চেইন মধ্যে থাকে ।  

  `provide` অপশন হয় কোনো অবজেক্ট বা কোনো ফাংশন হতে হবে যা কোনো অবজেক্টকে রিটার্ন দেয়। এই অবজেক্টটি ঐ প্রোপার্টিগুলোকে কন্টেইন করে যা তার বংশধরদের মধ্যে ইনজেকশনের জন্য অ্যাভেইঅ্যাবল । আপনি এই অবজেক্টটিতে কী হিসাবে সিম্বল ব্যবহার করতে পারেন।

- **উদাহরন**

  মৌলিক ব্যবহার:

  ```js
  const s = Symbol()

  export default {
    provide: {
      foo: 'foo',
      [s]: 'bar'
    }
  }
  ```

  প্রতি-কম্পোনেন্ট স্টেইট প্রোভাইড করতে একটি ফাংশন ব্যবহার করা:

  ```js
  export default {
    data() {
      return {
        msg: 'foo'
      }
    }
    provide() {
      return {
        msg: this.msg
      }
    }
  }
  ```

 উপরের উদাহরনে লক্ষ্য রাখবেন, প্রদত্ত `msg` রিয়েক্টিভ হবে না। আরো বিস্তারিত জানার জন্য [Working with Reactivity](/guide/components/provide-inject#working-with-reactivity) দেখুন।

- **আরো দেখুন** [Provide / Inject](/guide/components/provide-inject)

## inject {#inject}

অ্যানচেস্টর প্রোভাইডারদের থেকে সনাক্ত করে বর্তমান কম্পোনেন্টে ইনজেক্ট করার প্রোপার্টিগুলি ডিকলার করুন৷

- **প্রকার**

  ```ts
  interface ComponentOptions {
    inject?: ArrayInjectOptions | ObjectInjectOptions
  }

  type ArrayInjectOptions = string[]

  type ObjectInjectOptions = {
    [key: string | symbol]:
      | string
      | symbol
      | { from?: string | symbol; default?: any }
  }
  ```

- **বিস্তারিত**

  `inject` অপশনটি হতে হবেঃ

  - স্ট্রিংস এর একটি অ্যারে, বা
  - একটি অবজেক্ট যেখানে কীগুলি লোকাল বাইন্ডিং নেইম এবং ভ্যালু হয়:
    - অ্যাভেইলঅ্যাবল ইনজেকশনে সার্চ করার জন্য কী (string বা Symbol), অথবা 
    - একটি অবজেক্ট যেখানে:
      - অ্যাভেইলঅ্যাবল ইনজেকশনগুলিতে সার্চ করার জন্য `from` প্রোপার্টি হল key (string বা Symbol), এবং
      - `default` প্রোপারটি ফলব্যাক ভ্যালু হিসাবে ব্যবহৃত হয়। প্রপস ডিফল্ট ভ্যালুর অনুরূপ, একাধিক কম্পোনেন্ট ইন্সট্যান্সের মধ্যে ভ্যালু শেয়ারিং এড়াতে অবজেক্টের টাইপগুলির জন্য একটি ফ্যাক্টরি ফাংশন প্রয়োজন।

  একটি ইনজেকটেক  প্রোপার্টি `undefined` হবে যদি কোনো ম্যাচিং প্রোপার্টি বা কোনো ডিফল্ট ভ্যালু প্রদান করা না হয়।

  লক্ষ্য করুন যে ইনজেকশন বাইন্ডিং রিয়েক্টিভ নয়। এটা ইচ্ছাকৃত। যাইহোক, যদি ইনজেকশনের মানটি একটি রিয়েক্টিভ অবজেক্ট হয়, তবে সেই অবজেক্টের প্রোপার্টিগুলি রিয়েক্টিভ থাকে। আরো বিস্তারিত জানার জন্য [Working with Reactivity](/guide/components/provide-inject#working-with-reactivity) দেখুন।

- **উদাহরন**

  মৌলিক ব্যবহার:

  ```js
  export default {
    inject: ['foo'],
    created() {
      console.log(this.foo)
    }
  }
  ```

  একটি প্রপের জন্য ডিফল্ট হিসাবে একটি ইনজেকটেড ভ্যালু ব্যবহার করা:

  ```js
  const Child = {
    inject: ['foo'],
    props: {
      bar: {
        default() {
          return this.foo
        }
      }
    }
  }
  ```

  ডেটা এন্ট্রি হিসাবে একটি ইনজেকটেড ভ্যালু ব্যবহার করা:

  ```js
  const Child = {
    inject: ['foo'],
    data() {
      return {
        bar: this.foo
      }
    }
  }
  ```

  ইনজেকশনগুলি ডিফল্ট ভ্যালুসহ অপশনাল হতে পারে:

  ```js
  const Child = {
    inject: {
      foo: { default: 'foo' }
    }
  }
  ```

  যদি এটি একটি ভিন্ন নামের একটি প্রোপার্টি থেকে ইনজেকশনের প্রয়োজন হয়, সোর্স প্রোপার্টি বোঝাতে `from` ব্যবহার করুন:

  ```js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: 'foo'
      }
    }
  }
  ```

  প্রপ ডিফল্টের মতো, আপনাকে নন-প্রিমিটিভ ভ্যালুগুলির জন্য একটি ফ্যাক্টরি ফাংশন ব্যবহার করতে হবে:

  ```js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: () => [1, 2, 3]
      }
    }
  }
  ```

- **আরো দেখুন** [Provide / Inject](/guide/components/provide-inject)

## mixins {#mixins}

কারেন্ট কম্পোনেন্টে মিক্সড, অপশন অবজক্টের একটি অ্যারে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    mixins?: ComponentOptions[]
  }
  ```

- **বিস্তারিত**

  `mixins` অপশনটি মিক্সিন অবজেক্টের একটি অ্যারে গ্রহণ করে। এই মিক্সিন অবজেক্টে স্বাভাবিক ইনস্ট্যান্স অবজেক্টের মত ইনস্ট্যান্স অপশন থাকতে পারে এবং নির্দিষ্ট অপশন মার্জিং লজিক ব্যবহার করে এগুলি ইভেনচুয়াল অপশনের সাথে মার্জ করা হবে। উদাহরণস্বরূপ, যদি আপনার মিক্সিনে একটি `create` হুক থাকে এবং কম্পোনেন্টটিতেও একটি থাকে, উভয় ফাংশন কল করা হবে।

  মিক্সিন হুকগুলিকে যে ক্রমে দেওয়া হয় সেই ক্রমে কল করা হয় এবং কম্পোনেন্টের নিজস্ব হুকের আগে কল করা হয়।

  :::warning এখন আর রিকোমেন্ডেড নয়
  Vue 2-এ, কম্পোনেন্ট লজিক, পুনরায় ব্যবহারযোগ্য অংশগুলি তৈরি করার জন্য মিক্সিনগুলি ছিল প্রাথমিক প্রক্রিয়া। Vue 3-তে মিক্সিনগুলিকে সাপোর্ট করা অব্যাহত থাকলেও, [Composable functions using Composition API](/guide/reusability/composables) এখন কম্পোনেন্টগুলির মধ্যে কোড পুনঃব্যবহারের জন্য পছন্দের পদ্ধতি।
  :::

- **উদাহরন**

  ```js
  const mixin = {
    created() {
      console.log(1)
    }
  }

  createApp({
    created() {
      console.log(2)
    },
    mixins: [mixin]
  })

  // => 1
  // => 2
  ```

## extends {#extends}

A "base class" component to extend from.

- **Type**

  ```ts
  interface ComponentOptions {
    extends?: ComponentOptions
  }
  ```

- **Details**

  Allows one component to extend another, inheriting its component options.

  From an implementation perspective, `extends` is almost identical to `mixins`. The component specified by `extends` will be treated as though it were the first mixin.

  However, `extends` and `mixins` express different intents. The `mixins` option is primarily used to compose chunks of functionality, whereas `extends` is primarily concerned with inheritance.

  As with `mixins`, any options (except for `setup()`) will be merged using the relevant merge strategy.

- **Example**

  ```js
  const CompA = { ... }

  const CompB = {
    extends: CompA,
    ...
  }
  ```

  :::warning Not Recommended for Composition API
  `extends` is designed for Options API and does not handle the merging of the `setup()` hook.

  In Composition API, the preferred mental model for logic reuse is "compose" over "inheritance". If you have logic from a component that needs to be reused in another one, consider extracting the relevant logic into a [Composable](/guide/reusability/composables#composables).

  If you still intend to "extend" a component using Composition API, you can call the base component's `setup()` in the extending component's `setup()`:

  ```js
  import Base from './Base.js'
  export default {
    extends: Base,
    setup(props, ctx) {
      return {
        ...Base.setup(props, ctx),
        // local bindings
      }
    }
  }
  ```
  :::
