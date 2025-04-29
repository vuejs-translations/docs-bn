---
outline: deep
---

# Render Functions & JSX {#render-functions-jsx}

Vue বেশিরভাগ ক্ষেত্রে অ্যাপ্লিকেশন তৈরি করতে টেমপ্লেট ব্যবহার করার পরামর্শ দেয়। যাইহোক, এমন পরিস্থিতি রয়েছে যেখানে আমাদের জাভাস্ক্রিপ্টের সম্পূর্ণ প্রোগ্রাম্যাটিক শক্তি প্রয়োজন। সেখানেই আমরা **রেন্ডার ফাংশন** ব্যবহার করতে পারি।

আপনি যদি ভার্চুয়াল DOM এবং রেন্ডার ফাংশনের ধারণায় নতুন হন, তাহলে প্রথমে [রেন্ডারিং মেকানিজম](/guide/extras/rendering-mechanism) অধ্যায়টি পড়তে ভুলবেন না।

## Basic Usage {#basic-usage}

### Creating Vnodes {#creating-vnodes}

Vue vnodes তৈরি করার জন্য একটি `h()` ফাংশন প্রদান করে:

```js
import { h } from 'vue'

const vnode = h(
  'div', // type
  { id: 'foo', class: 'bar' }, // props
  [
    /* children */
  ]
)
```

`h()` **hyperscript**-এর জন্য সংক্ষিপ্ত - যার মানে "জাভাস্ক্রিপ্ট যা HTML (হাইপারটেক্সট মার্কআপ ভাষা) তৈরি করে"। এই নামটি অনেক ভার্চুয়াল DOM বাস্তবায়ন দ্বারা শেয়ার করা কনভেনশন থেকে উত্তরাধিকারসূত্রে প্রাপ্ত। একটি আরও বর্ণনামূলক নাম হতে পারে `createVNode()`, কিন্তু একটি ছোট নাম সাহায্য করে যখন আপনাকে রেন্ডার ফাংশনে অনেকবার এই ফাংশনটি কল করতে হয়।

`h()` ফাংশনটি খুব নমনীয় হওয়ার জন্য ডিজাইন করা হয়েছে:

```js
// all arguments except the type are optional
h('div')
h('div', { id: 'foo' })

// both attributes and properties can be used in props
// Vue automatically picks the right way to assign it
h('div', { class: 'bar', innerHTML: 'hello' })

// props modifiers such as `.prop` and `.attr` can be added
// with `.` and `^` prefixes respectively
h('div', { '.name': 'some-name', '^width': '100' })

// class and style have the same object / array
// value support that they have in templates
h('div', { class: [foo, { bar }], style: { color: 'red' } })

// event listeners should be passed as onXxx
h('div', { onClick: () => {} })

// children can be a string
h('div', { id: 'foo' }, 'hello')

// props can be omitted when there are no props
h('div', 'hello')
h('div', [h('span', 'hello')])

// children array can contain mixed vnodes and strings
h('div', ['hello', h('span', 'hello')])
```

ফলস্বরূপ vnode নিম্নলিখিত আকৃতি আছে:

```js
const vnode = h('div', { id: 'foo' }, [])

vnode.type // 'div'
vnode.props // { id: 'foo' }
vnode.children // []
vnode.key // null
```

:::warning বিঃদ্রঃ
সম্পূর্ণ `VNode` ইন্টারফেসে অন্যান্য অনেক অভ্যন্তরীণ বৈশিষ্ট্য রয়েছে, তবে এখানে তালিকাভুক্ত বৈশিষ্ট্যগুলি ব্যতীত অন্য কোনো বৈশিষ্ট্যের উপর নির্ভর না করার জন্য এটি দৃঢ়ভাবে সুপারিশ করা হয়। অভ্যন্তরীণ বৈশিষ্ট্যগুলি পরিবর্তন করা হলে এটি অনিচ্ছাকৃত ভাঙ্গন এড়ায়।
:::

### Declaring Render Functions {#declaring-render-functions}

<div class="composition-api">

