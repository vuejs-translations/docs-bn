# Global API: General {#global-api-general}

## version {#version}

Vue এর বর্তমান সংস্করন দেখার জন্য। 

- **প্রকার:** `string`

- **উদাহরন**

  ```js
  import { version } from 'vue'

  console.log(version)
  ```

## nextTick() {#nexttick}

পরবর্তী DOM আপডেট ফ্লাশের অপেক্ষা করার জন্য একটি ইউটিলিটি।

- **প্রকার**

  ```ts
  function nextTick(callback?: () => void): Promise<void>
  ```

- **বিস্তারিত**

  যখন আপনি Vue-তে রিয়েক্টিভ স্টেটকে পরিবর্তন করেন, তখন ফলস্বরূপ DOM আপডেটগুলি সিঙ্ক্রোনাসভাবে প্রয়োগ করা হয় না। পরিবর্তে, Vue এগুলিকে "পরবর্তী টিক" না হওয়া পর্যন্ত বাফার করে যাতে প্রতিটি কম্পোনেন্ট শুধুমাত্র একবার আপডেট হয় তা আপনি যতই স্টেট পরিবর্তন করেন না কেন।

  `nextTick()` DOM আপডেট সম্পূর্ণ হওয়ার জন্য অপেক্ষা করার জন্য একটি স্টেট পরিবর্তনের সাথে সাথে ব্যবহার করা যেতে পারে। আপনি একটি আরগুমেন্ট হিসাবে একটি কলব্যাক পাস করতে পারেন, অথবা রিটার্ন প্রমিজের জন্য অপেক্ষা করতে পারেন৷

- **উদাহরন**

  <div class="composition-api">

  ```vue
  <script setup>
  import { ref, nextTick } from 'vue'

  const count = ref(0)

  async function increment() {
    count.value++

    // DOM not yet updated
    console.log(document.getElementById('counter').textContent) // 0

    await nextTick()
    // DOM is now updated
    console.log(document.getElementById('counter').textContent) // 1
  }
  </script>

  <template>
    <button id="counter" @click="increment">{{ count }}</button>
  </template>
  ```

  </div>
  <div class="options-api">

  ```vue
  <script>
  import { nextTick } from 'vue'

  export default {
    data() {
      return {
        count: 0
      }
    },
    methods: {
      async increment() {
        this.count++

        // DOM not yet updated
        console.log(document.getElementById('counter').textContent) // 0

        await nextTick()
        // DOM is now updated
        console.log(document.getElementById('counter').textContent) // 1
      }
    }
  }
  </script>

  <template>
    <button id="counter" @click="increment">{{ count }}</button>
  </template>
  ```

  </div>

