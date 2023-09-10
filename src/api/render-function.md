# Render Function APIs {#render-function-apis}

## h() {#h}

ভার্চুয়াল DOM নোড (vnodes) তৈরি করে।

- **প্রকার**

  ```ts
  // full signature
  function h(
    type: string | Component,
    props?: object | null,
    children?: Children | Slot | Slots
  ): VNode

  // omitting props
  function h(type: string | Component, children?: Children | Slot): VNode

  type Children = string | number | boolean | VNode | null | Children[]

  type Slot = () => Children

  type Slots = { [name: string]: Slot }
  ```

  > টাইপগুলি রিডিবিলিটির জন্য সরলীকৃত।

- **বিস্তারিত**

  প্রথম আরগুমেন্টটি হয় একটি স্ট্রিং (নেটিভ ইলিমেন্টগুলির জন্য) বা একটি Vue কম্পোনেন্ট ডেফিনেইশন হতে পারে। দ্বিতীয় আরগুমেন্টটি হল প্রপস পাস করা, এবং তৃতীয় আর্গুমেন্ট হল চিল্ডেন।

  একটি কম্পোনেন্ট vnode তৈরি করার সময়, চিল্ডেনকে স্লট ফাংশন হিসাবে পাস করা আবশ্যক. একটি সিঙ্গেল স্লট ফাংশন পাস করা যেতে পারে যদি কম্পোনেন্টটি শুধুমাত্র ডিফল্ট স্লট এক্সপেক্ট করে। অন্যথায়, স্লটগুলিকে স্লট ফাংশনগুলির একটি অবজেক্ট হিসাবে পাস করতে হবে।

  সুবিধার জন্য, প্রপস আর্গুমেন্ট বাদ দেওয়া যেতে পারে যখন চিল্ডেন স্লট অবজেক্ট না হয়।

