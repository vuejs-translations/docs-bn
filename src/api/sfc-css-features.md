# SFC CSS Features {#sfc-css-features}

## Scoped CSS {#scoped-css}

যখন একটি `<style>` ট্যাগে `scoped` অ্যাট্রিবিউট থাকে, তখন এর CSS শুধুমাত্র বর্তমান কম্পোনেন্টের ইলিমেন্টগুলিতে প্রযোজ্য হবে। এটি শ্যাডো ডোমে পাওয়া স্টাইল এনক্যাপসুলেশনের অনুরূপ। এটি কিছু সতর্কতার সাথে আসে, কিন্তু কোনো পলিফিলের প্রয়োজন হয় না। এটি নিম্নলিখিত রূপান্তর করতে PostCSS ব্যবহার করে অর্জন করা হয়:

```vue
<style scoped>
.example {
  color: red;
}
</style>

<template>
  <div class="example">hi</div>
</template>
```

নিম্নলিখিত মধ্যে:

```vue
<style>
.example[data-v-f3f3eg9] {
  color: red;
}
</style>

<template>
  <div class="example" data-v-f3f3eg9>hi</div>
</template>
```

### Child Component Root Elements {#child-component-root-elements}

`scoped` এর সাথে, প্যারেন্ট কম্পোনেন্টের স্টাইল চাইল্ড কম্পোনেন্টে লিক হবে না। যাইহোক, একটি চাইল্ড কম্পোনেন্টের রুট নোড প্যারেন্টস স্কোপড CSS এবং চাইল্ড স্কোপড CSS উভয় দ্বারা এ্যফেক্টেড হবে। এটি ডিজাইনের মাধ্যমে যাতে প্যারেন্ট লেআউটের উদ্দেশ্যে চাইল্ড রুট এলিমেন্টটিকে স্টাইল করতে পারেন।

### Deep Selectors {#deep-selectors}

আপনি যদি `scoped` style-এ  একটি সিলেক্টরকে "deep" হতে চান, অর্থাৎ চাইল্ড কম্পোনেন্টগুলিকে প্রভাবিত করে, আপনি `:deep()` pseudo-class ব্যবহার করতে পারেন:

```vue
<style scoped>
.a :deep(.b) {
  /* ... */
}
</style>
```

উপরে কম্পাইল করা হবে:

```css
.a[data-v-f3f3eg9] .b {
  /* ... */
}
```

:::tip
`v-html` দিয়ে তৈরি DOM কন্টেন্ট স্কোপড স্টাইল দ্বারা প্রভাবিত হয় না, তবে আপনি এখনও ডীপ সিলেক্টর ব্যবহার করে তাদের স্টাইল করতে পারেন।
:::

### Slotted Selectors {#slotted-selectors}

ডিফল্টরূপে, স্কোপড স্টাইলগুলি `<slot/>` দ্বারা রেন্ডার করা কন্টেন্টকে প্রভাবিত করে না, কারণ সেগুলিকে প্যারেন্ট কম্পোনেন্টের মালিকানাধীন বলে মনে করা হয়।

```vue
<style scoped>
:slotted(div) {
  color: red;
}
</style>
```

### Global Selectors {#global-selectors}

আপনি যদি গ্লোবালি প্রয়োগ করতে চান শুধুমাত্র একটি নিয়ম, আপনি অন্য `<style>` তৈরি করার পরিবর্তে `:global` pseudo-class ব্যবহার করতে পারেন (নীচে দেখুন):

```vue
<style scoped>
:global(.red) {
  color: red;
}
</style>
```

### Mixing Local and Global Styles {#mixing-local-and-global-styles}

আপনি একই উপাদানে স্কোপড এবং নন-স্কোপড স্টাইল উভয়ই অন্তর্ভুক্ত করতে পারেন:

```vue
<style>
/* global styles */
</style>

<style scoped>
/* local styles */
</style>
```

### Scoped Style Tips {#scoped-style-tips}

- **স্কোপড স্টাইল ক্লাসের প্রয়োজনীয়তা দূর করে না**। ব্রাউজারগুলি যেভাবে বিভিন্ন CSS সিলেক্টরকে রেন্ডার করে তার কারণে, `p { color: red }` স্কোপ করার সময় অনেকগুণ ধীর হবে (অর্থাৎ যখন একটি অ্যাট্রিবিউট সিলেক্টরের সাথে মিলিত হয়)। আপনি যদি এর পরিবর্তে ক্লাস বা আইডি ব্যবহার করেন, যেমন `.example { color: red }`, তাহলে আপনি ভার্চুয়ালি সেই পারফরম্যান্স হিটটি মুছে ফেলবেন।

