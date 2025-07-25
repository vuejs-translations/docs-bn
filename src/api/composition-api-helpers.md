# Composition API: Helpers {#composition-api-helpers}

## useAttrs() {#useattrs}

[সেটআপ প্রসঙ্গ](/api/composition-api-setup#setup-context) থেকে `attrs` অবজেক্ট ফেরত দেয়, যার মধ্যে বর্তমান কম্পোনেন্টের [fallthrough বৈশিষ্ট্য](/guide/components/attrs#fallthrough-attributes) অন্তর্ভুক্ত থাকে . এটি `<script setup>`-এ ব্যবহার করার উদ্দেশ্যে যেখানে সেটআপ প্রসঙ্গ অবজেক্ট উপলব্ধ নেই৷

- **Type**

  ```ts
  function useAttrs(): Record<string, unknown>
  ```

## useSlots() {#useslots}

[সেটআপ কনটেক্সট](/api/composition-api-setup#setup-context) থেকে `স্লট` অবজেক্ট ফেরত দেয়, যার মধ্যে প্যারেন্ট পাস করা স্লটগুলিকে কলযোগ্য ফাংশন হিসেবে অন্তর্ভুক্ত করে যা ভার্চুয়াল DOM নোড ফেরত দেয়। এটি `<script setup>`-এ ব্যবহার করার উদ্দেশ্যে যেখানে সেটআপ প্রসঙ্গ অবজেক্ট উপলব্ধ নেই৷

TypeScript ব্যবহার করলে, [`defineSlots()`](/api/sfc-script-setup#defineslots) এর পরিবর্তে পছন্দ করা উচিত।

- **Type**

  ```ts
  function useSlots(): Record<string, (...args: any[]) => VNode[]>
  ```

## useModel() {#usemodel}

এটি অন্তর্নিহিত সাহায্যকারী যা [`defineModel()`](/api/sfc-script-setup#definemodel) কে ক্ষমতা দেয়। `<স্ক্রিপ্ট সেটআপ>` ব্যবহার করলে, এর পরিবর্তে `defineModel()` পছন্দ করা উচিত।

- শুধুমাত্র 3.4+ এ উপলব্ধ

- **Type**

  ```ts
  function useModel(
    props: Record<string, any>,
    key: string,
    options?: DefineModelOptions
  ): ModelRef

  type DefineModelOptions<T = any> = {
    get?: (v: T) => any
    set?: (v: T) => any
  }

  type ModelRef<T, M extends PropertyKey = string, G = T, S = T> = Ref<G, S> & [
    ModelRef<T, M, G, S>,
    Record<M, true | undefined>
  ]
  ```

- **Example**

  ```js
  export default {
    props: ['count'],
    emits: ['update:count'],
    setup(props) {
      const msg = useModel(props, 'count')
      msg.value = 1
    }
  }
  ```

- **Details**

  `useModel()` can be used in non-SFC components, e.g. when using raw `setup()` function. It expects the `props` object as the first argument, and the model name as the second argument. The optional third argument can be used to declare custom getter and setter for the resulting model ref. Note that unlike `defineModel()`, you are responsible for declaring the props and emits yourself.

## useTemplateRef() <sup class="vt-badge" data-text="3.5+" /> {#usetemplateref}

Returns a shallow ref whose value will be synced with the template element or component with a matching ref attribute.

- **Type**

  ```ts
  function useTemplateRef<T>(key: string): Readonly<ShallowRef<T | null>>
  ```

- **Example**

  ```vue
  <script setup>
  import { useTemplateRef, onMounted } from 'vue'

  const inputRef = useTemplateRef('input')

  onMounted(() => {
    inputRef.value.focus()
  })
  </script>

  <template>
    <input ref="input" />
  </template>
  ```

- **See also**
  - [Guide - Template Refs](/guide/essentials/template-refs)
  - [Guide - Typing Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />
  - [Guide - Typing Component Template Refs](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

## useId() <sup class="vt-badge" data-text="3.5+" /> {#useid}

অ্যাক্সেসিবিলিটি অ্যাট্রিবিউট বা ফর্ম কম্পোনেন্টগুলির জন্য অনন্য-প্রতি-অ্যাপ্লিকেশন আইডি তৈরি করতে ব্যবহৃত হয়।

- **Type**

  ```ts
  function useId(): string
  ```

- **Example**

  ```vue
  <script setup>
  import { useId } from 'vue'

  const id = useId()
  </script>

  <template>
    <form>
      <label :for="id">Name:</label>
      <input :id="id" type="text" />
    </form>
  </template>
  ```

- **Details**

  `useId()` দ্বারা উত্পন্ন আইডিগুলি অ্যাপ্লিকেশন-প্রতি অনন্য। এটি ফর্ম কম্পোনেন্ট এবং অ্যাক্সেসিবিলিটি বৈশিষ্ট্যগুলির জন্য আইডি তৈরি করতে ব্যবহার করা যেতে পারে। একই কম্পোনেন্টে একাধিক কল ভিন্ন আইডি তৈরি করবে; একই কম্পোনেন্ট কলিং `useId()` এর একাধিক দৃষ্টান্তেরও আলাদা আইডি থাকবে।

 `useId()` দ্বারা উত্পন্ন আইডিগুলি সার্ভার এবং ক্লায়েন্ট রেন্ডার জুড়ে স্থিতিশীল হওয়ার গ্যারান্টি দেওয়া হয়, তাই সেগুলি হাইড্রেশনের অমিল না হয়ে SSR অ্যাপ্লিকেশনগুলিতে ব্যবহার করা যেতে পারে।

  যদি আপনার একই পৃষ্ঠার একাধিক Vue অ্যাপ্লিকেশন ইনস্ট্যান্স থাকে, তাহলে আপনি [`app.config.idPrefix`](/api/application#app-config-idprefix) এর মাধ্যমে প্রতিটি অ্যাপকে একটি ID প্রিফিক্স দিয়ে ID দ্বন্দ্ব এড়াতে পারেন।

  :::warning সতর্কতা
  `useId()` কে `computed()` প্রপার্টির ভিতরে ডাকা উচিত নয় কারণ এটি ইনস্ট্যান্স কনফ্লিক্ট তৈরি করতে পারে। পরিবর্তে, `computed()` এর বাইরে আইডি ঘোষণা করুন এবং কম্পিউটেড ফাংশনের মধ্যে এটি উল্লেখ করুন।
  :::
