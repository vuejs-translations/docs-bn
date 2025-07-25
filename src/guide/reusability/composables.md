# Composables {#composables}

<script setup>
import { useMouse } from './mouse'
const { x, y } = useMouse()
</script>

:::tip
এই বিভাগটি কম্পোজিশন এপিআই এর প্রাথমিক জ্ঞান গ্রহণ করে। আপনি যদি শুধুমাত্র অপশন এপিআই এর সাথে Vue শিখছেন, তাহলে আপনি কম্পোজিশন এপিআই (বাম সাইডবারের উপরে টগলটি ব্যবহার করে) এপিআই প্রেফারেন্স সেট করতে পারেন এবং [রিঅ্যাক্টিভিটি ফান্ডামেন্টাল](/guide/essentials/reactivity-fundamentals) পুনরায় পড়তে পারেন ) এবং [লাইফসাইকেল হুকস](/guide/essentials/lifecycle) অধ্যায়।
:::

## What is a "Composable"? {#what-is-a-composable}

Vue অ্যাপ্লিকেশনের পরিপ্রেক্ষিতে, একটি "composable" হল একটি ফাংশন যা **stateful logic** কে এনক্যাপসুলেট এবং পুনঃব্যবহারের জন্য Vue-এর কম্পোজিশন এপিআই ব্যবহার করে।

