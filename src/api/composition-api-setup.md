# Composition API: setup() {#composition-api-setup}

## Basic Usage {#basic-usage}

`setup()` হুক নিম্নলিখিত ক্ষেত্রে কম্পোনেন্টগুলোতে কম্পোজিশন API ব্যবহারের জন্য এন্ট্রি পয়েন্ট হিসাবে কাজ করে:

1. বিল্ড স্টেপ ছাড়া কম্পোজিশন API ব্যবহার করা;
2. একটি বিকল্প API কম্পোনেন্টে কম্পোজিশন-এপিআই-ভিত্তিক কোডের সাথে একত্রিত  করা।

:::info নোট
আপনি যদি সিঙ্গেল-ফাইল কম্পোনেন্টগুলির সাথে কম্পোজিশন API ব্যবহার করেন, তবে আরও সংক্ষিপ্ত এবং এরগনোমিক সিনট্যাক্সের জন্য, [`<script setup>`](/api/sfc-script-setup) দৃঢ়ভাবে রিকোমেন্ডেড করা হয়।
:::

আমরা [Reactivity APIs](./reactivity-core) ব্যবহার করে রিয়েক্টিভ স্টেট  ডিক্লেয়ার করতে পারি এবং `setup()` থেকে একটি অবজেক্ট রিটার্ন দিয়ে টেমপ্লেটে তাদের প্রকাশ করতে পারি। রিটার্ন অবজেক্টের বৈশিষ্ট্যগুলিও কম্পোনেন্ট ইনস্ট্যান্সে সহজলভ্য করা হবে (যদি অন্য অপশনগুলো ব্যবহার করা হয়):

```vue
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    // টেমপ্লেট এবং অনান্য options API hooks-কে প্রকাশ করা
    return {
      count
    }
  },

  mounted() {
    console.log(this.count) // 0
  }
}
</script>

<template>
  <button @click="count++">{{ count }}</button>
</template>
```

