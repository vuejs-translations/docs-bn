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

একটি ফাংশন যা প্রোগ্রাম্যাটিকভাবে কম্পোনেন্টের ভার্চুয়াল DOM ট্রি রিটার্ন করে।

- **প্রকার**

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

- **বিস্তারিত**

  `render` হল স্ট্রিং টেমপ্লেটগুলির একটি বিকল্প যা আপনাকে কম্পোনেন্টের রেন্ডার আউটপুট ডিকলার করতে জাভাস্ক্রিপ্টের সম্পূর্ণ প্রোগ্রাম্যাটিক শক্তির সুবিধা নিতে দেয়।

  প্রি-কম্পাইল করা টেমপ্লেট, যেমন সিঙ্গেল-ফাইল কম্পোনেন্টে, বিল্ড টাইমে `render` অপশনে কম্পাইল করা হয়। যদি একটি কম্পোনেন্টে `render` এবং `template` উভয়ই উপস্থিত থাকে, তাহলে `render` অধিক অগ্রাধিকার নেবে।

- **আরো দেখুন**
  - [Rendering Mechanism](/guide/extras/rendering-mechanism)
  - [Render Functions](/guide/extras/render-function)

## compilerOptions {#compileroptions}

 কম্পোনেন্টের টেমপ্লেটের জন্য রানটাইম কম্পাইলার অপশন কনফিগার করুন।

- **প্রকার**

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

- **বিস্তারিত**

  এই কনফিগার অপশনটি শুধুমাত্র সম্পূর্ণ বিল্ড ব্যবহার করার সময় রেসপেক্টেড করা হয় (অর্থাৎ স্বতন্ত্র `vue.js` যা ব্রাউজারে টেমপ্লেট কম্পাইল করতে পারে)। এটি অ্যাপ-লেভেল [app.config.compilerOptions](/api/application#app-config-compileroptions) এর মতো একই অপশনগুলিকে সমর্থন করে এবং বর্তমান কম্পোনেন্টের জন্য অধিক অগ্রাধিকার রয়েছে৷

- **আরো দেখুন** [app.config.compilerOptions](/api/application#app-config-compileroptions)

## slots<sup class="vt-badge ts"/> {#slots}

রেন্ডার ফাংশনে প্রোগ্রাম্যাটিকভাবে স্লট ব্যবহার করার সময় টাইপ ইনফারেন্সে সহায়তা করার একটি অপশন। শুধুমাত্র 3.3+ এ সমর্থিত।

- **বিস্তারিত**

  এই অপশনের রানটাইম ভ্যালু ব্যবহার করা হয় না। প্রকৃত টাইপগুলি `SlotsType` টাইপ হেল্পার ব্যবহার করে টাইপ কাস্টিংয়ের মাধ্যমে ডিকলার করা উচিত:

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
