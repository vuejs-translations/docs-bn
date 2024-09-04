# Watchers {#watchers}

## Basic Example {#basic-example}

গণনাকৃত বৈশিষ্ট্য আমাদেরকে ডিক্লেয়ারভাবে প্রাপ্ত মান গণনা করতে দেয়। যাইহোক, এমন কিছু ক্ষেত্রে আছে যেখানে রাষ্ট্রীয় পরিবর্তনের প্রতিক্রিয়ায় আমাদের "পার্শ্ব প্রতিক্রিয়া" করতে হবে - উদাহরণস্বরূপ, DOM পরিবর্তন করা, বা অ্যাসিঙ্ক অপারেশনের ফলাফলের ভিত্তিতে রাষ্ট্রের অন্য অংশ পরিবর্তন করা।

<div class="options-api">

অপশন এপিআই এর সাথে, যখনই একটি প্রতিক্রিয়াশীল প্রপার্টি পরিবর্তন হয় তখন আমরা একটি ফাংশন ট্রিগার করতে [`ওয়াচ` বিকল্প](/api/options-state#watch) ব্যবহার করতে পারি:

```js
export default {
  data() {
    return {
      question: '',
      answer: 'Questions usually contain a question mark. ;-)',
      loading: false
    }
  },
  watch: {
    // whenever question changes, this function will run
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      this.loading = true
      this.answer = 'Thinking...'
      try {
        const res = await fetch('https://yesno.wtf/api')
        this.answer = (await res.json()).answer
      } catch (error) {
        this.answer = 'Error! Could not reach the API. ' + error
      } finally {
        this.loading = false
      }
    }
  }
}
```

```vue-html
<p>
  Ask a yes/no question:
  <input v-model="question" :disabled="loading" />
</p>
<p>{{ answer }}</p>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9VE1v2zAM/SucLnaw1D70lqUbsiKH7rB1W4++aDYdq5ElTx9xgiD/fbT8lXZFAQO2+Mgn8pH0mW2aJjl4ZCu2trkRjfucKTw22jgosOReOjhnCqDgjseL/hvAoPNGjSeAvx6tE1qtIIqWo5Er26Ih088BteCt51KeINfKcaGAT5FQc7NP4NPNYiaQmhdC7VZQcmlxMF+61yUcWu7yajVmkabQVqjwgGZmzSuudmiX4CphofQqD+ZWSAnGqz5y9I4VtmOuS9CyGA9T3QCihGu3RKhc+gJtHH2JFld+EG5Mdug2QYZ4MSKhgBd11OgqXdipEm5PKoer0Jk2kA66wB044/EF1GtOSPRUCbUnryRJosnFnK4zpC5YR7205M9bLhyUSIrGUeVcY1dpekKrdNK6MuWNiKYKXt8V98FElDxbknGxGLCpZMi7VkGMxmjzv0pz1tvO4QPcay8LULoj5RToKoTN40MCEXyEQDJTl0KFmXpNOqsUxudN+TNFzzqdJp8ODutGcod0Alg34QWwsXsaVtIjVXqe9h5bC9V4B4ebWhco7zI24hmDVSEs/yOxIPOQEFnTnjzt2emS83nYFrhcevM6nRJhS+Ys9aoUu6Av7WqoNWO5rhsh0fxownplbBqhjJEmuv0WbN2UDNtDMRXm+zfsz/bY2TL2SH1Ec8CMTZjjhqaxh7e/v+ORvieQqvaSvN8Bf6HV0veSdG5fvSoo7Su/kO1D3f13SKInuz06VHYsahzzfl0yRj+s+3dKn9O9TW7HPrPLP624lFU=)

`watch` option key হিসাবে একটি ডট-ডিলিমিটেড পাথকেও সমর্থন করে:

```js
export default {
  watch: {
    // Note: only simple paths. Expressions are not supported.
    'some.nested.key'(newValue) {
      // ...
    }
  }
}
```

</div>

<div class="composition-api">

কম্পোজিশন এপিআই-এর সাথে, যখনই প্রতিক্রিয়াশীল অবস্থার একটি অংশ পরিবর্তিত হয় তখন আমরা একটি কলব্যাক ট্রিগার করতে [`ওয়াচ` ফাংশন](/api/reactivity-core#watch) ব্যবহার করতে পারি:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')
const loading = ref(false)

// watch works directly on a ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.includes('?')) {
    loading.value = true
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    } finally {
      loading.value = false
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" :disabled="loading" />
  </p>
  <p>{{ answer }}</p>
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9U8Fy0zAQ/ZVFF9tDah96C2mZ0umhHKBAj7oIe52oUSQjyXEyGf87KytyoDC9JPa+p+e3b1cndtd15b5HtmQrV1vZeXDo++6Wa7nrjPVwAovtAgbh6w2M0Fqzg4xOZFxzXRvtPPzq0XlpNNwEbp5lRUKEdgPaVP925jnoXS+UOgKxvJAaxEVjJ+y2hA9XxUVFGdFIvT7LtEI5JIzrqjrbGozdOmikxdqTKqmIQOV6gvOkvQDhjrqGXOOQvCzAqCa9FHBzCyeuAWT7F6uUulZ9gy7PPmZFETmQjJV7oXoke972GJHY+Axkzxupt4FalhRcYHh7TDIQcqA+LTriikFIDy0G59nG+84tq+qITpty8G0lOhmSiedefSaPZ0mnfHFG50VRRkbkj1BPceVorbFzF/+6fQj4O7g3vWpAm6Ao6JzfINw9PZaQwXuYNJJuK/U0z1nxdTLT0M7s8Ec/I3WxquLS0brRi8ddp4RHegNYhR0M/Du3pXFSAJU285osI7aSuus97K92pkF1w1nCOYNlI534qbCh8tkOVasoXkV1+sjplLZ0HGN5Vc1G2IJ5R8Np5XpKlK7J1CJntdl1UqH92k0bzdkyNc8ZRWGGz1MtbMQi1esN1tv/1F/cIdQ4e6LJod0jZzPmhV2jj/DDjy94oOcZpK57Rew3wO/ojOpjJIH2qdcN2f6DN7l9nC47RfTsHg4etUtNpZUeJz5ndPPv32j9Yve6vE6DZuNvu1R2Tg==)

### Watch Source Types {#watch-source-types}

`ওয়াচ`-এর প্রথম যুক্তি বিভিন্ন ধরনের প্রতিক্রিয়াশীল "উৎস" হতে পারে: এটি একটি রেফ (গণনা করা রেফ সহ), একটি প্রতিক্রিয়াশীল বস্তু, একটি [গেটার ফাংশন](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), অথবা একাধিক উৎসের একটি অ্যারে:

```js
const x = ref(0)
const y = ref(0)

// single ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// array of multiple sources
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

মনে রাখবেন যে আপনি এই মত একটি প্রতিক্রিয়াশীল অবজেক্টর একটি কম্পিউটেড প্রপার্টি দেখতে পারবেন না:

```js
const obj = reactive({ count: 0 })

// this won't work because we are passing a number to watch()
watch(obj.count, (count) => {
  console.log(`Count is: ${count}`)
})
```

পরিবর্তে, একটি গেটার ব্যবহার করুন:

```js
// instead, use a getter:
watch(
  () => obj.count,
  (count) => {
    console.log(`Count is: ${count}`)
  }
)
```

</div>

## Deep Watchers {#deep-watchers}

<div class="options-api">

`watch` ডিফল্টরূপে অগভীর: কলব্যাক শুধুমাত্র তখনই ট্রিগার হবে যখন প্রেক্ষিত কম্পিউটেড প্রপার্টির একটি নতুন মান বরাদ্দ করা হয়েছে - এটি নেস্টেড কম্পিউটেড প্রপার্টি পরিবর্তনে ট্রিগার করবে না। আপনি যদি সমস্ত নেস্টেড মিউটেশনে কলব্যাক ফায়ার করতে চান তবে আপনাকে একটি গভীর পর্যবেক্ষণকারী ব্যবহার করতে হবে:

```js
export default {
  watch: {
    someObject: {
      handler(newValue, oldValue) {
        // Note: `newValue` will be equal to `oldValue` here
        // on nested mutations as long as the object itself
        // hasn't been replaced.
      },
      deep: true
    }
  }
}
```

</div>

<div class="composition-api">

যখন আপনি একটি প্রতিক্রিয়াশীল অবজেক্টতে সরাসরি `watch()` কল করেন, তখন এটি অন্তর্নিহিতভাবে একটি গভীর পর্যবেক্ষণকারী তৈরি করবে - কলব্যাকটি সমস্ত নেস্টেড মিউটেশনে ট্রিগার হবে:

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // fires on nested property mutations
  // Note: `newValue` will be equal to `oldValue` here
  // because they both point to the same object!
})