ফ্রন্টএন্ড অ্যাপ্লিকেশন তৈরি করার সময়, আমাদের প্রায়ই সাধারণ কাজের জন্য যুক্তি পুনরায় ব্যবহার করতে হবে। উদাহরণস্বরূপ, আমাদের অনেক জায়গায় তারিখগুলি ফর্ম্যাট করতে হতে পারে, তাই আমরা এটির জন্য একটি পুনঃব্যবহারযোগ্য ফাংশন বের করি। এই ফরম্যাটার ফাংশনটি **stateless logic** কে এনক্যাপসুলেট করে: এটি কিছু ইনপুট নেয় এবং অবিলম্বে প্রত্যাশিত আউটপুট প্রদান করে। স্টেটলেস লজিক পুনঃব্যবহারের জন্য অনেক লাইব্রেরি আছে - উদাহরণস্বরূপ [lodash](https://lodash.com/) এবং [date-fns](https://date-fns.org/), যা আপনি শুনে থাকবেন এর

বিপরীতে, রাষ্ট্রীয় যুক্তিতে এমন অবস্থা পরিচালনা করা জড়িত যা সময়ের সাথে সাথে পরিবর্তিত হয়। একটি সাধারণ উদাহরণ একটি পৃষ্ঠায় মাউসের বর্তমান অবস্থান ট্র্যাক করা হবে। বাস্তব-বিশ্বের পরিস্থিতিতে, এটি আরও জটিল যুক্তি হতে পারে যেমন স্পর্শ অঙ্গভঙ্গি বা ডাটাবেসের সাথে সংযোগের স্থিতি।

## Mouse Tracker Example {#mouse-tracker-example}

যদি আমরা সরাসরি একটি কম্পোনেন্টের ভিতরে কম্পোজিশন API ব্যবহার করে মাউস ট্র্যাকিং কার্যকারিতা বাস্তবায়ন করি, তাহলে এটি দেখতে এরকম হবে:

```vue [MouseComponent.vue]
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const x = ref(0)
const y = ref(0)

function update(event) {
  x.value = event.pageX
  y.value = event.pageY
}

onMounted(() => window.addEventListener('mousemove', update))
onUnmounted(() => window.removeEventListener('mousemove', update))
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

কিন্তু আমরা যদি একই যুক্তিকে একাধিক কম্পোনেন্টে পুনরায় ব্যবহার করতে চাই? আমরা একটি কম্পোজেবল ফাংশন হিসাবে একটি বহিরাগত ফাইলে যুক্তি বের করতে পারি:

```js [mouse.js]
import { ref, onMounted, onUnmounted } from 'vue'

// by convention, composable function names start with "use"
export function useMouse() {
  // state encapsulated and managed by the composable
  const x = ref(0)
  const y = ref(0)

  // a composable can update its managed state over time.
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // a composable can also hook into its owner component's
  // lifecycle to setup and teardown side effects.
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // expose managed state as return value
  return { x, y }
}
```

এবং এইভাবে এটি কম্পোনেন্টগুলিতে ব্যবহার করা যেতে পারে:

```vue [MouseComponent.vue]
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

<div class="demo">
  Mouse position is at: {{ x }}, {{ y }}
</div>

[চেষ্টা করুন](https://play.vuejs.org/#eNqNkj1rwzAQhv/KocUOGKVzSAIdurVjoQUvJj4XlfgkJNmxMfrvPcmJkkKHLrbu69H7SlrEszFyHFDsxN6drDIeHPrBHGtSvdHWwwKDwzfNHwjQWd1DIbd9jOW3K2qq6aTJxb6pgpl7Dnmg3NS0365YBnLgsTfnxiNHACvUaKe80gTKQeN3sDAIQqjignEhIvKYqMRta1acFVrsKtDEQPLYxuU7cV8Msmg2mdTilIa6gU5p27tYWKKq1c3ENphaPrGFW25+yMXsHWFaFlfiiOSvFIBJjs15QJ5JeWmaL/xYS/Mfpc9YYrPxl52ULOpwhIuiVl9k07Yvsf9VOY+EtizSWfR6xKK6itgkvQ/+fyNs6v4XJXIsPwVL+WprCiL8AEUxw5s=)

আমরা দেখতে পাচ্ছি, মূল যুক্তিটি অভিন্ন রয়ে গেছে - আমাদের যা করতে হবে তা হল এটিকে একটি বাহ্যিক ফাংশনে স্থানান্তরিত করা এবং যে অবস্থাটি প্রকাশ করা উচিত তা ফিরিয়ে দেওয়া। ঠিক একটি কম্পোনেন্টের ভিতরের মতো, আপনি কম্পোজেবলগুলিতে [Composition API functions](/api/#composition-api) এর সম্পূর্ণ পরিসর ব্যবহার করতে পারেন। একই `useMouse()` কার্যকারিতা এখন যেকোনো কম্পোনেন্টে ব্যবহার করা যেতে পারে।

যদিও কম্পোজেবল সম্পর্কে শীতল অংশটি হল যে আপনি সেগুলিকে নেস্ট করতে পারেন: একটি কম্পোজেবল ফাংশন এক বা একাধিক অন্যান্য কম্পোজেবল ফাংশনকে কল করতে পারে। এটি আমাদেরকে ছোট, বিচ্ছিন্ন একক ব্যবহার করে জটিল যুক্তি রচনা করতে সক্ষম করে, যেভাবে আমরা কম্পোনেন্ট ব্যবহার করে একটি সম্পূর্ণ অ্যাপ্লিকেশন রচনা করি। প্রকৃতপক্ষে, এই কারণেই আমরা এপিআইগুলির সংগ্রহকে কল করার সিদ্ধান্ত নিয়েছি যা এই প্যাটার্নটিকে Composition API তৈরি করে।

উদাহরণস্বরূপ, আমরা একটি DOM ইভেন্ট শ্রোতাকে তার নিজস্ব কম্পোজেবলে যুক্ত এবং সরানোর যুক্তি বের করতে পারি:

```js [event.js]
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
  // if you want, you can also make this
  // support selector strings as target
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

এবং এখন আমাদের `useMouse()` কম্পোজেবলকে সরলীকৃত করা যেতে পারে:

```js{2,8-11} [mouse.js]
import { ref } from 'vue'
import { useEventListener } from './event'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  useEventListener(window, 'mousemove', (event) => {
    x.value = event.pageX
    y.value = event.pageY
  })

  return { x, y }
}
```

:::tip
প্রতিটি কম্পোনেন্ট ইন্সট্যান্স যাকে `useMouse()` কল করে তা `x` এবং `y` অবস্থার নিজস্ব কপি তৈরি করবে যাতে তারা একে অপরের সাথে হস্তক্ষেপ না করে। আপনি যদি কম্পোনেন্টগুলির মধ্যে ভাগ করা অবস্থা পরিচালনা করতে চান তবে [স্টেট ম্যানেজমেন্ট](/guide/scaling-up/state-management) অধ্যায়টি পড়ুন।
:::

## Async State Example {#async-state-example}

`useMouse()` কম্পোজেবল কোনো আর্গুমেন্ট নেয় না, তাই আসুন আরেকটি উদাহরণ দেখি যা একটি ব্যবহার করে। অ্যাসিঙ্ক ডেটা আনার সময়, আমাদের প্রায়শই বিভিন্ন অবস্থা পরিচালনা করতে হয়: লোডিং, সাফল্য এবং ত্রুটি:

```vue
<script setup>
import { ref } from 'vue'

const data = ref(null)
const error = ref(null)

fetch('...')
  .then((res) => res.json())
  .then((json) => (data.value = json))
  .catch((err) => (error.value = err))
</script>

<template>
  <div v-if="error">Oops! Error encountered: {{ error.message }}</div>
  <div v-else-if="data">
    Data loaded:
    <pre>{{ data }}</pre>
  </div>
  <div v-else>Loading...</div>
</template>
```

ডেটা আনার জন্য প্রয়োজন এমন প্রতিটি কম্পোনেন্টে এই প্যাটার্নটি পুনরাবৃত্তি করা ক্লান্তিকর হবে। আসুন এটিকে কম্পোজেবলে বের করি:

```js [fetch.js]
import { ref } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  fetch(url)
    .then((res) => res.json())
    .then((json) => (data.value = json))
    .catch((err) => (error.value = err))

  return { data, error }
}
```

এখন আমাদের কম্পোনেন্টে আমরা শুধু করতে পারি:

```vue
<script setup>
import { useFetch } from './fetch.js'

const { data, error } = useFetch('...')
</script>
```

### Accepting Reactive State {#accepting-reactive-state}

`useFetch()` ইনপুট হিসাবে একটি স্ট্যাটিক ইউআরএল স্ট্রিং নেয় - তাই এটি শুধুমাত্র একবার আনয়ন সম্পাদন করে এবং তারপর সম্পন্ন হয়। যখনই ইউআরএল পরিবর্তিত হয় তখন আমরা যদি এটি পুনরায় আনতে চাই? এটি অর্জন করার জন্য, আমাদের কম্পোজেবল ফাংশনে প্রতিক্রিয়াশীল অবস্থা পাস করতে হবে এবং কম্পোজেবলকে এমন প্রহরী তৈরি করতে দিন যা পাস করা অবস্থা ব্যবহার করে ক্রিয়া সম্পাদন করে।

উদাহরণস্বরূপ, `useFetch()` একটি রেফারেন্স গ্রহণ করতে সক্ষম হওয়া উচিত:

```js
const url = ref('/initial-url')

const { data, error } = useFetch(url)

// this should trigger a re-fetch
url.value = '/new-url'
```

অথবা, একটি গ্রহণ করুন [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description):

```js
// re-fetch when props.id changes
const { data, error } = useFetch(() => `/posts/${props.id}`)
```

আমরা আমাদের বিদ্যমান বাস্তবায়নকে [`watchEffect()`](/api/reactivity-core.html#watcheffect) এবং [`toValue()`](/api/reactivity-utilities.html#tovalue) API-এর সাথে রিফ্যাক্টর করতে পারি:

```js{7,12} [fetch.js]
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)

  const fetchData = () => {
    // reset state before fetching..
    data.value = null
    error.value = null

    fetch(toValue(url))
      .then((res) => res.json())
      .then((json) => (data.value = json))
      .catch((err) => (error.value = err))
  }

  watchEffect(() => {
    fetchData()
  })

  return { data, error }
}
```

`toValue()` হল 3.3-এ যোগ করা একটি API। এটি রেফ বা গেটারকে মানগুলিতে স্বাভাবিক করার জন্য ডিজাইন করা হয়েছে। যদি যুক্তিটি একটি রেফ হয়, তবে এটি রেফের মান প্রদান করে; আর্গুমেন্ট যদি একটি ফাংশন হয়, তাহলে এটি ফাংশনটিকে কল করবে এবং এর রিটার্ন মান প্রদান করবে। অন্যথায়, এটি যুক্তিটিকে যেমন-ই ফিরিয়ে দেয়। এটি একইভাবে কাজ করে [`unref()`](/api/reactivity-utilities.html#unref), কিন্তু ফাংশনের জন্য বিশেষ চিকিত্সার সাথে।

লক্ষ্য করুন যে `toValue(url)` কে `watchEffect` কলব্যাকের **ভিতরে** বলা হয়। এটি নিশ্চিত করে যে `toValue()` স্বাভাবিকীকরণের সময় অ্যাক্সেস করা যেকোনো প্রতিক্রিয়াশীল নির্ভরতা পর্যবেক্ষক দ্বারা ট্র্যাক করা হয়।

`useFetch()` এর এই সংস্করণটি এখন স্ট্যাটিক ইউআরএল স্ট্রিং, রেফ এবং গেটার গ্রহণ করে, এটিকে অনেক বেশি নমনীয় করে তোলে। ওয়াচর প্রভাব অবিলম্বে চলবে এবং `toValue(url)`-এর সময় অ্যাক্সেস করা যেকোনো নির্ভরতা ট্র্যাক করবে। যদি কোন নির্ভরতা ট্র্যাক করা না হয় (যেমন url ইতিমধ্যেই একটি স্ট্রিং), প্রভাব শুধুমাত্র একবার চলে; অন্যথায়, যখনই একটি ট্র্যাক করা নির্ভরতা পরিবর্তিত হয় তখন এটি পুনরায় চালানো হবে।

এখানে [`useFetch()` এর আপডেট করা সংস্করণ](https://play.vuejs.org/#eNp9Vdtu20YQ/ZUpUUA0qpAOjL4YktCbC7Rom8BN8sSHrMihtfZql9iLZEHgv2dml6SpxMiDIWkuZ+acmR2fs1+7rjgEzG6zlaut7Dw49KHbVFruO2M9nMFiu4Ta7LvgsYEeWmv2sKCkxSwoOPwTfb2b/EU5mopHR5GVro12HrbC4UerYA2Lnfeduy3LR2d0p0SNO6MatIU/dbI2DRZUtPSmMa4kgJQuG8qkjvLF28XVaAwRb2wxz69gvZkK/UQ5xUGogBQ/ZpyhEV4sAa01lnpeTwRyApsFWvT2RO6Eea40THBMgfq6NLwlS1/pVZnUJB3ph8c98fNIvwD+MaKBzkQut2xYbYP3RsPhTWvsusokSA0/Vxn8UitZP7GFSX/+8Sz7z1W2OZ9BQt+vypQXS1R+1cgDQciW4iMrimR0wu8270znfoC7SBaJWdAeLTa3QFgxuNijc+IBIy5PPyYOjU19RDEI954/Z/UptKTy6VvqA5XD1AwLTTl/0Aco4s5lV51F5sG+VJJ+v4qxYbmkfiiKYvSvyknPbJnNtoyW+HJpj4Icd22LtV+CN5/ikC4XuNL4HFPaoGsvie3FIqSJp1WIzabl00HxkoyetEVfufhv1kAu3EnX8z0CKEtKofcGzhMb2CItAELL1SPlFMV1pwVj+GROc/vWPoc26oDgdxhfSArlLnbWaBOcOoEzIP3CgbeifqLXLRyICaDBDnVD+3KC7emCSyQ4sifspOx61Hh4Qy/d8BsaOEdkYb1sZS2FoiJKnIC6FbqhsaTVZfk8gDgK6cHLPZowFGUzAQTNWl/BUSrFbzRYHXmSdeAp28RMsI0fyFDaUJg9Spd0SbERZcvZDBRleCPdQMCPh8ARwdRRnBCTjGz5WkT0i0GlSMqixTR6VKyHmmWEHIfV+naSOETyRx8vEYwMv7pa8dJU+hU9Kz2t86ReqjcgaTzCe3oGpEOeD4uyJOcjTXe+obScHwaAi82lo9dC/q/wuyINjrwbuC5uZrS4WAQeyTN9ftOXIVwy537iecoX92kR4q/F1UvqIMsSbq6vo5XF6ekCeEcTauVDFJpuQESvMv53IBXadx3r4KqMrt0w0kwoZY5/R5u3AZejvd5h/fSK/dE9s63K3vN7tQesssnnhX1An9x3//+Hz/R9cu5NExRFf8d5zyIF7jGF/RZ0Q23P4mK3f8XLRmfhg7t79qjdSIobjXLE+Cqju/b7d6i/tHtT3MQ8VrH/Ahstp5A=), with an artificial delay and randomized error for demo purposes.

## Conventions and Best Practices {#conventions-and-best-practices}

### Naming {#naming}

এটি "ব্যবহার" দিয়ে শুরু হওয়া ক্যামেলকেস নামগুলির সাথে কম্পোজেবল ফাংশনগুলির নামকরণ একটি প্রথা।

### Input Arguments {#input-arguments}

একটি কম্পোজেবল রেফ বা গেটার আর্গুমেন্ট গ্রহণ করতে পারে এমনকি যদি এটি প্রতিক্রিয়াশীলতার জন্য তাদের উপর নির্ভর না করে। আপনি যদি অন্য ডেভেলপারদের দ্বারা ব্যবহার করা হতে পারে এমন একটি কম্পোজেবল লিখছেন, তাহলে raw মানের পরিবর্তে রেফ বা গেটার হওয়ার ক্ষেত্রে ইনপুট আর্গুমেন্টগুলিকে পরিচালনা করা একটি ভাল ধারণা। [`toValue()`](/api/reactivity-utilities#tovalue) ইউটিলিটি ফাংশন এই উদ্দেশ্যে কাজে আসবে:

```js
import { toValue } from 'vue'

