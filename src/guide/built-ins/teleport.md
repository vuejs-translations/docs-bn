# Teleport {#teleport}

 <VueSchoolLink href="https://vueschool.io/lessons/vue-3-teleport" title="বিনামূল্যে Vue.js টেলিপোর্ট পাঠ"/>

`<Teleport>` হল একটি অন্তর্নির্মিত কম্পোনেন্ট যা আমাদেরকে একটি কম্পোনেন্টের টেমপ্লেটের একটি অংশকে একটি DOM নোডে "টেলিপোর্ট" করতে দেয় যা সেই কম্পোনেন্টের DOM অনুক্রমের বাইরে বিদ্যমান।

## Basic Usage {#basic-usage}

কখনও কখনও আমরা নিম্নলিখিত পরিস্থিতিতে যেতে পারি: একটি কম্পোনেন্টের টেমপ্লেটের একটি অংশ যৌক্তিকভাবে এটির অন্তর্গত, কিন্তু একটি ভিজ্যুয়াল দৃষ্টিকোণ থেকে, এটি Vue অ্যাপ্লিকেশনের বাইরে DOM-এর অন্য কোথাও প্রদর্শিত হওয়া উচিত।

এটির সবচেয়ে সাধারণ উদাহরণ হল একটি পূর্ণ-স্ক্রিন মডেল তৈরি করার সময়। আদর্শভাবে, আমরা চাই যে মডেলের বোতাম এবং মডেলটি একই কম্পোনেন্টের মধ্যে থাকুক, যেহেতু তারা উভয়ই মডেলের খোলা / বন্ধ অবস্থার সাথে সম্পর্কিত। কিন্তু যে মানে মোডালটি বোতামের সাথে রেন্ডার করা হবে, অ্যাপ্লিকেশনের DOM অনুক্রমের মধ্যে গভীরভাবে নেস্ট করা হবে। CSS এর মাধ্যমে মোডেল অবস্থান করার সময় এটি কিছু জটিল সমস্যা তৈরি করতে পারে।

নিম্নলিখিত HTML গঠন বিবেচনা করুন.

```vue-html
<div class="outer">
  <h3>Vue Teleport Example</h3>
  <div>
    <MyModal />
  </div>
</div>
```

এবং এখানে `<MyModal>` এর বাস্তবায়ন:

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const open = ref(false)
</script>

<template>
  <button @click="open = true">Open Modal</button>

  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      open: false
    }
  }
}
</script>

<template>
  <button @click="open = true">Open Modal</button>

  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
}
</style>
```

</div>

 কম্পোনেন্টটিতে রয়েছে একটি `<button>` মোডাল খোলার ট্রিগার করার জন্য, এবং একটি `<div>` রয়েছে `.modal` এর একটি ক্লাস সহ, যেটিতে মোডালের বিষয়অবজেক্ট থাকবে এবং স্ব-বন্ধ করার জন্য একটি button থাকবে।

প্রাথমিক HTML কাঠামোর ভিতরে এই কম্পোনেন্টটি ব্যবহার করার সময়, বেশ কয়েকটি সম্ভাব্য সমস্যা রয়েছে:

- `position: fixed` শুধুমাত্র ভিউপোর্টের সাপেক্ষে কম্পোনেন্টটিকে রাখে যখন কোনো ancestor কম্পোনেন্টের `transform`, `perspective` বা `filter` বৈশিষ্ট্য সেট না থাকে। উদাহরণস্বরূপ, যদি আমরা একটি CSS ট্রান্সফর্মের সাথে ancestor  `<div class="outer">`কে অ্যানিমেট করতে চাই, তাহলে এটি মডেল বিন্যাসকে ভেঙে দিবে!

- মডেলের `z-index` এর কম্পোনেন্টগুলি দ্বারা সীমাবদ্ধ। যদি আরেকটি কম্পোনেন্ট থাকে যা `<div class="outer">` এর সাথে ওভারল্যাপ করে এবং উচ্চতর `z-index` থাকে, তাহলে এটি আমাদের মডেলকে কভার করবে।

`<Teleport>` আমাদের নেস্টেড DOM কাঠামো থেকে বেরিয়ে আসার অনুমতি দিয়ে এগুলোর চারপাশে কাজ করার একটি পরিষ্কার উপায় প্রদান করে। চলুন `<Teleport>` ব্যবহার করতে `<MyModal>` পরিবর্তন করি:

```vue-html{3,8}
<button @click="open = true">Open Modal</button>

