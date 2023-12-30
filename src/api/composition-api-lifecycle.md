# Composition API: Lifecycle Hooks {#composition-api-lifecycle-hooks}

:::info Usage Note
এই পৃষ্ঠায় তালিকাভুক্ত সমস্ত API একটি উপাদানের `সেটআপ()` পর্বের সময় সিঙ্ক্রোনাসভাবে কল করতে হবে। আরও বিস্তারিত জানার জন্য [Guide - Lifecycle Hooks](/guide/essentials/lifecycle) দেখুন।
:::

## onMounted() {#onmounted}

কম্পোনেন্ট মাউন্ট করার পরে কল করার জন্য একটি কলব্যাক নিবন্ধন করে।

- **Type**

  ```ts
  function onMounted(callback: () => void): void
  ```

- **Details**

  একটি উপাদান পরে মাউন্ট করা বিবেচনা করা হয়:

  - এর সমস্ত সিঙ্ক্রোনাস চাইল্ড উপাদান মাউন্ট করা হয়েছে (`<সাসপেন্স>` গাছের ভিতরে অ্যাসিঙ্ক উপাদান বা উপাদান অন্তর্ভুক্ত নয়)।

  - এর নিজস্ব DOM গাছ তৈরি করা হয়েছে এবং প্যারেন্ট কন্টেইনারে ঢোকানো হয়েছে। মনে রাখবেন এটি শুধুমাত্র নিশ্চিত করে যে কম্পোনেন্টের DOM ট্রি ইন-ডকুমেন্ট আছে যদি অ্যাপ্লিকেশনের রুট কন্টেইনারটিও ইন-ডকুমেন্ট হয়।

  এই হুকটি সাধারণত পার্শ্বপ্রতিক্রিয়া সম্পাদনের জন্য ব্যবহৃত হয় যার জন্য উপাদানের রেন্ডার করা DOM অ্যাক্সেসের প্রয়োজন হয়, অথবা একটি [সার্ভার-রেন্ডার করা অ্যাপ্লিকেশন](/guide/scaling-up/ssr) এ ক্লায়েন্টের কাছে DOM-সম্পর্কিত কোড সীমিত করার জন্য।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

- **Example**

  Accessing an element via template ref:

  ```vue
  <script setup>
  import { ref, onMounted } from 'vue'

  const el = ref()

  onMounted(() => {
    el.value // <div>
  })
  </script>

  <template>
    <div ref="el"></div>
  </template>
  ```

## onUpdated() {#onupdated}

একটি প্রতিক্রিয়াশীল অবস্থা পরিবর্তনের কারণে উপাদানটি তার DOM ট্রি আপডেট করার পরে কল করার জন্য একটি কলব্যাক নিবন্ধন করে।

- **Type**

  ```ts
  function onUpdated(callback: () => void): void
  ```

