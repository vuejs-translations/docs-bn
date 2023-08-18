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

  ### Function Signature <sup class="vt-badge" data-text="3.3+" /> {#function-signature}

  `defineComponent()` এর সাথে একটি বিকল্প সিগনেচার রয়েছে যা Composition API এর সাথে ব্যবহার করা হয় এবং [render functions or JSX](/guide/extras/render-function.html).

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

  The main use case for this signature is with TypeScript (and in particular with TSX), as it supports generics:

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

  In the future, we plan to provide a Babel plugin that automatically infers and injects the runtime props (like for `defineProps` in SFCs) so that the runtime props declaration can be omitted.

  ### Note on webpack Treeshaking {#note-on-webpack-treeshaking}

  Because `defineComponent()` is a function call, it could look like that it would produce side-effects to some build tools, e.g. webpack. This will prevent the component from being tree-shaken even when the component is never used.

  To tell webpack that this function call is safe to be tree-shaken, you can add a `/*#__PURE__*/` comment notation before the function call:

  ```js
  export default /*#__PURE__*/ defineComponent(/* ... */)
  ```

  Note this is not necessary if you are using Vite, because Rollup (the underlying production bundler used by Vite) is smart enough to determine that `defineComponent()` is in fact side-effect-free without the need for manual annotations.

- **See also** [Guide - Using Vue with TypeScript](/guide/typescript/overview#general-usage-notes)

## defineAsyncComponent() {#defineasynccomponent}

Define an async component which is lazy loaded only when it is rendered. The argument can either be a loader function, or an options object for more advanced control of the loading behavior.

- **Type**

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

## defineCustomElement() {#definecustomelement}

This method accepts the same argument as [`defineComponent`](#definecomponent), but instead returns a native [Custom Element](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements) class constructor.

- **Type**

  ```ts
  function defineCustomElement(
    component:
      | (ComponentOptions & { styles?: string[] })
      | ComponentOptions['setup']
  ): {
    new (props?: object): HTMLElement
  }
  ```

  > Type is simplified for readability.

- **Details**

  In addition to normal component options, `defineCustomElement()` also supports a special option `styles`, which should be an array of inlined CSS strings, for providing CSS that should be injected into the element's shadow root.

  The return value is a custom element constructor that can be registered using [`customElements.define()`](https://developer.mozilla.org/en-US/docs/Web/API/CustomElementRegistry/define).

- **Example**

  ```js
  import { defineCustomElement } from 'vue'

  const MyVueElement = defineCustomElement({
    /* component options */
  })

  // Register the custom element.
  customElements.define('my-vue-element', MyVueElement)
  ```

- **See also**

  - [Guide - Building Custom Elements with Vue](/guide/extras/web-components#building-custom-elements-with-vue)

  - Also note that `defineCustomElement()` requires [special config](/guide/extras/web-components#sfc-as-custom-element) when used with Single-File Components.