function useFeature(maybeRefOrGetter) {
  // If maybeRefOrGetter is a ref or a getter,
  // its normalized value will be returned.
  // Otherwise, it is returned as-is.
  const value = toValue(maybeRefOrGetter)
}
```

যদি আপনার কম্পোজেবল প্রতিক্রিয়াশীল প্রভাব তৈরি করে যখন ইনপুট একটি রেফ বা গেটার হয়, তাহলে নিশ্চিত করুন যে হয় স্পষ্টভাবে রেফ/গেটারকে `ওয়াচ()` দিয়ে দেখুন, অথবা `ওয়াচ ইফেক্ট()` এর ভিতরে `toValue()` কল করুন যাতে এটি সঠিকভাবে ট্র্যাক করা হয়।

[useFetch() বাস্তবায়ন পূর্বে আলোচনা করা হয়েছে](#accepting-reactive-state) একটি কম্পোজেবলের একটি সুনির্দিষ্ট উদাহরণ প্রদান করে যা রেফ, গেটার এবং প্লেইন মানকে ইনপুট আর্গুমেন্ট হিসেবে গ্রহণ করে।

### Return Values {#return-values}

আপনি সম্ভবত লক্ষ্য করেছেন যে আমরা কম্পোজেবলগুলিতে `reactive()` এর পরিবর্তে একচেটিয়াভাবে `ref()` ব্যবহার করছি। কম্পোজেবলের জন্য প্রস্তাবিত কনভেনশন হল একটি সরল, অ-প্রতিক্রিয়াশীল অবজেক্ট যাতে একাধিক রেফ থাকে। এটি প্রতিক্রিয়াশীলতা বজায় রাখার সময় কম্পোনেন্টগুলিতে এটিকে ধ্বংস করার অনুমতি দেয়:

```js
// x and y are refs
const { x, y } = useMouse()
```

একটি কম্পোজেবল থেকে একটি প্রতিক্রিয়াশীল অবজেক্ট ফিরিয়ে আনার ফলে এই ধরনের ধ্বংসগুলি কম্পোজেবলের ভিতরে থাকা অবস্থার সাথে প্রতিক্রিয়াশীলতার সংযোগ হারাবে, যখন রেফগুলি সেই সংযোগটি ধরে রাখবে।

আপনি যদি অবজেক্ট প্রোপার্টি হিসাবে কম্পোজেবল থেকে রিটার্নড স্টেট ব্যবহার করতে পছন্দ করেন, তাহলে আপনি প্রত্যাবর্তিত অবজেক্টকে `reactive()` দিয়ে মোড়ানো করতে পারেন যাতে রেফগুলি খুলে যায়। উদাহরণ স্বরূপ:

```js
const mouse = reactive(useMouse())
// mouse.x is linked to original ref
console.log(mouse.x)
```

```vue-html
Mouse position is at: {{ mouse.x }}, {{ mouse.y }}
```

### Side Effects {#side-effects}

কম্পোজেবলগুলিতে পার্শ্ব প্রতিক্রিয়া (যেমন DOM ইভেন্ট শ্রোতা যোগ করা বা ডেটা আনা) করা ঠিক, তবে নিম্নলিখিত নিয়মগুলিতে মনোযোগ দিন:

- আপনি যদি [সার্ভার-সাইড রেন্ডারিং](/guide/scaling-up/ssr) (SSR) ব্যবহার করে এমন একটি অ্যাপ্লিকেশনে কাজ করছেন, তবে পোস্ট-মাউন্ট লাইফসাইকেল হুকগুলিতে DOM-নির্দিষ্ট পার্শ্ব প্রতিক্রিয়াগুলি সম্পাদন করতে ভুলবেন না, যেমন `onMounted()`। এই হুকগুলি শুধুমাত্র ব্রাউজারে কল করা হয়, তাই আপনি নিশ্চিত হতে পারেন যে তাদের ভিতরের কোডের DOM-এ অ্যাক্সেস আছে।

- 'onUnmounted()' এর পার্শ্বপ্রতিক্রিয়াগুলি পরিষ্কার করতে মনে রাখবেন। উদাহরণস্বরূপ, যদি একটি কম্পোজেবল একটি DOM ইভেন্ট লিসেনার সেট আপ করে, তাহলে এটি সেই শ্রোতাকে `onUnmounted()` থেকে সরিয়ে দিবে যেমনটি আমরা `useMouse()` উদাহরণে দেখেছি। একটি কম্পোজেবল ব্যবহার করা একটি ভাল ধারণা হতে পারে যা স্বয়ংক্রিয়ভাবে আপনার জন্য এটি করে, যেমন `useEventListener()` উদাহরণ।

### Usage Restrictions {#usage-restrictions}

কম্পোজেবল শুধুমাত্র `<script setup>` বা `setup()` হুকে কল করা উচিত। এই প্রেক্ষাপটে তাদের **synchronously** বলা উচিত। কিছু ক্ষেত্রে, আপনি তাদের লাইফসাইকেল হুকগুলিতেও কল করতে পারেন যেমন `onMounted()`।

এই বিধিনিষেধগুলি গুরুত্বপূর্ণ কারণ এইগুলি হল সেই প্রসঙ্গ যেখানে Vue বর্তমান সক্রিয় কম্পোনেন্টের উদাহরণ নির্ধারণ করতে সক্ষম। একটি সক্রিয় কম্পোনেন্ট উদাহরণে অ্যাক্সেস প্রয়োজন যাতে:

1. লাইফসাইকেল হুকগুলি এতে নিবন্ধিত হতে পারে।

2. কম্পিউটেড প্রপার্টি এবং প্রহরীদের এটির সাথে লিঙ্ক করা যেতে পারে, যাতে মেমরি লিক রোধ করার জন্য উদাহরণটি আনমাউন্ট করা হলে সেগুলি নিষ্পত্তি করা যায়।

:::tip
`<script setup>` হল একমাত্র জায়গা যেখানে আপনি `await` ব্যবহার করে **after** কম্পোজেবল কল করতে পারেন। অ্যাসিঙ্ক অপারেশনের পরে কম্পাইলার স্বয়ংক্রিয়ভাবে আপনার জন্য সক্রিয় উদাহরণ প্রসঙ্গ পুনরুদ্ধার করে।
:::

## Extracting Composables for Code Organization {#extracting-composables-for-code-organization}

কম্পোজেবলগুলি কেবল পুনঃব্যবহারের জন্যই নয়, কোড সংগঠিত করার জন্যও বের করা যেতে পারে। আপনার কম্পোনেন্টগুলির জটিলতা বাড়ার সাথে সাথে, আপনার কাছে এমন কম্পোনেন্টগুলি থাকতে পারে যা নেভিগেট করা এবং যুক্তি করার জন্য খুব বড়। কম্পোজিশন API আপনাকে যৌক্তিক উদ্বেগের উপর ভিত্তি করে আপনার কম্পোনেন্ট কোডকে ছোট ফাংশনে সংগঠিত করার সম্পূর্ণ নমনীয়তা দেয়:

```vue
<script setup>
import { useFeatureA } from './featureA.js'
import { useFeatureB } from './featureB.js'
import { useFeatureC } from './featureC.js'

