# Custom Directives {#custom-directives}

<script setup>
const vHighlight = {
  mounted: el => {
    el.classList.add('is-highlight')
  }
}
</script>

<style>
.vt-doc p.is-highlight {
  margin-bottom: 0;
}

.is-highlight {
  background-color: yellow;
  color: black;
}
</style>

## Introduction {#introduction}

কোরে পাঠানো নির্দেশাবলীর ডিফল্ট সেট ছাড়াও (যেমন `v-model` বা `v-show`), Vue আপনাকে আপনার নিজস্ব কাস্টম নির্দেশাবলী নিবন্ধন করার অনুমতি দেয়।

আমরা Vue-তে কোড পুনঃব্যবহারের দুটি ফর্ম চালু করেছি: [components](/guide/essentials/component-basics) এবং [composables](./composables)। কম্পোনেন্টগুলি হল প্রধান বিল্ডিং ব্লক, যখন কম্পোজেবলগুলি রাষ্ট্রীয় যুক্তি পুনঃব্যবহারের উপর দৃষ্টি নিবদ্ধ করে। অন্যদিকে, কাস্টম নির্দেশাবলী প্রধানত যুক্তি পুনঃব্যবহারের উদ্দেশ্যে তৈরি করা হয় যা প্লেইন কম্পোনেন্টগুলিতে নিম্ন-স্তরের DOM অ্যাক্সেস জড়িত।

একটি কাস্টম নির্দেশিকাকে একটি অবজেক্ট হিসাবে সংজ্ঞায়িত করা হয় যাতে একটি কম্পোনেন্টের অনুরূপ লাইফসাইকেল হুক থাকে। হুকগুলি সেই কম্পোনেন্টটি গ্রহণ করে যা নির্দেশে আবদ্ধ। এখানে একটি নির্দেশের একটি উদাহরণ যা একটি ইনপুট ফোকাস করে যখন কম্পোনেন্টটি Vue দ্বারা DOM-এ ঢোকানো হয়:

<div class="composition-api">

```vue
<script setup>
// enables v-highlight in templates
const vHighlight = {
  mounted: (el) => {
    el.classList.add('is-highlight')
  }
}
</script>

<template>
  <p v-highlight>This sentence is important!</p>
</template>
```

</div>

<div class="options-api">

```js
const highlight = {
  mounted: (el) => el.classList.add('is-highlight')
}

export default {
  directives: {
    // enables v-highlight in template
    highlight
  }
}
```

```vue-html
<p v-highlight>This sentence is important!</p>
```

</div>

<div class="demo">
  <p v-highlight>This sentence is important!</p>
</div>

<div class="composition-api">

In `<script setup>`, any camelCase variable that starts with the `v` prefix can be used as a custom directive. In the example above, `vHighlight` can be used in the template as `v-highlight`.

If you are not using `<script setup>`, custom directives can be registered using the `directives` option:

```js
export default {
  setup() {
    /*...*/
  },
  directives: {
    // enables v-highlight in template
    highlight: {
      /* ... */
    }
  }
}
```

</div>

<div class="options-api">

Similar to components, custom directives must be registered so that they can be used in templates. In the example above, we are using local registration via the `directives` option.

</div>

It is also common to globally register custom directives at the app level:

```js
const app = createApp({})

// make v-highlight usable in all components
app.directive('highlight', {
  /* ... */
})
```

## When to use custom directives {#when-to-use}

Custom directives should only be used when the desired functionality can only be achieved via direct DOM manipulation.

A common example of this is a `v-focus` custom directive that brings an element into focus.

<div class="composition-api">

```vue
<script setup>
// enables v-focus in templates
const vFocus = {
  mounted: (el) => el.focus()
}
</script>

<template>
  <input v-focus />
</template>
```

</div>

<div class="options-api">

```js
const focus = {
  mounted: (el) => el.focus()
}

export default {
  directives: {
    // enables v-focus in template
    focus
  }
}
```

```vue-html
<input v-focus />
```

</div>

এই নির্দেশিকাটি `autofocus` অ্যাট্রিবিউটের চেয়ে বেশি কার্যকর কারণ এটি শুধু পৃষ্ঠা লোডের ক্ষেত্রেই কাজ করে না - এটিও কাজ করে যখন উপাদানটি গতিশীলভাবে Vue দ্বারা সন্নিবেশ করা হয়!

বিল্ট-ইন নির্দেশাবলী যেমন `v-bind` এর সাথে ঘোষণামূলক টেমপ্লেটিং সুপারিশ করা হয় যখন সম্ভব হয় কারণ সেগুলি আরও দক্ষ এবং সার্ভার-রেন্ডারিং বন্ধুত্বপূর্ণ।

## Directive Hooks {#directive-hooks}

একটি নির্দেশমূলক সংজ্ঞা অবজেক্ট বিভিন্ন হুক ফাংশন প্রদান করতে পারে (সমস্ত ঐচ্ছিক):

```js
const myDirective = {
  // called before bound element's attributes
  // or event listeners are applied
  created(el, binding, vnode) {
    // see below for details on arguments
  },
  // called right before the element is inserted into the DOM.
  beforeMount(el, binding, vnode) {},
  // called when the bound element's parent component
  // and all its children are mounted.
  mounted(el, binding, vnode) {},
  // called before the parent component is updated
  beforeUpdate(el, binding, vnode, prevVnode) {},
  // called after the parent component and
  // all of its children have updated
  updated(el, binding, vnode, prevVnode) {},
  // called before the parent component is unmounted
  beforeUnmount(el, binding, vnode) {},
  // called when the parent component is unmounted
  unmounted(el, binding, vnode) {}
}
```

### Hook Arguments {#hook-arguments}

