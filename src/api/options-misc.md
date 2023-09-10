# Options: Misc {#options-misc}

## name {#name}

স্পষ্টভাবে কম্পোনেন্টের জন্য একটি ডিসপ্লে নাম ডিক্লেয়ার করুন।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    name?: string
  }
  ```

- **বিস্তারিত**

  একটি কম্পোনেন্টের নাম নিম্নলিখিত জন্য ব্যবহৃত হয়:

  - কম্পোনেন্টের নিজস্ব টেমপ্লেটে পুনরাবৃত্ত সেলফ-রেফারেন্স
  - Vue DevTools এর উপাদান ইনসেপশন ট্রি ডিসপ্লেই করুন
  - ওয়ার্নিং  কম্পোনেন্ট ট্রেস প্রদর্শন ডিসপ্লেই করুন

  আপনি যখন সিঙ্গেল-ফাইল কম্পোনেন্ট ব্যবহার করেন, তখন কম্পোনেন্টটি ইতিমধ্যেই ফাইলের নাম থেকে তার নিজস্ব নাম অনুমান করে। উদাহরণ স্বরূপ, `MyComponent.vue` নামের একটি ফাইলে অনুমানকৃত ডিসপ্লে নাম "MyComponent" থাকবে।

  আরেকটি ক্ষেত্রে হল যে যখন একটি কম্পোনেন্ট গ্লোবালি [`app.component`](/api/application#app-component) এর সাথে রেজিস্ট্রাড হয়, তখন গ্লোবাল আইডি স্বয়ংক্রিয়ভাবে তার নাম হিসাবে সেট হয়ে যায়।

  `name` অপশনটি আপনাকে অনুমানকৃত নামটিকে ওভাররাইড করতে বা কোনো নাম অনুমান করা না গেলে স্পষ্টভাবে একটি নাম প্রদান করতে দেয় (যেমন যখন বিল্ড টুল ব্যবহার করা হয় না, বা একটি ইনলাইনড নন-এসএফসি কম্পোনেন্ট)।

  একটি কেস আছে যেখানে `name` স্পষ্টভাবে প্রয়োজনীয়: যখন ক্যাশেযোগ্য কম্পোনেন্টগুলির সাথে মিলিত হয় [`<KeepAlive>`](/guide/built-ins/keep-alive) এর `include/exclude` প্রপসের মাধ্যমে।

  :::tip
  ভার্সন 3.2.34 থেকে, `<script setup>` ব্যবহার করে একটি সিঙ্গেল-ফাইল কম্পোনেন্ট স্বয়ংক্রিয়ভাবে ফাইলের নামের উপর ভিত্তি করে তার `name` অপশনটি অনুমান করবে, এমনকি `<KeepAlive>` ব্যবহার করলেও নামটি ম্যানুয়ালি ঘোষণা করার প্রয়োজনীয়তা দূর করে।
  :::

## inheritAttrs {#inheritattrs}

ডিফল্ট কম্পোনেন্ট অ্যাট্রিবিউট ব্যর্থ বেহিভিয়ার এনাবল করা উচিত কিনা তা নিয়ন্ত্রণ করে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    inheritAttrs?: boolean // default: true
  }
  ```

- **বিস্তারিত**

  ডিফল্টরূপে, প্যারেন্ট স্কোপ অ্যাট্রিবিউট বাইন্ডিং যা প্রপস হিসাবে স্বীকৃত নয় "fallthrough" হবে। এর মানে হল যে যখন আমাদের একটি একক-মূল কম্পোনেন্ট থাকে, তখন এই বাইন্ডিংগুলি সাধারণ HTML বৈশিষ্ট্য হিসাবে চাইল্ড কম্পোনেন্টের রুট এলিমেন্টে প্রয়োগ করা হবে। একটি টার্গেট ইলিমেন্ট বা অন্য কম্পোনেন্ট  মোড়ানো একটি ইলিমেন্ট রচনা করার সময়, এটি সবসময় পছন্দসই আচরণ নাও হতে পারে। `false` তে `inheritAttrs` সেট করে, এই ডিফল্ট আচরণটি নিষ্ক্রিয় করা যেতে পারে। অ্যাট্রিবিউটগুলি `$attrs` ইনস্ট্যান্স প্রপার্টির মাধ্যমে পাওয়া যায় এবং `v-bind` ব্যবহার করে একটি নন-রুট এলিমেন্টের সাথে স্পষ্টভাবে আবদ্ধ হতে পারে।

- **উদাহরন**

  <div class="options-api">

  ```vue
  <script>
  export default {
    inheritAttrs: false,
    props: ['label', 'value'],
    emits: ['input']
  }
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>
  <div class="composition-api">

  `<script setup>` ব্যবহার করে এমন একটি কম্পোনেন্টে এই অপশনটি ঘোষণা করার সময়, আপনি [`defineOptions`](/api/sfc-script-setup#defineoptions) ম্যাক্রো ব্যবহার করতে পারেন:

  ```vue
  <script setup>
  defineProps(['label', 'value'])
  defineEmits(['input'])
  defineOptions({
    inheritAttrs: false
  })
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  3.3 থেকে আপনি সরাসরি `<script setup>`-এ `defineOptions` ব্যবহার করতে পারেন:

  ```vue
  <script setup>
  defineProps(['label', 'value'])
  defineEmits(['input'])
  defineOptions({ inheritAttrs: false })
  </script>

  <template>
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      />
    </label>
  </template>
  ```

  </div>

- **আরো দেখুন** [Fallthrough Attributes](/guide/components/attrs)

## components {#components}

একটি অবজেক্ট যা কম্পোনেন্ট ইনস্ট্যান্সে অ্যাভইলএবল করার জন্য কম্পোনেন্ট রেজিস্ট্রার করে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    components?: { [key: string]: Component }
  }
  ```

- **উদাহরন**

  ```js
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  export default {
    components: {
      // shorthand
      Foo,
      // register under a different name
      RenamedBar: Bar
    }
  }
  ```

- **আরো দেখুন** [Component Registration](/guide/components/registration)

## directives {#directives}

একটি অবজেক্ট যা কম্পোনেন্ট ইনস্ট্যান্সের জন্য অ্যাভইলএবল করার নির্দেশাবলী রেজিস্ট্রার করে।

- **প্রকার**

  ```ts
  interface ComponentOptions {
    directives?: { [key: string]: Directive }
  }
  ```

- **উদাহরন**

  ```js
  export default {
    directives: {
      // enables v-focus in template
      focus: {
        mounted(el) {
          el.focus()
        }
      }
    }
  }
  ```

  ```vue-html
  <input v-focus>
  ```

  ডিরেক্টিভগুলো একটি হ্যাশ কম্পোনেন্টের উদাহরণ অ্যাভইলএবল করা হবে.

- **আরো দেখুন** [Custom Directives](/guide/reusability/custom-directives)