Composition API এর সাথে টেমপ্লেট ব্যবহার করার সময়, `setup()` হুকের রিটার্ন মান টেমপ্লেটে ডেটা প্রকাশ করতে ব্যবহৃত হয়। রেন্ডার ফাংশন ব্যবহার করার সময়, যাইহোক, আমরা এর পরিবর্তে সরাসরি রেন্ডার ফাংশন ফেরত দিতে পারি:

```js
import { ref, h } from 'vue'

export default {
  props: {
    /* ... */
  },
  setup(props) {
    const count = ref(1)

    // return the render function
    return () => h('div', props.msg + count.value)
  }
}
```

রেন্ডার ফাংশনটি `সেটআপ()` এর ভিতরে ঘোষণা করা হয়েছে তাই এটি স্বাভাবিকভাবেই প্রপস এবং একই সুযোগে ঘোষিত যেকোনো প্রতিক্রিয়াশীল অবস্থার অ্যাক্সেস রয়েছে।

একটি একক ভনোড ফেরত দেওয়ার পাশাপাশি, আপনি স্ট্রিং বা অ্যারেও ফেরত দিতে পারেন:

```js
export default {
  setup() {
    return () => 'hello world!'
  }
}
```

```js
import { h } from 'vue'

export default {
  setup() {
    // use an array to return multiple root nodes
    return () => [
      h('div'),
      h('div'),
      h('div')
    ]
  }
}
```

:::tip
সরাসরি মান ফেরত দেওয়ার পরিবর্তে একটি ফাংশন ফেরত নিশ্চিত করুন! `setup()` ফাংশন প্রতি কম্পোনেন্টে শুধুমাত্র একবার কল করা হয়, যখন রিটার্ন করা রেন্ডার ফাংশন একাধিকবার কল করা হবে।
:::

</div>
<div class="options-api">

আমরা `render` বিকল্প ব্যবহার করে রেন্ডার ফাংশন ঘোষণা করতে পারি:

```js
import { h } from 'vue'

export default {
  data() {
    return {
      msg: 'hello'
    }
  },
  render() {
    return h('div', this.msg)
  }
}
```

`render()` ফাংশনের `this` এর মাধ্যমে কম্পোনেন্ট ইনস্ট্যান্সে অ্যাক্সেস আছে।

একটি একক ভনোড ফেরত দেওয়ার পাশাপাশি, আপনি স্ট্রিং বা অ্যারেও ফেরত দিতে পারেন:

```js
export default {
  render() {
    return 'hello world!'
  }
}
```

```js
import { h } from 'vue'

export default {
  render() {
    // use an array to return multiple root nodes
    return [
      h('div'),
      h('div'),
      h('div')
    ]
  }
}
```

</div>

যদি একটি রেন্ডার ফাংশন কম্পোনেন্টের কোনো ইনস্ট্যান্স স্টেটের প্রয়োজন না হয়, তবে সেগুলিকে সংক্ষিপ্ততার জন্য একটি ফাংশন হিসাবে সরাসরি ঘোষণা করা যেতে পারে:

```js
function Hello() {
  return 'hello world!'
}
```