- **আরোও দেখুন** [`this.$nextTick()`](/api/component-instance#nexttick)

## defineComponent() {#definecomponent}

টাইপ ইনফারেন্স সহ একটি Vue কম্পোনেন্ট সংজ্ঞায়িত করার জন্য একটি টাইপ হেল্পার।

- **ধরন**

  ```ts
  // options syntax
  function defineComponent(
    component: ComponentOptions
  ): ComponentConstructor

  // function syntax (requires 3.3+)
  function defineComponent(
    setup: ComponentOptions['setup'],
    extraOptions?: ComponentOptions
  ): () => any
  ```

  > সহজে পাঠ করার জন্য টাইপ সিম্পলীফাইড

- **বিস্তারিত**

  প্রথম আর্গুমেন্টটি একটি কম্পোনেন্ট অপশন অবজেক্ট এক্সপেক্ট করে। রিটার্ন ভ্যালু একই অপশন অবজেক্ট হবে, যেহেতু ফাংশনটি মূলত টাইপ ইনফারেন্সের উদ্দেশ্যে একটি রানটাইম নো-অপ।

  লক্ষ্য করুন যে রিটার্ন টাইপটি একটু বিশেষ: এটি একটি কনস্ট্রাক্টর টাইপ হবে যার ইনস্ট্যান্স টাইপ হল অপশনের উপর ভিত্তি করে অনুমানকৃত কম্পোনেন্ট ইনস্ট্যান্স টাইপ। এটি টাইপ অনুমানের জন্য ব্যবহৃত হয় যখন রিটার্ন টাইপটি TSX-এ ট্যাগ হিসাবে ব্যবহার করা হয়।

  আপনি এইভাবে `defineComponent()` এর রিটার্ন টাইপ থেকে একটি কম্পোনেন্টের ইনস্ট্যান্স টাইপ (এর অপশনগুলোতে `this` এর টাইপের সমতুল্য) বের করতে পারেন:

  ```ts
  const Foo = defineComponent(/* ... */)

  type FooInstance = InstanceType<typeof Foo>
  ```

  ### Function Signature {#function-signature}

  - শুধুমাত্র ৩.৩+ এ সমর্থিত

  `defineComponent()` এছাড়াও একটি বিকল্প স্বাক্ষর রয়েছে যা Composition API এবং [রেন্ডার ফাংশন বা JSX](/guide/extras/render-function.html) এর সাথে ব্যবহার করার জন্য।

  একটি অপশন অবজেক্টে পাস করার পরিবর্তে একটি ফাংশন এক্সপেক্টেড। এই ফাংশনটি Composition API [`setup()`](/api/composition-api-setup.html#composition-api-setup) ফাংশনের মতোই কাজ করে: এটি প্রপস এবং সেটআপ কন্টেক্সট গ্রহণ করে। রিটার্ন ভ্যালু একটি রেন্ডার ফাংশন হওয়া উচিত - উভয় `h()` এবং JSX সমর্থিত:

  ```js
  import { ref, h } from 'vue'

  const Comp = defineComponent(
    (props) => {
      // use Composition API here like in <script setup>
      const count = ref(0)

      return () => {
        // render function or JSX
        return h('div', count.value)
      }
    },
    // extra options, e.g. declare props and emits
    {
      props: {
        /* ... */
      }
    }
  )
  ```

  এই সিগনেচারের জন্য প্রধান ব্যবহারের ক্ষেত্রে হল TypeScript (এবং বিশেষ করে TSX-এর সাথে), কারণ এটি জেনেরিক সাপোর্ট করে:

  ```tsx
  const Comp = defineComponent(
    <T extends string | number>(props: { msg: T; list: T[] }) => {
      // use Composition API here like in <script setup>
      const count = ref(0)

      return () => {
        // render function or JSX
        return <div>{count.value}</div>
      }
    },
    // manual runtime props declaration is currently still needed.
    {
      props: ['msg', 'list']
    }
  )
  ```

  ভবিষ্যতে, আমরা একটি ব্যাবেল প্লাগইন প্রদান করার পরিকল্পনা করছি, যা স্বয়ংক্রিয়ভাবে রানটাইম প্রপস অনুমান করে এবং ইনজেক্ট করে (যেমন SFC-তে `defineProps` এর জন্য) যাতে রানটাইম প্রপস ডিক্লেয়ার বাদ দেওয়া যায়।

  ### Note on webpack Treeshaking {#note-on-webpack-treeshaking}

  কারণ `defineComponent()` হল একটি ফাংশন কল, এটি দেখে মনে হতে পারে এটি কিছু বিল্ড টুলে পার্শ্ব-প্রতিক্রিয়া সৃষ্টি করবে, যেমন ওয়েবপ্যাক এটি কম্পোনেন্টটিকে গাছ-ঝাঁকানো থেকে প্রতিরোধ করবে এমনকি যখন কম্পোনেন্টটি ব্যবহার করা হয় না।

  ওয়েবপ্যাককে জানাতে যে এই ফাংশন কলটি ট্রি-শেক করা নিরাপদ, আপনি ফাংশন কলের আগে একটি `/*#__PURE__*/` নোটেশন স্বরলিপি যোগ করতে পারেন:

  ```js
  export default /*#__PURE__*/ defineComponent(/* ... */)
  ```

  লক্ষ্য রাখবেন আপনি যদি Vite ব্যবহার করেন তবে এটি প্রয়োজনীয় নয়, কারণ Rollup (Vite দ্বারা ব্যবহৃত অন্তর্নিহিত প্রোডাকশন বান্ডলার) যথেষ্ট স্মার্ট যে `defineComponent()` আসলে ম্যানুয়াল টীকাগুলির প্রয়োজন ছাড়াই পার্শ্ব-প্রতিক্রিয়া-মুক্ত।

- **আরও দেখুন** [Guide - Using Vue with TypeScript](/guide/typescript/overview#general-usage-notes)

## defineAsyncComponent() {#defineasynccomponent}

একটি অ্যাসিঙ্ক কম্পোনেন্ট ডিফাইন করে যা লেজি লোড হয় যখন এটি রেন্ডার করা হয়। আরগুমেন্টটি হয় একটি লোডার ফাংশন, অথবা লোডিং আচরণের আরও উন্নত নিয়ন্ত্রণের জন্য একটি অপশন অবজেক্ট হতে পারে।

- **ধরন**

  ```ts
  function defineAsyncComponent(
    source: AsyncComponentLoader | AsyncComponentOptions
  ): Component

  type AsyncComponentLoader = () => Promise<Component>

  interface AsyncComponentOptions {
    loader: AsyncComponentLoader
    loadingComponent?: Component
    errorComponent?: Component
    delay?: number
    timeout?: number
    suspensible?: boolean
    onError?: (
      error: Error,
      retry: () => void,
      fail: () => void,
      attempts: number
    ) => any
  }
  ```

- **See also** [Guide - Async Components](/guide/components/async)
