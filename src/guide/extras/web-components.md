# Vue and Web Components {#vue-and-web-components}

[ওয়েব কম্পোনেন্ট](https://developer.mozilla.org/en-US/docs/Web/Web_Components) হল ওয়েব নেটিভ API-এর একটি সেটের জন্য একটি ছাতা শব্দ যা ডেভেলপারদের পুনরায় ব্যবহারযোগ্য কাস্টম কম্পোনেন্ট তৈরি করতে দেয়।

আমরা Vue এবং ওয়েব কম্পোনেন্টগুলিকে প্রাথমিকভাবে পরিপূরক প্রযুক্তি হিসাবে বিবেচনা করি। Vue ব্যবহার এবং কাস্টম কম্পোনেন্ট তৈরি উভয়ের জন্য চমৎকার সমর্থন রয়েছে। আপনি একটি বিদ্যমান Vue অ্যাপ্লিকেশনে কাস্টম কম্পোনেন্টগুলিকে ব্যবহার করছেন বা কাস্টম কম্পোনেন্টগুলি তৈরি এবং বিতরণ করতে Vue ব্যবহার করছেন, আপনি ভাল কোম্পানিতে আছেন৷

## Using Custom Elements in Vue {#using-custom-elements-in-vue}

Vue [কাস্টম এলিমেন্টস এভরিওয়ের পরীক্ষায় একটি নিখুঁত ১০০% স্কোর করে](https://custom-elements-everywhere.com/libraries/vue/results/results.html)। একটি Vue অ্যাপ্লিকেশনের মধ্যে কাস্টম কম্পোনেন্টগুলি ব্যবহার করা মূলত নেটিভ HTML কম্পোনেন্টগুলি ব্যবহার করার মতোই কাজ করে, কয়েকটি জিনিস মাথায় রাখতে হবে:

### Skipping Component Resolution {#skipping-component-resolution}

ডিফল্টরূপে, Vue একটি নন-নেটিভ HTML ট্যাগকে একটি কাস্টম কম্পোনেন্ট হিসাবে রেন্ডার করার আগে একটি নিবন্ধিত Vue কম্পোনেন্ট হিসাবে সমাধান করার চেষ্টা করবে৷ এটি ডেভেলপমেন্টের সময় Vue একটি " কম্পোনেন্ট সমাধান করতে ব্যর্থ" সতর্কতা নির্গত করবে। Vue কে জানাতে যে নির্দিষ্ট কম্পোনেন্টগুলিকে কাস্টম কম্পোনেন্ট হিসাবে বিবেচনা করা উচিত এবং কম্পোনেন্ট রেজোলিউশন এড়িয়ে যাওয়া উচিত, আমরা [`compilerOptions.isCustomElement` বিকল্প](/api/application#app-config-compileroptions) নির্দিষ্ট করতে পারি।

আপনি যদি বিল্ড সেটআপের সাথে Vue ব্যবহার করেন তবে বিকল্পটি বিল্ড কনফিগারেশনের মাধ্যমে পাস করা উচিত কারণ এটি একটি কম্পাইল-টাইম বিকল্প।

#### Example In-Browser Config {#example-in-browser-config}

```js
// Only works if using in-browser compilation.
// If using build tools, see config examples below.
app.config.compilerOptions.isCustomElement = (tag) => tag.includes('-')
```

#### Example Vite Config {#example-vite-config}

```js
// vite.config.js
import vue from '@vitejs/plugin-vue'

export default {
  plugins: [
    vue({
      template: {
        compilerOptions: {
          // treat all tags with a dash as custom elements
          isCustomElement: (tag) => tag.includes('-')
        }
      }
    })
  ]
}
```

#### Example Vue CLI Config {#example-vue-cli-config}

```js
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => ({
        ...options,
        compilerOptions: {
          // treat any tag that starts with ion- as custom elements
          isCustomElement: (tag) => tag.startsWith('ion-')
        }
      }))
  }
}
```

### Passing DOM Properties {#passing-dom-properties}

যেহেতু DOM বৈশিষ্ট্যগুলি শুধুমাত্র স্ট্রিং হতে পারে, তাই আমাদের DOM বৈশিষ্ট্য হিসাবে কাস্টম কম্পোনেন্টগুলিতে জটিল ডেটা প্রেরণ করতে হবে। একটি কাস্টম কম্পোনেন্টে প্রপ সেট করার সময়, Vue 3 স্বয়ংক্রিয়ভাবে `in` অপারেটর ব্যবহার করে DOM- কম্পিউটেড প্রপার্টি উপস্থিতি পরীক্ষা করে এবং কী উপস্থিত থাকলে মানটিকে DOM বৈশিষ্ট্য হিসাবে সেট করতে পছন্দ করবে। এর মানে হল যে, বেশিরভাগ ক্ষেত্রে, যদি কাস্টম কম্পোনেন্টটি [প্রস্তাবিত সেরা অনুশীলনগুলি](https://web.dev/custom-elements-best-practices/) অনুসরণ করে তবে আপনাকে এটি নিয়ে ভাবতে হবে না।

যাইহোক, এমন কিছু বিরল ঘটনা ঘটতে পারে যেখানে ডেটা অবশ্যই একটি DOM কম্পিউটেড প্রপার্টি হিসাবে পাস করতে হবে, কিন্তু কাস্টম কম্পোনেন্টটি সঠিকভাবে কম্পিউটেড প্রপার্টিকে সংজ্ঞায়িত/প্রতিফলিত করে না (যার ফলে `in` চেক ব্যর্থ হয়)। এই ক্ষেত্রে, আপনি `.prop` সংশোধক ব্যবহার করে একটি `v-bind` বাইন্ডিংকে DOM বৈশিষ্ট্য হিসেবে সেট করতে বাধ্য করতে পারেন:

```vue-html
<my-element :user.prop="{ name: 'jack' }"></my-element>

<!-- shorthand equivalent -->
<my-element .user="{ name: 'jack' }"></my-element>
```

## Building Custom Elements with Vue {#building-custom-elements-with-vue}

কাস্টম কম্পোনেন্টগুলির প্রাথমিক সুবিধা হ'ল এগুলি যে কোনও কাঠামোর সাথে বা এমনকি কোনও কাঠামো ছাড়াই ব্যবহার করা যেতে পারে। এটি কম্পোনেন্টগুলি বিতরণ করার জন্য তাদের আদর্শ করে তোলে যেখানে শেষ ভোক্তা একই ফ্রন্টএন্ড স্ট্যাক ব্যবহার নাও করতে পারে, বা যখন আপনি এটি ব্যবহার করে এমন কম্পোনেন্টগুলির বাস্তবায়নের বিশদ থেকে শেষ অ্যাপ্লিকেশনটিকে আলাদা করতে চান৷

### defineCustomElement {#definecustomelement}

Vue [`defineCustomElement`](/api/custom-elements#definecustomelement) পদ্ধতির মাধ্যমে ঠিক একই Vue কম্পোনেন্ট API ব্যবহার করে কাস্টম কম্পোনেন্ট তৈরি করতে সমর্থন করে। পদ্ধতিটি [`defineComponent`](/api/general#definecomponent) হিসাবে একই যুক্তি গ্রহণ করে, কিন্তু পরিবর্তে একটি কাস্টম কম্পোনেন্ট কন্সট্রাকটর প্রদান করে যা `HTMLElement` প্রসারিত করে:

```vue-html
<my-vue-element></my-vue-element>
```

```js
import { defineCustomElement } from 'vue'

const MyVueElement = defineCustomElement({
  // normal Vue component options here
  props: {},
  emits: {},
  template: `...`,

  // defineCustomElement only: CSS to be injected into shadow root
  styles: [`/* inlined css */`]
})

// Register the custom element.
// After registration, all `<my-vue-element>` tags
// on the page will be upgraded.
customElements.define('my-vue-element', MyVueElement)

// You can also programmatically instantiate the element:
// (can only be done after registration)
document.body.appendChild(
  new MyVueElement({
    // initial props (optional)
  })
)
```

#### Lifecycle {#lifecycle}

- একটি Vue কাস্টম এলিমেন্ট যখন এলিমেন্টের [`connectedCallback`](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements#using_the_lifecycle_callbacks) হয় তখন তার শ্যাডো রুটের ভিতরে একটি অভ্যন্তরীণ Vue কম্পোনেন্ট ইন্সট্যান্স মাউন্ট করবে। প্রথমবার ডাকলেন।

- যখন কম্পোনেন্টটির `disconnectedCallback` আহ্বান করা হয়, Vue একটি মাইক্রোটাস্ক টিক করার পরে নথি থেকে কম্পোনেন্টটি বিচ্ছিন্ন হয়েছে কিনা তা পরীক্ষা করবে।

   - যদি কম্পোনেন্টটি এখনও নথিতে থাকে তবে এটি একটি সরানো এবং কম্পোনেন্ট উদাহরণটি সংরক্ষণ করা হবে;

   - কম্পোনেন্টটি নথি থেকে বিচ্ছিন্ন হলে, এটি একটি অপসারণ এবং কম্পোনেন্ট উদাহরণটি আনমাউন্ট করা হবে।

#### Props {#props}

-  `props` বিকল্প ব্যবহার করে ঘোষিত সমস্ত প্রপগুলি বৈশিষ্ট্য হিসাবে কাস্টম কম্পোনেন্টে সংজ্ঞায়িত করা হবে। Vue স্বয়ংক্রিয়ভাবে বৈশিষ্ট্য / বৈশিষ্ট্যের মধ্যে প্রতিফলন পরিচালনা করবে যেখানে উপযুক্ত।

   - গুণাবলী সবসময় সংশ্লিষ্ট বৈশিষ্ট্য প্রতিফলিত হয়.

   - আদিম মান (`string`, `boolean` বা `number`) সহ বৈশিষ্ট্যগুলি বৈশিষ্ট্য হিসাবে প্রতিফলিত হয়।

- Vue স্বয়ংক্রিয়ভাবে `Boolean` বা `Number` প্রকারের সাথে ঘোষিত প্রপগুলিকে পছন্দসই টাইপে কাস্ট করে যখন সেগুলি বৈশিষ্ট্য হিসাবে সেট করা হয় (যা সর্বদা strings)। উদাহরণস্বরূপ, নিম্নলিখিত প্রপস ঘোষণা দেওয়া হয়েছে:

  ```js
  props: {
    selected: Boolean,
    index: Number
  }
  ```

  And the custom element usage:

  ```vue-html
  <my-element selected index="1"></my-element>
  ```

  কম্পোনেন্টে, `selected` কে `true` (বুলিয়ান) তে কাস্ট করা হবে এবং `index` কে `1` (সংখ্যা) এ কাস্ট করা হবে।

#### Events {#events}

`this.$emit` বা সেটআপ `emit` এর মাধ্যমে নির্গত ইভেন্টগুলি নেটিভ [CustomEvents](https://developer.mozilla.org/en-US/docs/Web/Events/Creating_and_triggering_events#adding_custom_data_%E2%80%) হিসাবে পাঠানো হয় 93_customevent) কাস্টম কম্পোনেন্টে। অতিরিক্ত ইভেন্ট আর্গুমেন্ট (পেলোড) কাস্টম ইভেন্ট অবজেক্টের 'বিস্তারিত' কম্পিউটেড প্রপার্টি হিসাবে একটি অ্যারে হিসাবে প্রকাশ করা হবে।

#### Slots {#slots}

 কম্পোনেন্টের ভিতরে, যথারীতি `<slot/>` কম্পোনেন্ট ব্যবহার করে স্লট রেন্ডার করা যেতে পারে। যাইহোক, ফলস্বরূপ কম্পোনেন্ট গ্রহণ করার সময়, এটি শুধুমাত্র [নেটিভ স্লট সিনট্যাক্স](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_templates_and_slots) গ্রহণ করে:

- [স্কোপড স্লট](/guide/components/slots#scoped-slots) সমর্থিত নয়।

- নামযুক্ত স্লট পাস করার সময়, `v-slot` নির্দেশের পরিবর্তে `slot` বৈশিষ্ট্য ব্যবহার করুন:

  ```vue-html
  <my-element>
    <div slot="named">hello</div>
  </my-element>
  ```

#### Provide / Inject {#provide-inject}

[প্রোভাইড/ইনজেক্ট API](/guide/components/provide-inject#provide-inject) এবং এর [কম্পোজিশন API সমতুল্য](/api/composition-api-dependency-injection#provide) Vue-সংজ্ঞায়িত কাস্টম কম্পোনেন্টগুলির মধ্যেও কাজ করে . যাইহোক, মনে রাখবেন এটি **শুধুমাত্র কাস্টম কম্পোনেন্টগুলির মধ্যে** কাজ করে। যেমন একটি Vue-সংজ্ঞায়িত কাস্টম কম্পোনেন্ট একটি নন-কাস্টম-কম্পোনেন্ট Vue কম্পোনেন্ট দ্বারা প্রদত্ত বৈশিষ্ট্যগুলি ইনজেক্ট করতে সক্ষম হবে না।

#### App Level Config <sup class="vt-badge" data-text="3.5+" /> {#app-level-config}

You can configure the app instance of a Vue custom element using the `configureApp` option:

```js
defineCustomElement(MyComponent, {
  configureApp(app) {
    app.config.errorHandler = (err) => {
      /* ... */
    }
  }
})
```

### SFC as Custom Element {#sfc-as-custom-element}

`defineCustomElement` এছাড়াও Vue একক-ফাইল কম্পোনেন্ট (SFCs) এর সাথে কাজ করে। যাইহোক, ডিফল্ট টুলিং সেটআপের সাথে, SFC-এর ভিতরে `<style>` এখনও নিষ্কাশন করা হবে এবং প্রোডাকশন বিল্ডের সময় একক CSS ফাইলে মার্জ করা হবে। একটি কাস্টম কম্পোনেন্ট হিসাবে একটি SFC ব্যবহার করার সময়, এর পরিবর্তে কাস্টম কম্পোনেন্টের ছায়া রুটে `<style>` ট্যাগগুলি ইনজেকশন করা বাঞ্ছনীয়।

অফিসিয়াল SFC টুলিং "কাস্টম এলিমেন্ট মোডে" SFC আমদানি করতে সমর্থন করে (`@vitejs/plugin-vue@^1.4.0` বা `vue-loader@^16.5.0` প্রয়োজন)। কাস্টম এলিমেন্ট মোডে লোড করা একটি SFC তার `<style>` ট্যাগকে CSS-এর স্ট্রিং হিসেবে ইনলাইন করে এবং কম্পোনেন্টের `styles` বিকল্পের অধীনে সেগুলোকে প্রকাশ করে। এটিকে `defineCustomElement` দ্বারা বাছাই করা হবে এবং তাৎক্ষণিকভাবে কম্পোনেন্টের শ্যাডো রুটে ইনজেক্ট করা হবে।

এই মোডে অপ্ট-ইন করতে, আপনার কম্পোনেন্ট ফাইলের নামটি `.ce.vue` দিয়ে শেষ করুন:

```js
import { defineCustomElement } from 'vue'
import Example from './Example.ce.vue'

console.log(Example.styles) // ["/* inlined css */"]

// convert into custom element constructor
const ExampleElement = defineCustomElement(Example)

// register
customElements.define('my-example', ExampleElement)
```

আপনি যদি কাস্টম এলিমেন্ট মোডে কোন ফাইল আমদানি করতে চান তা কাস্টমাইজ করতে চান (উদাহরণস্বরূপ, _all_ SFC কে কাস্টম কম্পোনেন্ট হিসাবে বিবেচনা করা), আপনি সংশ্লিষ্ট বিল্ড প্লাগইনগুলিতে `customElement` বিকল্পটি পাস করতে পারেন:

- [@vitejs/plugin-vue](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue#using-vue-sfcs-as-custom-elements)
- [vue-loader](https://github.com/vuejs/vue-loader/tree/next#v16-only-options)

### Tips for a Vue Custom Elements Library {#tips-for-a-vue-custom-elements-library}

Vue এর সাথে কাস্টম কম্পোনেন্টগুলি তৈরি করার সময়, কম্পোনেন্টগুলি Vue এর রানটাইমের উপর নির্ভর করবে। কতগুলি বৈশিষ্ট্য ব্যবহার করা হচ্ছে তার উপর নির্ভর করে একটি ~16kb বেসলাইন আকারের ব্যয় রয়েছে৷ এর অর্থ হল আপনি যদি একটি একক কাস্টম কম্পোনেন্ট শিপিং করেন তবে Vue ব্যবহার করা আদর্শ নয় - আপনি ভ্যানিলা জাভাস্ক্রিপ্ট, [petite-vue](https://github.com/vuejs/petite-vue) বা ফ্রেমওয়ার্ক ব্যবহার করতে চাইতে পারেন ছোট রানটাইম আকার বিশেষজ্ঞ. যাইহোক, যদি আপনি জটিল যুক্তি সহ কাস্টম কম্পোনেন্টগুলির একটি সংগ্রহ প্রেরণ করেন তবে বেস আকারটি যুক্তিযুক্ত নয়, কারণ Vue প্রতিটি কম্পোনেন্টকে অনেক কম কোডের সাথে লেখার অনুমতি দিবে। আপনি যত বেশি কম্পোনেন্ট একসাথে শিপিং করছেন, ট্রেড-অফ তত ভাল।

যদি কাস্টম কম্পোনেন্টগুলি এমন একটি অ্যাপ্লিকেশনে ব্যবহার করা হয় যা Vue ব্যবহার করছে, তাহলে আপনি নির্মিত বান্ডেল থেকে Vue-কে বাহ্যিক করতে বেছে নিতে পারেন যাতে কম্পোনেন্টগুলি হোস্ট অ্যাপ্লিকেশন থেকে Vue-এর একই অনুলিপি ব্যবহার করে।

আপনার ব্যবহারকারীদের চাহিদা অনুযায়ী আমদানি করতে এবং পছন্দসই ট্যাগ নাম দিয়ে তাদের নিবন্ধন করার জন্য ফ্লেক্সিবল দেওয়ার জন্য পৃথক কম্পোনেন্ট নির্মাণকারী রপ্তানি করার পরামর্শ দেওয়া হয়। আপনি স্বয়ংক্রিয়ভাবে সমস্ত কম্পোনেন্ট নিবন্ধন করার জন্য একটি সুবিধার ফাংশন রপ্তানি করতে পারেন। এখানে একটি Vue কাস্টম কম্পোনেন্ট লাইব্রেরির একটি উদাহরণ এন্ট্রি পয়েন্ট:

```js
// elements.js

import { defineCustomElement } from 'vue'
import Foo from './MyFoo.ce.vue'
import Bar from './MyBar.ce.vue'

const MyFoo = defineCustomElement(Foo)
const MyBar = defineCustomElement(Bar)

// export individual elements
export { MyFoo, MyBar }

export function register() {
  customElements.define('my-foo', MyFoo)
  customElements.define('my-bar', MyBar)
}
```

একজন ব্যবহারকারী একটি Vue ফাইলে উপাদান ব্যবহার করতে পারেন:

```vue
<script setup>
import { register } from 'path/to/elements.js'
register()
</script>

<template>
  <my-foo ...>
    <my-bar ...></my-bar>
  </my-foo>
</template>
```

অথবা অন্য কোন ফ্রেমওয়ার্কে যেমন JSX সহ একটি, এবং কাস্টম নাম সহ:

```jsx
import { MyFoo, MyBar } from 'path/to/elements.js'

customElements.define('some-foo', MyFoo)
customElements.define('some-bar', MyBar)

export function MyComponent() {
  return <>
    <some-foo ... >
      <some-bar ... ></some-bar>
    </some-foo>
  </>
}
```

### Vue-based Web Components and TypeScript {#web-components-and-typescript}

When writing Vue SFC templates, you may want to [type check](/guide/scaling-up/tooling.html#typescript) your Vue components, including those that are defined as custom elements.

Custom elements are registered globally in browsers using their built-in APIs, and by default they won't have type inference when used in Vue templates. To provide type support for Vue components registered as custom elements, we can register global component typings by augmenting the [`GlobalComponents` interface](https://github.com/vuejs/language-tools/wiki/Global-Component-Types) for type checking in Vue templates (JSX users can augment the [JSX.IntrinsicElements](https://www.typescriptlang.org/docs/handbook/jsx.html#intrinsic-elements) type instead, which is not shown here).

Here is how to define the type for a custom element made with Vue:

```typescript
import { defineCustomElement } from 'vue'

// Import the Vue component.
import SomeComponent from './src/components/SomeComponent.ce.vue'

// Turn the Vue component into a Custom Element class.
export const SomeElement = defineCustomElement(SomeComponent)

// Remember to register the element class with the browser.
customElements.define('some-element', SomeElement)

// Add the new element type to Vue's GlobalComponents type.
declare module 'vue' {
  interface GlobalComponents {
    // Be sure to pass in the Vue component type here 
    // (SomeComponent, *not* SomeElement).
    // Custom Elements require a hyphen in their name, 
    // so use the hyphenated element name here.
    'some-element': typeof SomeComponent
  }
}
```

## Non-Vue Web Components and TypeScript {#non-vue-web-components-and-typescript}

Here is the recommended way to enable type checking in SFC templates of Custom Elements that are not built with Vue.

> [!Note]
> This approach is one possible way to do it, but it may vary depending on the
> framework being used to create the custom elements.

Suppose we have a custom element with some JS properties and events defined, and it is shipped in a library called `some-lib`:

```ts
// file: some-lib/src/SomeElement.ts

// Define a class with typed JS properties.
export class SomeElement extends HTMLElement {
  foo: number = 123
  bar: string = 'blah'

  lorem: boolean = false

  // This method should not be exposed to template types.
  someMethod() {
    /* ... */
  }

  // ... implementation details omitted ...
  // ... assume the element dispatches events named "apple-fell" ...
}

customElements.define('some-element', SomeElement)

// This is a list of properties of SomeElement that will be selected for type
// checking in framework templates (f.e. Vue SFC templates). Any other
// properties will not be exposed.
export type SomeElementAttributes = 'foo' | 'bar'

// Define the event types that SomeElement dispatches.
export type SomeElementEvents = {
  'apple-fell': AppleFellEvent
}

export class AppleFellEvent extends Event {
  /* ... details omitted ... */
}
```

The implementation details have been omitted, but the important part is that we have type definitions for two things: prop types and event types.

Let's create a type helper for easily registering custom element type definitions in Vue:

```ts
// file: some-lib/src/DefineCustomElement.ts

// We can re-use this type helper per each element we need to define.
type DefineCustomElement<
  ElementType extends HTMLElement,
  Events extends EventMap = {},
  SelectedAttributes extends keyof ElementType = keyof ElementType
> = new () => ElementType & {
  // Use $props to define the properties exposed to template type checking. Vue
  // specifically reads prop definitions from the `$props` type. Note that we
  // combine the element's props with the global HTML props and Vue's special
  // props.
  /** @deprecated Do not use the $props property on a Custom Element ref, 
    this is for template prop types only. */
  $props: HTMLAttributes &
    Partial<Pick<ElementType, SelectedAttributes>> &
    PublicProps

  // Use $emit to specifically define event types. Vue specifically reads event
  // types from the `$emit` type. Note that `$emit` expects a particular format
  // that we map `Events` to.
  /** @deprecated Do not use the $emit property on a Custom Element ref, 
    this is for template prop types only. */
  $emit: VueEmit<Events>
}

type EventMap = {
  [event: string]: Event
}

// This maps an EventMap to the format that Vue's $emit type expects.
type VueEmit<T extends EventMap> = EmitFn<{
  [K in keyof T]: (event: T[K]) => void
}>
```

> [!Note]
> We marked `$props` and `$emit` as deprecated so that when we get a `ref` to
> a custom element we will not be tempted to use these properties, as these
> properties are for type checking purposes only when it comes to custom elements.
> These properties do not actually exist on the custom element instances.

Using the type helper we can now select the JS properties that should be exposed for type checking in Vue templates:

```ts
// file: some-lib/src/SomeElement.vue.ts

import {
  SomeElement,
  SomeElementAttributes,
  SomeElementEvents
} from './SomeElement.js'
import type { Component } from 'vue'
import type { DefineCustomElement } from './DefineCustomElement'

// Add the new element type to Vue's GlobalComponents type.
declare module 'vue' {
  interface GlobalComponents {
    'some-element': DefineCustomElement<
      SomeElement,
      SomeElementAttributes,
      SomeElementEvents
    >
  }
}
```

Suppose that `some-lib` builds its source TypeScript files into a `dist/` folder. A user of `some-lib` can then import `SomeElement` and use it in a Vue SFC like so:

```vue
<script setup lang="ts">
// This will create and register the element with the browser.
import 'some-lib/dist/SomeElement.js'

// A user that is using TypeScript and Vue should additionally import the
// Vue-specific type definition (users of other frameworks may import other
// framework-specific type definitions).
import type {} from 'some-lib/dist/SomeElement.vue.js'

import { useTemplateRef, onMounted } from 'vue'

const el = useTemplateRef('el')

onMounted(() => {
  console.log(
    el.value!.foo,
    el.value!.bar,
    el.value!.lorem,
    el.value!.someMethod()
  )

  // Do not use these props, they are `undefined`
  // IDE will show them crossed out
  el.$props
  el.$emit
})
</script>

<template>
  <!-- Now we can use the element, with type checking: -->
  <some-element
    ref="el"
    :foo="456"
    :blah="'hello'"
    @apple-fell="
      (event) => {
        // The type of `event` is inferred here to be `AppleFellEvent`
      }
    "
  ></some-element>
</template>
```

If an element does not have type definitions, the types of the properties and events can be defined in a more manual fashion:

```vue
<script setup lang="ts">
// Suppose that `some-lib` is plain JS without type definitions, and TypeScript
// cannot infer the types:
import { SomeElement } from 'some-lib'

// We'll use the same type helper as before.
import { DefineCustomElement } from './DefineCustomElement'

type SomeElementProps = { foo?: number; bar?: string }
type SomeElementEvents = { 'apple-fell': AppleFellEvent }
interface AppleFellEvent extends Event {
  /* ... */
}

// Add the new element type to Vue's GlobalComponents type.
declare module 'vue' {
  interface GlobalComponents {
    'some-element': DefineCustomElement<
      SomeElementProps,
      SomeElementEvents
    >
  }
}

// ... same as before, use a reference to the element ...
</script>

<template>
  <!-- ... same as before, use the element in the template ... -->
</template>
```

Custom Element authors should not automatically export framework-specific custom element type definitions from their libraries, for example they should not export them from an `index.ts` file that also exports the rest of the library, otherwise users will have unexpected module augmentation errors. Users should import the framework-specific type definition file that they need.

## Web Components vs. Vue Components {#web-components-vs-vue-components}

কিছু ডেভেলপার বিশ্বাস করেন যে ফ্রেমওয়ার্ক-মালিকানা কম্পোনেন্ট মডেল এড়ানো উচিত, এবং শুধুমাত্র কাস্টম এলিমেন্ট ব্যবহার করে একটি অ্যাপ্লিকেশন "ভবিষ্যত-প্রমাণ" করে তোলে। এখানে আমরা ব্যাখ্যা করার চেষ্টা করব কেন আমরা বিশ্বাস করি যে এটি একটি অত্যধিক সরলীকৃত সমস্যা।

কাস্টম কম্পোনেন্ট এবং Vue কম্পোনেন্টগুলির মধ্যে প্রকৃতপক্ষে বৈশিষ্ট্যগুলির একটি নির্দিষ্ট স্তরের ওভারল্যাপ রয়েছে: তারা উভয়ই আমাদের ডেটা পাসিং, ইভেন্ট নির্গমন এবং লাইফসাইকেল পরিচালনার সাথে পুনরায় ব্যবহারযোগ্য কম্পোনেন্টগুলিকে সংজ্ঞায়িত করার অনুমতি দেয়। যাইহোক, ওয়েব কম্পোনেন্ট এপিআই তুলনামূলকভাবে নিম্ন-স্তরের এবং বেয়ার-বোন। একটি প্রকৃত অ্যাপ্লিকেশন তৈরি করতে, আমাদের বেশ কয়েকটি অতিরিক্ত ক্ষমতার প্রয়োজন যা প্ল্যাটফর্মটি কভার করে না:

- একটি ডিক্লেয়ার এবং দক্ষ টেমপ্লেটিং সিস্টেম;

- একটি প্রতিক্রিয়াশীল রাষ্ট্র পরিচালনা ব্যবস্থা যা ক্রস- কম্পোনেন্ট লজিক নিষ্কাশন এবং পুনঃব্যবহারের সুবিধা দেয়;

- সার্ভারে কম্পোনেন্টগুলি রেন্ডার করার এবং ক্লায়েন্টে (SSR) হাইড্রেট করার একটি কার্যকর উপায়, যা SEO এবং [ওয়েব ভাইটালস মেট্রিক্স যেমন LCP](https://web.dev/vitals/) এর জন্য গুরুত্বপূর্ণ। নেটিভ কাস্টম কম্পোনেন্ট SSR সাধারণত Node.js-এ DOM কে সিমুলেট করে এবং তারপরে রূপান্তরিত DOM কে সিরিয়ালাইজ করে, যখন Vue SSR যখনই সম্ভব স্ট্রিং কনক্যাটেনেশনে কম্পাইল করে, যা অনেক বেশি কার্যকর।

Vue এর কম্পোনেন্ট মডেলটি একটি সুসংগত সিস্টেম হিসাবে এই চাহিদাগুলিকে মাথায় রেখে ডিজাইন করা হয়েছে।

একটি দক্ষ ইঞ্জিনিয়ারিং দলের সাথে, আপনি সম্ভবত নেটিভ কাস্টম কম্পোনেন্টগুলির শীর্ষে সমতুল্য তৈরি করতে পারেন - তবে এর অর্থ হল আপনি একটি ইন-হাউস ফ্রেমওয়ার্কের দীর্ঘমেয়াদী রক্ষণাবেক্ষণের বোঝাটি গ্রহণ করছেন, যদিও এর ইকোসিস্টেম এবং সম্প্রদায়ের সুবিধাগুলি হারাচ্ছেন Vue এর মত একটি পরিপক্ক ফ্রেমওয়ার্ক।

কাস্টম এলিমেন্টগুলিকে তাদের কম্পোনেন্ট মডেলের ভিত্তি হিসাবে ব্যবহার করে তৈরি ফ্রেমওয়ার্কগুলিও রয়েছে, তবে তাদের সকলকে অনিবার্যভাবে উপরে তালিকাভুক্ত সমস্যাগুলির জন্য তাদের মালিকানাধীন সমাধানগুলি প্রবর্তন করতে হবে। এই ফ্রেমওয়ার্কগুলি ব্যবহার করার ফলে এই সমস্যাগুলি কীভাবে সমাধান করা যায় সে সম্পর্কে তাদের প্রযুক্তিগত সিদ্ধান্তগুলি কেনার অন্তর্ভুক্ত - যা, যা বিজ্ঞাপন দেওয়া হতে পারে, তা স্বয়ংক্রিয়ভাবে আপনাকে ভবিষ্যতের সম্ভাব্য মন্থন থেকে দূরে রাখে না।

এমন কিছু ক্ষেত্রও রয়েছে যেখানে আমরা কাস্টম কম্পোনেন্টগুলিকে সীমাবদ্ধ বলে মনে করি:

- আগ্রহী স্লট মূল্যায়ন কম্পোনেন্ট রচনা বাধা দেয়. Vue-এর [স্কোপড স্লট](/guide/components/slots#scoped-slots) হল কম্পোনেন্ট কম্পোজিশনের জন্য একটি শক্তিশালী মেকানিজম, যা নেটিভ স্লটের আগ্রহের কারণে কাস্টম কম্পোনেন্ট দ্বারা সমর্থিত হতে পারে না। আগ্রহী স্লটগুলির মানে হল প্রাপ্তির কম্পোনেন্ট কখন বা স্লট সামগ্রীর একটি অংশ রেন্ডার করবে তা নিয়ন্ত্রণ করতে পারে না।

- শ্যাডো ডম স্কোপড সিএসএসের সাথে কাস্টম কম্পোনেন্ট শিপিং করার জন্য আজ জাভাস্ক্রিপ্টের ভিতরে সিএসএস এম্বেড করা প্রয়োজন যাতে রানটাইমে সেগুলি শ্যাডো রুটে ইনজেক্ট করা যায়। তারা SSR পরিস্থিতিতে মার্কআপে সদৃশ স্টাইলতেও পরিণত হয়। এই এলাকায় [প্ল্যাটফর্মের বৈশিষ্ট্যগুলি](https://github.com/whatwg/html/pull/4898/) কাজ করা হচ্ছে - কিন্তু এখন পর্যন্ত সেগুলি সর্বজনীনভাবে সমর্থিত নয় এবং এখনও প্রোডাকশন পারফরম্যান্স / SSR আছে উদ্বেগ মোকাবেলা করতে হবে। ইতিমধ্যে, Vue SFC গুলি [CSS স্কোপিং মেকানিজম](/api/sfc-css-features) প্রদান করে যা প্লেইন CSS ফাইলগুলিতে স্টাইলগুলি বের করতে সমর্থন করে।

Vue সর্বদা ওয়েব প্ল্যাটফর্মের সর্বশেষ মানগুলির সাথে আপ টু ডেট থাকবে এবং প্ল্যাটফর্মটি আমাদের কাজকে সহজ করে দিলে আমরা আনন্দের সাথে যা কিছু সরবরাহ করে তা লাভ করব। যাইহোক, আমাদের লক্ষ্য হল সমাধান প্রদান করা যা ভাল কাজ করে এবং আজ কাজ করে। এর অর্থ হল আমাদের একটি সমালোচনামূলক মানসিকতার সাথে নতুন প্ল্যাটফর্ম বৈশিষ্ট্যগুলিকে অন্তর্ভুক্ত করতে হবে - এবং এর মধ্যে সেই ফাঁকগুলি পূরণ করা জড়িত যেখানে মানগুলি কম পড়ে যখন এটি এখনও হয়।
