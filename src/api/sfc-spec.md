# SFC Syntax Specification {#sfc-syntax-specification}

## Overview {#overview}

একটি Vue সিঙ্গেল-ফাইল কম্পোনেন্ট (SFC), প্রচলিতভাবে `*.vue` ফাইল এক্সটেনশন ব্যবহার করে, একটি কাস্টম ফাইল ফরম্যাট যা একটি Vue কম্পোনেন্ট বর্ণনা করার জন্য একটি HTML-এর মতো সিনট্যাক্স ব্যবহার করে। একটি Vue SFC এইচটিএমএল এর সাথে সিনট্যাক্টিভাবে সামঞ্জস্যপূর্ণ।

প্রতিটি `*.vue` ফাইলে তিন ধরনের টপ-লেভেলের ভাষা ব্লক থাকে: `<template>`, `<script>`, এবং `<style>`, এবং ঐচ্ছিকভাবে অতিরিক্ত কাস্টম ব্লক:

```vue
<template>
  <div class="example">{{ msg }}</div>
</template>

<script>
export default {
  data() {
    return {
      msg: 'Hello world!'
    }
  }
}
</script>

<style>
.example {
  color: red;
}
</style>

<custom1>
  This could be e.g. documentation for the component.
</custom1>
```

## Language Blocks {#language-blocks}

### `<template>` {#template}

- প্রতিটি `*.vue` ফাইলে সর্বাধিক একটি টপ-লেভেলেরর `<template>` ব্লক থাকতে পারে।

- কন্টেন্ট এক্সট্রাক্ট করা হবে এবং `@vue/compiler-dom`-এ পাস করা হবে, জাভাস্ক্রিপ্ট রেন্ডার ফাংশনে প্রি-কম্পাইলড করা হবে এবং এক্সপোর্টেড কম্পোনেন্টেরর সাথে এর `render` অপশন হিসেবে সংযুক্ত করা হবে।

### `<script>` {#script}

- প্রতিটি `*.vue` ফাইলে সর্বাধিক একটি `<script>` ব্লক থাকতে পারে ([`<script setup>`](/api/sfc-script-setup) ব্যতীত)।

- স্ক্রিপ্টটি একটি ES মডিউল হিসাবে এক্সিকিউট করা হয়।

- **ডিফল্ট এক্সপোর্ট** একটি Vue কম্পোনেন্ট অপশন অবজেক্ট হওয়া উচিত, হয় একটি প্লেইন অবজেক্ট হিসাবে বা [defineComponent](/api/general#definecomponent) এর রিটার্ন ভ্যালু হিসাবে।

### `<script setup>` {#script-setup}

- প্রতিটি `*.vue` ফাইলে সর্বাধিক একটি `<script setup>` ব্লক থাকতে পারে (সাধারণ `<script>` ব্যতীত)।

- স্ক্রিপ্টটি প্রি-প্রসেস করা হয় এবং কম্পোনেন্টের `setup()` ফাংশন হিসেবে ব্যবহার করা হয়, যার মানে এটি **কম্পোনেন্টের প্রতিটি ইনস্ট্যান্সের জন্য** কার্যকর করা হবে। `<script setup>`-এ টপ-লেভেলের বাইন্ডিংগুলি স্বয়ংক্রিয়ভাবে টেমপ্লেটে এক্সপোজড হয়৷ আরও বিশদ বিবরণের জন্য, [`<script setup>`](/api/sfc-script-setup-এ ডেডিকেটেড ডকুমেন্টেশন) দেখুন।

### `<style>` {#style}

- একটি একক `*.vue` ফাইলে একাধিক `<style>` ট্যাগ থাকতে পারে।

- একটি `<style>` ট্যাগে `scoped` বা `module` অ্যাট্রিবিউট থাকতে পারে (আরো বিশদ বিবরণের জন্য [SFC স্টাইল ফিচারস](/api/sfc-css-features) দেখুন) কারন্ট কম্পোনেন্টে স্টাইলগুলিকে এনক্যাপসুলেট করতে হেল্প করে। বিভিন্ন এনক্যাপসুলেশন মোড সহ একাধিক `<style>` ট্যাগ একই কম্পোনেন্টে মিশ্রিত করা যেতে পারে।

### Custom Blocks {#custom-blocks}

Additional custom blocks can be included in a `*.vue` file for any project-specific needs, for example a `<docs>` block. Some real-world examples of custom blocks include:

- [Gridsome: `<page-query>`](https://gridsome.org/docs/querying-data/)
- [vite-plugin-vue-gql: `<gql>`](https://github.com/wheatjs/vite-plugin-vue-gql)
- [vue-i18n: `<i18n>`](https://github.com/intlify/bundle-tools/tree/main/packages/vite-plugin-vue-i18n#i18n-custom-block)

Handling of Custom Blocks will depend on tooling - if you want to build your own custom block integrations, see the [SFC custom block integrations tooling section](/guide/scaling-up/tooling#sfc-custom-block-integrations) for more details.

## Automatic Name Inference {#automatic-name-inference}

An SFC automatically infers the component's name from its **filename** in the following cases:

- Dev warning formatting
- DevTools inspection
- Recursive self-reference, e.g. a file named `FooBar.vue` can refer to itself as `<FooBar/>` in its template. This has lower priority than explicitly registered/imported components.

## Pre-Processors {#pre-processors}

Blocks can declare pre-processor languages using the `lang` attribute. The most common case is using TypeScript for the `<script>` block:

```vue-html
<script lang="ts">
  // use TypeScript
</script>
```

`lang` can be applied to any block - for example we can use `<style>` with [Sass](https://sass-lang.com/) and `<template>` with [Pug](https://pugjs.org/api/getting-started.html):

```vue-html
<template lang="pug">
p {{ msg }}
</template>

<style lang="scss">
  $primary-color: #333;
  body {
    color: $primary-color;
  }
</style>
```

Note that integration with various pre-processors may differ by toolchain. Check out the respective documentation for examples:

- [Vite](https://vitejs.dev/guide/features.html#css-pre-processors)
- [Vue CLI](https://cli.vuejs.org/guide/css.html#pre-processors)
- [webpack + vue-loader](https://vue-loader.vuejs.org/guide/pre-processors.html#using-pre-processors)

## `src` Imports {#src-imports}

If you prefer splitting up your `*.vue` components into multiple files, you can use the `src` attribute to import an external file for a language block:

```vue
<template src="./template.html"></template>
<style src="./style.css"></style>
<script src="./script.js"></script>
```

Beware that `src` imports follow the same path resolution rules as webpack module requests, which means:

- Relative paths need to start with `./`
- You can import resources from npm dependencies:

```vue
<!-- import a file from the installed "todomvc-app-css" npm package -->
<style src="todomvc-app-css/index.css" />
```

`src` imports also work with custom blocks, e.g.:

```vue
<unit-test src="./unit-test.js">
</unit-test>
```

## Comments {#comments}

Inside each block you shall use the comment syntax of the language being used (HTML, CSS, JavaScript, Pug, etc.). For top-level comments, use HTML comment syntax: `<!-- comment contents here -->`
