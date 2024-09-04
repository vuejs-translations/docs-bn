# Application API {#application-api}

## createApp() {#createapp}

একটি অ্যাপ্লিকেশন উদাহরণ তৈরি করে।

- **প্রকার**

  ```ts
  function createApp(rootComponent: Component, rootProps?: object): App
  ```

- **বিস্তারিত**

  The first argument is the root component. The second optional argument is the props to be passed to the root component.

- **উদাহরণ**

  ইনলাইন রুট কম্পোনেন্ট সহ:

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* root component options */
  })
  ```

  imported component সহ:

  ```js
  import { createApp } from 'vue'
  import App from './App.vue'

  const app = createApp(App)
  ```

- **আরো দেখুন** [Guide - Creating a Vue Application](/guide/essentials/application)

## createSSRApp() {#createssrapp}

[SSR হাইড্রেশন](/guide/scaling-up/ssr#client-hydration) মোডে একটি অ্যাপ্লিকেশন উদাহরণ তৈরি করে। ব্যবহার ঠিক `createApp()` এর মতোই।

## app.mount() {#app-mount}

একটি ধারক কম্পোনেন্টে অ্যাপ্লিকেশন দৃষ্টান্ত মাউন্ট করে।

- **প্রকার**

  ```ts
  interface App {
    mount(rootContainer: Element | string): ComponentPublicInstance
  }
  ```

- **বিস্তারিত**

  যুক্তিটি হয় একটি প্রকৃত DOM কম্পোনেন্ট বা একটি CSS নির্বাচক হতে পারে (প্রথম মিলে যাওয়া কম্পোনেন্টটি ব্যবহার করা হবে)। মূল কম্পোনেন্ট উদাহরণ প্রদান করে।

  যদি কম্পোনেন্টটির একটি টেমপ্লেট বা একটি রেন্ডার ফাংশন সংজ্ঞায়িত থাকে, তাহলে এটি কন্টেইনারের ভিতরে বিদ্যমান যেকোনো DOM নোডকে প্রতিস্থাপন করবে। অন্যথায়, রানটাইম কম্পাইলার উপলব্ধ থাকলে, টেমপ্লেট হিসাবে কন্টেইনারের `ইনারএইচটিএমএল` ব্যবহার করা হবে।

  SSR হাইড্রেশন মোডে, এটি পাত্রের ভিতরে বিদ্যমান DOM নোডগুলিকে হাইড্রেট করবে। যদি [অমিল থাকে](/guide/scaling-up/ssr#hydration-mismatch), বিদ্যমান DOM নোডগুলি প্রত্যাশিত আউটপুটের সাথে মেলে।

  প্রতিটি অ্যাপের উদাহরণের জন্য, `মাউন্ট()` শুধুমাত্র একবার কল করা যেতে পারে।

- **উদাহরণ**

  ```js
  import { createApp } from 'vue'
  const app = createApp(/* ... */)

  app.mount('#app')
  ```

  Can also mount to an actual DOM element:

  ```js
  app.mount(document.body.firstChild)
  ```

## app.unmount() {#app-unmount}

একটি মাউন্ট করা অ্যাপ্লিকেশন দৃষ্টান্ত আনমাউন্ট করে, অ্যাপ্লিকেশনের কম্পোনেন্ট গাছের সমস্ত কম্পোনেন্টস জন্য আনমাউন্ট লাইফসাইকেল হুকগুলিকে ট্রিগার করে৷

- **প্রকার**

  ```ts
  interface App {
    unmount(): void
  }
  ```

## app.onUnmount() <sup class="vt-badge" data-text="3.5+" /> {#app-onunmount}

Registers a callback to be called when the app is unmounted.

- **Type**

  ```ts
  interface App {
    onUnmount(callback: () => any): void
  }
  ```

## app.component() {#app-component}

একটি নাম স্ট্রিং এবং একটি কম্পোনেন্ট সংজ্ঞা উভয় পাস করলে একটি বিশ্বব্যাপী কম্পোনেন্ট নিবন্ধন করে, অথবা শুধুমাত্র নাম পাস হলে ইতিমধ্যে নিবন্ধিত একটি পুনরুদ্ধার করে।

- **প্রকার**

  ```ts
  interface App {
    component(name: string): Component | undefined
    component(name: string, component: Component): this
  }
  ```

- **উদাহরণ**

  ```js
  import { createApp } from 'vue'

  const app = createApp({})

  // register an options object
  app.component('my-component', {
    /* ... */
  })

  // retrieve a registered component
  const MyComponent = app.component('my-component')
  ```

- **আরো দেখুন** [Component Registration](/guide/components/registration)

## app.directive() {#app-directive}

একটি নাম স্ট্রিং এবং একটি নির্দেশিক সংজ্ঞা উভয় পাস করলে একটি বিশ্বব্যাপী কাস্টম নির্দেশিকা নিবন্ধন করে, অথবা শুধুমাত্র নামটি পাস হলে ইতিমধ্যেই নিবন্ধিত একটি পুনরুদ্ধার করে৷

- **প্রকার**

  ```ts
  interface App {
    directive(name: string): Directive | undefined
    directive(name: string, directive: Directive): this
  }
  ```

- **উদাহরণ**

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* ... */
  })

  // register (object directive)
  app.directive('my-directive', {
    /* custom directive hooks */
  })

  // register (function directive shorthand)
  app.directive('my-directive', () => {
    /* ... */
  })

  // retrieve a registered directive
  const myDirective = app.directive('my-directive')
  ```