- **রিকার্সিভ কম্পোনেন্টে ডিসেন্ড্যান্ট সিলেক্টরদের ব্যাপারে সতর্ক থাকুন!** সিএসএস নিয়মের জন্য সিলেক্টর `.a .b` এর সাথে, যদি `.a` এর সাথে মেলে এমন এলিমেন্টে রিকারসিভ চাইল্ড কম্পোনেন্ট থাকে, তাহলে সেই চাইল্ড কম্পোনেন্টের সব `.b` নিয়ম অনুযায়ী মিলবে।

## CSS Modules {#css-modules}

একটি `<style module>` ট্যাগ [CSS Module](https://github.com/css-modules/css-modules) হিসাবে কম্পাইল করা হয় এবং ফলাফলে CSS ক্লাসগুলিকে `$style` -এর অধীনে একটি অবজেক্ট হিসেবে কম্পোনেন্টে প্রকাশ করে:

```vue
<template>
  <p :class="$style.red">This should be red</p>
</template>

<style module>
.red {
  color: red;
}
</style>
```

সংঘর্ষ এড়াতে ফলস্বরূপ ক্লাসগুলি হ্যাশ করা হয়, শুধুমাত্র বর্তমান কম্পোনেন্টে CSS স্কোপ করার একই ইফেক্ট অর্জন করে।

আরও বিস্তারিত জানার জন্য [CSS Module spec](https://github.com/css-modules/css-modules) দেখুন যেমন [global exceptions](https://github.com/css-modules/css-modules) #exceptions) এবং [composition](https://github.com/css-modules/css-modules#composition)।

### Custom Inject Name {#custom-inject-name}

আপনি `module` অ্যাট্রিবিউটকে একটি মান দিয়ে ইনজেকশন করা ক্লাস অবজেক্টের প্রোপার্টি কী কাস্টমাইজ করতে পারেন:

```vue
<template>
  <p :class="classes.red">red</p>
</template>

<style module="classes">
.red {
  color: red;
}
</style>
```

### Usage with Composition API {#usage-with-composition-api}

ইনজেকশন করা ক্লাসগুলি `setup()` এবং `<script setup>`-এ `useCssModule` API এর মাধ্যমে অ্যাক্সেস করা যেতে পারে। কাস্টম ইনজেকশন নাম সহ `<style module>` ব্লকের জন্য, `useCssModule` প্রথম আর্গুমেন্ট হিসাবে ম্যাচিং `module` অ্যাট্রিবিউট ভ্যালুকে রিসিভ করে:

```js
import { useCssModule } from 'vue'

// inside setup() scope...
// default, returns classes for <style module>
useCssModule()

// named, returns classes for <style module="classes">
useCssModule('classes')
```

## `v-bind()` in CSS {#v-bind-in-css}

SFC `<style>` ট্যাগগুলি `v-bind` CSS ফাংশন ব্যবহার করে CSS ভ্যালুগুলিকে ডাইনামিক কম্পোনেন্ট অবস্থায় লিঙ্ক করতে সাপোর্ট করে:

```vue
<template>
  <div class="text">hello</div>
</template>

<script>
export default {
  data() {
    return {
      color: 'red'
    }
  }
}
</script>

<style>
.text {
  color: v-bind(color);
}
</style>
```

সিনট্যাক্স [`<script setup>`](./sfc-script-setup) এর সাথে কাজ করে, এবং জাভাস্ক্রিপ্ট এক্সপ্রেশন সাপোর্ট করে (কোর্ট দ্বারা আবৃত করা আবশ্যক):

```vue
<script setup>
const theme = {
  color: 'red'
}
</script>

<template>
  <p>hello</p>
</template>

<style scoped>
p {
  color: v-bind('theme.color');
}
</style>
```

একচুয়াল ভ্যালু একটি হ্যাশ করা CSS কাস্টম প্রপার্টিতে কম্পাইল করা হবে, তাই CSS এখনও স্ট্যাটিক। কাস্টম প্রপার্টি ইনলাইন স্টাইলের মাধ্যমে কম্পোনেন্টের রুট ইলিমেন্টে প্রয়োগ করা হবে এবং সোর্স ভ্যালু পরিবর্তন হলে রিয়েক্টিভভাবে আপডেট করা হবে।