নির্দেশিক হুক এই আর্গুমেন্ট পাস করা হয়:

- `el`: নির্দেশিকাটি যে কম্পোনেন্টটির সাথে আবদ্ধ। এটি সরাসরি DOM ম্যানিপুলেট করতে ব্যবহার করা যেতে পারে।

- `binding`: নিম্নলিখিত বৈশিষ্ট্য ধারণকারী একটি অবজেক্ট।

  - `value`: নির্দেশিকায় পাঠানো মান। যেমন `v-my-directive="1 + 1"`-এ, মান হবে `2`।
  - `oldValue`: পূর্ববর্তী মান, শুধুমাত্র `beforeUpdate` এবং `updated`-এ উপলব্ধ। মান পরিবর্তিত হয়েছে কিনা তা উপলব্ধ।
  - `arg`: আর্গুমেন্ট নির্দেশে চলে গেছে, যদি থাকে। যেমন `v-my-directive:foo`-এ, arg হবে `"foo"`।
  - `modifiers`: কোনো অবজেক্ট যাতে মডিফায়ার থাকে, যদি থাকে। যেমন `v-my-directive.foo.bar`-এ, মডিফায়ার অবজেক্ট হবে `{ foo: true, bar: true }`।
  - `instance`: কম্পোনেন্টের উদাহরণ যেখানে নির্দেশিকা ব্যবহার করা হয়।
  - `dir`: নির্দেশক সংজ্ঞা অবজেক্ট।

- `vnode`: আন্ডারল্যায়েরিং VNode আবদ্ধ কম্পোনেন্টস প্রতিনিধিত্ব করে।
- `prevVnode`: VNode পূর্ববর্তী রেন্ডার থেকে আবদ্ধ কম্পোনেন্টস প্রতিনিধিত্ব করে। শুধুমাত্র `beforeUpdate` এবং `updated` হুকগুলিতে উপলব্ধ।

একটি উদাহরণ হিসাবে, নিম্নলিখিত নির্দেশিক ব্যবহার বিবেচনা করুন:

```vue-html
<div v-example:foo.bar="baz">
```

`binding` আর্গুমেন্ট এর আকারে একটি অবজেক্ট হবে:

```js
{
  arg: 'foo',
  modifiers: { bar: true },
  value: /* value of `baz` */,
  oldValue: /* value of `baz` from previous update */
}
```

অন্তর্নির্মিত নির্দেশের অনুরূপ, কাস্টম নির্দেশিক আর্গুমেন্টগুলি গতিশীল হতে পারে। উদাহরণ স্বরূপ:

```vue-html
<div v-example:[arg]="value"></div>
```

এখানে নির্দেশমূলক আর্গুমেন্ট আমাদের কম্পোনেন্ট স্টেটে `arg` কম্পিউটেড প্রপার্টির উপর ভিত্তি করে প্রতিক্রিয়াশীলভাবে আপডেট করা হবে।

:::tip Note
'el' ছাড়াও, আপনার এই আর্গুমেন্টগুলিকে শুধুমাত্র পঠনযোগ্য হিসাবে বিবেচনা করা উচিত এবং সেগুলিকে কখনও সংশোধন করবেন না৷ আপনি যদি হুক জুড়ে তথ্য ভাগ করতে চান তবে কম্পোনেন্টের [dataset](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset) এর মাধ্যমে এটি করার পরামর্শ দেওয়া হয়।
:::

## Function Shorthand {#function-shorthand}

একটি কাস্টম নির্দেশের জন্য `mounted` এবং `updated`-এর জন্য একই আচরণ করা সাধারণ, অন্য হুকের প্রয়োজন নেই। এই ধরনের ক্ষেত্রে আমরা নির্দেশকে একটি ফাংশন হিসাবে সংজ্ঞায়িত করতে পারি:

```vue-html
<div v-color="color"></div>
```

```js
app.directive('color', (el, binding) => {
  // this will be called for both `mounted` and `updated`
  el.style.color = binding.value
})
```

## Object Literals {#object-literals}

আপনার নির্দেশের একাধিক মান প্রয়োজন হলে, আপনি জাভাস্ক্রিপ্ট অবজেক্ট আক্ষরিকভাবেও পাস করতে পারেন। মনে রাখবেন, নির্দেশাবলী যেকোনো বৈধ জাভাস্ক্রিপ্ট এক্সপ্রেশন নিতে পারে।

```vue-html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```js
app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "hello!"
})
```

## Usage on Components {#usage-on-components}

:::warning প্রস্তাবিত নয়
উপাদানগুলিতে কাস্টম নির্দেশাবলী ব্যবহার করার পরামর্শ দেওয়া হয় না। একটি উপাদানের একাধিক রুট নোড থাকলে অপ্রত্যাশিত আচরণ ঘটতে পারে।
:::

উপাদানগুলিতে ব্যবহার করা হলে, কাস্টম নির্দেশাবলী সর্বদা একটি উপাদানের রুট নোডে প্রযোজ্য হবে, [Fallthrough Attributes](/guide/components/attrs) এর মতো।

```vue-html
<MyComponent v-demo="test" />
```

```vue-html
<!-- template of MyComponent -->

<div> <!-- v-demo directive will be applied here -->
  <span>My component content</span>
</div>
```

মনে রাখবেন যে উপাদানগুলির সম্ভাব্য একাধিক রুট নোড থাকতে পারে। মাল্টি-রুট উপাদানে প্রয়োগ করা হলে, একটি নির্দেশ উপেক্ষা করা হবে এবং একটি সতর্কতা নিক্ষেপ করা হবে। অ্যাট্রিবিউটের বিপরীতে, নির্দেশাবলী `v-bind="$attrs"` সহ একটি ভিন্ন উপাদানে পাঠানো যাবে না।
