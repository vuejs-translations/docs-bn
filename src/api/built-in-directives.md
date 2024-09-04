# Built-in Directives {#built-in-directives}

## v-text {#v-text}

কম্পোনেন্টটির পাঠ্য বিষয়অবজেক্ট আপডেট করুন।

- **Expects:** `string`

- **বিস্তারিত**

'v-text' এলিমেন্টের [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) প্রপার্টি সেট করে কাজ করে, তাই এটি এলিমেন্টের ভিতরে বিদ্যমান যেকোনো কন্টেন্ট ওভাররাইট করবে . আপনার যদি `textContent`-এর অংশ আপডেট করতে হয়, তাহলে আপনার পরিবর্তে [mustache interpolations](/guide/essentials/template-syntax#text-interpolation) ব্যবহার করা উচিত।

- **উদাহরণ**

  ```vue-html
  <span v-text="msg"></span>
  <!-- same as -->
  <span>{{msg}}</span>
  ```

- **আরো দেখুন** [Template Syntax - Text Interpolation](/guide/essentials/template-syntax#text-interpolation)

## v-html {#v-html}

কম্পোনেন্ট এর আপডেট [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML).

- **Expects:** `string`

- **বিস্তারিত**

  `v-html` এর বিষয়অবজেক্ট প্লেইন HTML হিসাবে ঢোকানো হয়েছে - Vue টেমপ্লেট সিনট্যাক্স প্রক্রিয়া করা হবে না। আপনি যদি নিজেকে `v-html` ব্যবহার করে টেমপ্লেট রচনা করার চেষ্টা করেন, তবে পরিবর্তে কম্পোনেন্টগুলি ব্যবহার করে সমাধানটি পুনর্বিবেচনা করার চেষ্টা করুন।

  ::: warning নিরাপত্তা নোট
  গতিশীলভাবে আপনার ওয়েবসাইটে নির্বিচারে HTML রেন্ডার করা খুব বিপজ্জনক হতে পারে কারণ এটি সহজেই [XSS আক্রমণ](https://en.wikipedia.org/wiki/Cross-site_scripting) হতে পারে। শুধুমাত্র বিশ্বস্ত কন্টেন্টে `v-html` ব্যবহার করুন এবং ব্যবহারকারীর দেওয়া কন্টেন্টে **কখনই না** ব্যবহার করুন।
  :::

 [Single-File Components](/guide/scaling-up/sfc), `v-html`-এর ভিতরের সামগ্রীতে `scoped` স্টাইল প্রযোজ্য হবে না, কারণ সেই HTML Vue-এর টেমপ্লেট কম্পাইলার দ্বারা প্রক্রিয়া করা হয় না। আপনি যদি স্কোপড CSS দিয়ে `v-html` কন্টেন্ট টার্গেট করতে চান, তাহলে আপনি [CSS মডিউল](./sfc-css-features#css-modules) অথবা ম্যানুয়াল সহ একটি অতিরিক্ত, গ্লোবাল `<style>` কম্পোনেন্ট ব্যবহার করতে পারেন স্কোপিং কৌশল যেমন BEM।

- **উদাহরণ**

  ```vue-html
  <div v-html="html"></div>
  ```

- **আরো দেখুন** [Template Syntax - Raw HTML](/guide/essentials/template-syntax#raw-html)

## v-show {#v-show}

অভিব্যক্তি মানের সত্যতার উপর ভিত্তি করে কম্পোনেন্টটির দৃশ্যমানতা টগল করুন।

- **Expects:** `any`

- **বিস্তারিত**

  `v-show` ইনলাইন স্টাইলর মাধ্যমে `display` CSS প্রপার্টি সেট করে কাজ করে এবং কম্পোনেন্টটি দৃশ্যমান হলে প্রাথমিক `display` মানকে সম্মান করার চেষ্টা করবে। এর অবস্থা পরিবর্তিত হলে এটি রূপান্তরকেও ট্রিগার করে।

- **আরো দেখুন** [Conditional Rendering - v-show](/guide/essentials/conditional#v-show)

## v-if {#v-if}

অভিব্যক্তি মানের সত্যতার উপর ভিত্তি করে শর্তসাপেক্ষে একটি কম্পোনেন্ট বা একটি টেমপ্লেট খণ্ড রেন্ডার করুন।

- **Expects:** `any`

- **বিস্তারিত**

  যখন একটি `v-if` কম্পোনেন্ট টগল করা হয়, তখন কম্পোনেন্ট এবং এর মধ্যে থাকা নির্দেশাবলী/কম্পোনেন্টগুলি ধ্বংস হয়ে পুনরায় তৈরি করা হয়। যদি প্রাথমিক শর্তটি মিথ্যা হয়, তাহলে ভিতরের বিষয়অবজেক্ট মোটেও রেন্ডার করা হবে না।

  শুধুমাত্র পাঠ্য বা একাধিক কম্পোনেন্ট সম্বলিত শর্তাধীন ব্লক বোঝাতে `<template>`-এ ব্যবহার করা যেতে পারে।

  এই নির্দেশিকা যখন এর অবস্থা পরিবর্তিত হয় তখন ট্রানজিশন শুরু করে।

  একসাথে ব্যবহার করা হলে, `v-for` এর চেয়ে `v-if` এর অগ্রাধিকার বেশি থাকে। আমরা একটি কম্পোনেন্টে এই দুটি নির্দেশ একসাথে ব্যবহার করার পরামর্শ দিই না — বিস্তারিত জানার জন্য [তালিকা রেন্ডারিং গাইড](/guide/essentials/list#v-for-with-v-if) দেখুন।

- **আরো দেখুন** [Conditional Rendering - v-if](/guide/essentials/conditional#v-if)

## v-else {#v-else}

`v-if` বা `v-if` / `v-else-if` চেইনের জন্য "অন্য ব্লক" নির্দেশ করুন।

- **এক্সপ্রেশন আশা করি না**

- **বিস্তারিত**

    - সীমাবদ্ধতা: পূর্ববর্তী ভাইবোন কম্পোনেন্টে অবশ্যই `v-if` বা `v-else-if` থাকতে হবে।

    - শুধুমাত্র পাঠ্য বা একাধিক কম্পোনেন্ট সমন্বিত একটি শর্তাধীন ব্লক বোঝাতে `<টেমপ্লেট>`-এ ব্যবহার করা যেতে পারে।

- **উদাহরণ**

  ```vue-html
  <div v-if="Math.random() > 0.5">
    Now you see me
  </div>
  <div v-else>
    Now you don't
  </div>
  ```

- **আরো দেখুন** [Conditional Rendering - v-else](/guide/essentials/conditional#v-else)

## v-else-if {#v-else-if}

`v-if` এর জন্য "else if block" নির্দেশ করুন। শৃঙ্খলিত হতে পারে।

- **Expects:** `any`

- **বিস্তারিত**

    - সীমাবদ্ধতা: পূর্ববর্তী ভাইবোন কম্পোনেন্টে অবশ্যই `v-if` বা `v-else-if` থাকতে হবে।

    - শুধুমাত্র পাঠ্য বা একাধিক কম্পোনেন্ট সমন্বিত একটি শর্তাধীন ব্লক বোঝাতে `<template>`-এ ব্যবহার করা যেতে পারে।

- **উদাহরণ**

  ```vue-html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Not A/B/C
  </div>
  ```

- **আরো দেখুন** [Conditional Rendering - v-else-if](/guide/essentials/conditional#v-else-if)

## v-for {#v-for}

উৎস ডেটার উপর ভিত্তি করে কম্পোনেন্ট বা টেমপ্লেট ব্লক একাধিকবার রেন্ডার করুন।

- **Expects:** `Array | Object | number | string | Iterable`

- **বিস্তারিত**

  নির্দেশের মানটিকে অবশ্যই বিশেষ সিনট্যাক্স ব্যবহার করতে হবে `alias in expression` ব্যবহার করে বর্তমান কম্পোনেন্টটির জন্য একটি উপনাম প্রদান করার জন্য:

  ```vue-html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  বিকল্পভাবে, আপনি সূচকের জন্য একটি উপনামও নির্দিষ্ট করতে পারেন (বা কীটি যদি কোনো অবজেক্টতে ব্যবহার করা হয়):

  ```vue-html
  <div v-for="(item, index) in items"></div>
  <div v-for="(value, key) in object"></div>
  <div v-for="(value, name, index) in object"></div>
  ```

  `v-for` এর ডিফল্ট আচরণ কম্পোনেন্টগুলিকে স্থানান্তর না করেই প্যাচ করার চেষ্টা করবে। কম্পোনেন্টগুলিকে পুনরায় ক্রমানুসারে জোর করতে, আপনাকে `key` বিশেষ বৈশিষ্ট্য সহ একটি অর্ডারিং ইঙ্গিত প্রদান করা উচিত:

  ```vue-html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  'v-for' এমন মানগুলির উপরও কাজ করতে পারে যা [Iterable Protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol), native `Map` সহ প্রয়োগ করে এবং `Set`।

- **আরো দেখুন**
  - [List Rendering](/guide/essentials/list)

## v-on {#v-on}

কম্পোনেন্টটির সাথে একটি ইভেন্ট শ্রোতা সংযুক্ত করুন।

- **Shorthand:** `@`

- **Expects:** `Function | Inline Statement | Object (without argument)`

- **Argument:** `event` (optional if using Object syntax)

- **Modifiers**

  - `.stop` - call `event.stopPropagation()`.
  - `.prevent` - call `event.preventDefault()`.
  - `.capture` - add event listener in capture mode.
  - `.self` - only trigger handler if event was dispatched from this element.
  - `.{keyAlias}` - only trigger handler on certain keys.
  - `.once` - trigger handler at most once.
  - `.left` - only trigger handler for left button mouse events.
  - `.right` - only trigger handler for right button mouse events.
  - `.middle` - only trigger handler for middle button mouse events.
  - `.passive` - attaches a DOM event with `{ passive: true }`.

- **বিস্তারিত**

  ইভেন্টের ধরনটি যুক্তি দ্বারা চিহ্নিত করা হয়। অভিব্যক্তিটি একটি পদ্ধতির নাম, একটি ইনলাইন বিবৃতি বা বাদ দেওয়া হতে পারে যদি সেখানে সংশোধক উপস্থিত থাকে।

  একটি সাধারণ কম্পোনেন্টে ব্যবহার করা হলে, এটি শুধুমাত্র [**নেটিভ DOM ইভেন্ট**](https://developer.mozilla.org/en-US/docs/Web/Events) শোনে। একটি কাস্টম কম্পোনেন্ট কম্পোনেন্ট ব্যবহার করা হলে, এটি সেই  চাইল্ড কম্পোনেন্টে নির্গত **কাস্টম ইভেন্ট** শোনে।

  নেটিভ DOM ইভেন্টগুলি শোনার সময়, পদ্ধতিটি নেটিভ ইভেন্টটিকে একমাত্র যুক্তি হিসাবে গ্রহণ করে। ইনলাইন স্টেটমেন্ট ব্যবহার করলে, স্টেটমেন্টের বিশেষ `$event` প্রপার্টিতে অ্যাক্সেস আছে: `v-on:click="handle('ok', $event)"`।

  `v-on` কোনো যুক্তি ছাড়াই ইভেন্ট/শ্রোতা জোড়ার কোনো অবজেক্টর সাথে বাঁধাই সমর্থন করে। অবজেক্ট সিনট্যাক্স ব্যবহার করার সময় নোট করুন, এটি কোনো পরিবর্তনকারীকে সমর্থন করে না।

- **উদাহরণ**

  ```vue-html
  <!-- method handler -->
  <button v-on:click="doThis"></button>

  <!-- dynamic event -->
  <button v-on:[event]="doThis"></button>

  <!-- inline statement -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- shorthand -->
  <button @click="doThis"></button>

  <!-- shorthand dynamic event -->
  <button @[event]="doThis"></button>

  <!-- stop propagation -->
  <button @click.stop="doThis"></button>

  <!-- prevent default -->
  <button @click.prevent="doThis"></button>

  <!-- prevent default without expression -->
  <form @submit.prevent></form>

  <!-- chain modifiers -->
  <button @click.stop.prevent="doThis"></button>

  <!-- key modifier using keyAlias -->
  <input @keyup.enter="onEnter" />

  <!-- the click event will be triggered at most once -->
  <button v-on:click.once="doThis"></button>

  <!-- object syntax -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  চাইল্ড কম্পোনেন্টে কাস্টম ইভেন্ট শোনা (যখন বাচ্চার উপর "my-event" নির্গত হয় তখন হ্যান্ডলারকে বলা হয়):

  ```vue-html
  <MyComponent @my-event="handleThis" />

  <!-- inline statement -->
  <MyComponent @my-event="handleThis(123, $event)" />
  ```

- **আরো দেখুন**
  - [Event Handling](/guide/essentials/event-handling)
  - [Components - Custom Events](/guide/essentials/component-basics#listening-to-events)

## v-bind {#v-bind}

গতিশীলভাবে এক বা একাধিক বৈশিষ্ট্য বা একটি কম্পোনেন্ট প্রপ একটি এক্সপ্রেশনে আবদ্ধ করুন।

- **Shorthand:**
  - `:` or `.` (when using `.prop` modifier)
  - Omitting value (when attribute and bound value has the same name, requires 3.4+)

- **Expects:** `any (with argument) | Object (without argument)`

- **Argument:** `attrOrProp (optional)`

- **Modifiers**

  - `.camel` - transform the kebab-case attribute name into camelCase.
  - `.prop` - force a binding to be set as a DOM property (3.2+).
  - `.attr` - force a binding to be set as a DOM attribute (3.2+).

- **Usage**

  যখন `class` বা `style` অ্যাট্রিবিউটকে আবদ্ধ করতে ব্যবহৃত হয়, তখন `v-bind` অ্যারে বা অবজেক্টের মতো অতিরিক্ত মান প্রকারকে সমর্থন করে। আরো বিস্তারিত জানার জন্য নীচের লিঙ্ক গাইড বিভাগ দেখুন.

  একটি কম্পোনেন্টস উপর একটি বাইন্ডিং সেট করার সময়, একটি `in` অপারেটর চেক ব্যবহার করে কম্পোনেন্টটির কী একটি বৈশিষ্ট্য হিসাবে সংজ্ঞায়িত আছে কিনা তা ডিফল্টরূপে Vue চেক করে। যদি কম্পিউটেড প্রপার্টি সংজ্ঞায়িত করা হয়, Vue একটি বৈশিষ্ট্যের পরিবর্তে একটি DOM কম্পিউটেড প্রপার্টি হিসাবে মান সেট করবে। এটি বেশিরভাগ ক্ষেত্রে কাজ করা উচিত, তবে আপনি স্পষ্টভাবে `.prop` বা `.attr` সংশোধক ব্যবহার করে এই আচরণটিকে ওভাররাইড করতে পারেন। এটি কখনও কখনও প্রয়োজনীয়, বিশেষ করে যখন [কাস্টম কম্পোনেন্টগুলির সাথে কাজ করা](/guide/extras/web-components#passing-dom-properties)।

  যখন কম্পোনেন্ট প্রপ বাইন্ডিং এর জন্য ব্যবহার করা হয়, তখন প্রপটি অবশ্যই চাইল্ড কম্পোনেন্টে সঠিকভাবে ঘোষণা করতে হবে।

  যখন কোন যুক্তি ছাড়া ব্যবহার করা হয়, তখন অ্যাট্রিবিউট নাম-মানের জোড়া ধারণকারী অবজেক্টকে আবদ্ধ করতে ব্যবহার করা যেতে পারে।

- **উদাহরণ**

  ```vue-html
  <!-- bind an attribute -->
  <img v-bind:src="imageSrc" />

  <!-- dynamic attribute name -->
  <button v-bind:[key]="value"></button>

  <!-- shorthand -->
  <img :src="imageSrc" />

  <!-- same-name shorthand (3.4+), expands to :src="src" -->
  <img :src />

  <!-- shorthand dynamic attribute name -->
  <button :[key]="value"></button>

  <!-- with inline string concatenation -->
  <img :src="'/path/to/images/' + fileName" />

  <!-- class binding -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]"></div>

  <!-- style binding -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- binding an object of attributes -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- prop binding. "prop" must be declared in the child component. -->
  <MyComponent :prop="someThing" />

  <!-- pass down parent props in common with a child component -->
  <MyComponent v-bind="$props" />

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  The `.prop` modifier also has a dedicated shorthand, `.`:

  ```vue-html
  <div :someProperty.prop="someObject"></div>

  <!-- equivalent to -->
  <div .someProperty="someObject"></div>
  ```

  ইন-ডোম টেমপ্লেট ব্যবহার করার সময় `.camel` সংশোধক একটি `v-bind` অ্যাট্রিবিউটের নাম উট করার অনুমতি দেয়, যেমন SVG `viewBox` বৈশিষ্ট্য:

  ```vue-html
  <svg :view-box.camel="viewBox"></svg>
  ```

  আপনি যদি স্ট্রিং টেমপ্লেট ব্যবহার করেন, বা একটি বিল্ড স্টেপ সহ টেমপ্লেটটি প্রাক-কম্পাইল করেন তাহলে `.camel` এর প্রয়োজন নেই।

- **আরো দেখুন**
  - [Class and Style Bindings](/guide/essentials/class-and-style)
  - [Components - Prop Passing Details](/guide/components/props#prop-passing-details)

## v-model {#v-model}

একটি ফর্ম ইনপুট কম্পোনেন্ট বা একটি কম্পোনেন্টে একটি দ্বি-মুখী বাঁধাই তৈরি করুন।

- **Expects:** varies based on value of form inputs element or output of components

- **Limited to:**

  - `<input>`
  - `<select>`
  - `<textarea>`
  - components

- **Modifiers**

  - [`.lazy`](/guide/essentials/forms#lazy) - listen to `change` events instead of `input`
  - [`.number`](/guide/essentials/forms#number) - cast valid input string to numbers
  - [`.trim`](/guide/essentials/forms#trim) - trim input

- **আরো দেখুন**

  - [Form Input Bindings](/guide/essentials/forms)
  - [Component Events - Usage with `v-model`](/guide/components/v-model)

## v-slot {#v-slot}

নামযুক্ত স্লট বা স্কোপড স্লটগুলি নির্দেশ করুন যা প্রপস পাওয়ার আশা করে।

- **Shorthand:** `#`

- **Expects:** জাভাস্ক্রিপ্ট এক্সপ্রেশন যা একটি ফাংশন আর্গুমেন্ট পজিশনে বৈধ, ধ্বংস করার জন্য সমর্থন সহ। ঐচ্ছিক - শুধুমাত্র প্রয়োজন যদি প্রপস স্লটে পাস করার আশা করা হয়।

- **Argument:** slot name (optional, defaults to `default`)

- **Limited to:**

  - `<template>`
  - [components](/guide/components/slots#scoped-slots) (for a lone default slot with props)

- **উদাহরণ**

  ```vue-html
  <!-- Named slots -->
  <BaseLayout>
    <template v-slot:header>
      Header content
    </template>

    <template v-slot:default>
      Default slot content
    </template>

    <template v-slot:footer>
      Footer content
    </template>
  </BaseLayout>

  <!-- Named slot that receives props -->
  <InfiniteScroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </InfiniteScroll>

  <!-- Default slot that receive props, with destructuring -->
  <Mouse v-slot="{ x, y }">
    Mouse position: {{ x }}, {{ y }}
  </Mouse>
  ```

- **আরো দেখুন**
  - [Components - Slots](/guide/components/slots)

## v-pre {#v-pre}

এই কম্পোনেন্ট এবং এর সমস্ত  চাইল্ডদের জন্য সংকলন এড়িয়ে যান।

- **এক্সপ্রেশন আশা করি না**

- **বিস্তারিত**

  `v-pre` সহ কম্পোনেন্টস ভিতরে, সমস্ত Vue টেমপ্লেট সিনট্যাক্স সংরক্ষণ করা হবে এবং যেমন আছে তেমন রেন্ডার করা হবে। এর সবচেয়ে সাধারণ ব্যবহার হল raw গোঁফের ট্যাগ প্রদর্শন করা।

- **উদাহরণ**

  ```vue-html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

## v-once {#v-once}

শুধুমাত্র একবার কম্পোনেন্ট এবং কম্পোনেন্ট রেন্ডার করুন এবং ভবিষ্যতের আপডেটগুলি এড়িয়ে যান।

- **এক্সপ্রেশন আশা করি না**

- **বিস্তারিত**

  পরবর্তী রি-রেন্ডারে, কম্পোনেন্ট/কম্পোনেন্ট এবং এর সমস্ত সন্তানকে স্ট্যাটিক কন্টেন্ট হিসেবে গণ্য করা হবে এবং বাদ দেওয়া হবে। এটি আপডেট কর্মক্ষমতা অপ্টিমাইজ করতে ব্যবহার করা যেতে পারে.

  ```vue-html
  <!-- single element -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- the element have children -->
  <div v-once>
    <h1>Comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <MyComponent v-once :comment="msg"></MyComponent>
  <!-- `v-for` directive -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

  3.2 থেকে, আপনি [`v-memo`](#v-memo) ব্যবহার করে অবৈধ অবস্থার সাথে টেমপ্লেটের কিছু অংশ স্মরণ করতে পারেন।

- **আরো দেখুন**
  - [Data Binding Syntax - interpolations](/guide/essentials/template-syntax#text-interpolation)
  - [v-memo](#v-memo)

## v-memo {#v-memo}

- Only supported in 3.2+

- **Expects:** `any[]`

- **বিস্তারিত**

  টেমপ্লেটের একটি সাব-ট্রি মেমোাইজ করুন। উভয় কম্পোনেন্ট এবং কম্পোনেন্ট ব্যবহার করা যেতে পারে. নির্দেশিকাটি মেমোাইজেশনের জন্য তুলনা করার জন্য নির্ভরতা মানগুলির একটি নির্দিষ্ট দৈর্ঘ্যের অ্যারে আশা করে। যদি অ্যারের প্রতিটি মান শেষ রেন্ডারের মতোই হয়, তাহলে সমগ্র সাব-ট্রির আপডেটগুলি এড়িয়ে যাবে৷ উদাহরণ স্বরূপ:

  ```vue-html
  <div v-memo="[valueA, valueB]">
    ...
  </div>
  ```

  যখন কম্পোনেন্ট রি-রেন্ডার হয়, যদি `valueA` এবং `valueB` উভয়ই একই থাকে, তাহলে এই `<div>` এবং এর বাচ্চাদের জন্য সমস্ত আপডেট এড়িয়ে যাবে। প্রকৃতপক্ষে, এমনকি ভার্চুয়াল DOM VNode সৃষ্টিও বাদ দেওয়া হবে যেহেতু সাব-ট্রির মেমোকৃত কপি পুনরায় ব্যবহার করা যেতে পারে।

  মেমোাইজেশন অ্যারেটি সঠিকভাবে উল্লেখ করা গুরুত্বপূর্ণ, অন্যথায় আমরা এমন আপডেটগুলি এড়িয়ে যেতে পারি যা প্রকৃতপক্ষে প্রয়োগ করা উচিত। একটি খালি নির্ভরতা অ্যারে সহ `v-মেমো` (`v-memo="[]"`) কার্যকরীভাবে `v-একবার` এর সমতুল্য হবে।

  **Usage with `v-for`**

  `v-memo` শুধুমাত্র পারফরম্যান্স-সমালোচনামূলক পরিস্থিতিতে মাইক্রো অপ্টিমাইজেশনের জন্য প্রদান করা হয় এবং খুব কমই প্রয়োজন হওয়া উচিত। সবচেয়ে সাধারণ ক্ষেত্রে যেখানে এটি সহায়ক হতে পারে তা হল বড় `v-for` তালিকা রেন্ডার করার সময় (যেখানে `length > 1000`):

  ```vue-html
  <div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
    <p>ID: {{ item.id }} - selected: {{ item.id === selected }}</p>
    <p>...more child nodes</p>
  </div>
  ```

যখন কম্পোনেন্টের `selected` অবস্থা পরিবর্তিত হয়, বেশিরভাগ আইটেম ঠিক একই থাকা সত্ত্বেও প্রচুর পরিমাণে VNodes তৈরি করা হবে। এখানে `v-memo` ব্যবহারটি মূলত বলছে "শুধুমাত্র এই আইটেমটি আপডেট করুন যদি এটি অ-নির্বাচিত থেকে নির্বাচিত হয়ে যায়, বা অন্য উপায়ে"। এটি প্রতিটি অপ্রভাবিত আইটেমকে তার আগের VNode পুনরায় ব্যবহার করতে এবং সম্পূর্ণরূপে ভিন্নতা এড়িয়ে যেতে দেয়। মনে রাখবেন আমাদের এখানে মেমো নির্ভরতা অ্যারেতে `item.id` অন্তর্ভুক্ত করার দরকার নেই কারণ Vue স্বয়ংক্রিয়ভাবে আইটেমের `:key` থেকে এটিকে অনুমান করে।

  :::warning
  `v-for` এর সাথে `v-memo` ব্যবহার করার সময়, নিশ্চিত করুন যে সেগুলি একই কম্পোনেন্টে ব্যবহার করা হয়েছে। **`v-memo` `v-for` এর ভিতরে কাজ করে না।**
  :::

  কিছু প্রান্তের ক্ষেত্রে যেখানে চাইল্ড কম্পোনেন্ট আপডেট চেক ডি-অপ্টিমাইজ করা হয়েছে সেখানে অবাঞ্ছিত আপডেটগুলি ম্যানুয়ালি প্রতিরোধ করতে কম্পোনেন্টগুলিতে `v-memo` ব্যবহার করা যেতে পারে। কিন্তু আবার, প্রয়োজনীয় আপডেটগুলি এড়িয়ে যাওয়ার জন্য সঠিক নির্ভরতা অ্যারেগুলি নির্দিষ্ট করা ডেভেলপমেন্টকারীর দায়িত্ব।

- **আরো দেখুন**
  - [v-once](#v-once)

## v-cloak {#v-cloak}

এটি প্রস্তুত না হওয়া পর্যন্ত আন-সংকলিত টেমপ্লেট লুকানোর জন্য ব্যবহৃত হয়।

- **এক্সপ্রেশন আশা করি না**

- **বিস্তারিত**

  **এই নির্দেশিকা শুধুমাত্র নো-বিল্ড-স্টেপ সেটআপে প্রয়োজন।**

  ইন-ডোম টেমপ্লেটগুলি ব্যবহার করার সময়, "অসংকলিত টেমপ্লেটগুলির ফ্ল্যাশ" হতে পারে: মাউন্ট করা কম্পোনেন্টটি রেন্ডার করা সামগ্রীর সাথে প্রতিস্থাপন না করা পর্যন্ত ব্যবহারকারী raw গোঁফের ট্যাগ দেখতে পারেন৷

  সংশ্লিষ্ট কম্পোনেন্ট ইনস্ট্যান্স মাউন্ট না হওয়া পর্যন্ত 'v-cloak' কম্পোনেন্টটিতে থাকবে। CSS নিয়মের সাথে মিলিত যেমন `[v-cloak] { display: none }`, এটি কম্পোনেন্ট প্রস্তুত না হওয়া পর্যন্ত raw টেমপ্লেট লুকানোর জন্য ব্যবহার করা যেতে পারে।

- **উদাহরণ**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```vue-html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  সংকলন সম্পন্ন না হওয়া পর্যন্ত `<div>` দৃশ্যমান হবে না।
