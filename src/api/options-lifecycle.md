# Options: Lifecycle {#options-lifecycle}

:::info আরো দেখুন
লাইফসাইকেল হুকগুলির শেয়ারড ব্যবহারের জন্য, [Guide - Lifecycle Hooks](/guide/essentials/lifecycle)
:::

## beforeCreate {#beforecreate}

ইনস্ট্যান্স ইনিশিয়ালাইজড হলে কল করা হয়।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    beforeCreate?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  `data()` বা `computed`-এর মতো অন্যান্য অপশনগুলি প্রসেস করার আগে, প্রপস রেজোলিউশনের পরে, ইনস্ট্যান্স ইনিশিয়ালাইজড হলে অবিলম্বে কল করা হয়।

  লক্ষ্য রাখবেন যে কম্পোজিশন API-এর `setup()` হুক কোনো অপশন API হুকের আগে কল করা হয়, এমনকি `beforeCreate()`।

## created {#created}

সকল স্টেট-সম্পর্কিত অপশনগুলি  কল করা হয় ইন্সট্যান্সটি প্রসেসিং শেষ করার পরে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    created?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  যখন এই হুকটি কল করা হয়, তখন নিম্নলিখিতগুলি সেট আপ করা হয়েছে: রিয়েক্টিভ ডেটা, কম্পিউটেড প্রোপার্টিস, মেথডস এবং ওয়াচার্স। যাইহোক, মাউন্টিং পর্ব শুরু করা হয়নি, এবং `$el` প্রোপার্টি এখনও এভেইলঅ্যাবল হবে না।

## beforeMount {#beforemount}

কম্পোনেন্ট মাউন্ট করার ঠিক আগে কল করা হয়।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    beforeMount?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  যখন এই হুকটি কল করা হয়, কম্পোনেন্টটি তার রিয়েক্টিভ স্টেট সেট আপ করা শেষ করেছে, কিন্তু এখনও কোন DOM নোড তৈরি করা হয়নি। এটি প্রথমবারের মতো এর DOM রেন্ডার ইফেক্ট এক্সিকিউট করতে চলেছে৷

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি কল করা হয় না।**

## mounted {#mounted}

কল করার পরে কম্পোনেন্ট মাউন্ট করা হয়েছে.