- **Details**

  একটি প্যারেন্ট কম্পোনেন্টের আপডেটেড হুককে এর চাইল্ড কম্পোনেন্টের পরে বলা হয়।

  এই হুকটিকে কম্পোনেন্টের যেকোনো DOM আপডেটের পরে বলা হয়, যা বিভিন্ন স্টেট পরিবর্তনের কারণে হতে পারে, কারণ পারফরম্যান্সের কারণে একাধিক স্টেট পরিবর্তন একটি একক রেন্ডার সাইকেলে ব্যাচ করা যেতে পারে। আপনি যদি একটি নির্দিষ্ট অবস্থা পরিবর্তনের পরে আপডেট হওয়া DOM অ্যাক্সেস করতে চান তবে পরিবর্তে [nextTick()](/api/general#nexttick) ব্যবহার করুন।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

  :::warning
  আপডেট করা হুকে কম্পোনেন্টের অবস্থা পরিবর্তন করবেন না - এটি সম্ভবত একটি অসীম আপডেট লুপের দিকে নিয়ে যাবে!
  :::

- **Example**

  Accessing updated DOM:

  ```vue
  <script setup>
  import { ref, onUpdated } from 'vue'

  const count = ref(0)

  onUpdated(() => {
    // text content should be the same as current `count.value`
    console.log(document.getElementById('count').textContent)
  })
  </script>

  <template>
    <button id="count" @click="count++">{{ count }}</button>
  </template>
  ```

## onUnmounted() {#onunmounted}

কম্পোনেন্ট আনমাউন্ট করার পরে কল করার জন্য একটি কলব্যাক নিবন্ধন করে।

- **Type**

  ```ts
  function onUnmounted(callback: () => void): void
  ```

- **Details**

  একটি উপাদান পরে আনমাউন্ট করা বলে মনে করা হয়:

  - এর সমস্ত চাইল্ড কম্পোনেন্ট আনমাউন্ট করা হয়েছে।

  - এর সমস্ত সম্পর্কিত প্রতিক্রিয়াশীল প্রভাব (`setup()` এর সময় তৈরি করা রেন্ডার প্রভাব এবং গণনা করা / প্রহরী) বন্ধ করা হয়েছে।

  টাইমার, DOM ইভেন্ট লিসেনার বা সার্ভার সংযোগের মতো ম্যানুয়ালি তৈরি করা পার্শ্ব প্রতিক্রিয়াগুলি পরিষ্কার করতে এই হুকটি ব্যবহার করুন।

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

- **Example**

  ```vue
  <script setup>
  import { onMounted, onUnmounted } from 'vue'

  let intervalId
  onMounted(() => {
    intervalId = setInterval(() => {
      // ...
    })
  })

  onUnmounted(() => clearInterval(intervalId))
  </script>
  ```

## onBeforeMount() {#onbeforemount}

কম্পোনেন্ট মাউন্ট করার ঠিক আগে কল করার জন্য একটি হুক নিবন্ধন করে।

- **Type**

  ```ts
  function onBeforeMount(callback: () => void): void
  ```

- **Details**

  যখন এই হুকটি কল করা হয়, উপাদানটি তার প্রতিক্রিয়াশীল অবস্থা সেট আপ করা শেষ করেছে, কিন্তু এখনও কোন DOM নোড তৈরি করা হয়নি। এটি প্রথমবারের মতো এর DOM রেন্ডার প্রভাব কার্যকর করতে চলেছে৷

  **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

## onBeforeUpdate() {#onbeforeupdate}

একটি প্রতিক্রিয়াশীল অবস্থা পরিবর্তনের কারণে উপাদানটি তার DOM ট্রি আপডেট করতে চলেছে তার ঠিক আগে কল করার জন্য একটি হুক নিবন্ধন করে৷

- **Type**

  ```ts
  function onBeforeUpdate(callback: () => void): void
  ```

- **Details**

  Vue DOM আপডেট করার আগে এই হুকটি DOM অবস্থা অ্যাক্সেস করতে ব্যবহার করা যেতে পারে। এই হুকের ভিতরে কম্পোনেন্ট স্টেট পরিবর্তন করাও নিরাপদ।

   **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

## onBeforeUnmount() {#onbeforeunmount}

একটি কম্পোনেন্ট ইন্সট্যান্স আনমাউন্ট করার ঠিক আগে কল করার জন্য একটি হুক নিবন্ধন করে।

- **Type**

  ```ts
  function onBeforeUnmount(callback: () => void): void
  ```

- **Details**

  যখন এই হুক বলা হয়, তখনও কম্পোনেন্ট ইনস্ট্যান্স সম্পূর্ণরূপে কার্যকরী থাকে।

   **সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

## onErrorCaptured() {#onerrorcaptured}

একটি বংশধর উপাদান থেকে প্রচারিত ত্রুটি ধরা পড়লে কল করার জন্য একটি হুক নিবন্ধন করে৷

- **Type**

  ```ts
  function onErrorCaptured(callback: ErrorCapturedHook): void

  type ErrorCapturedHook = (
    err: unknown,
    instance: ComponentPublicInstance | null,
    info: string
  ) => boolean | void
  ```

- **Details**

  Errors can be captured from the following sources:

  - Component renders
  - Event handlers
  - Lifecycle hooks
  - `setup()` function
  - Watchers
  - Custom directive hooks
  - Transition hooks

  হুক তিনটি আর্গুমেন্ট পায়: ত্রুটি, কম্পোনেন্ট ইনস্ট্যান্স যা ত্রুটিটি ট্রিগার করে এবং একটি তথ্য স্ট্রিং যা ত্রুটির উৎসের ধরনটি নির্দিষ্ট করে।

  :::tip
  উৎপাদনে, 3য় আর্গুমেন্ট (`তথ্য`) সম্পূর্ণ তথ্যের স্ট্রিংয়ের পরিবর্তে একটি সংক্ষিপ্ত কোড হবে। আপনি স্ট্রিং ম্যাপিংয়ের কোডটি [উৎপাদন ত্রুটি কোড রেফারেন্স](/error-reference/#runtime-errors) এ খুঁজে পেতে পারেন।
  :::

  ব্যবহারকারীর কাছে একটি ত্রুটির অবস্থা প্রদর্শন করতে আপনি `errorCaptured()` এ উপাদানের অবস্থা পরিবর্তন করতে পারেন। যাইহোক, এটি গুরুত্বপূর্ণ যে ত্রুটির অবস্থাটি মূল বিষয়বস্তুকে রেন্ডার করা উচিত নয় যা ত্রুটি সৃষ্টি করেছে; অন্যথায় উপাদানটি একটি অসীম রেন্ডার লুপে নিক্ষিপ্ত হবে।

  ত্রুটিটিকে আরও প্রচার করা থেকে থামাতে হুকটি `false` ফেরত দিতে পারে। নীচে ত্রুটি বিস্তার বিবরণ দেখুন.

  **ত্রুটি প্রচারের নিয়ম**

  - ডিফল্টরূপে, সমস্ত ত্রুটিগুলি এখনও অ্যাপ্লিকেশন-লেভেলে পাঠানো হয় [`app.config.errorHandler`](/api/application#app-config-errorhandler) যদি এটি সংজ্ঞায়িত করা হয়, যাতে এই ত্রুটিগুলি এখনও একটি রিপোর্ট করা যেতে পারে একক জায়গায় বিশ্লেষণ সেবা।

  - যদি একটি উপাদানের উত্তরাধিকার শৃঙ্খলে বা প্যারেন্ট চেইনে একাধিক `errorCaptured` হুক বিদ্যমান থাকে, তাহলে নিচে থেকে উপরের ক্রমানুসারে তাদের সকলকে একই ত্রুটির জন্য আহ্বান করা হবে। এটি নেটিভ DOM ইভেন্টগুলির বুদবুদ করার পদ্ধতির অনুরূপ।

  - যদি `errorCaptured` হুক নিজেই একটি ত্রুটি ছুড়ে দেয়, তাহলে এই ত্রুটি এবং মূল ক্যাপচার করা ত্রুটি উভয়ই `app.config.errorHandler`-এ পাঠানো হবে।

  - একটি `errorCaptured` হুক `false` ফেরত দিতে পারে যাতে ত্রুটিটিকে আরও প্রচার করা থেকে রোধ করা যায়। এটি মূলত বলছে "এই ত্রুটিটি পরিচালনা করা হয়েছে এবং উপেক্ষা করা উচিত।" এটি কোনো অতিরিক্ত `errorCaptured` হুক বা `app.config.errorHandler`কে এই ত্রুটির জন্য আহ্বান করা থেকে বাধা দিবে।

## onRenderTracked() <sup class="vt-badge dev-only" /> {#onrendertracked}

 কম্পোনেন্টের রেন্ডার প্রভাব দ্বারা একটি প্রতিক্রিয়াশীল নির্ভরতা ট্র্যাক করা হলে কল করার জন্য একটি ডিবাগ হুক নিবন্ধন করে।

**এই হুকটি শুধুমাত্র বিকাশ-মোড এবং সার্ভার-সাইড রেন্ডারিংয়ের সময় বলা হয় না।**

- **Type**

  ```ts
  function onRenderTracked(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

  type DebuggerEvent = {
    effect: ReactiveEffect
    target: object
    type: TrackOpTypes /* 'get' | 'has' | 'iterate' */
    key: any
  }
  ```

- **আরো দেখুন** [Reactivity in Depth](/guide/extras/reactivity-in-depth)

## onRenderTriggered() <sup class="vt-badge dev-only" /> {#onrendertriggered}

যখন একটি প্রতিক্রিয়াশীল নির্ভরতা কম্পোনেন্টের রেন্ডার প্রভাবকে পুনরায় চালানোর জন্য ট্রিগার করে তখন কল করার জন্য একটি ডিবাগ হুক নিবন্ধন করে।

**এই হুকটি শুধুমাত্র বিকাশ-মোড এবং সার্ভার-সাইড রেন্ডারিংয়ের সময় বলা হয় না।**

- **Type**

  ```ts
  function onRenderTriggered(callback: DebuggerHook): void

  type DebuggerHook = (e: DebuggerEvent) => void

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

## onActivated() {#onactivated}

ক্যাশে করা একটি গাছের অংশ হিসাবে DOM-এ কম্পোনেন্ট ইনস্ট্যান্স ঢোকানোর পরে কল করার জন্য একটি কলব্যাক নিবন্ধন করে [`<KeepAlive>`](/api/built-in-components#keepalive).

**This hook is not called during server-side rendering.**

- **Type**

  ```ts
  function onActivated(callback: () => void): void
  ```

- **আরো দেখুন** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onDeactivated() {#ondeactivated}

দ্বারা ক্যাশে করা একটি গাছের অংশ হিসাবে DOM থেকে কম্পোনেন্ট ইন্সট্যান্স মুছে ফেলার পরে কল করার জন্য একটি কলব্যাক নিবন্ধন করে [`<KeepAlive>`](/api/built-in-components#keepalive).

**সার্ভার-সাইড রেন্ডারিংয়ের সময় এই হুকটি বলা হয় না।**

- **Type**

  ```ts
  function onDeactivated(callback: () => void): void
  ```

- **আরো দেখুন** [Guide - Lifecycle of Cached Instance](/guide/built-ins/keep-alive#lifecycle-of-cached-instance)

## onServerPrefetch() <sup class="vt-badge" data-text="SSR only" /> {#onserverprefetch}

 কম্পোনেন্ট ইন্সট্যান্স সার্ভারে রেন্ডার করার আগে সমাধান করার জন্য একটি অ্যাসিঙ্ক ফাংশন নিবন্ধন করে।

- **Type**

  ```ts
  function onServerPrefetch(callback: () => Promise<any>): void
  ```

- **Details**

  যদি কলব্যাক একটি প্রতিশ্রুতি প্রদান করে, সার্ভার রেন্ডারার কম্পোনেন্ট রেন্ডার করার আগে প্রতিশ্রুতির সমাধান না হওয়া পর্যন্ত অপেক্ষা করবে।

  এই হুকটি শুধুমাত্র সার্ভার-সাইড রেন্ডারিংয়ের সময় বলা হয় সার্ভার-শুধু ডেটা আনার জন্য ব্যবহার করা যেতে পারে।

- **Example**

  ```vue
  <script setup>
  import { ref, onServerPrefetch, onMounted } from 'vue'

  const data = ref(null)

  onServerPrefetch(async () => {
    // component is rendered as part of the initial request
    // pre-fetch data on server as it is faster than on the client
    data.value = await fetchOnServer(/* ... */)
  })

  onMounted(async () => {
    if (!data.value) {
      // if data is null on mount, it means the component
      // is dynamically rendered on the client. Perform a
      // client-side fetch instead.
      data.value = await fetchOnClient(/* ... */)
    }
  })
  </script>
  ```

- **আরো দেখুন** [Server-Side Rendering](/guide/scaling-up/ssr)