obj.count++
```

এটি একটি প্রাপ্তকারীর সাথে পার্থক্য করা উচিত যা একটি প্রতিক্রিয়াশীল অবজেক্ট ফেরত দেয় - পরবর্তী ক্ষেত্রে, কলব্যাক শুধুমাত্র তখনই চালু হবে যদি প্রাপ্তকারী একটি ভিন্ন অবজেক্ট ফেরত দেয়:

```js
watch(
  () => state.someObject,
  () => {
    // fires only when state.someObject is replaced
  }
)
```

তবে, আপনি স্পষ্টভাবে `deep` বিকল্পটি ব্যবহার করে দ্বিতীয় কেসটিকে গভীর পর্যবেক্ষণকারীতে জোর করতে পারেন:

```js
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // Note: `newValue` will be equal to `oldValue` here
    // *unless* state.someObject has been replaced
  },
  { deep: true }
)
```

</div>

Vue 3.5+-এ, `deep` বিকল্পটি সর্বাধিক ট্রাভার্সাল গভীরতা নির্দেশ করে এমন একটি সংখ্যাও হতে পারে - যেমন Vue একটি বস্তুর নেস্টেড বৈশিষ্ট্যগুলিকে কতটি স্তর অতিক্রম করবে।

:::warning Use with Caution
deep watch জন্য প্রেক্ষিত বস্তুর সমস্ত নেস্টেড বৈশিষ্ট্যগুলিকে অতিক্রম করা প্রয়োজন এবং বড় ডেটা স্ট্রাকচারে ব্যবহার করা হলে এটি ব্যয়বহুল হতে পারে। প্রয়োজন হলেই এটি ব্যবহার করুন এবং কর্মক্ষমতার প্রভাব সম্পর্কে সতর্ক থাকুন।
:::

## Eager Watchers {#eager-watchers}

`watch` ডিফল্টরূপে অলস: দেখা উৎস পরিবর্তন না হওয়া পর্যন্ত কলব্যাক কল করা হবে না। কিন্তু কিছু ক্ষেত্রে আমরা একই কলব্যাক লজিক সাগ্রহে চালিত করতে চাই - উদাহরণস্বরূপ, আমরা কিছু প্রাথমিক ডেটা আনতে চাই, এবং তারপরে প্রাসঙ্গিক অবস্থার পরিবর্তন হলে ডেটা পুনরায় আনতে চাই৷

<div class="options-api">

আমরা একটি `handler` ফাংশন এবং `immediate: true` বিকল্পের সাথে একটি অবজেক্ট ব্যবহার করে ঘোষণা করে অবিলম্বে একজন প্রহরীর কলব্যাক কার্যকর করতে বাধ্য করতে পারি:

```js
export default {
  // ...
  watch: {
    question: {
      handler(newQuestion) {
        // this will be run immediately on component creation.
      },
      // force eager callback execution
      immediate: true
    }
  }
  // ...
}
```

হ্যান্ডলার ফাংশনের প্রাথমিক সঞ্চালন 'তৈরি' হুকের ঠিক আগে ঘটবে। Vue ইতিমধ্যেই `data`, `computed` এবং `created` বিকল্পগুলিকে প্রক্রিয়া করেছে, তাই সেই বৈশিষ্ট্যগুলি প্রথম আহ্বানে উপলব্ধ হবে।

</div>

<div class="composition-api">

আমরা `immediate: true` বিকল্পটি পাস করে অবিলম্বে একজন পর্যবেক্ষকের কলব্যাক কার্যকর করতে বাধ্য করতে পারি:

```js
watch(
  source,
  (newValue, oldValue) => {
    // executed immediately, then again when `source` changes
  },
  { immediate: true }
)
```

</div>

## Once Watchers {#once-watchers}

- Only supported in 3.4+

যখনই ওয়াচার সোর্স পরিবর্তন হবে তখনই ওয়াচার কলব্যাক কার্যকর হবে৷ আপনি যদি সোর্স পরিবর্তনের সময় কলব্যাক শুধুমাত্র একবার ট্রিগার করতে চান, তাহলে `once: true` বিকল্পটি ব্যবহার করুন।

<div class="options-api">

```js
export default {
  watch: {
    source: {
      handler(newValue, oldValue) {
        // when `source` changes, triggers only once
      },
      once: true
    }
  }
}
```

</div>

<div class="composition-api">

```js
watch(
  source,
  (newValue, oldValue) => {
    // when `source` changes, triggers only once
  },
  { once: true }
)
```

</div>

<div class="composition-api">

## `watchEffect()` \*\* {#watcheffect}

পর্যবেক্ষক কলব্যাকের জন্য উত্স হিসাবে ঠিক একই প্রতিক্রিয়াশীল অবস্থা ব্যবহার করা সাধারণ। উদাহরণ স্বরূপ, নিম্নলিখিত কোডটি বিবেচনা করুন, যেটি রিমোট রিসোর্স লোড করার জন্য একটি প্রহরী ব্যবহার করে যখনই `todoId` রেফ পরিবর্তন হয়:

```js
const todoId = ref(1)
const data = ref(null)

