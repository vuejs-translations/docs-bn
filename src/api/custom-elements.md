# Custom Elements API {#custom-elements-api}

## defineCustomElement() {#definecustomelement}

এই পদ্ধতিটি [`defineComponent`](#definecomponent) হিসাবে একই যুক্তি গ্রহণ করে, কিন্তু পরিবর্তে একটি নেটিভ [কাস্টম এলিমেন্ট](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements) ক্লাস প্রদান করে। নির্মাণকারী

- **Type**

  ```ts
  function defineCustomElement(
    component:
      | (ComponentOptions & CustomElementsOptions)
      | ComponentOptions['setup'],
    options?: CustomElementsOptions
  ): {
    new (props?: object): HTMLElement
  }

  interface CustomElementsOptions {
    styles?: string[]

    // the following options are 3.5+
    configureApp?: (app: App) => void
    shadowRoot?: boolean
    nonce?: string
  }
  ```

  > প্রকার পঠনযোগ্যতার জন্য সরলীকৃত।

- **বিস্তারিত**

 সাধারণ উপাদান বিকল্পগুলি ছাড়াও, `defineCustomElement()` এছাড়াও কাস্টম-উপাদান-নির্দিষ্ট কিছু বিকল্পকে সমর্থন করে:

 - **`শৈলী`**: CSS প্রদানের জন্য ইনলাইনযুক্ত CSS স্ট্রিংগুলির একটি অ্যারে যা উপাদানটির ছায়া রুটে ইনজেকশন করা উচিত।

 - **`configureApp`** <sup class="vt-badge" data-text="3.5+"/>: একটি ফাংশন যা কাস্টম উপাদানের জন্য Vue অ্যাপের উদাহরণ কনফিগার করতে ব্যবহার করা যেতে পারে।

 - **`shadowRoot`** <sup class="vt-badge" data-text="3.5+"/>: `বুলিয়ান`, ডিফল্ট `সত্য`। শ্যাডো রুট ছাড়াই কাস্টম এলিমেন্ট রেন্ডার করতে `false` এ সেট করুন। এর মানে `<style>` কাস্টম এলিমেন্টে SFC আর এনক্যাপসুলেট করা হবে না।

 - **`nonce`** <sup class="vt-badge" data-text="3.5+"/>: `স্ট্রিং`, প্রদান করা হলে, স্টাইল ট্যাগে ইনজেকশন করা স্টাইল ট্যাগগুলিতে `nonce` অ্যাট্রিবিউট হিসেবে সেট করা হবে ছায়া মূল

 মনে রাখবেন যে উপাদানের অংশ হিসাবে পাস করার পরিবর্তে, এই বিকল্পগুলি একটি দ্বিতীয় আর্গুমেন্টের মাধ্যমেও পাস করা যেতে পারে:

  ```js
  import Element from './MyElement.ce.vue'

  defineCustomElement(Element, {
    configureApp(app) {
      // ...
    }
  })
  ```

  রিটার্ন মান হল একটি কাস্টম এলিমেন্ট কনস্ট্রাক্টর যা [`customElements.define()`](https://developer.mozilla.org/en-US/docs/Web/API/CustomElementRegistry/define) ব্যবহার করে নিবন্ধিত হতে পারে।

- **Example**

  ```js
  import { defineCustomElement } from 'vue'

  const MyVueElement = defineCustomElement({
    /* component options */
  })

  // Register the custom element.
  customElements.define('my-vue-element', MyVueElement)
  ```

- **এছাড়াও দেখুন**

 - [গাইড - Vue দিয়ে কাস্টম উপাদান তৈরি করা](/guide/extras/web-components#building-custom-elements-with-vue)

 - এছাড়াও মনে রাখবেন যে একক-ফাইল উপাদানগুলির সাথে ব্যবহার করার সময় `defineCustomElement()` এর জন্য [বিশেষ কনফিগার](/guide/extras/web-components#sfc-as-custom-element) প্রয়োজন।

## useHost() <sup class="vt-badge" data-text="3.5+"/> {#usehost}

একটি রচনা API সহায়ক যা বর্তমান Vue কাস্টম উপাদানের হোস্ট উপাদান প্রদান করে।

## useShadowRoot() <sup class="vt-badge" data-text="3.5+"/> {#useshadowroot}

একটি কম্পোজিশন API সাহায্যকারী যা বর্তমান Vue কাস্টম উপাদানের শ্যাডো রুট প্রদান করে।

## this.$host <sup class="vt-badge" data-text="3.5+"/> {#this-host}

একটি Options API প্রপার্টি যা বর্তমান Vue কাস্টম এলিমেন্টের হোস্ট এলিমেন্টকে প্রকাশ করে।
