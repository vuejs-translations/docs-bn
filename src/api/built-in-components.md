---
pageClass: api
---

# Built-in Components {#built-in-components}

:::info নিবন্ধন এবং ব্যবহার
অন্তর্নির্মিত উপাদানগুলি নিবন্ধিত হওয়ার প্রয়োজন ছাড়াই সরাসরি টেমপ্লেটগুলিতে ব্যবহার করা যেতে পারে। এগুলি গাছ-কাঁপানো যায়: এগুলি ব্যবহার করা হলেই বিল্ডে অন্তর্ভুক্ত করা হয়।

এগুলিকে [render functions](/guide/extras/render-function) এ ব্যবহার করার সময়, সেগুলি স্পষ্টভাবে আমদানি করতে হবে৷ উদাহরণ স্বরূপ:

```js
import { h, Transition } from 'vue'

h(Transition, {
  /* props */
})
```

:::

## `<Transition>` {#transition}

একটি **একক** উপাদান বা উপাদানে অ্যানিমেটেড ট্রানজিশন প্রভাব প্রদান করে।

- **Props**

  ```ts
  interface TransitionProps {
    /**
     * Used to automatically generate transition CSS class names.
     * e.g. `name: 'fade'` will auto expand to `.fade-enter`,
     * `.fade-enter-active`, etc.
     */
    name?: string
    /**
     * Whether to apply CSS transition classes.
     * Default: true
     */
    css?: boolean
    /**
     * Specifies the type of transition events to wait for to
     * determine transition end timing.
     * Default behavior is auto detecting the type that has
     * longer duration.
     */
    type?: 'transition' | 'animation'
    /**
     * Specifies explicit durations of the transition.
     * Default behavior is wait for the first `transitionend`
     * or `animationend` event on the root transition element.
     */
    duration?: number | { enter: number; leave: number }
    /**
     * Controls the timing sequence of leaving/entering transitions.
     * Default behavior is simultaneous.
     */
    mode?: 'in-out' | 'out-in' | 'default'
    /**
     * Whether to apply transition on initial render.
     * Default: false
     */
    appear?: boolean

    /**
     * Props for customizing transition classes.
     * Use kebab-case in templates, e.g. enter-from-class="xxx"
     */
    enterFromClass?: string
    enterActiveClass?: string
    enterToClass?: string
    appearFromClass?: string
    appearActiveClass?: string
    appearToClass?: string
    leaveFromClass?: string
    leaveActiveClass?: string
    leaveToClass?: string
  }
  ```

- **ইভেন্টস**

  - `@before-enter`
  - `@before-leave`
  - `@enter`
  - `@leave`
  - `@appear`
  - `@after-enter`
  - `@after-leave`
  - `@after-appear`
  - `@enter-cancelled`
  - `@leave-cancelled` (`v-show` only)
  - `@appear-cancelled`

- **উদাহরণ**

  সাধারণ উপাদান:

  ```vue-html
  <Transition>
    <div v-if="ok">toggled content</div>
  </Transition>
  ```

  `key` বৈশিষ্ট্য পরিবর্তন করে একটি ট্রানজিশন জোর করে:

  ```vue-html
  <Transition>
    <div :key="text">{{ text }}</div>
  </Transition>
  ```

  ট্রানজিশন মোড সহ ডাইনামিক কম্পোনেন্ট + অ্যানিমেট দেখা যাচ্ছে:

  ```vue-html
  <Transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </Transition>
  ```

  ট্রানজিশন ইভেন্টগুলি শোনা:

  ```vue-html
  <Transition @after-enter="onTransitionComplete">
    <div v-show="ok">toggled content</div>
  </Transition>
  ```

- **আরো দেখুন** [`<Transition>` Guide](/guide/built-ins/transition)

## `<TransitionGroup>` {#transitiongroup}

একটি তালিকায় **একাধিক** উপাদান বা উপাদানগুলির জন্য রূপান্তর প্রভাব প্রদান করে।