- **উদাহরন**

  নেটিভ ইলিমেন্টস তৈরি করা:

  ```js
  import { h } from 'vue'

  // all arguments except the type are optional
  h('div')
  h('div', { id: 'foo' })

  // both attributes and properties can be used in props
  // Vue automatically picks the right way to assign it
  h('div', { class: 'bar', innerHTML: 'hello' })

  // class and style have the same object / array
  // value support like in templates
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

  কম্পোনেন্টস তৈরি করা:

  ```js
  import Foo from './Foo.vue'

  // passing props
  h(Foo, {
    // equivalent of some-prop="hello"
    someProp: 'hello',
    // equivalent of @update="() => {}"
    onUpdate: () => {}
  })

  // passing single default slot
  h(Foo, () => 'default slot')

  // passing named slots
  // notice the `null` is required to avoid
  // slots object being treated as props
  h(MyComponent, null, {
    default: () => 'default slot',
    foo: () => h('div', 'foo'),
    bar: () => [h('span', 'one'), h('span', 'two')]
  })
  ```

- **আরো দেখুন** [Guide - Render Functions - Creating VNodes](/guide/extras/render-function#creating-vnodes)

## mergeProps() {#mergeprops}

নির্দিষ্ট প্রপসের জন্য বিশেষ হ্যান্ডলিং সহ একাধিক প্রপস অবজেক্ট মার্জ করুন।

- **প্রকার**

  ```ts
  function mergeProps(...args: object[]): object
  ```

- **বিস্তারিত**

  `mergeProps()` নিম্নলিখিত প্রপসের জন্য বিশেষ হ্যান্ডলিং সহ একাধিক প্রপস অবজেক্ট মার্জিং সাপোর্ট করে:

  - `class`
  - `style`
  - `onXxx` ইভেন্ট লিসেনার - একই নামের একাধিক লিসেনার একটি অ্যারেতে মার্জ হবে।

  আপনার যদি মার্জ বিহ্যেভিয়ার প্রয়োজন না হয় এবং সিম্পল ওভাররাইট চান, তাহলে এর পরিবর্তে নেটিভ অবজেক্ট স্প্রেড ব্যবহার করা যেতে পারে।

- **উদাহরন**

  ```js
  import { mergeProps } from 'vue'

  const one = {
    class: 'foo',
    onClick: handlerA
  }

  const two = {
    class: { bar: true },
    onClick: handlerB
  }

  const merged = mergeProps(one, two)
  /**
   {
     class: 'foo bar',
     onClick: [handlerA, handlerB]
   }
   */
  ```

## cloneVNode() {#clonevnode}

 একটি vnode তৈরি করা।

- **প্রকার**

  ```ts
  function cloneVNode(vnode: VNode, extraProps?: object): VNode
  ```

- **বিস্তারিত**

  একটি ক্লোন করা Vnode রিটার্ন দেয়, অপশনালি অরিজিনালটির সাথে মার্জ করার জন্য এক্সট্রা প্রপস সহ।

  একবার তৈরি হয়ে গেলে Vnode-গুলিকে অপরিবর্তনীয় হিসাবে বিবেচনা করা উচিত এবং আপনার বিদ্যমান vnode-এর প্রপসগুলিকে পরিবর্তন করা উচিত নয়। পরিবর্তে, এটি বিভিন্ন / অতিরিক্ত প্রপস দিয়ে ক্লোন করুন।

  vnode-এর বিশেষ ইন্টারন্যাল প্রোপার্টিস রয়েছে, তাই তাদের ক্লোন করা একটি অবজেক্টের স্পেডের মতো সিম্পল নয়। `cloneVNode()` বেশিরভাগ ইন্টারন্যাল লজিক পরিচালনা করে।

- **উদাহরন**

  ```js
  import { h, cloneVNode } from 'vue'

  const original = h('div')
  const cloned = cloneVNode(original, { id: 'foo' })
  ```

## isVNode() {#isvnode}

একটি ভ্যালু vnode কিনা পরীক্ষা করে.

- **প্রকার**

  ```ts
  function isVNode(value: unknown): boolean
  ```

## resolveComponent() {#resolvecomponent}

নেইম অনুসারে একটি রেজিস্ট্রাড কম্পোনেন্ট ম্যানুয়ালি সমাধানের জন্য।

- **প্রকার**

  ```ts
  function resolveComponent(name: string): Component | string
  ```

- **বিস্তারিত**

  **Note: আপনি যদি সরাসরি কম্পোনেন্ট ইম্পোর্ট করতে পারেন তবে আপনার এটির প্রয়োজন নেই।**

  `resolveComponent()` কে অবশ্যই <span class="composition-api"> হয় `setup()` অথবা </span> রেন্ডার ফাংশনের ভিতরে কল করতে হবে, যাতে কারেক্ট কম্পোনেন্টের কন্টেক্স থেকে সমাধান করা যায়।

  যদি কম্পোনেন্টটি পাওয়া না যায়, একটি রানটাইম সতর্কতা নির্গত হবে এবং নেইম স্ট্রিংটি রিটার্ন দেওয়া হবে।

- **উদাহরন**

  <div class="composition-api">

  ```js
  const { h, resolveComponent } = Vue

  export default {
    setup() {
      const ButtonCounter = resolveComponent('ButtonCounter')

      return () => {
        return h(ButtonCounter)
      }
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  const { h, resolveComponent } = Vue

  export default {
    render() {
      const ButtonCounter = resolveComponent('ButtonCounter')
      return h(ButtonCounter)
    }
  }
  ```

  </div>

- **আরো দেখুন** [Guide - Render Functions - Components](/guide/extras/render-function#components)

## resolveDirective() {#resolvedirective}

নেইম দ্বারা একটি রেজিস্ট্রাড ডিরেক্টিভ ম্যানুয়ালি সমাধানের জন্য।

- **প্রকার**

  ```ts
  function resolveDirective(name: string): Directive | undefined
  ```

- **বিস্তারিত**

  **Note: আপনি যদি সরাসরি কম্পোনেন্ট ইম্পোর্ট করতে পারেন তবে আপনার এটির প্রয়োজন নেই।**

  কারেক্ট কম্পোনেন্ট কনটেক্স থেকে সমাধান করার জন্য `resolveDirective()` কে অবশ্যই <span class="composition-api"> হয় `setup()` অথবা </span> রেন্ডার ফাংশনের ভিতরে কল করতে হবে।

  যদি ডিরেক্টিভটি না পাওয়া যায়, একটি রানটাইম সতর্কতা নির্গত হবে, এবং ফাংশনটি `undefined` প্রদান করে।

- **আরো দেখুন** [Guide - Render Functions - Custom Directives](/guide/extras/render-function#custom-directives)

## withDirectives() {#withdirectives}

For adding custom directives to vnodes.

- **Type**

  ```ts
  function withDirectives(
    vnode: VNode,
    directives: DirectiveArguments
  ): VNode

  // [Directive, value, argument, modifiers]
  type DirectiveArguments = Array<
    | [Directive]
    | [Directive, any]
    | [Directive, any, string]
    | [Directive, any, string, DirectiveModifiers]
  >
  ```

- **Details**

  Wraps an existing vnode with custom directives. The second argument is an array of custom directives. Each custom directive is also represented as an array in the form of `[Directive, value, argument, modifiers]`. Tailing elements of the array can be omitted if not needed.

- **Example**

  ```js
  import { h, withDirectives } from 'vue'

  // a custom directive
  const pin = {
    mounted() {
      /* ... */
    },
    updated() {
      /* ... */
    }
  }

  // <div v-pin:top.animate="200"></div>
  const vnode = withDirectives(h('div'), [
    [pin, 200, 'top', { animate: true }]
  ])
  ```

- **See also** [Guide - Render Functions - Custom Directives](/guide/extras/render-function#custom-directives)

## withModifiers() {#withmodifiers}

For adding built-in [`v-on` modifiers](/guide/essentials/event-handling#event-modifiers) to an event handler function.

- **Type**

  ```ts
  function withModifiers(fn: Function, modifiers: string[]): Function
  ```

- **Example**

  ```js
  import { h, withModifiers } from 'vue'

  const vnode = h('button', {
    // equivalent of v-on:click.stop.prevent
    onClick: withModifiers(() => {
      // ...
    }, ['stop', 'prevent'])
  })
  ```

- **See also** [Guide - Render Functions - Event Modifiers](/guide/extras/render-function#event-modifiers)