watch(
  todoId,
  async () => {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    data.value = await response.json()
  },
  { immediate: true }
)
```

বিশেষ করে, লক্ষ্য করুন কিভাবে পর্যবেক্ষক দুইবার `todoId` ব্যবহার করে, একবার উৎস হিসেবে এবং তারপর আবার কলব্যাকের ভিতরে।

এটি [`watchEffect()`](/api/reactivity-core#watcheffect) দিয়ে সরলীকৃত করা যেতে পারে। `watchEffect()` আমাদের কলব্যাকের প্রতিক্রিয়াশীল নির্ভরতা স্বয়ংক্রিয়ভাবে ট্র্যাক করতে দেয়। উপরের প্রহরীটিকে এইভাবে পুনরায় লেখা যেতে পারে:

```js
watchEffect(async () => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  data.value = await response.json()
})
```

এখানে, কলব্যাক অবিলম্বে চালানো হবে, `immediate: true` নির্দিষ্ট করার প্রয়োজন নেই। এটি কার্যকর করার সময়, এটি স্বয়ংক্রিয়ভাবে নির্ভরতা হিসাবে `todoId.value` ট্র্যাক করবে (গণনা করা বৈশিষ্ট্যের মতো)। যখনই `todoId.value` পরিবর্তিত হয়, কলব্যাক আবার চালানো হবে। `watchEffect()` এর সাথে, আমাদের আর `todoId` সুস্পষ্টভাবে উৎস মান হিসেবে পাস করতে হবে না।

আপনি `watchEffect()`-এর [এই উদাহরণ](/examples/#fetching-data) এবং প্রতিক্রিয়াশীল ডেটা-ফেচিং অ্যাকশনে দেখতে পারেন।

এই ধরনের উদাহরণের জন্য, শুধুমাত্র একটি নির্ভরতার সাথে, `watchEffect()` এর সুবিধা তুলনামূলকভাবে ছোট। কিন্তু পর্যবেক্ষকদের জন্য যাদের একাধিক নির্ভরতা রয়েছে, `watchEffect()` ব্যবহার করে নির্ভরতার তালিকা ম্যানুয়ালি বজায় রাখার বোঝা সরিয়ে দেয়। উপরন্তু, যদি আপনি একটি নেস্টেড ডেটা স্ট্রাকচারে বেশ কয়েকটি বৈশিষ্ট্য দেখতে চান, তাহলে `watchEffect()` একটি গভীর পর্যবেক্ষণকারীর চেয়ে বেশি কার্যকরী প্রমাণিত হতে পারে, কারণ এটি কেবলমাত্র কলব্যাকে ব্যবহৃত বৈশিষ্ট্যগুলিকে ট্র্যাক করবে, পুনরাবৃত্তিমূলকভাবে সমস্ত ট্র্যাক করার পরিবর্তে তাদের

:::tip
`watchEffect` শুধুমাত্র তার **synchronous** সম্পাদনের সময় নির্ভরতা ট্র্যাক করে। একটি async কলব্যাকের সাথে এটি ব্যবহার করার সময়, প্রথম `await` টিক টিক করার আগে শুধুমাত্র অ্যাক্সেস করা বৈশিষ্ট্যগুলি ট্র্যাক করা হবে৷
:::

### `watch` vs. `watchEffect` {#watch-vs-watcheffect}

`watch` এবং `watchEffect` উভয়ই আমাদের প্রতিক্রিয়াশীলভাবে পার্শ্বপ্রতিক্রিয়া সম্পাদন করতে দেয়। তাদের প্রধান পার্থক্য হল যেভাবে তারা তাদের প্রতিক্রিয়াশীল নির্ভরতা ট্র্যাক করে:

- `watch` শুধুমাত্র স্পষ্টভাবে দেখা উৎস ট্র্যাক করে। এটি কলব্যাকের ভিতরে অ্যাক্সেস করা কিছু ট্র্যাক করবে না। উপরন্তু, কলব্যাক শুধুমাত্র তখনই ট্রিগার হয় যখন উৎস আসলে পরিবর্তিত হয়। `watch` নির্ভরতা ট্র্যাকিংকে পার্শ্বপ্রতিক্রিয়া থেকে আলাদা করে, কলব্যাক কখন চালু হবে তার উপর আমাদের আরও সুনির্দিষ্ট নিয়ন্ত্রণ দেয়।

- `watchEffect`, অন্যদিকে, নির্ভরতা ট্র্যাকিং এবং পার্শ্ব প্রতিক্রিয়াকে এক পর্যায়ে একত্রিত করে। এটি স্বয়ংক্রিয়ভাবে তার সিঙ্ক্রোনাস সম্পাদনের সময় অ্যাক্সেস করা প্রতিটি প্রতিক্রিয়াশীল কম্পিউটেড প্রপার্টি ট্র্যাক করে। এটি আরও সুবিধাজনক এবং সাধারণত টারসার কোডে পরিণত হয়, তবে এর প্রতিক্রিয়াশীল নির্ভরতা কম স্পষ্ট করে তোলে।

</div>

## Side Effect Cleanup {#side-effect-cleanup}

Sometimes we may perform side effects, e.g. asynchronous requests, in a watcher:

<div class="composition-api">

```js
watch(id, (newId) => {
  fetch(`/api/${newId}`).then(() => {
    // callback logic
  })
})
```

</div>
<div class="options-api">

```js
export default {
  watch: {
    id(newId) {
      fetch(`/api/${newId}`).then(() => {
        // callback logic
      })
    }
  }
}
```

</div>

But what if `id` changes before the request completes? When the previous request completes, it will still fire the callback with an ID value that is already stale. Ideally, we want to be able to cancel the stale request when `id` changes to a new value.

We can use the [`onWatcherCleanup()`](/api/reactivity-core#onwatchercleanup) <sup class="vt-badge" data-text="3.5+" /> API to register a cleanup function that will be called when the watcher is invalidated and is about to re-run:

<div class="composition-api">

```js {10-13}
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const controller = new AbortController()

  fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
    // callback logic
  })

  onWatcherCleanup(() => {
    // abort stale request
    controller.abort()
  })
})
```

</div>
<div class="options-api">

```js {12-15}
import { onWatcherCleanup } from 'vue'