const { foo, bar } = useFeatureA()
const { baz } = useFeatureB(foo)
const { qux } = useFeatureC(baz)
</script>
```

কিছু পরিমাণে, আপনি এই নিষ্কাশিত কম্পোজেবলগুলিকে কম্পোনেন্ট-স্কোপড পরিষেবা হিসাবে ভাবতে পারেন যা একে অপরের সাথে কথা বলতে পারে।

## Using Composables in Options API {#using-composables-in-options-api}

আপনি যদি Options API ব্যবহার করেন, তাহলে কম্পোজেবলকে অবশ্যই `setup()`-এর ভিতরে কল করতে হবে, এবং প্রত্যাবর্তিত বাইন্ডিংগুলি অবশ্যই `setup()` থেকে ফেরত দিতে হবে যাতে সেগুলি `this` এবং টেমপ্লেটের সংস্পর্শে আসে:

```js
import { useMouse } from './mouse.js'
import { useFetch } from './fetch.js'

export default {
  setup() {
    const { x, y } = useMouse()
    const { data, error } = useFetch('...')
    return { x, y, data, error }
  },
  mounted() {
    // setup() exposed properties can be accessed on `this`
    console.log(this.x)
  }
  // ...other options
}
```

## Comparisons with Other Techniques {#comparisons-with-other-techniques}

### vs. Mixins {#vs-mixins}

Vue 2 থেকে আগত ব্যবহারকারীরা [mixins](/api/options-composition#mixins) বিকল্পের সাথে পরিচিত হতে পারে, যা আমাদেরকে পুনঃব্যবহারযোগ্য ইউনিটগুলিতে কম্পোনেন্ট লজিক বের করতে দেয়। মিশ্রণের তিনটি প্রাথমিক ত্রুটি রয়েছে:

1. **Unclear source of properties**: অনেকগুলি মিক্সিন ব্যবহার করার সময়, কোন মিক্সিনের দ্বারা কোন উদাহরণের বৈশিষ্ট্যটি ইনজেকশন করা হয়েছে তা অস্পষ্ট হয়ে যায়, যার ফলে বাস্তবায়নটি সনাক্ত করা এবং কম্পোনেন্টটির আচরণ বোঝা কঠিন হয়। এই কারণেই আমরা কম্পোজেবলের জন্য refs + destructure প্যাটার্ন ব্যবহার করার পরামর্শ দিই: এটি কম্পোনেন্টগুলি ব্যবহার করার ক্ষেত্রে কম্পিউটেড প্রপার্টির উৎসকে স্পষ্ট করে তোলে।

2. **Namespace collisions**: বিভিন্ন লেখকের একাধিক মিশ্রণ সম্ভাব্যভাবে একই কম্পিউটেড প্রপার্টি কী নিবন্ধন করতে পারে, যার ফলে নামস্থানের সংঘর্ষ হয়। কম্পোজেবলের সাহায্যে, যদি বিভিন্ন কম্পোজেবল থেকে বিরোধপূর্ণ কী থাকে তাহলে আপনি ধ্বংসকৃত ভেরিয়েবলের নাম পরিবর্তন করতে পারেন।

3. **Implicit cross-mixin communication**: একাধিক মিক্সিন যেগুলি একে অপরের সাথে ইন্টারঅ্যাক্ট করতে হবে তাদের শেয়ার্ড প্রপার্টি কীগুলির উপর নির্ভর করতে হবে, যাতে সেগুলি পরোক্ষভাবে মিলিত হয়। কম্পোজেবলের সাথে, একটি কম্পোজেবল থেকে প্রত্যাবর্তিত মানগুলিকে আর্গুমেন্ট হিসাবে অন্যটিতে প্রেরণ করা যেতে পারে, ঠিক স্বাভাবিক ফাংশনের মতো।

উপরের কারণগুলির জন্য, আমরা আর Vue 3-এ মিক্সিন ব্যবহার করার পরামর্শ দিই না৷ বৈশিষ্ট্যটি শুধুমাত্র স্থানান্তর এবং পরিচিতির কারণে রাখা হয়েছে৷

### vs. Renderless Components {#vs-renderless-components}

 কম্পোনেন্ট স্লট অধ্যায়ে, আমরা স্কোপড স্লটের উপর ভিত্তি করে [রেন্ডারলেস কম্পোনেন্ট](/guide/components/slots#renderless-components) প্যাটার্ন নিয়ে আলোচনা করেছি। এমনকি আমরা রেন্ডারলেস কম্পোনেন্ট ব্যবহার করে একই মাউস ট্র্যাকিং ডেমো প্রয়োগ করেছি।

রেন্ডারলেস কম্পোনেন্টের উপর কম্পোজেবলের প্রধান সুবিধা হল যে কম্পোজেবল অতিরিক্ত কম্পোনেন্ট ইনস্ট্যান্স ওভারহেড বহন করে না। একটি সম্পূর্ণ অ্যাপ্লিকেশন জুড়ে ব্যবহার করা হলে, রেন্ডারলেস কম্পোনেন্ট প্যাটার্ন দ্বারা তৈরি অতিরিক্ত কম্পোনেন্ট ইনস্ট্যান্সের পরিমাণ একটি লক্ষণীয় কার্যক্ষমতা ওভারহেড হয়ে উঠতে পারে।

বিশুদ্ধ যুক্তি পুনরায় ব্যবহার করার সময় কম্পোজেবল ব্যবহার করার সুপারিশ করা হয় এবং যুক্তি এবং ভিজ্যুয়াল লেআউট উভয়ই পুনরায় ব্যবহার করার সময় কম্পোনেন্টগুলি ব্যবহার করা হয়।

### vs. React Hooks {#vs-react-hooks}

আপনার যদি প্রতিক্রিয়ার অভিজ্ঞতা থাকে তবে আপনি লক্ষ্য করতে পারেন যে এটি কাস্টম প্রতিক্রিয়া হুকের মতো দেখতে। কম্পোজিশন এপিআই আংশিকভাবে রিঅ্যাক্ট হুক দ্বারা অনুপ্রাণিত ছিল, এবং ভ্যু কম্পোজেবল প্রকৃতপক্ষে লজিক কম্পোজিশন ক্ষমতার পরিপ্রেক্ষিতে রিঅ্যাক্ট হুকের মতো। যাইহোক, Vue কম্পোজেবলগুলি Vue-এর সূক্ষ্ম-দানাযুক্ত প্রতিক্রিয়াশীলতা সিস্টেমের উপর ভিত্তি করে তৈরি করা হয়েছে, যা রিঅ্যাক্ট হুকের এক্সিকিউশন মডেল থেকে মৌলিকভাবে আলাদা। এটি [Composition API FAQ](/guide/extras/composition-api-faq#comparison-with-react-hooks) এ আরো বিস্তারিতভাবে আলোচনা করা হয়েছে।

## Further Reading {#further-reading}

- [Reactivity In Depth](/guide/extras/reactivity-in-depth): for a low-level understanding of how Vue's reactivity system works.
- [State Management](/guide/scaling-up/state-management): for patterns of managing state shared by multiple components.
- [Testing Composables](/guide/scaling-up/testing#testing-composables): tips on unit testing composables.
- [VueUse](https://vueuse.org/): an ever-growing collection of Vue composables. The source code is also a great learning resource.