- **আরো দেখুন** [Custom Directives](/guide/reusability/custom-directives)

## app.use() {#app-use}

Installs a [plugin](/guide/reusability/plugins).

- **প্রকার**

  ```ts
  interface App {
    use(plugin: Plugin, ...options: any[]): this
  }
  ```

- **বিস্তারিত**

  প্রথম আর্গুমেন্ট হিসাবে প্লাগইন এবং দ্বিতীয় আর্গুমেন্ট হিসাবে ঐচ্ছিক প্লাগইন অপশন আশা করে।

  প্লাগইনটি হয় একটি `ইনস্টল()` পদ্ধতি সহ একটি অবজেক্ট হতে পারে, অথবা শুধুমাত্র একটি ফাংশন যা `ইনস্টল()` পদ্ধতি হিসেবে ব্যবহার করা হবে। বিকল্পগুলি (`app.use()` এর দ্বিতীয় আর্গুমেন্ট) প্লাগইনের `ইনস্টল()` পদ্ধতিতে পাস করা হবে।

  একই প্লাগইনে যখন `app.use()` একাধিকবার কল করা হয়, প্লাগইনটি একবার ইন্সটল করা হবে।

- **উদাহরণ**

  ```js
  import { createApp } from 'vue'
  import MyPlugin from './plugins/MyPlugin'

  const app = createApp({
    /* ... */
  })

  app.use(MyPlugin)
  ```

- **আরো দেখুন** [Plugins](/guide/reusability/plugins)

## app.mixin() {#app-mixin}

একটি গ্লোবাল মিক্সিন প্রয়োগ করে (অ্যাপ্লিকেশনের সুযোগ)। একটি গ্লোবাল মিক্সিন অ্যাপ্লিকেশনের প্রতিটি কম্পোনেন্ট উদাহরণে এর অন্তর্ভুক্ত বিকল্পগুলি প্রয়োগ করে।

:::warning প্রস্তাবিত নয়
ইকোসিস্টেম লাইব্রেরিতে তাদের ব্যাপক ব্যবহারের কারণে মিক্সিনগুলি মূলত পিছনের দিকের সামঞ্জস্যের জন্য Vue 3-তে সমর্থিত। অ্যাপ্লিকেশান কোডে মিক্সিনের ব্যবহার, বিশেষ করে গ্লোবাল মিক্সিন এড়ানো উচিত।

যুক্তি পুনঃব্যবহারের জন্য, পরিবর্তে [কম্পোজেবল](/guide/reusability/composables) পছন্দ করুন।
:::

- **প্রকার**

  ```ts
  interface App {
    mixin(mixin: ComponentOptions): this
  }
  ```