এটা ঠিক, এটি একটি বৈধ Vue কম্পোনেন্ট! এই সিনট্যাক্স সম্পর্কে আরও বিস্তারিত জানার জন্য [কার্যমূলক কম্পোনেন্ট](#functional-components) দেখুন।

### Vnodes Must Be Unique {#vnodes-must-be-unique}

 কম্পোনেন্ট ট্রির সমস্ত ভনোড অবশ্যই অনন্য হতে হবে। তার মানে নিম্নলিখিত রেন্ডার ফাংশনটি অবৈধ:

```js
function render() {
  const p = h('p', 'hi')
  return h('div', [
    // Yikes - duplicate vnodes!
    p,
    p
  ])
}
```

আপনি যদি সত্যিই একই কম্পোনেন্ট/ কম্পোনেন্টকে অনেকবার নকল করতে চান তবে আপনি ফ্যাক্টরি ফাংশন দিয়ে তা করতে পারেন। উদাহরণস্বরূপ, নিম্নলিখিত রেন্ডার ফাংশনটি 20টি অভিন্ন অনুচ্ছেদ রেন্ডার করার একটি সম্পূর্ণ বৈধ উপায়:

```js
function render() {
  return h(
    'div',
    Array.from({ length: 20 }).map(() => {
      return h('p', 'hi')
    })
  )
}
```

## JSX / TSX {#jsx-tsx}

[JSX](https://facebook.github.io/jsx/) হল জাভাস্ক্রিপ্টের একটি XML-এর মতো এক্সটেনশন যা আমাদেরকে এইরকম কোড লিখতে দেয়:

```jsx
const vnode = <div>hello</div>
```

JSX এক্সপ্রেশনের ভিতরে, গতিশীল মানগুলি এম্বেড করতে কোঁকড়া ধনুর্বন্ধনী ব্যবহার করুন:

```jsx
const vnode = <div id={dynamicId}>hello, {userName}</div>
```

`create-vue` এবং Vue CLI উভয়েরই প্রি-কনফিগার করা JSX সমর্থন সহ স্ক্যাফোল্ডিং প্রকল্পের বিকল্প রয়েছে। আপনি যদি JSX ম্যানুয়ালি কনফিগার করেন, তাহলে বিস্তারিত জানার জন্য অনুগ্রহ করে [`@vue/babel-plugin-jsx`](https://github.com/vuejs/jsx-next) এর ডকুমেন্টেশন দেখুন।

যদিও প্রথম রিঅ্যাক্ট দ্বারা প্রবর্তিত হয়, JSX এর আসলে কোন সংজ্ঞায়িত রানটাইম শব্দার্থবিদ্যা নেই এবং বিভিন্ন আউটপুটে কম্পাইল করা যেতে পারে। আপনি যদি আগে JSX-এর সাথে কাজ করে থাকেন, তাহলে মনে রাখবেন **Vue JSX transform React-এর JSX transform** থেকে আলাদা, তাই আপনি Vue অ্যাপ্লিকেশনে React-এর JSX রূপান্তর ব্যবহার করতে পারবেন না। প্রতিক্রিয়া JSX থেকে কিছু উল্লেখযোগ্য পার্থক্য অন্তর্ভুক্ত:

- আপনি প্রপ হিসাবে HTML বৈশিষ্ট্য যেমন `class` এবং `for` ব্যবহার করতে পারেন - `className` বা `htmlFor` ব্যবহার করার দরকার নেই।
- বাচ্চাদের কম্পোনেন্টে (যেমন স্লট) পাস করা [works differently](#passing-slots)।

Vue এর টাইপ সংজ্ঞা টিএসএক্স ব্যবহারের জন্য টাইপ ইনফারেন্সও প্রদান করে। TSX ব্যবহার করার সময়, `tsconfig.json`-এ `"jsx": "preserve"` উল্লেখ করতে ভুলবেন না যাতে TypeScript Vue JSX রূপান্তর প্রক্রিয়ার জন্য JSX সিনট্যাক্স অক্ষত রাখে।

### JSX Type Inference {#jsx-type-inference}

রূপান্তরের অনুরূপ, Vue-এর JSX-এরও বিভিন্ন ধরনের সংজ্ঞা প্রয়োজন।

Vue 3.4 থেকে শুরু করে, Vue আর পরোক্ষভাবে বিশ্বব্যাপী `JSX` নামস্থান নিবন্ধন করে না। Vue-এর JSX টাইপ সংজ্ঞা ব্যবহার করার জন্য TypeScript-কে নির্দেশ দিতে, আপনার `tsconfig.json`-এ নিম্নলিখিতগুলি অন্তর্ভুক্ত করতে ভুলবেন না:

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "vue"
    // ...
  }
}
```

আপনি ফাইলের শীর্ষে একটি `/* @jsxImportSource vue */` মন্তব্য যোগ করে ফাইল প্রতি অপ্ট-ইন করতে পারেন।

যদি এমন কোড থাকে যা গ্লোবাল `JSX` নামস্থানের উপস্থিতির উপর নির্ভর করে, তাহলে আপনি আপনার প্রজেক্টে স্পষ্টভাবে `vue/jsx` আমদানি বা উল্লেখ করে সঠিক প্রাক-3.4 বৈশ্বিক আচরণ বজায় রাখতে পারেন, যা বিশ্বব্যাপী `JSX` নামস্থান নিবন্ধন করে।

## Render Function Recipes {#render-function-recipes}

নীচে আমরা টেমপ্লেট বৈশিষ্ট্যগুলিকে তাদের সমতুল্য রেন্ডার ফাংশন / JSX হিসাবে প্রয়োগ করার জন্য কিছু সাধারণ রেসিপি সরবরাহ করব।

### `v-if` {#v-if}

Template:

```vue-html
<div>
  <div v-if="ok">yes</div>
  <span v-else>no</span>
</div>
```

সমতুল্য রেন্ডার ফাংশন / JSX:

<div class="composition-api">

```js
h('div', [ok.value ? h('div', 'yes') : h('span', 'no')])
```

```jsx
<div>{ok.value ? <div>yes</div> : <span>no</span>}</div>
```

</div>
<div class="options-api">

```js
h('div', [this.ok ? h('div', 'yes') : h('span', 'no')])
```

```jsx
<div>{this.ok ? <div>yes</div> : <span>no</span>}</div>
```

</div>

### `v-for` {#v-for}

Template:

```vue-html
<ul>
  <li v-for="{ id, text } in items" :key="id">
    {{ text }}
  </li>
</ul>
```

সমতুল্য রেন্ডার ফাংশন / JSX:

<div class="composition-api">

```js
h(
  'ul',
  // assuming `items` is a ref with array value
  items.value.map(({ id, text }) => {
    return h('li', { key: id }, text)
  })
)
```

```jsx
<ul>
  {items.value.map(({ id, text }) => {
    return <li key={id}>{text}</li>
  })}
</ul>
```

</div>
<div class="options-api">

```js
h(
  'ul',
  this.items.map(({ id, text }) => {
    return h('li', { key: id }, text)
  })
)
```

```jsx
<ul>
  {this.items.map(({ id, text }) => {
    return <li key={id}>{text}</li>
  })}
</ul>
```

</div>

### `v-on` {#v-on}

একটি বড় হাতের অক্ষর দ্বারা `on` দিয়ে শুরু হওয়া নামগুলিকে ইভেন্ট শ্রোতা হিসাবে বিবেচনা করা হয়। উদাহরণস্বরূপ, টেমপ্লেটে `@click` এর সমতুল্য `onClick`।

```js
h(
  'button',
  {
    onClick(event) {
      /* ... */
    }
  },
  'Click Me'
)
```

```jsx
<button
  onClick={(event) => {
    /* ... */
  }}
>
  Click Me
</button>
```

#### Event Modifiers {#event-modifiers}

`.passive`, `.capture` এবং `.once` ইভেন্ট মডিফায়ারের জন্য, ক্যামেলকেস ব্যবহার করে ইভেন্টের নামের পরে সংযুক্ত করা যেতে পারে।

উদাহরণ স্বরূপ:

```js
h('input', {
  onClickCapture() {
    /* listener in capture mode */
  },
  onKeyupOnce() {
    /* triggers only once */
  },
  onMouseoverOnceCapture() {
    /* once + capture */
  }
})
```

```jsx
<input
  onClickCapture={() => {}}
  onKeyupOnce={() => {}}
  onMouseoverOnceCapture={() => {}}
/>
```

অন্যান্য ইভেন্ট এবং কী মডিফায়ারের জন্য, [`withModifiers`](/api/render-function#withmodifiers) সাহায্যকারী ব্যবহার করা যেতে পারে:

```js
import { withModifiers } from 'vue'

h('div', {
  onClick: withModifiers(() => {}, ['self'])
})
```

```jsx
<div onClick={withModifiers(() => {}, ['self'])} />
```

### Components {#components}

একটি কম্পোনেন্টের জন্য একটি vnode তৈরি করতে, `h()` এ পাস করা প্রথম আর্গুমেন্টটি কম্পোনেন্ট সংজ্ঞা হওয়া উচিত। এর মানে রেন্ডার ফাংশন ব্যবহার করার সময়, কম্পোনেন্টগুলি নিবন্ধন করা অপ্রয়োজনীয় - আপনি সরাসরি আমদানি করা কম্পোনেন্টগুলি ব্যবহার করতে পারেন:

```js
import Foo from './Foo.vue'
import Bar from './Bar.jsx'

function render() {
  return h('div', [h(Foo), h(Bar)])
}
```

```jsx
function render() {
  return (
    <div>
      <Foo />
      <Bar />
    </div>
  )
}
```

আমরা দেখতে পাচ্ছি, `h` যেকোনো ফাইল ফরম্যাট থেকে আমদানি করা কম্পোনেন্টের সাথে কাজ করতে পারে যতক্ষণ না এটি একটি বৈধ Vue কম্পোনেন্ট।

গতিশীল কম্পোনেন্টগুলি রেন্ডার ফাংশনগুলির সাথে সহজবোধ্য:

```js
import Foo from './Foo.vue'
import Bar from './Bar.jsx'

function render() {
  return ok.value ? h(Foo) : h(Bar)
}
```

```jsx
function render() {
  return ok.value ? <Foo /> : <Bar />
}
```

যদি একটি কম্পোনেন্ট নাম দ্বারা নিবন্ধিত হয় এবং সরাসরি আমদানি করা না যায় (উদাহরণস্বরূপ, একটি লাইব্রেরি দ্বারা বিশ্বব্যাপী নিবন্ধিত), এটি [`resolveComponent()`](/api/render-function#resolvecomponent) সাহায্যকারী ব্যবহার করে প্রোগ্রাম্যাটিকভাবে সমাধান করা যেতে পারে।

### Rendering Slots {#rendering-slots}

<div class="composition-api">

রেন্ডার ফাংশনে, স্লটগুলি `setup()` প্রসঙ্গ থেকে অ্যাক্সেস করা যেতে পারে। `slots` অবজেক্টের প্রতিটি স্লট একটি **ফাংশন যা ভনোডের একটি অ্যারে প্রদান করে**:

```js
export default {
  props: ['message'],
  setup(props, { slots }) {
    return () => [
      // default slot:
      // <div><slot /></div>
      h('div', slots.default()),

      // named slot:
      // <div><slot name="footer" :text="message" /></div>
      h(
        'div',
        slots.footer({
          text: props.message
        })
      )
    ]
  }
}
```

JSX সমতুল্য:

```jsx
// default
<div>{slots.default()}</div>

// named
<div>{slots.footer({ text: props.message })}</div>
```

</div>
<div class="options-api">

রেন্ডার ফাংশনে, স্লটগুলি [`this.$slots`](/api/component-instance#slots) থেকে অ্যাক্সেস করা যেতে পারে:

```js
export default {
  props: ['message'],
  render() {
    return [
      // <div><slot /></div>
      h('div', this.$slots.default()),

      // <div><slot name="footer" :text="message" /></div>
      h(
        'div',
        this.$slots.footer({
          text: this.message
        })
      )
    ]
  }
}
```

JSX equivalent:

```jsx
// <div><slot /></div>
<div>{this.$slots.default()}</div>

// <div><slot name="footer" :text="message" /></div>
<div>{this.$slots.footer({ text: this.message })}</div>
```

</div>

### Passing Slots {#passing-slots}

Passing children to components কিছুটা ভিন্নভাবে কাজ করে। একটি অ্যারের পরিবর্তে, আমাদের হয় একটি স্লট ফাংশন বা স্লট ফাংশনের একটি অবজেক্ট পাস করতে হবে। স্লট ফাংশনগুলি একটি সাধারণ রেন্ডার ফাংশন যা ফেরত দিতে পারে তা ফিরিয়ে দিতে পারে - যা চাইল্ড কম্পোনেন্টে অ্যাক্সেস করার সময় সর্বদা ভনোডের অ্যারেতে স্বাভাবিক করা হবে।

```js
// single default slot
h(MyComponent, () => 'hello')

// named slots
// notice the `null` is required to avoid
// the slots object being treated as props
h(MyComponent, null, {
  default: () => 'default slot',
  foo: () => h('div', 'foo'),
  bar: () => [h('span', 'one'), h('span', 'two')]
})
```

JSX equivalent:

```jsx
// default
<MyComponent>{() => 'hello'}</MyComponent>

// named
<MyComponent>{{
  default: () => 'default slot',
  foo: () => <div>foo</div>,
  bar: () => [<span>one</span>, <span>two</span>]
}}</MyComponent>
```

ফাংশন হিসাবে স্লট পাস করা তাদের  চাইল্ড কম্পোনেন্ট দ্বারা অলসভাবে আমন্ত্রণ জানানোর অনুমতি দেয়। এটি পিতামাতার পরিবর্তে সন্তানের দ্বারা স্লটের নির্ভরতা ট্র্যাক করার দিকে পরিচালিত করে, যার ফলে আরও সঠিক এবং দক্ষ আপডেট হয়।

### Scoped Slots {#scoped-slots}

প্যারেন্ট কম্পোনেন্টে একটি স্কোপড স্লট রেন্ডার করতে, একটি স্লট চাইল্ডকে দেওয়া হয়। লক্ষ্য করুন কিভাবে স্লটে এখন একটি প্যারামিটার `text` আছে। স্লটটি চাইল্ড কম্পোনেন্টে কল করা হবে এবং চাইল্ড কম্পোনেন্ট থেকে ডেটা প্যারেন্ট কম্পোনেন্টে পাঠানো হবে।

```js
// parent component
export default {
  setup() {
    return () => h(MyComp, null, {
      default: ({ text }) => h('p', text)
    })
  }
}
```

`null` পাস করতে মনে রাখবেন যাতে স্লটগুলিকে প্রপস হিসাবে গণ্য করা হবে না।

```js
// child component
export default {
  setup(props, { slots }) {
    const text = ref('hi')
    return () => h('div', null, slots.default({ text: text.value }))
  }
}
```

JSX সমতুল্য:

```jsx
<MyComponent>{{
  default: ({ text }) => <p>{ text }</p>  
}}</MyComponent>
```

### Built-in Components {#built-in-components}

[বিল্ট-ইন কম্পোনেন্ট](/api/built-in-components) যেমন `<KeepAlive>`, `<Transition>`, `<TransitionGroup>`, `<Teleport>` এবং `<Suspense>` অবশ্যই আমদানি করতে হবে রেন্ডার ফাংশনে ব্যবহারের জন্য:

<div class="composition-api">

```js
import { h, KeepAlive, Teleport, Transition, TransitionGroup } from 'vue'

export default {
  setup () {
    return () => h(Transition, { mode: 'out-in' }, /* ... */)
  }
}
```

</div>
<div class="options-api">

```js
import { h, KeepAlive, Teleport, Transition, TransitionGroup } from 'vue'

export default {
  render () {
    return h(Transition, { mode: 'out-in' }, /* ... */)
  }
}
```

</div>

### `v-model` {#v-model}

টেমপ্লেট সংকলনের সময় `v-model` নির্দেশিকাকে `modelValue` এবং `onUpdate:modelValue` প্রপসে প্রসারিত করা হয়েছে—আমাদের নিজেদেরকে এই প্রপগুলি প্রদান করতে হবে:

<div class="composition-api">

```js
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    return () =>
      h(SomeComponent, {
        modelValue: props.modelValue,
        'onUpdate:modelValue': (value) => emit('update:modelValue', value)
      })
  }
}
```

</div>
<div class="options-api">

```js
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  render() {
    return h(SomeComponent, {
      modelValue: this.modelValue,
      'onUpdate:modelValue': (value) => this.$emit('update:modelValue', value)
    })
  }
}
```

</div>

### Custom Directives {#custom-directives}

কাস্টম নির্দেশাবলী [`withDirectives`](/api/render-function#withdirectives) ব্যবহার করে একটি vnode-এ প্রয়োগ করা যেতে পারে:

```js
import { h, withDirectives } from 'vue'

