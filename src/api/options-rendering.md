# Options: Rendering {#options-rendering}

## template {#template}

কম্পোনেন্টের জন্য একটি স্ট্রিং টেমপ্লেট.

- **প্রকার**

  ```ts
  interface ComponentOptions {
    template?: string
  }
  ```

- **বিস্তারিত**

  `template` অপশনের মাধ্যমে প্রদত্ত একটি টেমপ্লেট রানটাইমে সাথে সাথে  কম্পাইল করা হবে। টেমপ্লেট কম্পাইলার অন্তর্ভুক্ত Vue-এর একটি বিল্ড ব্যবহার করার সময় এটি শুধুমাত্র সমর্থিত। টেমপ্লেট কম্পাইলার **NOT** Vue বিল্ডে অন্তর্ভুক্ত যেগুলির নামে `runtime` শব্দ আছে, যেমন `vue.runtime.esm-bundler.js`। বিভিন্ন বিল্ড সম্পর্কে আরও বিস্তারিত জানার জন্য [dist file guide](https://github.com/vuejs/core/tree/main/packages/vue#which-dist-file-to-use) দেখুন।

  যদি স্ট্রিংটি `#` দিয়ে শুরু হয় তবে এটি একটি `querySelector` হিসেবে ব্যবহার করা হবে এবং সিলেক্টেড ইলিমেন্টের `innerHTML` টেমপ্লেট স্ট্রিং হিসেবে ব্যবহার করা হবে। এটি সোর্স টেমপ্লেটটিকে নেটিভ `<template>` ইলিমেন্ট ব্যবহার করে লেখার অনুমতি দেয়।

  যদি একই কম্পোনেন্টে `render` অপশনটিও উপস্থিত থাকে, তাহলে `template` ইগনোর করা হবে।

  আপনার অ্যাপ্লিকেশনের রুট কম্পোনেন্টে একটি `template` বা `render` অপশন নির্দিষ্ট না থাকলে, Vue পরিবর্তে টেমপ্লেট হিসেবে মাউন্ট করা কম্পোনেন্টের `innerHTML` ব্যবহার করার চেষ্টা করবে।

  :::warning সিকিউরিটি নোট
  শুধুমাত্র টেমপ্লেট সোর্স ব্যবহার করুন যা আপনি বিশ্বাস করতে পারেন। আপনার টেমপ্লেট হিসাবে ব্যবহারকারী-প্রদত্ত কন্টেন্ট ব্যবহার করবেন না। আরও বিস্তারিত জানার জন্য [Security Guide](/guide/best-practices/security#rule-no-1-never-use-non-trusted-templates) দেখুন।
  :::

## render {#render}

A function that programmatically returns the virtual DOM tree of the component.

- **Type**

  ```ts
  interface ComponentOptions {
    render?(this: ComponentPublicInstance) => VNodeChild
  }

  type VNodeChild = VNodeChildAtom | VNodeArrayChildren

  type VNodeChildAtom =
    | VNode
    | string
    | number
    | boolean
    | null
    | undefined
    | void

  type VNodeArrayChildren = (VNodeArrayChildren | VNodeChildAtom)[]
  ```

- **Details**

  `render` is an alternative to string templates that allows you to leverage the full programmatic power of JavaScript to declare the render output of the component.

  Pre-compiled templates, for example those in Single-File Components, are compiled into the `render` option at build time. If both `render` and `template` are present in a component, `render` will take higher priority.

- **See also**
  - [Rendering Mechanism](/guide/extras/rendering-mechanism)
  - [Render Functions](/guide/extras/render-function)

## compilerOptions {#compileroptions}

Configure runtime compiler options for the component's template.

- **Type**

  ```ts
  interface ComponentOptions {
    compilerOptions?: {
      isCustomElement?: (tag: string) => boolean
      whitespace?: 'condense' | 'preserve' // default: 'condense'
      delimiters?: [string, string] // default: ['{{', '}}']
      comments?: boolean // default: false
    }
  }
  ```

- **Details**

  This config option is only respected when using the full build (i.e. the standalone `vue.js` that can compile templates in the browser). It supports the same options as the app-level [app.config.compilerOptions](/api/application#app-config-compileroptions), and has higher priority for the current component.

- **See also** [app.config.compilerOptions](/api/application#app-config-compileroptions)

## slots<sup class="vt-badge ts"/> {#slots}

An option to assist with type inference when using slots programmatically in render functions. Only supported in 3.3+.

- **Details**

  This option's runtime value is not used. The actual types should be declared via type casting using the `SlotsType` type helper:

  ```ts
  import { SlotsType } from 'vue'

  defineComponent({
    slots: Object as SlotsType<{
      default: { foo: string; bar: number }
      item: { data: number }
    }>,
    setup(props, { slots }) {
      expectType<
        undefined | ((scope: { foo: string; bar: number }) => any)
      >(slots.default)
      expectType<undefined | ((scope: { data: number }) => any)>(
        slots.item
      )
    }
  })
  ```