## app.provide() {#app-provide}

একটি মান প্রদান করুন যা অ্যাপ্লিকেশনের মধ্যে সমস্ত বংশধর কম্পোনেন্টগুলিতে ইনজেকশন করা যেতে পারে।

- **প্রকার**

  ```ts
  interface App {
    provide<T>(key: InjectionKey<T> | symbol | string, value: T): this
  }
  ```

- **বিস্তারিত**

  প্রথম আর্গুমেন্ট হিসাবে ইনজেকশন কী এবং দ্বিতীয় হিসাবে প্রদত্ত মান প্রত্যাশা করে। অ্যাপ্লিকেশন উদাহরণ নিজেই প্রদান করে.

- **উদাহরণ**

  ```js
  import { createApp } from 'vue'

  const app = createApp(/* ... */)

  app.provide('message', 'hello')
  ```

  Inside a component in the application:

  <div class="composition-api">

  ```js
  import { inject } from 'vue'

  export default {
    setup() {
      console.log(inject('message')) // 'hello'
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  export default {
    inject: ['message'],
    created() {
      console.log(this.message) // 'hello'
    }
  }
  ```

  </div>

- **আরো দেখুন**
  - [Provide / Inject](/guide/components/provide-inject)
  - [App-level Provide](/guide/components/provide-inject#app-level-provide)
  - [app.runWithContext()](#app-runwithcontext)

## app.runWithContext() {#app-runwithcontext}

- Only supported in 3.3+

ইনজেকশন প্রসঙ্গ হিসাবে বর্তমান অ্যাপের সাথে একটি কলব্যাক চালান।

- **প্রকার**

  ```ts
  interface App {
    runWithContext<T>(fn: () => T): T
  }
  ```

- **বিস্তারিত**

  একটি কলব্যাক ফাংশন আশা করে এবং অবিলম্বে কলব্যাক চালায়। কলব্যাকের সিঙ্ক্রোনাস কল চলাকালীন, `inject()` কলগুলি বর্তমান অ্যাপ দ্বারা প্রদত্ত মানগুলি থেকে ইনজেকশনগুলি সন্ধান করতে সক্ষম হয়, এমনকি কোনো বর্তমান সক্রিয় কম্পোনেন্টস উদাহরণ না থাকলেও৷ কলব্যাকের রিটার্ন মানও ফেরত দেওয়া হবে।

- **উদাহরণ**

  ```js
  import { inject } from 'vue'

  app.provide('id', 1)

  const injected = app.runWithContext(() => {
    return inject('id')
  })

  console.log(injected) // 1
  ```

## app.version {#app-version}

Vue এর সংস্করণ প্রদান করে যেটি দিয়ে অ্যাপ্লিকেশনটি তৈরি করা হয়েছিল। এটি [plugins](/guide/reusability/plugins) এর ভিতরে দরকারী, যেখানে আপনার বিভিন্ন Vue সংস্করণের উপর ভিত্তি করে শর্তসাপেক্ষ যুক্তির প্রয়োজন হতে পারে।

- **প্রকার**

  ```ts
  interface App {
    version: string
  }
  ```

- **উদাহরণ**

  একটি প্লাগইনের ভিতরে একটি সংস্করণ পরীক্ষা করা:

  ```js
  export default {
    install(app) {
      const version = Number(app.version.split('.')[0])
      if (version < 3) {
        console.warn('This plugin requires Vue 3')
      }
    }
  }
  ```

- **আরো দেখুন** [Global API - version](/api/general#version)

## app.config {#app-config}

প্রতিটি অ্যাপ্লিকেশন দৃষ্টান্ত একটি `config` অবজেক্টকে প্রকাশ করে যেটিতে সেই অ্যাপ্লিকেশনটির জন্য কনফিগারেশন সেটিংস রয়েছে। আপনি আপনার অ্যাপ্লিকেশন মাউন্ট করার আগে এর বৈশিষ্ট্যগুলি (নীচে নথিভুক্ত) সংশোধন করতে পারেন।

```js
import { createApp } from 'vue'

const app = createApp(/* ... */)

console.log(app.config)
```

## app.config.errorHandler {#app-config-errorhandler}

অ্যাপ্লিকেশনের মধ্যে থেকে প্রচারিত অজানা ত্রুটিগুলির জন্য একটি বিশ্বব্যাপী হ্যান্ডলারকে বরাদ্দ করুন৷

- **প্রকার**

  ```ts
  interface AppConfig {
    errorHandler?: (
      err: unknown,
      instance: ComponentPublicInstance | null,
      // `info` is a Vue-specific error info,
      // e.g. which lifecycle hook the error was thrown in
      info: string
    ) => void
  }
  ```

- **বিস্তারিত**

  error handler তিনটি আর্গুমেন্ট পায়: ত্রুটি, কম্পোনেন্ট ইনস্ট্যান্স যা ত্রুটিটি ট্রিগার করে এবং একটি তথ্য স্ট্রিং যা ত্রুটির উৎসের ধরন নির্দিষ্ট করে।

   এটি নিম্নলিখিত sources গুলি থেকে ত্রুটিগুলি capture  করতে পারে:

  - Component renders
  - Event handlers
  - Lifecycle hooks
  - `setup()` function
  - Watchers
  - Custom directive hooks
  - Transition hooks

  :::tip
  উৎপাদনে, 3য় আর্গুমেন্ট (`তথ্য`) সম্পূর্ণ তথ্যের স্ট্রিংয়ের পরিবর্তে একটি সংক্ষিপ্ত কোড হবে। আপনি স্ট্রিং ম্যাপিংয়ের কোডটি [উৎপাদন ত্রুটি কোড রেফারেন্স](/error-reference/#runtime-errors) এ খুঁজে পেতে পারেন।
  :::

- **Example**

  ```js
  app.config.errorHandler = (err, instance, info) => {
    // handle error, e.g. report to a service
  }
  ```

## app.config.warnHandler {#app-config-warnhandler}

Vue থেকে রানটাইম সতর্কতার জন্য একটি কাস্টম হ্যান্ডলার বরাদ্দ করুন।

- **প্রকার**

  ```ts
  interface AppConfig {
    warnHandler?: (
      msg: string,
      instance: ComponentPublicInstance | null,
      trace: string
    ) => void
  }
  ```

- **বিস্তারিত**

  সতর্কতা হ্যান্ডলার প্রথম আর্গুমেন্ট হিসেবে সতর্কীকরণ বার্তা পায়, দ্বিতীয় আর্গুমেন্ট হিসেবে সোর্স কম্পোনেন্ট ইনস্ট্যান্স এবং তৃতীয় হিসেবে একটি কম্পোনেন্ট ট্রেস স্ট্রিং।

   এটি কনসোল ভার্বোসিটি কমাতে নির্দিষ্ট সতর্কতা ফিল্টার করতে ব্যবহার করা যেতে পারে। সমস্ত Vue সতর্কতাগুলি ডেভেলপমেন্টের সময় সম্বোধন করা উচিত, তাই এটি শুধুমাত্র ডিবাগ সেশনের সময় অনেকের মধ্যে নির্দিষ্ট সতর্কতার উপর ফোকাস করার জন্য সুপারিশ করা হয়, এবং ডিবাগিং সম্পন্ন হলে এটি সরানো উচিত।

  :::tip
  সতর্কতাগুলি শুধুমাত্র ডেভেলপমেন্টের সময় কাজ করে, তাই এই কনফিগারেশনটি উত্পাদন মোডে উপেক্ষা করা হয়।
  :::

- **উদাহরণ**

  ```js
  app.config.warnHandler = (msg, instance, trace) => {
    // `trace` is the component hierarchy trace
  }
  ```

## app.config.performance {#app-config-performance}

ব্রাউজার ডেভটুল পারফরম্যান্স/টাইমলাইন প্যানেলে কম্পোনেন্ট ইনিট, কম্পাইল, রেন্ডার এবং প্যাচ পারফরম্যান্স ট্রেসিং সক্ষম করতে এটিকে `true` তে সেট করুন। শুধুমাত্র ডেভেলপমেন্ট মোডে এবং [performance.mark](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API সমর্থন করে এমন ব্রাউজারে কাজ করে।

- **প্রকার:** `boolean`

- **আরো দেখুন** [Guide - Performance](/guide/best-practices/performance)

## app.config.compilerOptions {#app-config-compileroptions}

রানটাইম কম্পাইলার বিকল্পগুলি কনফিগার করুন। এই অবজেক্টে সেট করা মানগুলি ইন-ব্রাউজার টেমপ্লেট কম্পাইলারে পাঠানো হবে এবং কনফিগার করা অ্যাপের প্রতিটি কম্পোনেন্টকে প্রভাবিত করবে। মনে রাখবেন আপনি [`compilerOptions` বিকল্প](/api/options-rendering#compileroptions) ব্যবহার করে প্রতি- কম্পোনেন্ট ভিত্তিতে এই বিকল্পগুলিকে ওভাররাইড করতে পারেন।

::: warning গুরুত্বপূর্ণ
এই কনফিগার বিকল্পটি শুধুমাত্র সম্পূর্ণ বিল্ড ব্যবহার করার সময় সম্মান করা হয় (অর্থাৎ স্বতন্ত্র `vue.js` যা ব্রাউজারে টেমপ্লেট কম্পাইল করতে পারে)। আপনি যদি বিল্ড সেটআপের সাথে রানটাইম-অনলি বিল্ড ব্যবহার করেন, তাহলে কম্পাইলার বিকল্পগুলি অবশ্যই বিল্ড টুল কনফিগারেশনের মাধ্যমে `@vue/compiler-dom`-এ পাস করতে হবে।

- `vue-loader` এর জন্য: [`compilerOptions` লোডার বিকল্পের মাধ্যমে পাস করুন](https://vue-loader.vuejs.org/options.html#compileroptions)। এছাড়াও [কিভাবে এটিকে `vue-cli` এ কনফিগার করবেন](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader) দেখুন।

- `vite`-এর জন্য: [`@vitejs/plugin-vue` বিকল্পের মাধ্যমে পাস](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#options)।
  :::

### app.config.compilerOptions.isCustomElement {#app-config-compileroptions-iscustomelement}

নেটিভ কাস্টম কম্পোনেন্ট চিনতে একটি চেক পদ্ধতি নির্দিষ্ট করে।

- **প্রকার:** `(tag: string) => boolean`

- **বিস্তারিত**

  ট্যাগটিকে একটি নেটিভ কাস্টম কম্পোনেন্ট হিসাবে বিবেচনা করা হলে `true` ফেরত দেওয়া উচিত। একটি মিলে যাওয়া ট্যাগের জন্য, Vue এটিকে একটি Vue কম্পোনেন্ট হিসাবে সমাধান করার চেষ্টা করার পরিবর্তে একটি নেটিভ কম্পোনেন্ট হিসাবে রেন্ডার করবে।

  এই ফাংশনে নেটিভ এইচটিএমএল এবং এসভিজি ট্যাগগুলির সাথে মিলিত হওয়ার প্রয়োজন নেই - Vue-এর পার্সার স্বয়ংক্রিয়ভাবে সেগুলিকে চিনতে পারে৷

- **উদাহরণ**

  ```js
  // treat all tags starting with 'ion-' as custom elements
  app.config.compilerOptions.isCustomElement = (tag) => {
    return tag.startsWith('ion-')
  }
  ```

- **আরো দেখুন** [Vue and Web Components](/guide/extras/web-components)

### app.config.compilerOptions.whitespace {#app-config-compileroptions-whitespace}

টেমপ্লেট হোয়াইটস্পেস হ্যান্ডলিং আচরণ সামঞ্জস্য করে।

- **প্রকার:** `'condense' | 'preserve'`

- **Default:** `'condense'`

- **বিস্তারিত**

  Vue আরও দক্ষ কম্পাইল আউটপুট তৈরি করতে টেমপ্লেটগুলিতে হোয়াইটস্পেস অক্ষরগুলি সরিয়ে / ঘনীভূত করে। ডিফল্ট কৌশল হল "কনডেন্স", নিম্নলিখিত আচরণ সহ:

  1. একটি কম্পোনেন্টস অভ্যন্তরে অগ্রণী / শেষ হোয়াইটস্পেস অক্ষরগুলিকে একক স্থানে ঘনীভূত করা হয়।
  2. নতুন লাইন ধারণকারী কম্পোনেন্টগুলির মধ্যে হোয়াইটস্পেস অক্ষরগুলি সরানো হয়।
  3. টেক্সট নোডের ক্রমাগত হোয়াইটস্পেস অক্ষরগুলিকে একক স্থানে ঘনীভূত করা হয়।

  এই বিকল্পটিকে `'preserve'-এ সেট করলে (2) এবং (3) নিষ্ক্রিয় হবে।

- **উদাহরণ**

  ```js
  app.config.compilerOptions.whitespace = 'preserve'
  ```

### app.config.compilerOptions.delimiters {#app-config-compileroptions-delimiters}

টেমপ্লেটের মধ্যে টেক্সট ইন্টারপোলেশনের জন্য ব্যবহৃত ডিলিমিটারগুলিকে সামঞ্জস্য করে।

- **প্রকার:** `[string, string]`

- **Default:** `{{ "['\u007b\u007b', '\u007d\u007d']" }}`

- **বিস্তারিত**

  এটি সাধারণত সার্ভার-সাইড ফ্রেমওয়ার্কের সাথে বিরোধ এড়াতে ব্যবহৃত হয় যা গোঁফের সিনট্যাক্স ব্যবহার করে।

- **উদাহরণ**

  ```js
  // Delimiters changed to ES6 template string style
  app.config.compilerOptions.delimiters = ['${', '}']
  ```

### app.config.compilerOptions.comments {#app-config-compileroptions-comments}

টেমপ্লেটে এইচটিএমএল মন্তব্যের চিকিৎসা সামঞ্জস্য করে।

- **প্রকার:** `boolean`

- **Default:** `false`

- **বিস্তারিত**

  ডিফল্টরূপে, Vue প্রোডাকশনে মন্তব্যগুলি সরিয়ে দিবে। এই বিকল্পটিকে `true` তে সেট করা Vue কে এমনকি উৎপাদনেও মন্তব্য সংরক্ষণ করতে বাধ্য করবে। মন্তব্য সবসময় উন্নয়নের সময় সংরক্ষিত হয়. এই বিকল্পটি সাধারণত ব্যবহৃত হয় যখন Vue অন্যান্য লাইব্রেরির সাথে ব্যবহার করা হয় যা HTML মন্তব্যের উপর নির্ভর করে।

- **উদাহরণ**

  ```js
  app.config.compilerOptions.comments = true
  ```

## app.config.globalProperties {#app-config-globalproperties}

একটি অবজেক্ট যা বিশ্বব্যাপী বৈশিষ্ট্য নিবন্ধন করতে ব্যবহার করা যেতে পারে যা অ্যাপ্লিকেশনের ভিতরে যেকোন কম্পোনেন্টস উদাহরণে অ্যাক্সেস করা যেতে পারে।

- **প্রকার**

  ```ts
  interface AppConfig {
    globalProperties: Record<string, any>
  }
  ```

- **বিস্তারিত**

  এটি Vue 2 এর `Vue.prototype` এর একটি প্রতিস্থাপন যা এখন আর Vue 3 তে নেই। বৈশ্বিক যেকোনো কিছুর মতো, এটিকে অল্প ব্যবহার করা উচিত।

   যদি একটি বৈশ্বিক কম্পিউটেড প্রপার্টি একটি কম্পোনেন্টস নিজস্ব কম্পিউটেড প্রপার্টির সাথে দ্বন্দ্ব করে, তবে কম্পোনেন্টটির নিজস্ব কম্পিউটেড প্রপার্টির অগ্রাধিকার থাকবে।

- **ব্যবহার**

  ```js
  app.config.globalProperties.msg = 'hello'
  ```

  এটি অ্যাপ্লিকেশানের যেকোনো কম্পোনেন্ট টেমপ্লেটের ভিতরে `msg` উপলব্ধ করে, এবং কোনো কম্পোনেন্ট উদাহরণের `এ`তেও:

  ```js
  export default {
    mounted() {
      console.log(this.msg) // 'hello'
    }
  }
  ```

- **আরো দেখুন** [Guide - Augmenting Global Properties](/guide/typescript/options-api#augmenting-global-properties) <sup class="vt-badge ts" />

## app.config.optionMergeStrategies {#app-config-optionmergestrategies}

কাস্টম কম্পোনেন্ট বিকল্পগুলির জন্য মার্জিং কৌশল নির্ধারণের জন্য একটি অবজেক্ট।

- **প্রকার**

  ```ts
  interface AppConfig {
    optionMergeStrategies: Record<string, OptionMergeFunction>
  }

  type OptionMergeFunction = (to: unknown, from: unknown) => any
  ```

- **বিস্তারিত**

  কিছু প্লাগইন/লাইব্রেরি কাস্টম কম্পোনেন্ট বিকল্পের জন্য সমর্থন যোগ করে (গ্লোবাল মিক্সিন ইনজেকশনের মাধ্যমে)। এই বিকল্পগুলির জন্য বিশেষ মার্জিং যুক্তির প্রয়োজন হতে পারে যখন একই বিকল্পটিকে একাধিক উত্স থেকে "একত্রীকরণ" করতে হবে (যেমন মিক্সিন বা কম্পোনেন্ট উত্তরাধিকার)।

   `app.config.optionMergeStrategies` অবজেক্টে কী হিসাবে বিকল্পের নাম ব্যবহার করে একটি কাস্টম বিকল্পের জন্য একটি মার্জ কৌশল ফাংশন নিবন্ধিত করা যেতে পারে।

   মার্জ স্ট্র্যাটেজি ফাংশন যথাক্রমে প্রথম এবং দ্বিতীয় আর্গুমেন্ট হিসাবে অভিভাবক এবং সন্তানের দৃষ্টান্তগুলিতে সংজ্ঞায়িত বিকল্পটির মান গ্রহণ করে।

- **উদাহরণ**

  ```js
  const app = createApp({
    // option from self
    msg: 'Vue',
    // option from a mixin
    mixins: [
      {
        msg: 'Hello '
      }
    ],
    mounted() {
      // merged options exposed on this.$options
      console.log(this.$options.msg)
    }
  })

  // define a custom merge strategy for `msg`
  app.config.optionMergeStrategies.msg = (parent, child) => {
    return (parent || '') + (child || '')
  }

  app.mount('#app')
  // logs 'Hello Vue'
  ```

- **See also** [Component Instance - `$options`](/api/component-instance#options)

## app.config.idPrefix <sup class="vt-badge" data-text="3.5+" /> {#app-config-idprefix}

Configure a prefix for all IDs generated via [useId()](/api/general#useid) inside this application.

- **Type:** `string`

- **Default:** `undefined`

- **Example**

  ```js
  app.config.idPrefix = 'my-app'
  ```

  ```js
  // in a component:
  const id1 = useId() // 'my-app:0'
  const id2 = useId() // 'my-app:1'
  ```

## app.config.throwUnhandledErrorInProduction <sup class="vt-badge" data-text="3.5+" /> {#app-config-throwunhandlederrorinproduction}

Force unhandled errors to be thrown in production mode.

- **Type:** `boolean`

- **Default:** `false`

- **Details**

  By default, errors thrown inside a Vue application but not explicitly handled have different behavior between development and production modes:

  - In development, the error is thrown and can possibly crash the application. This is to make the error more prominent so that it can be noticed and fixed during development.

  - In production, the error will only be logged to the console to minimize the impact to end users. However, this may prevent errors that only happen in production from being caught by error monitoring services.

  By setting `app.config.throwUnhandledErrorInProduction` to `true`, unhandled errors will be thrown even in production mode.