// a custom directive
const pin = {
  mounted() { /* ... */ },
  updated() { /* ... */ }
}

// <div v-pin:top.animate="200"></div>
const vnode = withDirectives(h('div'), [
  [pin, 200, 'top', { animate: true }]
])
```

যদি নির্দেশনাটি নাম দ্বারা নিবন্ধিত হয় এবং সরাসরি আমদানি করা না যায় তবে এটি [`resolveDirective`](/api/render-function#resolvedirective) সাহায্যকারী ব্যবহার করে সমাধান করা যেতে পারে।

### Template Refs {#template-refs}

<div class="composition-api">

কম্পোজিশন এপিআই ব্যবহার করার সময়, [`useTemplateRef()`](/api/composition-api-helpers#usetemplateref) <sup class="vt-badge" data-text="3.5+" /> টেমপ্লেট রেফ তৈরি করা হয় vnode-এ prop হিসেবে স্ট্রিং মান পাস করে:

```js
import { h, useTemplateRef } from 'vue'

export default {
  setup() {
    const divEl = useTemplateRef('my-div')

    // <div ref="my-div">
    return () => h('div', { ref: 'my-div' })
  }
}
```

<details>
<summary>Usage before 3.5</summary>

In versions before 3.5 where useTemplateRef() was not introduced, template refs are created by passing the ref() itself as a prop to the vnode:

```js
import { h, ref } from 'vue'