export default {
  watch: {
    id(newId) {
      const controller = new AbortController()

      fetch(`/api/${newId}`, { signal: controller.signal }).then(() => {
        // callback logic
      })

      onWatcherCleanup(() => {
        // abort stale request
        controller.abort()
      })
    }
  }
}
```

</div>

Note that `onWatcherCleanup` is only supported in Vue 3.5+ and must be called during the synchronous execution of a `watchEffect` effect function or `watch` callback function: you cannot call it after an `await` statement in an async function.

Alternatively, an `onCleanup` function is also passed to watcher callbacks as the 3rd argument<span class="composition-api">, and to the `watchEffect` effect function as the first argument</span>:

<div class="composition-api">

```js
watch(id, (newId, oldId, onCleanup) => {
  // ...
  onCleanup(() => {
    // cleanup logic
  })
})

watchEffect((onCleanup) => {
  // ...
  onCleanup(() => {
    // cleanup logic
  })
})
```

</div>
<div class="options-api">

```js
export default {
  watch: {
    id(newId, oldId, onCleanup) {
      // ...
      onCleanup(() => {
        // cleanup logic
      })
    }
  }
}
```

</div>

This works in versions before 3.5. In addition, `onCleanup` passed via function argument is bound to the watcher instance so it is not subject to the synchronously constraint of `onWatcherCleanup`.

## Callback Flush Timing {#callback-flush-timing}

আপনি যখন প্রতিক্রিয়াশীল অবস্থা পরিবর্তন করেন, তখন এটি আপনার দ্বারা তৈরি Vue কম্পোনেন্ট আপডেট এবং প্রহরী কলব্যাক উভয়ই ট্রিগার করতে পারে।

কম্পোনেন্ট আপডেটের মতোই, ব্যবহারকারীর তৈরি প্রহরী কলব্যাকগুলি ডুপ্লিকেট আহ্বান এড়াতে ব্যাচ করা হয়। উদাহরণস্বরূপ, আমরা সম্ভবত চাই না যে একজন প্রহরী হাজার বার ফায়ার করুক যদি আমরা সিঙ্ক্রোনাসভাবে এক হাজার আইটেমকে দেখা একটি অ্যারেতে পুশ করি।

ডিফল্টরূপে, একজন পর্যবেক্ষকের কলব্যাককে বলা হয় **after** প্যারেন্ট কম্পোনেন্ট আপডেট (যদি থাকে), এবং মালিক কম্পোনেন্টের DOM আপডেটের **before**। এর অর্থ হল আপনি যদি একজন প্রহরী কলব্যাকের ভিতরে মালিকের কম্পোনেন্টস নিজস্ব DOM অ্যাক্সেস করার চেষ্টা করেন, DOM একটি প্রাক-আপডেট অবস্থায় থাকবে।

### Post Watchers {#post-watchers}

আপনি যদি মালিকের কম্পোনেন্টের DOM অ্যাক্সেস করতে চান একটি প্রহরী কলব্যাকে **after** Vue এটি আপডেট করার পরে, আপনাকে `flush: 'post'` বিকল্পটি নির্দিষ্ট করতে হবে:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'post'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

পোস্ট-ফ্লাশ `watchEffect()` এরও একটি সুবিধার উপনাম আছে, `watchPostEffect()`:

```js
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* executed after Vue updates */
})
```

</div>

### Sync Watchers {#sync-watchers}

কোনো Vue-পরিচালিত আপডেটের আগে সিঙ্ক্রোনাসভাবে ফায়ার করে এমন একটি ওয়াচ তৈরি করাও সম্ভব:

<div class="options-api">

```js{6}
export default {
  // ...
  watch: {
    key: {
      handler() {},
      flush: 'sync'
    }
  }
}
```

</div>

<div class="composition-api">

```js{2,6}
watch(source, callback, {
  flush: 'sync'
})