- **প্রকার**

  ```ts
  interface ComponentOptions {
    mounted?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  একটি কম্পোনেন্ট  বিবেচনা করা হয় মাউন্টেড এর পরে:

  - এর সকল সিঙ্ক্রোনাস চাইল্ড কম্পোনেন্ট মাউন্ট করা হয়েছে (`<Suspense>` ট্রি এর  ভিতরে অ্যাসিঙ্ক কম্পোনেন্ট বা কম্পোনেন্ট অন্তর্ভুক্ত নয়)।

  - এর নিজস্ব DOM ট্রি তৈরি করে প্যারেন্ট কন্টেইনারে ইনসার্ট করা হয়েছে। খেয়াল রাখবেন এটি শুধুমাত্র নিশ্চিত করে যে কম্পোনেন্টের DOM ট্রি ইন-ডকুমেন্ট আছে কিনা যদি অ্যাপ্লিকেশনের রুট কন্টেনারটিও ইন-ডকুমেন্ট হয়।

  এই হুকটি সাধারণত সাইড ইফেক্ট সম্পাদনের জন্য ব্যবহৃত হয় যার জন্য কম্পোনেন্টের রেন্ডার করা DOM-এ অ্যাক্সেস প্রয়োজন, অথবা  [server-rendered application](/guide/scaling-up/ssr) এ ক্লায়েন্টের কাছে DOM-সম্পর্কিত কোড লিমিট করার জন্য।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি কল করা হয় না।**

## beforeUpdate {#beforeupdate}

একটি রিয়েক্টিভ স্টেট পরিবর্তনের কারণে কম্পোনেন্টটি তার DOM ট্রি আপডেট করার  ঠিক আগে কল করা হয়েছে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    beforeUpdate?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  Vue DOM আপডেট করার আগে এই হুকটি DOM স্টেট অ্যাক্সেস করতে ব্যবহার করা যেতে পারে। এই হুকের ভিতরে কম্পোনেন্ট স্টেট পরিবর্তন করাও সেইফ।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি কল করা হয় না।**

## updated {#updated}

একটি রিয়েক্টিভ স্টেট পরিবর্তনের কারণে কম্পোনেন্টটি তার DOM ট্রি আপডেট করার পরে কল করা হয়।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    updated?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  একটি প্যারেন্ট কম্পোনেন্টের আপডেটেড হুককে এর চাইল্ড কম্পোনেন্টের পরে কল করা হয়।

  এই হুকটিকে কম্পোনেন্টের যেকোনো DOM আপডেটের পর কল করা হয়, যা বিভিন্ন স্টেটের পরিবর্তনের কারণে হতে পারে। আপনি যদি একটি নির্দিষ্ট স্টেটের পরিবর্তনের পরে আপডেট হওয়া DOM অ্যাক্সেস করতে চান তবে পরিবর্তে [nextTick()](/api/general#nexttick) ব্যবহার করুন।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি কল করা হয় না।**

  :::warning
  আপডেট করা হুকে কম্পোনেন্টের স্টেটের পরিবর্তন করবেন না - এটি সম্ভবত একটি ইনফিনিট আপডেট লুপের দিকে নিয়ে যাবে!
  :::

## beforeUnmount {#beforeunmount}

একটি কম্পোনেন্ট ইনস্ট্যান্স আনমাউন্ট করার ঠিক আগে কল করা হয়।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    beforeUnmount?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  যখন এই হুক কল করা হয়, তখনও কম্পোনেন্ট ইনস্ট্যান্স সম্পূর্ণরূপে ফাংশনাল থাকে।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি কল করা হয় না।**

## unmounted {#unmounted}

কম্পোনেন্ট আনমাউন্ট করার পরে কল করা হয়।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    unmounted?(this: ComponentPublicInstance): void
  }
  ```

- **বিস্তারিত**

  একটি কম্পোনেন্ট পরে আনমাউন্ট হিসেবে বিবেচনা করা হয়:

  - এর সমস্ত চাইল্ড কম্পোনেন্ট আনমাউন্ট করা হয়েছে।

  - এর সমস্ত এ্যাসোসিয়েটেড রিয়েক্টিভ ইফেক্ট (render effect and computed / watchers created during `setup()`) স্টপ করা হয়েছে।

  টাইমার, DOM ইভেন্ট লিসেনার বা সার্ভার কানেকশন এর মতো ম্যানুয়ালি তৈরি করা সাইড ইফেক্টগুলি ক্লিন আপ করতে এই হুকটি ব্যবহার করুন।

  **এই হুকটি সার্ভার-সাইড রেন্ডারিংয়ের সময় কল করা হয় না.**

## errorCaptured {#errorcaptured}

