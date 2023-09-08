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

Controls whether the default component attribute fallthrough behavior should be enabled.

- **Type**

  ```ts
  interface ComponentOptions {
    inheritAttrs?: boolean // default: true
  }
  ```

- **Details**

  By default, parent scope attribute bindings that are not recognized as props will "fallthrough". This means that when we have a single-root component, these bindings will be applied to the root element of the child component as normal HTML attributes. When authoring a component that wraps a target element or another component, this may not always be the desired behavior. By setting `inheritAttrs` to `false`, this default behavior can be disabled. The attributes are available via the `$attrs` instance property and can be explicitly bound to a non-root element using `v-bind`.

- **Example**

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

  When declaring this option in a component that uses `<script setup>`, you can use the [`defineOptions`](/api/sfc-script-setup#defineoptions) macro:

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

  Since 3.3 you can also use `defineOptions` directly in `<script setup>`:

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

- **See also** [Fallthrough Attributes](/guide/components/attrs)

## components {#components}

An object that registers components to be made available to the component instance.

- **Type**

  ```ts
  interface ComponentOptions {
    components?: { [key: string]: Component }
  }
  ```

- **Example**

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

- **See also** [Component Registration](/guide/components/registration)

## directives {#directives}

An object that registers directives to be made available to the component instance.

- **Type**

  ```ts
  interface ComponentOptions {
    directives?: { [key: string]: Directive }
  }
  ```

- **Example**

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

  A hash of directives to be made available to the component instance.

- **See also** [Custom Directives](/guide/reusability/custom-directives)