export default {
  setup() {
    const divEl = ref()

    // <div ref="divEl">
    return () => h('div', { ref: divEl })
  }
}
```
</details>
</div>
<div class="options-api">

অপশন এপিআই-এর সাহায্যে, vnode প্রপসে রেফ নামটি স্ট্রিং হিসাবে পাস করে টেমপ্লেট রেফ তৈরি করা হয়:

```js
export default {
  render() {
    // <div ref="divEl">
    return h('div', { ref: 'divEl' })
  }
}
```

</div>

## Functional Components {#functional-components}

কার্যকরী কম্পোনেন্ট হল কম্পোনেন্টের একটি বিকল্প রূপ যার নিজস্ব কোনো অবস্থা নেই। তারা বিশুদ্ধ ফাংশন মত কাজ করে: প্রপ ইন, vnodes আউট. এগুলি একটি কম্পোনেন্ট ইন্সট্যান্স তৈরি না করেই রেন্ডার করা হয় (অর্থাৎ `this` নয়), এবং স্বাভাবিক কম্পোনেন্ট লাইফসাইকেল হুক ছাড়াই।

একটি কার্যকরী কম্পোনেন্ট তৈরি করতে আমরা বিকল্প অবজেক্টর পরিবর্তে একটি প্লেইন ফাংশন ব্যবহার করি। ফাংশনটি কার্যকরভাবে কম্পোনেন্টটির জন্য `render` ফাংশন।

<div class="composition-api">

একটি কার্যকরী কম্পোনেন্টের স্বাক্ষর `setup()` হুকের মতোই:

```js
function MyComponent(props, { slots, emit, attrs }) {
  // ...
}
```

</div>
<div class="options-api">

যেহেতু একটি কার্যকরী কম্পোনেন্টের জন্য কোনো `this` রেফারেন্স নেই, তাই Vue প্রথম আর্গুমেন্ট হিসেবে `props`-এ পাস করবে:

```js
function MyComponent(props, context) {
  // ...
}
```

দ্বিতীয় যুক্তি, `context`, তিনটি বৈশিষ্ট্য ধারণ করে: `attrs`, `emit`, এবং `slots`। এগুলি দৃষ্টান্ত বৈশিষ্ট্যের সমতুল্য [`$attrs`](/api/component-instance#attrs), [`$emit`](/api/component-instance#emit), এবং [`$slots`](/api/component-instance#slots) যথাক্রমে।

</div>

কম্পোনেন্টগুলির জন্য সাধারণ কনফিগারেশন বিকল্পগুলির বেশিরভাগ কার্যকরী কম্পোনেন্টগুলির জন্য উপলব্ধ নয়। যাইহোক, বৈশিষ্ট্য হিসাবে যোগ করে [`props`](/api/options-state#props) এবং [`emits`](/api/options-state#emits) সংজ্ঞায়িত করা সম্ভব:

```js
MyComponent.props = ['value']
MyComponent.emits = ['click']
```

যদি `props` বিকল্পটি নির্দিষ্ট করা না থাকে, তাহলে ফাংশনে পাস করা `props` অবজেক্টে `attrs` এর মতোই সমস্ত বৈশিষ্ট্য থাকবে। `props` বিকল্পটি নির্দিষ্ট না করা পর্যন্ত প্রপ নামগুলি ক্যামেলকেসে স্বাভাবিক করা হবে না।

স্পষ্ট `props` সহ কার্যকরী কম্পোনেন্টগুলির জন্য, [অ্যাট্রিবিউট ফলথ্রু](/guide/components/attrs) সাধারণ কম্পোনেন্টগুলির মতোই কাজ করে। যাইহোক, কার্যকরী কম্পোনেন্টগুলির জন্য যেগুলি স্পষ্টভাবে তাদের `props` নির্দিষ্ট করে না, শুধুমাত্র `class`, `style` এবং `onXxx` ইভেন্ট শ্রোতারা ডিফল্টরূপে `attrs` থেকে উত্তরাধিকার সূত্রে প্রাপ্ত হবে। উভয় ক্ষেত্রেই, অ্যাট্রিবিউট উত্তরাধিকার নিষ্ক্রিয় করতে `inheritAttrs` কে `false` এ সেট করা যেতে পারে:

```js
MyComponent.inheritAttrs = false
```

কার্যকরী কম্পোনেন্টগুলি সাধারণ কম্পোনেন্টগুলির মতোই নিবন্ধিত এবং খাওয়া যেতে পারে। আপনি যদি একটি ফাংশনকে প্রথম আর্গুমেন্ট হিসেবে `h()`-এ পাস করেন, তাহলে এটি একটি কার্যকরী কম্পোনেন্ট হিসেবে বিবেচিত হবে।

### Typing Functional Components<sup class="vt-badge ts" /> {#typing-functional-components}

কার্যকরী কম্পোনেন্টগুলি নাম বা বেনামী কিনা তার উপর ভিত্তি করে টাইপ করা যেতে পারে। [Vue - অফিসিয়াল এক্সটেনশন](https://github.com/vuejs/language-tools) SFC টেমপ্লেটে ব্যবহার করার সময় সঠিকভাবে টাইপ করা কার্যকরী কম্পোনেন্ট টাইপ চেক করা সমর্থন করে।

**Named Functional Component**

```tsx
import type { SetupContext } from 'vue'
type FComponentProps = {
  message: string
}

type Events = {
  sendMessage(message: string): void
}

function FComponent(
  props: FComponentProps,
  context: SetupContext<Events>
) {
  return (
    <button onClick={() => context.emit('sendMessage', props.message)}>
        {props.message} {' '}
    </button>
  )
}

FComponent.props = {
  message: {
    type: String,
    required: true
  }
}

FComponent.emits = {
  sendMessage: (value: unknown) => typeof value === 'string'
}
```

**Anonymous Functional Component**

```tsx
import type { FunctionalComponent } from 'vue'

type FComponentProps = {
  message: string
}

type Events = {
  sendMessage(message: string): void
}

const FComponent: FunctionalComponent<FComponentProps, Events> = (
  props,
  context
) => {
  return (
    <button onClick={() => context.emit('sendMessage', props.message)}>
        {props.message} {' '}
    </button>
  )
}

FComponent.props = {
  message: {
    type: String,
    required: true
  }
}

FComponent.emits = {
  sendMessage: (value) => typeof value === 'string'
}
```
