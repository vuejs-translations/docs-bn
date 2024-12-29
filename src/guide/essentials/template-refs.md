# Template Refs {#template-refs}

যদিও Vue-এর ডিক্লেয়ার রেন্ডারিং মডেল আপনার জন্য বেশিরভাগ সরাসরি DOM ক্রিয়াকলাপগুলিকে বিমূর্ত করে দেয়, তখনও এমন কিছু ক্ষেত্রে হতে পারে যেখানে আমাদের অন্তর্নিহিত DOM কম্পোনেন্টগুলিতে সরাসরি অ্যাক্সেসের প্রয়োজন। এটি অর্জন করতে, আমরা বিশেষ `ref` বৈশিষ্ট্য ব্যবহার করতে পারি:

```vue-html
<input ref="input">
```

`ref` একটি বিশেষ বৈশিষ্ট্য, `v-for` অধ্যায়ে আলোচিত `key` বৈশিষ্ট্যের অনুরূপ। এটি মাউন্ট করার পরে এটি আমাদের একটি নির্দিষ্ট DOM কম্পোনেন্ট বা চাইল্ড কম্পোনেন্ট ইনস্ট্যান্সের সরাসরি রেফারেন্স পেতে দেয়। এটি উপযোগী হতে পারে যখন আপনি চান, উদাহরণস্বরূপ, প্রোগ্রাম্যাটিকভাবে কম্পোনেন্ট মাউন্টে একটি ইনপুট ফোকাস করতে, বা একটি কম্পোনেন্টে একটি 3য় পক্ষের লাইব্রেরি শুরু করতে।

## Accessing the Refs {#accessing-the-refs}

<div class="composition-api">