- **Props**

`<TransitionGroup>` `mode` ব্যতীত `<Transition>`-এর মতো একই প্রপস গ্রহণ করে, এছাড়াও দুটি অতিরিক্ত প্রপস:

  ```ts
  interface TransitionGroupProps extends Omit<TransitionProps, 'mode'> {
    /**
     * If not defined, renders as a fragment.
     */
    tag?: string
    /**
     * For customizing the CSS class applied during move transitions.
     * Use kebab-case in templates, e.g. move-class="xxx"
     */
    moveClass?: string
  }
  ```

- **ইভেন্টস**

  `<TransitionGroup>` emits the same events as `<Transition>`.

- **বিস্তারিত**

  ডিফল্টরূপে, `<TransitionGroup>` একটি মোড়ক DOM উপাদান রেন্ডার করে না, তবে একটিকে `ট্যাগ` প্রপের মাধ্যমে সংজ্ঞায়িত করা যেতে পারে।

  মনে রাখবেন যে অ্যানিমেশনগুলি সঠিকভাবে কাজ করার জন্য একটি `<TransitionGroup>`-এর প্রতিটি  চাইল্ডকে অবশ্যই [**অনন্যভাবে কীযুক্ত**](/guide/essentials/list#maintaining-state-with-key) থাকতে হবে।

  `<TransitionGroup>` CSS ট্রান্সফর্মের মাধ্যমে মুভিং ট্রানজিশন সমর্থন করে। একটি আপডেটের পরে যখন স্ক্রিনে একটি  চাইল্ডর অবস্থান পরিবর্তিত হয়, তখন এটি একটি চলমান CSS ক্লাস প্রয়োগ করা হবে (`name` বৈশিষ্ট্য থেকে স্বয়ংক্রিয়ভাবে তৈরি বা `move-class` প্রপের সাথে কনফিগার করা)। চলন্ত শ্রেণী প্রয়োগ করার সময় যদি CSS `transform` প্রপার্টি "transition-able" হয়, তাহলে উপাদানটি ব্যবহার করে তার গন্তব্যে মসৃণভাবে অ্যানিমেটেড হবে [FLIP technique](https://aerotwist.com/blog/flip-your-animations/)।

- **উদাহরণ**

  ```vue-html
  <TransitionGroup tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </TransitionGroup>
  ```

- **আরো দেখুন** [Guide - TransitionGroup](/guide/built-ins/transition-group)

## `<KeepAlive>` {#keepalive}

ক্যাশে dynamically toggled করা components wrapped inside.

- **Props**

  ```ts
  interface KeepAliveProps {
    /**
     * If specified, only components with names matched by
     * `include` will be cached.
     */
    include?: MatchPattern
    /**
     * Any component with a name matched by `exclude` will
     * not be cached.
     */
    exclude?: MatchPattern
    /**
     * The maximum number of component instances to cache.
     */
    max?: number | string
  }

  type MatchPattern = string | RegExp | (string | RegExp)[]
  ```

- **বিস্তারিত**

একটি ডায়নামিক কম্পোনেন্টের চারপাশে মোড়ানো হলে, `<KeepAlive>` নিষ্ক্রিয় কম্পোনেন্ট ইনস্ট্যান্সকে ধ্বংস না করে ক্যাশে করে।

   যে কোনো সময়ে `<KeepAlive>`-এর সরাসরি সন্তান হিসেবে শুধুমাত্র একটি সক্রিয় উপাদানের উদাহরণ থাকতে পারে।

   যখন একটি উপাদান `<KeepAlive>`-এর ভিতরে টগল করা হয়, তখন এর `activated` এবং `deactivated` লাইফসাইকেল হুকগুলিকে সেই অনুযায়ী আহ্বান করা হবে, যা `mounted` এবং `unmounted` এর বিকল্প প্রদান করবে, যেগুলিকে বলা হয় না। এটি `<KeepAlive>`-এর সরাসরি সন্তানের জন্য প্রযোজ্য এবং সেইসাথে এর সমস্ত বংশধরের ক্ষেত্রেও প্রযোজ্য।

- **উদাহরণ**

  প্রাথমিক ব্যবহার:

  ```vue-html
  <KeepAlive>
    <component :is="view"></component>
  </KeepAlive>
  ```

  যখন `v-if` / `v-else` শাখার সাথে ব্যবহার করা হয়, তখন একটি সময়ে শুধুমাত্র একটি উপাদান রেন্ডার করা আবশ্যক:

  ```vue-html
  <KeepAlive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </KeepAlive>
  ```

  Used together with `<Transition>`:

  ```vue-html
  <Transition>
    <KeepAlive>
      <component :is="view"></component>
    </KeepAlive>
  </Transition>
  ```

  Using `include` / `exclude`:

  ```vue-html
  <!-- comma-delimited string -->
  <KeepAlive include="a,b">
    <component :is="view"></component>
  </KeepAlive>

  <!-- regex (use `v-bind`) -->
  <KeepAlive :include="/a|b/">
    <component :is="view"></component>
  </KeepAlive>

  <!-- Array (use `v-bind`) -->
  <KeepAlive :include="['a', 'b']">
    <component :is="view"></component>
  </KeepAlive>
  ```

  Usage with `max`:

  ```vue-html
  <KeepAlive :max="10">
    <component :is="view"></component>
  </KeepAlive>
  ```

- **আরো দেখুন** [Guide - KeepAlive](/guide/built-ins/keep-alive)

## `<Teleport>` {#teleport}

এর স্লট বিষয়অবজেক্ট DOM এর অন্য অংশে রেন্ডার করে।

- **Props**

  ```ts
  interface TeleportProps {
    /**
     * Required. Specify target container.
     * Can either be a selector or an actual element.
     */
    to: string | HTMLElement
    /**
     * When `true`, the content will remain in its original
     * location instead of moved into the target container.
     * Can be changed dynamically.
     */
    disabled?: boolean
  }
  ```

- **উদাহরণ**

  Specifying target container:

  ```vue-html
  <Teleport to="#some-id" />
  <Teleport to=".some-class" />
  <Teleport to="[data-teleport]" />
  ```

  Conditionally disabling:

  ```vue-html
  <Teleport to="#popup" :disabled="displayVideoInline">
    <video src="./my-movie.mp4">
  </Teleport>
  ```

- **আরো দেখুন** [Guide - Teleport](/guide/built-ins/teleport)

## `<Suspense>` <sup class="vt-badge experimental" /> {#suspense}

একটি উপাদান গাছে নেস্টেড অ্যাসিঙ্ক নির্ভরতা অর্কেস্ট্রেট করার জন্য ব্যবহৃত হয়।

- **Props**

  ```ts
  interface SuspenseProps {
    timeout?: string | number
  }
  ```

- **ইভেন্টস**

  - `@resolve`
  - `@pending`
  - `@fallback`

- **বিস্তারিত**

  `<Suspense>` দুটি স্লট গ্রহণ করে: `#default` slot এবং `#fallback` slot। এটি মেমরিতে ডিফল্ট slot রেন্ডার করার সময় ফলব্যাক স্লটের বিষয়অবজেক্ট প্রদর্শন করবে।

  ডিফল্ট স্লট রেন্ডার করার সময় যদি এটি async নির্ভরতা ([Async Components](/guide/components/async) এবং [`async setup()`](/guide/built-ins/suspense#async-setup)) সহ উপাদানগুলির সম্মুখীন হয়, ডিফল্ট slot প্রদর্শন করার আগে তাদের সব সমাধান না হওয়া পর্যন্ত এটি অপেক্ষা করবে।

- **আরো দেখুন** [Guide - Suspense](/guide/built-ins/suspense)