[refs](/api/reactivity-core#ref) `setup` থেকে রিটার্ন করা হয় [automatically shallow unwrapped](/guide/essentials/reactivity-fundamentals#deep-reactivity) যখন টেমপ্লেটে অ্যাক্সেস করা হয় তাই আপনাকে `.value` ব্যবহার করার প্রয়োজন নেই তাদের অ্যাক্সেস করার সময় । `this`-এ অ্যাক্সেস করার সময় সেগুলিও একইভাবে unwrapperd হয়।

`setup()`-এর নিজেই কম্পোনেন্ট ইনস্ট্যান্সে অ্যাক্সেস নেই - `this` এর value `setup()`-এর ভিতরে `undefined` থাকবে। আপনি অপশন এপিআই থেকে কম্পোজিশন-এপিআই-উন্মুক্ত মানগুলি অ্যাক্সেস করতে পারেন, কিন্তু অন্য উপায়ে নয়।

`setup()` একটি অবজেক্টকে  _synchronously_ রিটার্ন দিতে হবে। একমাত্র ক্ষেত্রে যখন `async setup()` ব্যবহার করা যেতে পারে যখন কম্পোনেন্টটি একটি [Suspense](../guide/built-ins/suspense) কম্পোনেন্টের উত্তরাধিকারী হবে।

## Accessing Props {#accessing-props}

`setup` ফাংশনের প্রথম আর্গুমেন্ট হল `props` আর্গুমেন্ট। আপনি যেমন একটি আদর্শ কম্পোনেন্ট আশা করবেন, একটি `setup` ফাংশনের ভিতরে `props` রিয়েক্টিভ এবং নতুন প্রপস পাস করা হলে তা আপডেট করা হবে।

```js
export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```

মনে রাখবেন যে আপনি যদি `props` অবজেক্টটিকে ডিস্ট্রাকচার করেন, ডিস্ট্রাকচার করা ভেরিয়েবলগুলি রিয়েক্টিভিটি হারাবে। তাই সবসময় `props.xxx` আকারে প্রপস অ্যাক্সেস করার পরামর্শ দেওয়া হয়।

আপনার যদি সত্যিই প্রপসগুলিকে ডিস্ট্রাকচার করতে হয়, বা রিয়েক্টিভিটি বজায় রাখার সময় একটি বহিরাগত ফাংশনে একটি প্রপ pass করতে হয়, আপনি [toRefs()](./reactivity-utilities#torefs) এবং [toRef()](/api/reactivity-utilities#toref) এর সাথে এটি করতে পারেন। ইউটিলিটি API:

```js
import { toRefs, toRef } from 'vue'

export default {
  setup(props) {
    // `props` কে রেফের একটি অবজেক্টে পরিণত করুন, তারপর ডিস্ট্রাকচার করুন
    const { title } = toRefs(props)
    // `title` হচ্ছে একটি ref যা `props.title` কে ট্র্যাক করে। 
    console.log(title.value)

    // অথবা, `props`-কে একটি সিঙ্গেল প্রপার্টিতে পরিণত করুন
    const title = toRef(props, 'title')
  }
}
```

## Setup Context {#setup-context}

`setup` ফাংশনে pass করা দ্বিতীয় আর্গুমেন্ট হল একটি **Setup Context** অবজেক্ট। প্রসঙ্গ অবজেক্টটি অন্যান্য ভ্যালু-গুলোকে প্রকাশ করে যা `setup` ভিতরে দরকারী হতে পারে:

```js
export default {
  setup(props, context) {
    // Attributes (Non-reactive object, equivalent to $attrs)
    console.log(context.attrs)

    // Slots (Non-reactive object, equivalent to $slots)
    console.log(context.slots)

    // Emit events (Function, equivalent to $emit)
    console.log(context.emit)

    // Expose public properties (Function)
    console.log(context.expose)
  }
}
```

কন্টেক্সট অবজেক্টটি রিয়েক্টিভ নয় এবং নিরাপদে ডিস্ট্রাকচার করা যেতে পারে:

```js
export default {
  setup(props, { attrs, slots, emit, expose }) {
    ...
  }
}
```

`attrs` এবং `slots` হল স্টেটফুল অবজেক্ট যেগুলো সবসময় আপডেট করা হয় যখন কম্পোনেন্ট নিজেই আপডেট করা হয়। এর অর্থ হল আপনার সেগুলিকে ডিস্ট্রাকচার করা এড়ানো উচিত এবং সর্বদা `attrs.x` বা `slots.x` হিসাবে বৈশিষ্ট্য উল্লেখ করা উচিত। এছাড়াও মনে রাখবেন, `প্রপস` এর বিপরীতে, `attrs` এবং `slots`-এর বৈশিষ্ট্য **not** রিয়েক্টিভ। আপনি যদি `attrs` বা `slots`-এ পরিবর্তনের উপর ভিত্তি করে পার্শ্বপ্রতিক্রিয়া প্রয়োগ করতে চান, তাহলে আপনার তা `onBeforeUpdate` লাইফসাইকেল হুকের মধ্যে করা উচিত।

### Exposing Public Properties {#exposing-public-properties}

`expose` is a function that can be used to explicitly limit the properties exposed when the component instance is accessed by a parent component via [template refs](/guide/essentials/template-refs#ref-on-component):

```js{5,10}
export default {
  setup(props, { expose }) {
    // make the instance "closed" -
    // i.e. do not expose anything to the parent
    expose()

    const publicCount = ref(0)
    const privateCount = ref(0)
    // selectively expose local state
    expose({ count: publicCount })
  }
}
```

## Usage with Render Functions {#usage-with-render-functions}

`setup` can also return a [render function](/guide/extras/render-function) which can directly make use of the reactive state declared in the same scope:

```js{6}
import { h, ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return () => h('div', count.value)
  }
}
```

Returning a render function prevents us from returning anything else. Internally that shouldn't be a problem, but it can be problematic if we want to expose methods of this component to the parent component via template refs.

We can solve this problem by calling [`expose()`](#exposing-public-properties):

```js{8-10}
import { h, ref } from 'vue'

export default {
  setup(props, { expose }) {
    const count = ref(0)
    const increment = () => ++count.value

    expose({
      increment
    })

    return () => h('div', count.value)
  }
}
```

The `increment` method would then be available in the parent component via a template ref.