<Teleport to="body">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>
```

`<Teleport>`-এর `to` লক্ষ্য একটি CSS নির্বাচক স্ট্রিং বা একটি প্রকৃত DOM নোড আশা করে। এখানে, আমরা মূলত Vue কে বলছি "**teleport** এই template fragment **to** **`body`** tag"।

আপনি নীচের বোতামে ক্লিক করতে পারেন এবং আপনার ব্রাউজারের devtools এর মাধ্যমে `<body>` ট্যাগ পরিদর্শন করতে পারেন:

<script setup>
import { ref } from 'vue'
const open = ref(false)
</script>

<div class="demo">
  <button @click="open = true">Open Modal</button>
  <ClientOnly>
    <Teleport to="body">
      <div v-if="open" class="demo modal-demo">
        <p style="margin-bottom:20px">Hello from the modal!</p>
        <button @click="open = false">Close</button>
      </div>
    </Teleport>
  </ClientOnly>
</div>

<style>
.modal-demo {
  position: fixed;
  z-index: 999;
  top: 20%;
  left: 50%;
  width: 300px;
  margin-left: -150px;
  background-color: var(--vt-c-bg);
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}
</style>

অ্যানিমেটেড মডেল তৈরি করতে আপনি `<Teleport>` এর সাথে [`<Transition>`](./transition) একত্রিত করতে পারেন - [এখানে উদাহরণ](/examples/#modal) দেখুন।

:::tip
যখন `<Teleport>` কম্পোনেন্ট মাউন্ট করা হয় তখন টেলিপোর্ট `to` টার্গেটকে অবশ্যই DOM-এ থাকতে হবে। আদর্শভাবে, এটি সম্পূর্ণ Vue অ্যাপ্লিকেশনের বাইরে একটি কম্পোনেন্ট হওয়া উচিত। যদি Vue দ্বারা রেন্ডার করা অন্য একটি কম্পোনেন্টকে টার্গেট করা হয়, তাহলে আপনাকে নিশ্চিত করতে হবে যে কম্পোনেন্টটি `<Teleport>` এর আগে মাউন্ট করা আছে।
:::

## Using with Components {#using-with-components}

`<Teleport>` শুধুমাত্র রেন্ডার করা DOM কাঠামোকে পরিবর্তন করে - এটি কম্পোনেন্টগুলির যৌক্তিক অনুক্রমকে প্রভাবিত করে না। অর্থাৎ, যদি `<Teleport>` একটি কম্পোনেন্ট থাকে, তাহলে সেই কম্পোনেন্টটি `<Teleport>` ধারণকারী প্যারেন্ট কম্পোনেন্টের লজিক্যাল চাইল্ড হিসেবে থাকবে। প্রপস পাসিং এবং ইভেন্ট এমিটিং একইভাবে কাজ করতে থাকবে।

এর মানে হল যে একটি অভিভাবক কম্পোনেন্ট থেকে ইনজেকশনগুলি প্রত্যাশিতভাবে কাজ করে এবং প্রকৃত সামগ্রী যেখানে স্থানান্তরিত হয়েছে সেখানে স্থাপন করার পরিবর্তে, Vue Devtools-এ মূল কম্পোনেন্টের নীচে  চাইল্ড কম্পোনেন্টটি নেস্ট করা হবে৷

## Disabling Teleport {#disabling-teleport}

কিছু ক্ষেত্রে, আমরা শর্তসাপেক্ষে `<Teleport>` অক্ষম করতে চাই। উদাহরণস্বরূপ, আমরা ডেস্কটপের জন্য একটি ওভারলে হিসাবে একটি কম্পোনেন্ট রেন্ডার করতে চাই, কিন্তু মোবাইলে ইনলাইন। `<Teleport>` `disabled` প্রপ সমর্থন করে যা গতিশীলভাবে টগল করা যেতে পারে:

```vue-html
<Teleport :disabled="isMobile">
  ...
</Teleport>
```

যেখানে `isMobile` অবস্থা মিডিয়া ক্যোয়ারী পরিবর্তন সনাক্ত করে গতিশীলভাবে আপডেট করা যেতে পারে।

## Multiple Teleports on the Same Target {#multiple-teleports-on-the-same-target}

একটি সাধারণ ব্যবহারের ক্ষেত্রে একটি পুনঃব্যবহারযোগ্য `<Modal>` কম্পোনেন্ট হবে, একই সময়ে একাধিক দৃষ্টান্ত সক্রিয় হওয়ার সম্ভাবনা সহ। এই ধরনের দৃশ্যের জন্য, একাধিক `<Teleport>` কম্পোনেন্ট একই টার্গেট এলিমেন্টে তাদের বিষয়অবজেক্ট মাউন্ট করতে পারে। অর্ডারটি একটি সাধারণ সংযোজন হবে - পরবর্তী মাউন্টগুলি লক্ষ্য কম্পোনেন্টের মধ্যে আগেরগুলির পরে অবস্থিত হবে।

নিম্নলিখিত ব্যবহার দেওয়া:

```vue-html
<Teleport to="#modals">
  <div>A</div>
</Teleport>
<Teleport to="#modals">
  <div>B</div>
</Teleport>
```

রেন্ডার করা ফলাফল হবে:

```html
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

---

**সম্পর্কিত**

- [`<Teleport>` API রেফারেন্স](/api/built-in-components#teleport)
- [SSR-এ টেলিপোর্ট হ্যান্ডলিং](/guide/scaling-up/ssr#teleports)
