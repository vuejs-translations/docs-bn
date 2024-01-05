---
outline: deep
---

# Compile-Time Flags {#compile-time-flags}

:::tip
কম্পাইল-টাইম ফ্ল্যাগ শুধুমাত্র Vue এর `esm-bundler` বিল্ড ব্যবহার করার সময় প্রযোজ্য (যেমন `vue/dist/vue.esm-bundler.js`)।
:::

একটি বিল্ড ধাপের সাথে Vue ব্যবহার করার সময়, নির্দিষ্ট বৈশিষ্ট্যগুলি সক্রিয়/নিষ্ক্রিয় করতে বেশ কয়েকটি কম্পাইল-টাইম ফ্ল্যাগ কনফিগার করা সম্ভব। কম্পাইল-টাইম ফ্ল্যাগ ব্যবহার করার সুবিধা হল যে এইভাবে নিষ্ক্রিয় বৈশিষ্ট্যগুলি ট্রি-শেকিং এর মাধ্যমে চূড়ান্ত বান্ডিল থেকে সরানো যেতে পারে।

এই ফ্ল্যাগগুলি স্পষ্টভাবে কনফিগার করা না থাকলেও Vue কাজ করবে। যাইহোক, এটি সর্বদা তাদের কনফিগার করার সুপারিশ করা হয় যাতে প্রাসঙ্গিক বৈশিষ্ট্যগুলি সম্ভব হলে সঠিকভাবে সরানো যায়।

আপনার বিল্ড টুলের উপর নির্ভর করে কিভাবে কনফিগার করতে হয় সে সম্পর্কে [কনফিগারেশন গাইড](#configuration-guides) দেখুন।

## `__VUE_OPTIONS_API__` {#VUE_OPTIONS_API}

- **Default:** `true`

  Options API সমর্থন সক্রিয়/নিষ্ক্রিয় করুন। এটি নিষ্ক্রিয় করার ফলে ছোট বান্ডিল তৈরি হবে, তবে 3য় পক্ষের লাইব্রেরিগুলির সাথে সামঞ্জস্যতা প্রভাবিত করতে পারে যদি তারা Options API এর উপর নির্ভর করে।

## `__VUE_PROD_DEVTOOLS__` {#VUE_PROD_DEVTOOLS}

- **Default:** `false`

  প্রোডাকশন বিল্ডে devtools সমর্থন সক্রিয়/নিষ্ক্রিয় করুন। এর ফলে বান্ডেলে আরও কোড অন্তর্ভুক্ত হবে, তাই এটি শুধুমাত্র ডিবাগিংয়ের উদ্দেশ্যে এটি সক্রিয় করার পরামর্শ দেওয়া হচ্ছে।

## `__VUE_PROD_HYDRATION_MISMATCH_DETAILS__` <sup class="vt-badge" data-text="3.4+" /> {#VUE_PROD_HYDRATATION_MISMATCH_DETAILS}

- **Default:** `false`

  প্রোডাকশন বিল্ডে হাইড্রেশনের অমিলের জন্য বিস্তারিত সতর্কতা সক্রিয়/নিষ্ক্রিয় করুন। এর ফলে বান্ডেলে আরও কোড অন্তর্ভুক্ত হবে, তাই এটি শুধুমাত্র ডিবাগিংয়ের উদ্দেশ্যে এটি সক্রিয় করার পরামর্শ দেওয়া হচ্ছে।

## Configuration Guides {#configuration-guides}

### Vite {#vite}

`@vitejs/plugin-vue` স্বয়ংক্রিয়ভাবে এই ফ্ল্যাগগুলির জন্য ডিফল্ট মান প্রদান করে। ডিফল্ট মান পরিবর্তন করতে, Vite's ব্যবহার করুন[`define` config option](https://vitejs.dev/config/shared-options.html#define):

```js
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
  define: {
    // enable hydration mismatch details in production build
    __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'true'
  }
})
```

### vue-cli {#vue-cli}

`@vue/cli-service` স্বয়ংক্রিয়ভাবে এই ফ্ল্যাগর কিছুর জন্য ডিফল্ট মান প্রদান করে। মান কনফিগার/পরিবর্তন করতে:

```js
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.plugin('define').tap((definitions) => {
      Object.assign(definitions[0], {
        __VUE_OPTIONS_API__: 'true',
        __VUE_PROD_DEVTOOLS__: 'false',
        __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
      })
      return definitions
    })
  }
}
```

### webpack {#webpack}

ফ্ল্যাগ ওয়েবপ্যাক ব্যবহার করে সংজ্ঞায়িত করা উচিত [DefinePlugin](https://webpack.js.org/plugins/define-plugin/):

```js
// webpack.config.js
module.exports = {
  // ...
  plugins: [
    new webpack.DefinePlugin({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```

### Rollup {#rollup}

ফ্ল্যাগ ব্যবহার করে সংজ্ঞায়িত করা উচিত [@rollup/plugin-replace](https://github.com/rollup/plugins/tree/master/packages/replace):

```js
// rollup.config.js
import replace from '@rollup/plugin-replace'

export default {
  plugins: [
    replace({
      __VUE_OPTIONS_API__: 'true',
      __VUE_PROD_DEVTOOLS__: 'false',
      __VUE_PROD_HYDRATION_MISMATCH_DETAILS__: 'false'
    })
  ]
}
```