একটি ডিসেন্ডেন্ট কম্পোনেন্ট থেকে প্রচারিত একটি এ্যারর ক্যাপচার করা হলে কল করা হয়।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    errorCaptured?(
      this: ComponentPublicInstance,
      err: unknown,
      instance: ComponentPublicInstance | null,
      info: string
    ): boolean | void
  }
  ```

- **বিস্তারিত**

  নিম্নলিখিত সোর্স থেকে এ্যাররগুলি ক্যাপচার করা যেতে পারে:

  - কম্পোনেন্ট রেন্ডারস
  - ইভেন্ট হ্যান্ডলার
  - লাইফসাইকেল হুক
  - `setup()` ফাংশন
  - ওয়াচার্স
  - কাস্টম ডিরেক্টিভ হুক
  - ট্রানজিশন হুক

  হুক তিনটি আর্গুমেন্ট রিসিভ করে: এ্যারর, কম্পোনেন্ট ইন্সট্যান্স যা এ্যরর-টিকে  ট্রিগার করে এবং একটি ইনফরমেইশন স্ট্রিং যা এ্যারর উৎসের ধরন নির্দিষ্ট করে।

  ব্যবহারকারীর কাছে একটি এ্যারর স্টেট প্রদর্শন করতে আপনি `errorCaptured()` এ কম্পোনেন্টের স্টেট পরিবর্তন করতে পারেন। যাইহোক, এটি গুরুত্বপূর্ণ যে এ্যাররের অবস্থাটি অরিজিনাল কন্টেন্টকে রেন্ডার করা উচিত নয় যা এ্যারর সৃষ্টি করেছে; অন্যথায় কম্পোনেন্টটি একটি ইনফিনিট রেন্ডার লুপে নিক্ষিপ্ত হবে।

  এ্যাররটিকে আরও প্রচার করা থেকে থামাতে হুকটি `false` রিটার্ন দিতে পারে। নীচে ত্রুটি বিস্তার বিবরণ দেখুন.

  **Error Propagation Rules**

  - By default, all errors are still sent to the application-level [`app.config.errorHandler`](/api/application#app-config-errorhandler) if it is defined, so that these errors can still be reported to an analytics service in a single place.

  - If multiple `errorCaptured` hooks exist on a component's inheritance chain or parent chain, all of them will be invoked on the same error, in the order of bottom to top. This is similar to the bubbling mechanism of native DOM events.

  - If the `errorCaptured` hook itself throws an error, both this error and the original captured error are sent to `app.config.errorHandler`.

  - An `errorCaptured` hook can return `false` to prevent the error from propagating further. This is essentially saying "this error has been handled and should be ignored." It will prevent any additional `errorCaptured` hooks or `app.config.errorHandler` from being invoked for this error.

## renderTracked <sup class="vt-badge dev-only" /> {#rendertracked}

Called when a reactive dependency has been tracked by the component's render effect.

**This hook is development-mode-only and not called during server-side rendering.**

- **Type**

  ```ts
  interface ComponentOptions {
    renderTracked?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **See also** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## renderTriggered <sup class="vt-badge dev-only" /> {#rendertriggered}

Called when a reactive dependency triggers the component's render effect to be re-run.

**This hook is development-mode-only and not called during server-side rendering.**

- **Type**

  ```ts
  interface ComponentOptions {
    renderTriggered?(this: ComponentPublicInstance, e: DebuggerEvent): void
  }

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TriggerOpTypes /* 'set' | 'add' | 'delete' | 'clear' */
    key: any
    newValue?: any
    oldValue?: any
    oldTarget?: Map<any, any> | Set<any>
  }
  ```

- **See also** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## activated {#activated}

Called after the component instance is inserted into the DOM as part of a tree cached by [`<KeepAlive>`](/api/built-in-components#keepalive).

**This hook is not called during server-side rendering.**

- **Type**

  ```ts
  interface ComponentOptions {
    activated?(this: ComponentPublicInstance): void
  }
  ```

- **See also** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## deactivated {#deactivated}

Called after the component instance is removed from the DOM as part of a tree cached by [`<KeepAlive>`](/api/built-in-components#keepalive).

**This hook is not called during server-side rendering.**

- **Type**

  ```ts
  interface ComponentOptions {
    deactivated?(this: ComponentPublicInstance): void
  }
  ```

- **See also** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## serverPrefetch <sup class="vt-badge" data-text="SSR only" /> {#serverprefetch}

Async function to be resolved before the component instance is to be rendered on the server.

- **Type**

  ```ts
  interface ComponentOptions {
    serverPrefetch?(this: ComponentPublicInstance): Promise<any>
  }
  ```

- **Details**

  If the hook returns a Promise, the server renderer will wait until the Promise is resolved before rendering the component.

  This hook is only called during server-side rendering can be used to perform server-only data fetching.

- **Example**

  ```js
  export default {
    data() {
      return {
        data: null
      }
    },
    async serverPrefetch() {
      // component is rendered as part of the initial request
      // pre-fetch data on server as it is faster than on the client
      this.data = await fetchOnServer(/* ... */)
    },
    async mounted() {
      if (!this.data) {
        // if data is null on mount, it means the component
        // is dynamically rendered on the client. Perform a
        // client-side fetch instead.
        this.data = await fetchOnClient(/* ... */)
      }
    }
  }
  ```

- **See also** [Server-Side Rendering](/guide/scaling-up/ssr)