watchEffect(callback, {
  flush: 'sync'
})
```

Sync `watchEffect()` also has a convenience alias, `watchSyncEffect()`:

```js
import { watchSyncEffect } from 'vue'

watchSyncEffect(() => {
  /* executed synchronously upon reactive data change */
})
```

</div>

:::warning সতর্কতার সাথে ব্যবহার করুন
সিঙ্ক পর্যবেক্ষকদের ব্যাচিং নেই এবং প্রতিবার প্রতিক্রিয়াশীল মিউটেশন শনাক্ত হলে ট্রিগার করে। সাধারণ বুলিয়ান মানগুলি দেখার জন্য সেগুলি ব্যবহার করা ঠিক আছে, তবে ডেটা উত্সগুলিতে সেগুলি ব্যবহার করা এড়িয়ে চলুন যা বহুবার সিঙ্ক্রোনাসভাবে পরিবর্তিত হতে পারে, যেমন অ্যারে
:::

<div class="options-api">

## `this.$watch()` \* {#this-watch}

এটি ব্যবহার করে অপরিহার্যভাবে পর্যবেক্ষক তৈরি করাও সম্ভব [`$watch()` instance method](/api/component-instance#watch):

```js
export default {
  created() {
    this.$watch('question', (newQuestion) => {
      // ...
    })
  }
}
```

আপনি যখন শর্তসাপেক্ষে একজন পর্যবেক্ষক সেট আপ করতে চান বা শুধুমাত্র ব্যবহারকারীর ইন্টারঅ্যাকশনের প্রতিক্রিয়া হিসাবে কিছু দেখতে চান তখন এটি কার্যকর। এটি আপনাকে পর্যবেক্ষককে তাড়াতাড়ি থামাতে দেয়।

</div>

## Stopping a Watcher {#stopping-a-watcher}

<div class="options-api">

যখন মালিকের কম্পোনেন্ট আনমাউন্ট করা হয় তখন `watch` বিকল্প বা `$watch()` ইনস্ট্যান্স পদ্ধতি ব্যবহার করে ঘোষিত প্রহরীরা স্বয়ংক্রিয়ভাবে বন্ধ হয়ে যায়, তাই বেশিরভাগ ক্ষেত্রে আপনাকে প্রহরীকে থামানোর বিষয়ে চিন্তা করতে হবে না।

বিরল ক্ষেত্রে যেখানে মালিকের কম্পোনেন্ট আনমাউন্ট করার আগে আপনাকে একজন প্রহরীকে থামাতে হবে, `$watch()` API এর জন্য একটি ফাংশন প্রদান করে:

```js
const unwatch = this.$watch('foo', callback)