Composition API এর সাথে রেফারেন্স পেতে, আমরা [`useTemplateRef()`](/api/composition-api-helpers#usetemplateref) <sup class="vt-badge" data-text="3.5+" /> ব্যবহার করতে পারি সাহায্যকারী:

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'

// the first argument must match the ref value in the template
const input = useTemplateRef('my-input')

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="my-input" />
</template>
```

TypeScript ব্যবহার করার সময়, Vue-এর IDE সমর্থন এবং `vue-tsc` স্বয়ংক্রিয়ভাবে `input.value`-এর ধরন অনুমান করবে কোন কম্পোনেন্ট বা কম্পোনেন্টে মিলিত `ref` অ্যাট্রিবিউট ব্যবহার করা হয়েছে তার উপর ভিত্তি করে।

<details>
<summary>৩.৫ এর আগে ব্যবহার</summary>

3.5 এর আগের সংস্করণগুলিতে যেখানে `useTemplateRef()` চালু করা হয়নি, আমাদের একটি নাম দিয়ে একটি রেফ ঘোষণা করতে হবে যা টেমপ্লেট রেফ অ্যাট্রিবিউটের মানের সাথে মেলে:

```vue
<script setup>
import { ref, onMounted } from 'vue'

// declare a ref to hold the element reference
// the name must match template ref value
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

যদি `<script setup>` ব্যবহার না করেন, তাহলে নিশ্চিত করুন যে `setup()` থেকে রেফটিও ফেরত দিতে হবে:

```js{6}
export default {
  setup() {
    const input = ref(null)
    // ...
    return {
      input
    }
  }
}
```

</details>

</div>
<div class="options-api">

ফলাফলের রেফটি `this.$refs`-এ উন্মোচিত হয়:

```vue
<script>
export default {
  mounted() {
    this.$refs.input.focus()
  }
}
</script>

<template>
  <input ref="input" />
</template>
```

</div>

মনে রাখবেন যে আপনি শুধুমাত্র রেফারেন্স অ্যাক্সেস করতে পারবেন **কম্পোনেন্ট মাউন্ট করার পরে।** আপনি যদি একটি টেমপ্লেট এক্সপ্রেশনে <span class="options-api">`$refs.input`</span><span class="composition-api">`ইনপুট`</span> অ্যাক্সেস করার চেষ্টা করেন, তাহলে তা হবে প্রথম রেন্ডারে <span class="options-api">`undefined`</span><span class="composition-api">`null`</span> কারণ প্রথম রেন্ডার না হওয়া পর্যন্ত কম্পোনেন্টটির অস্তিত্ব নেই!

<div class="composition-api">

আপনি যদি একটি টেমপ্লেট রেফের পরিবর্তনগুলি দেখার চেষ্টা করছেন, তাহলে রেফের `null` মান আছে এমন ক্ষেত্রে অ্যাকাউন্ট নিশ্চিত করুন:

```js
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // not mounted yet, or the element was unmounted (e.g. by v-if)
  }
})
```

আরো দেখুন: [Typing Template Refs](/guide/typescript/composition-api#typing-template-refs) <sup class="vt-badge ts" />

</div>

## Refs inside `v-for` {#refs-inside-v-for}

> Requires v3.5 or above

<div class="composition-api">

যখন `v-for`-এর ভিতরে `ref` ব্যবহার করা হয়, তখন সংশ্লিষ্ট রেফটিতে একটি অ্যারে মান থাকা উচিত, যা মাউন্টের পরে কম্পোনেন্টগুলির সাথে পপুলেট করা হবে:

```vue
<script setup>
import { ref, useTemplateRef, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = useTemplateRef('items')

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[Try it in the Playground](https://play.vuejs.org/#eNp9UsluwjAQ/ZWRLwQpDepyQoDUIg6t1EWUW91DFAZq6tiWF4oU5d87dtgqVRyyzLw3b+aN3bB7Y4ptQDZkI1dZYTw49MFMuBK10dZDAxZXOQSHC6yNLD3OY6zVsw7K4xJaWFldQ49UelxxVWnlPEhBr3GszT6uc7jJ4fazf4KFx5p0HFH+Kme9CLle4h6bZFkfxhNouAIoJVqfHQSKbSkDFnVpMhEpovC481NNVcr3SaWlZzTovJErCqgydaMIYBRk+tKfFLC9Wmk75iyqg1DJBWfRxT7pONvTAZom2YC23QsMpOg0B0l0NDh2YjnzjpyvxLrYOK1o3ckLZ5WujSBHr8YL2gxnw85lxEop9c9TynkbMD/kqy+svv/Jb9wu5jh7s+jQbpGzI+ZLu0byEuHZ+wvt6Ays9TJIYl8A5+i0DHHGjvYQ1JLGPuOlaR/TpRFqvXCzHR2BO5iKg0Zmm/ic0W2ZXrB+Gve2uEt1dJKs/QXbwePE)

<details>
<summary>৩.৫ এর আগে ব্যবহার</summary>

In versions before 3.5 where `useTemplateRef()` was not introduced, we need to declare a ref with a name that matches the template ref attribute's value. The ref should also contain an array value:

```vue
<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
```

</details>

</div>
<div class="options-api">

যখন `v-for`-এর ভিতরে `ref` ব্যবহার করা হয়, তখন রেফ মানটি সংশ্লিষ্ট কম্পোনেন্ট সমন্বিত একটি অ্যারে হবে:

```vue
<script>
export default {
  data() {
    return {
      list: [
        /* ... */
      ]
    }
  },
  mounted() {
    console.log(this.$refs.items)
  }
}
</script>

<template>
  <ul>
    <li v-for="item in list" ref="items">
      {{ item }}
    </li>
  </ul>
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNpFjk0KwjAQha/yCC4Uaou6kyp4DuOi2KkGYhKSiQildzdNa4WQmTc/37xeXJwr35HEUdTh7pXjszT0cdYzWuqaqBm9NEDbcLPeTDngiaM3PwVoFfiI667AvsDhNpWHMQzF+L9sNEztH3C3JlhNpbaPNT9VKFeeulAqplfY5D1p0qurxVQSqel0w5QUUEedY8q0wnvbWX+SYgRAmWxIiuSzm4tBinkc6HvkuSE7TIBKq4lZZWhdLZfE8AWp4l3T)

</div>

এটা উল্লেখ করা উচিত যে রেফ অ্যারে সোর্স অ্যারের মতো একই অর্ডারের নিশ্চয়তা **না** দেয়।

## Function Refs {#function-refs}

একটি স্ট্রিং কী এর পরিবর্তে, `ref` অ্যাট্রিবিউটটি একটি ফাংশনের সাথেও আবদ্ধ হতে পারে, যা প্রতিটি কম্পোনেন্ট আপডেটে কল করা হবে এবং আপনাকে কম্পোনেন্টের রেফারেন্স কোথায় সংরক্ষণ করতে হবে সে সম্পর্কে সম্পূর্ণ ফ্লেক্সিবল দেয়। ফাংশন প্রথম যুক্তি হিসাবে কম্পোনেন্ট রেফারেন্স গ্রহণ করে:

```vue-html
<input :ref="(el) => { /* assign el to a property or ref */ }">
```

মনে রাখবেন আমরা একটি ডায়নামিক `:ref` বাইন্ডিং ব্যবহার করছি যাতে আমরা এটিকে রেফ নামের স্ট্রিংয়ের পরিবর্তে একটি ফাংশন পাস করতে পারি। যখন কম্পোনেন্টটি আনমাউন্ট করা হয়, আর্গুমেন্টটি হবে `null`। আপনি, অবশ্যই, ইনলাইন ফাংশনের পরিবর্তে একটি পদ্ধতি ব্যবহার করতে পারেন।

## Ref on Component {#ref-on-component}

> এই বিভাগটি [Components](/guide/essentials/component-basics) সম্পর্কে জ্ঞান গ্রহণ করে। নির্দ্বিধায় এটি এড়িয়ে যান এবং পরে ফিরে আসুন।

`ref` একটি child কম্পোনেন্টেও ব্যবহার করা যেতে পারে। এই ক্ষেত্রে রেফারেন্স একটি কম্পোনেন্ট উদাহরণ যে হবে:

<div class="composition-api">

```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'
import Child from './Child.vue'

const childRef = useTemplateRef('child')

onMounted(() => {
  // childRef.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

<details>
<summary>৩.৫ এর আগে ব্যবহার</summary>

```vue
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```

</details>

</div>
<div class="options-api">

```vue
<script>
import Child from './Child.vue'

export default {
  components: {
    Child
  },
  mounted() {
    // this.$refs.child will hold an instance of <Child />
  }
}
</script>

<template>
  <Child ref="child" />
</template>
```

</div>

<span class="composition-api">যদি চাইল্ড কম্পোনেন্ট Options API ব্যবহার করে বা `<script setup>` ব্যবহার না করে, তাহলে</span><span class="options-api">দি</span> রেফারেন্স করা উদাহরণ চাইল্ড কম্পোনেন্টের 'এই'-এর সাথে অভিন্ন হবে, যার মানে প্যারেন্ট কম্পোনেন্টের চাইল্ড কম্পোনেন্টের প্রতিটি কম্পিউটেড প্রপার্টি এবং পদ্ধতিতে সম্পূর্ণ অ্যাক্সেস থাকবে। এটি পিতামাতা এবং সন্তানের মধ্যে দৃঢ়ভাবে সংযুক্ত বাস্তবায়নের বিশদ তৈরি করা সহজ করে তোলে, তাই কম্পোনেন্ট রেফ শুধুমাত্র যখন একেবারে প্রয়োজন হয় তখনই ব্যবহার করা উচিত - বেশিরভাগ ক্ষেত্রে, আপনাকে স্ট্যান্ডার্ড প্রপস ব্যবহার করে অভিভাবক/ চাইল্ড মিথস্ক্রিয়া প্রয়োগ করার চেষ্টা করা উচিত এবং প্রথমে ইন্টারফেস নির্গত করার চেষ্টা করা উচিত।

<div class="composition-api">

এখানে একটি ব্যতিক্রম হল যে `<স্ক্রিপ্ট সেটআপ>` ব্যবহার করা কম্পোনেন্টগুলি **ডিফল্টরূপে ব্যক্তিগত**: একটি অভিভাবক কম্পোনেন্ট যা `<স্ক্রিপ্ট সেটআপ>` ব্যবহার করে একটি  চাইল্ড কম্পোনেন্টকে উল্লেখ করে কোনো কিছু অ্যাক্সেস করতে সক্ষম হবে না যদি না  চাইল্ড কম্পোনেন্ট পছন্দ করে 'defineExpose' ম্যাক্রো ব্যবহার করে একটি সর্বজনীন ইন্টারফেস প্রকাশ করুন:

```vue
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// Compiler macros, such as defineExpose, don't need to be imported
defineExpose({
  a,
  b
})
</script>
```

যখন একজন অভিভাবক টেমপ্লেট রেফের মাধ্যমে এই কম্পোনেন্টটির একটি দৃষ্টান্ত পান, তখন পুনরুদ্ধারকৃত দৃষ্টান্তটি `{ a: number , b: number  }` আকারের হবে (রেফগুলি স্বয়ংক্রিয়ভাবে স্বাভাবিক উদাহরণের মতোই খুলে ফেলা হয়)।

মনে রাখবেন যে কোনো অপেক্ষমাণ অপারেশনের আগে defineExpose কল করতে হবে। অন্যথায়, অপেক্ষমান অপারেশনের পরে উন্মুক্ত বৈশিষ্ট্য এবং পদ্ধতিগুলি অ্যাক্সেসযোগ্য হবে না।

আরও দেখুন: [টাইপিং কম্পোনেন্ট টেমপ্লেট রেফস](/guide/typescript/composition-api#typing-component-template-refs) <sup class="vt-badge ts" />

</div>
<div class="options-api">

`expose` option একটি child উদাহরণে অ্যাক্সেস সীমিত করতে ব্যবহার করা যেতে পারে:

```js
export default {
  expose: ['publicData', 'publicMethod'],
  data() {
    return {
      publicData: 'foo',
      privateData: 'bar'
    }
  },
  methods: {
    publicMethod() {
      /* ... */
    },
    privateMethod() {
      /* ... */
    }
  }
}
```

উপরের উদাহরণে, টেমপ্লেট রেফের মাধ্যমে এই কম্পোনেন্টটি উল্লেখ করা একজন অভিভাবক শুধুমাত্র `publicData` এবং `publicMethod` অ্যাক্সেস করতে সক্ষম হবেন।

</div>
