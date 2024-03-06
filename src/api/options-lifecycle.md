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

   ইনস্ট্যান্স শুরু হলে এবং প্রপস সমাধান করা হলে অবিলম্বে কল করা হয়।

   তারপর প্রপগুলিকে প্রতিক্রিয়াশীল বৈশিষ্ট্য হিসাবে সংজ্ঞায়িত করা হবে এবং রাষ্ট্র যেমন `data()` বা `computed` সেট আপ করা হবে।

   লক্ষ্য রাখুন যে কম্পিউটারে এপিআই-এর `setup()` হুক কোনো অপশন API হুকার আগে কল করা হয়, এমনকি `beforeCreate()`।

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

  :::tip
  উৎপাদনে, 3য় আর্গুমেন্ট (`তথ্য`) সম্পূর্ণ তথ্যের স্ট্রিংয়ের পরিবর্তে একটি সংক্ষিপ্ত কোড হবে। আপনি স্ট্রিং ম্যাপিংয়ের কোডটি [উৎপাদন ত্রুটি কোড রেফারেন্স](/error-reference/#runtime-errors) এ খুঁজে পেতে পারেন।
  :::

  ব্যবহারকারীর কাছে একটি ত্রুটির অবস্থা প্রদর্শন করতে আপনি `errorCaptured()` এ উপাদানের অবস্থা পরিবর্তন করতে পারেন। যাইহোক, এটি গুরুত্বপূর্ণ যে ত্রুটির অবস্থাটি মূল বিষয়বস্তুকে রেন্ডার করা উচিত নয় যা ত্রুটি সৃষ্টি করেছে; অন্যথায় উপাদানটি একটি অসীম রেন্ডার লুপে নিক্ষিপ্ত হবে।

  এ্যাররটিকে আরও প্রচার করা থেকে থামাতে হুকটি `false` রিটার্ন দিতে পারে। নীচে ত্রুটি বিস্তার বিবরণ দেখুন.

  **এ্যারর প্রচারের নিয়ম**

  - ডিফল্টরূপে, সমস্ত এ্যারর এখনও অ্যাপ্লিকেশন-লেভেলে পাঠানো হয় [`app.config.errorHandler`](/api/application#app-config-errorhandler) যদি এটি ডিফাইনড করা হয়, যাতে এই এ্যাররগুলি এখনও একটি বিশ্লেষণে রিপোর্ট করা যায় একটি সিঙ্গেল জায়গায়।

  - একটি কম্পোনেন্টের ইনহেরিট্যান্স চেইন বা প্যারেন্ট চেইনে একাধিক `errorCaptured` হুক বিদ্যমান থাকলে, নিচে থেকে উপরের ক্রমানুসারে তাদের সকলকে একই এ্যাররের জন্য আহ্বান করা হবে। এটি নেটিভ DOM ইভেন্টগুলির বাবলিং করার পদ্ধতির অনুরূপ।

  - যদি `errorCaptured` হুক নিজেই একটি এ্যারর ছুড়ে দেয়, তাহলে এই এ্যারর এবং মূল ক্যাপচার করা এ্যারর উভয়ই `app.config.errorHandler`-এ পাঠানো হবে।

  - একটি `errorCaptured` হুক `false` রিটার্ন দিতে পারে যাতে ত্রুটিটিকে আরও প্রচার করা থেকে রোধ করা যায়। এটি মূলত বলছে "এই এ্যাররটি হ্যান্ডল করা হয়েছে এবং ইগনোর করা উচিত।" এটি কোনো অতিরিক্ত `errorCaptured` হুক বা `app.config.errorHandler`কে এই এ্যররের জন্য আহ্বান করা থেকে বাধা দিবে।

## renderTracked <sup class="vt-badge dev-only" /> {#rendertracked}

 কম্পোনেন্টের রেন্ডার ইফেক্ট দ্বারা একটি রিয়েক্টিভ ডিপেন্ডেন্সি ট্র্যাক করা হলে কল করা হয়।

**এই হুকটি শুধুমাত্র ডেভেলপমেন্ট-মোড এবং সার্ভার-সাইড রেন্ডারিংয়ের সময় কল করা হয় না।**

- **প্রকার**

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

- **আরো দেখুন** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## renderTriggered <sup class="vt-badge dev-only" /> {#rendertriggered}

যখন একটি রিয়েক্টিভ ডিপেন্ডেন্সি কম্পোনেন্টের রেন্ডার ইফেক্টেকে পুনরায় চালানোর জন্য ট্রিগার করে তখন কল করা হয়।

**এই হুকটি শুধুমাত্র ডেভেলপমেন্ট-মোড এবং সার্ভার-সাইড রেন্ডারিংয়ের সময় কল করা হয় না।**

- **প্রকার**

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

- **আরো দেখুন** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## activated {#activated}

[`<KeepAlive>`](/api/built-in-components#keepalive) দ্বারা ক্যাশে করা একটি ট্রি-এর অংশ হিসাবে DOM-এ কম্পোনেন্ট ইনস্ট্যান্স ইনসার্টের পরে কল করা হয়।

**এই হুকটি সার্ভার-সাইড রেন্ডারিংয়ের সময় কল করা হয় না।**

- **প্রকার**

  ```ts
  interface ComponentOptions {
    activated?(this: ComponentPublicInstance): void
  }
  ```

- **আরো দেখুন** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## deactivated {#deactivated}

[`<KeepAlive>`](/api/built-in-components#keepalive) দ্বারা ক্যাশে করা একটি ট্রি-এর অংশ হিসাবে DOM থেকে কম্পোনেন্ট ইন্সট্যান্স রিমুভ করার পরে কল করা হয়।

**এই হুকটি সার্ভার-সাইড রেন্ডারিংয়ের সময় কল করা হয় না।**

- **প্রকার**

  ```ts
  interface ComponentOptions {
    deactivated?(this: ComponentPublicInstance): void
  }
  ```

- **আরো দেখুন** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## serverPrefetch <sup class="vt-badge" data-text="SSR only" /> {#serverprefetch}

 কম্পোনেন্ট ইনস্ট্যান্স সার্ভারে রেন্ডার করার আগে অ্যাসিঙ্ক ফাংশন সমাধান করা হবে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    serverPrefetch?(this: ComponentPublicInstance): Promise<any>
  }
  ```

- **বিস্তারিত**

  যদি হুকটি একটি প্রমিজ রিটার্ন করে, সার্ভার রেন্ডারার কম্পোনেন্ট রেন্ডার করার আগে প্রমিজটি সমাধান না হওয়া পর্যন্ত অপেক্ষা করবে।

  এই হুকটি শুধুমাত্র সার্ভার-সাইড রেন্ডারিংয়ের সময় কল করা হয় সার্ভার-শুধু ডেটা আনার জন্য ব্যবহার করা যেতে পারে।

- **উদাহরন**

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

- **আরো দেখুন** [Server-Side Rendering](/guide/scaling-up/ssr)