// ...when the watcher is no longer needed:
unwatch()
```

</div>

<div class="composition-api">

`setup()` বা `<script setup>` এর ভিতরে সিঙ্ক্রোনাসভাবে ঘোষিত প্রহরীরা মালিকের কম্পোনেন্ট ইনস্ট্যান্সের সাথে আবদ্ধ থাকে এবং মালিকের কম্পোনেন্ট আনমাউন্ট করা হলে স্বয়ংক্রিয়ভাবে বন্ধ হয়ে যাবে। বেশিরভাগ ক্ষেত্রে, আপনাকে প্রহরীকে থামানোর বিষয়ে চিন্তা করার দরকার নেই।

এখানে মূল বিষয় হল প্রহরীকে **synchronously** তৈরি করতে হবে: যদি প্রহরী একটি অ্যাসিঙ্ক কলব্যাকে তৈরি করা হয়, তবে এটি মালিকের কম্পোনেন্টের সাথে আবদ্ধ হবে না এবং মেমরি লিক এড়াতে ম্যানুয়ালি বন্ধ করতে হবে। এখানে একটি উদাহরণ:

```vue
<script setup>
import { watchEffect } from 'vue'

// this one will be automatically stopped
watchEffect(() => {})

// ...this one will not!
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

একজন প্রহরীকে ম্যানুয়ালি থামাতে, রিটার্ন করা হ্যান্ডেল ফাংশনটি ব্যবহার করুন। এটি `watch` এবং `watchEffect` উভয়ের জন্যই কাজ করে:

```js
const unwatch = watchEffect(() => {})

// ...later, when no longer needed
unwatch()
```

মনে রাখবেন যে খুব কম ক্ষেত্রেই আপনাকে অ্যাসিঙ্ক্রোনাসভাবে পর্যবেক্ষক তৈরি করতে হবে এবং যখনই সম্ভব সিঙ্ক্রোনাস তৈরিকে অগ্রাধিকার দেওয়া উচিত। আপনার যদি কিছু অ্যাসিঙ্ক ডেটার জন্য অপেক্ষা করতে হয়, তাহলে আপনি পরিবর্তে আপনার ওয়াচর যুক্তি শর্তসাপেক্ষ করতে পারেন:

```js
// data to be loaded asynchronously
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // do something when data is loaded
  }
})
```

</div>
