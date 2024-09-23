# Component v-model {#component-v-model}

## Basic Usage {#basic-usage}

টু-ওয়ে বাইন্ডিং বাস্তবায়ন করতে একটি কম্পোনেন্টে `v-model` ব্যবহার করা যেতে পারে।

<div class="composition-api">

Vue ৩.৪ থেকে শুরু করে, এটি অর্জন করার জন্য প্রস্তাবিত পদ্ধতি হল [`defineModel()`](/api/sfc-script-setup#definemodel) ম্যাক্রো ব্যবহার করা:

```vue
<!-- Child.vue -->
<script setup>
const model = defineModel()

function update() {
  model.value++
}
</script>

<template>
  <div>Parent bound v-model is: {{ model }}</div>
  <button @click="update">Increment</button>
</template>
```

প্যারেন্ট তারপর `v-মডেল` দিয়ে একটি মান আবদ্ধ করতে পারেন:

```vue-html
<!-- Parent.vue -->
<Child v-model="countModel" />
```

`defineModel()` দ্বারা প্রত্যাবর্তিত মানটি একটি রেফ। এটি অন্য যেকোন রেফের মতো অ্যাক্সেস এবং পরিবর্তন করা যেতে পারে, এটি ব্যতীত এটি একটি অভিভাবক মান এবং স্থানীয় একটির মধ্যে দ্বি-মুখী বাঁধাই হিসাবে কাজ করে:

- এর `.value` প্যারেন্ট `v-model` দ্বারা আবদ্ধ মানের সাথে সিঙ্ক করা হয়েছে;
- যখন এটি সন্তানের দ্বারা পরিবর্তিত হয়, তখন এটি পিতামাতার আবদ্ধ মানটিকেও আপডেট করে।

এর মানে হল আপনি এই রেফটিকে একটি নেটিভ ইনপুট কম্পোনেন্টস সাথে `v-model` এর সাথে আবদ্ধ করতে পারেন, একই `v-model` ব্যবহার প্রদান করার সময় নেটিভ ইনপুট কম্পোনেন্টগুলিকে মোড়ানো সহজ করে তোলে:

```vue
<script setup>
const model = defineModel()
</script>

<template>
  <input v-model="model" />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqFUtFKwzAU/ZWYl06YLbK30Q10DFSYigq+5KW0t11mmoQknZPSf/cm3eqEsT0l555zuefmpKV3WsfbBuiUpjY3XDtiwTV6ziSvtTKOLNZcFKQ0qiZRnATkG6JB0BIDJen2kp5iMlfSOlLbisw8P4oeQAhFPpURxVV0zWSa9PNwEgIHtRaZA0SEpOvbeduG5q5LE0Sh2jvZ3tSqADFjFHlGSYJkmhz10zF1FseXvIo3VklcrfX9jOaq1lyAedGOoz1GpyQwnsvQ3fdTqDnTwPhQz9eQf52ob+zO1xh9NWDBbIHRgXOZqcD19PL9GXZ4H0h03whUnyHfwCrReI+97L6RBdo+0gW3j+H9uaw+7HLnQNrDUt6oV3ZBzyhmsjiz+p/dSTwJfUx2+IpD1ic+xz5enwQGXEDJJaw8Gl2I1upMzlc/hEvdOBR6SNKAjqP1J6P/o6XdL11L5h4=)

### Under the Hood {#under-the-hood}

`defineModel` একটি সুবিধাজনক ম্যাক্রো। কম্পাইলার এটিকে নিম্নলিখিতগুলিতে প্রসারিত করে:

- `modelValue` নামের একটি প্রপ, যার সাথে স্থানীয় রেফের মান সিঙ্ক করা হয়;
- `update:modelValue` নামের একটি ইভেন্ট, যা স্থানীয় রেফের মান পরিবর্তন করা হলে নির্গত হয়।

এইভাবে আপনি ৩.৪ এর আগে উপরে দেখানো একই শিশু কম্পোনেন্টটি বাস্তবায়ন করবেন:

```vue
<!-- Child.vue -->
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="props.modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  />
</template>
```

তারপর, প্যারেন্ট কম্পোনেন্টে `v-model="foo"` এতে কম্পাইল করা হবে:

```vue-html
<!-- Parent.vue -->
<Child
  :modelValue="foo"
  @update:modelValue="$event => (foo = $event)"
/>
```

আপনি দেখতে পাচ্ছেন, এটি বেশ কিছুটা বেশি শব্দসমৃদ্ধ। যাইহোক, হুডের নীচে কী ঘটছে তা বোঝার জন্য এটি সহায়ক।

যেহেতু `defineModel` একটি প্রপ ঘোষণা করে, তাই আপনি `defineModel`-এ পাস করে অন্তর্নিহিত প্রপের বিকল্পগুলি ঘোষণা করতে পারেন:

```js
// making the v-model required
const model = defineModel({ required: true })

// providing a default value
const model = defineModel({ default: 0 })
```

:::warning
যদি আপনার কাছে `defineModel` প্রপের জন্য একটি `default` মান থাকে এবং আপনি এই প্রপের জন্য অভিভাবক কম্পোনেন্ট থেকে কোনো মান প্রদান না করেন, তাহলে এটি পিতামাতা এবং শিশু কম্পোনেন্টগুলির মধ্যে একটি ডি-সিঙ্ক্রোনাইজেশন সৃষ্টি করতে পারে। নীচের উদাহরণে, পিতামাতার `myRef` অসংজ্ঞায়িত, কিন্তু সন্তানের `model` হল 1:

```js
// child component:
const model = defineModel({ default: 1 })

// parent component:
const myRef = ref()
```

```html
<Child v-model="myRef"></Child>
```

:::

</div>

<div class="options-api">

প্রথমে আসুন একটি নেটিভ এলিমেন্টে কীভাবে `v-model` ব্যবহার করা হয় তা আবার দেখা যাক:

```vue-html
<input v-model="searchText" />
```

হুডের নিচে, টেমপ্লেট কম্পাইলার `v-model`-কে আমাদের জন্য আরও ভারবোজ সমতুল্য করে প্রসারিত করে। সুতরাং উপরের কোডটি নিম্নলিখিত হিসাবে একই কাজ করে:

```vue-html
<input
  :value="searchText"
  @input="searchText = $event.target.value"
/>
```

একটি কম্পোনেন্টে ব্যবহার করা হলে, `v-model` এর পরিবর্তে এতে প্রসারিত হয়:

```vue-html
<CustomInput
  :model-value="searchText"
  @update:model-value="newValue => searchText = newValue"
/>
```

যদিও এটি আসলে কাজ করার জন্য, `<CustomInput>` কম্পোনেন্টটিকে অবশ্যই দুটি জিনিস করতে হবে:

1. একটি নেটিভ `<input>` কম্পোনেন্টের `value` বৈশিষ্ট্যকে `modelValue` প্রপের সাথে আবদ্ধ করুন
2. যখন একটি নেটিভ `input` ইভেন্ট ট্রিগার হয়, তখন নতুন মান সহ একটি `update:modelValue` কাস্টম ইভেন্ট নির্গত করুন

এখানে এটি কর্মে রয়েছে:

```vue
<!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue']
}
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

এখন `v-model` এই কম্পোনেন্টটির সাথে পুরোপুরি কাজ করা উচিত:

```vue-html
<CustomInput v-model="searchText" />
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqFkctqwzAQRX9lEAEn4Np744aWrvoD3URdiHiSGvRCHpmC8b93JDfGKYGCkJjXvTrSJF69r8aIohHtcA69p6O0vfEuELzFgZx5tz4SXIIzUFT1JpfGCmmlxe/c3uFFRU0wSQtwdqxh0dLQwHSnNJep3ilS+8PSCxCQYrC3CMDgMKgrNlB8odaOXVJ2TgdvvNp6vSwHhMZrRcgRQLs1G5+M61A/S/ErKQXUR5immwXMWW1VEKX4g3j3Mo9QfXCeKU9FtvpQmp/lM0Oi6RP/qYieebHZNvyL0acLLODNmGYSxCogxVJ6yW1c2iWz/QOnEnY48kdUpMIVGSllD8t8zVZb+PkHqPG4iw==)

এই কম্পোনেন্টস মধ্যে `v-মডেল` বাস্তবায়নের আরেকটি উপায় হল একটি লিখনযোগ্য `computed` বৈশিষ্ট্য একটি গেটার এবং একটি সেটারের সাথে ব্যবহার করা। `get` পদ্ধতিটি `modelValue` বৈশিষ্ট্য প্রদান করবে এবং `set` পদ্ধতিটি সংশ্লিষ্ট ইভেন্ট নির্গত করবে:

```vue
<!-- CustomInput.vue -->
<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
}
</script>

<template>
  <input v-model="value" />
</template>
```

</div>

## `v-model` arguments {#v-model-arguments}

একটি কম্পোনেন্টে `v-model` একটি যুক্তিও গ্রহণ করতে পারে:

```vue-html
<MyComponent v-model:title="bookTitle" />
```

<div class="composition-api">

চাইল্ড কম্পোনেন্টে, আমরা এর প্রথম আর্গুমেন্ট হিসাবে `defineModel()`-এ একটি স্ট্রিং পাস করে সংশ্লিষ্ট আর্গুমেন্টকে সমর্থন করতে পারি:

```vue
<!-- MyComponent.vue -->
<script setup>
const title = defineModel('title')
</script>

<template>
  <input type="text" v-model="title" />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqFklFPwjAUhf9K05dhgiyGNzJI1PCgCWqUx77McQeFrW3aOxxZ9t+9LTAXA/q2nnN6+t12Db83ZrSvgE944jIrDTIHWJmZULI02iJrmIWctSy3umQRRaPOWhweNX0pUHiyR3FP870UZkyoTCuH7FPr3VJiAWzqSwfR/rbUKyhYatdV6VugTktTQHQjVBIfeYiEFgikpwi0YizZ3M2aplfXtklMWvD6UKf+CfrUVPBuh+AspngSd718yH+hX7iS4xihjUZYQS4VLPwJgyiI/3FLZSrafzAeBqFG4jgxeuEqGTo6OZfr0dZpRVxNuFWeEa4swL4alEQm+IQFx3tpUeiv56ChrWB41rMNZLsL+tbVXhP8zYIDuyeQzkN6HyBWb88/XgJ3ZxJ95bH/MN/B6aLyjMfYQ6VWhN3LBdqn8FdJtV66eY2g3HkoD+qTbcgLTo/jX+ra6D+449E47BOq5e039mr+gA==)

যদি প্রপ বিকল্পগুলিরও প্রয়োজন হয়, সেগুলি মডেলের নামের পরে পাস করা উচিত:

```js
const title = defineModel('title', { required: true })
```

<details>
<summary>৩.৪ এর পূর্বের ব্যবহার</summary>

```vue
<!-- MyComponent.vue -->
<script setup>
defineProps({
  title: {
    required: true
  }
})
defineEmits(['update:title'])
</script>

<template>
  <input
    type="text"
    :value="title"
    @input="$emit('update:title', $event.target.value)"
  />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9kE1rwzAMhv+KMIW00DXsGtKyMXYc7D7vEBplM8QfOHJoCfnvk+1QsjJ2svVKevRKk3h27jAGFJWoh7NXjmBACu4kjdLOeoIJPHYwQ+ethoJLi1vq7fpi+WfQ0JI+lCstcrkYQJqzNQMBKeoRjhG4LcYHbVvsofFfQUcCXhrteix20tRl9sIuOCBkvSHkCKD+fjxN04Ka57rkOOlrMwu7SlVHKdIrBZRcWpc3ntiLO7t/nKHFThl899YN248ikYpP9pj1V60o6sG1TMwDU/q/FZRxgeIPgK4uGcQLSZGlamz6sHKd1afUxOoGeeT298A9bHCMKxBfE3mTSNjl1vud5x8qNa76)

</details>
</div>
<div class="options-api">

এই ক্ষেত্রে, ডিফল্ট `modelValue` প্রপ এবং `update:modelValue` ইভেন্টের পরিবর্তে, চাইল্ড কম্পোনেন্টের একটি `title` প্রপ আশা করা উচিত এবং প্যারেন্ট মান আপডেট করার জন্য একটি `update:title` ইভেন্ট নির্গত করা উচিত:

```vue
<!-- MyComponent.vue -->
<script>
export default {
  props: ['title'],
  emits: ['update:title']
}
</script>

<template>
  <input
    type="text"
    :value="title"
    @input="$emit('update:title', $event.target.value)"
  />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqFUNFqwzAM/BVhCm6ha9hryMrGnvcFdR9Mo26B2DGuHFJC/n2yvZakDAohtuTTne5G8eHcrg8oSlFdTr5xtFe2Ma7zBF/Xz45vFi3B2XcG5K6Y9eKYVFZZHBK8xrMOLcGoLMDphrqUMC6Ypm18rzXp9SZjATxS8PZWAVBDLZYg+xfT1diC9t/BxGEctHFtlI2wKR78468q7ttzQcgoTcgVQPXzuh/HzAnTVBVcp/58qz+lMqHelEinElAwtCrufGIrHhJYBPdfEs53jkM4yEQpj8k+miYmc5DBcRKYZeXxqZXGukDZPF1dWhQHUiK3yl63YbZ97r6nIe6uoup6KbmFFfbRCnHGyI4iwyaPPnqffgGMlsEM)

</div>

## Multiple `v-model` bindings {#multiple-v-model-bindings}

[`v-model` arguments](#v-model-arguments) দিয়ে আমরা আগে যেমন শিখেছি তেমন কোনো নির্দিষ্ট প্রপ এবং ইভেন্টকে টার্গেট করার ক্ষমতাকে কাজে লাগিয়ে, আমরা এখন একটি একক কম্পোনেন্ট ইনস্ট্যান্সে একাধিক `v-model` বাইন্ডিং তৈরি করতে পারি।

প্রতিটি `v-model` কম্পোনেন্টে অতিরিক্ত বিকল্পের প্রয়োজন ছাড়াই একটি ভিন্ন প্রপের সাথে সিঙ্ক করবে:

```vue-html
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

<div class="composition-api">

```vue
<script setup>
const firstName = defineModel('firstName')
const lastName = defineModel('lastName')
</script>

<template>
  <input type="text" v-model="firstName" />
  <input type="text" v-model="lastName" />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqFkstuwjAQRX/F8iZUAqKKHQpIfbAoUmnVx86bKEzANLEt26FUkf+9Y4MDSAg2UWbu9fjckVv6oNRw2wAd08wUmitLDNhGTZngtZLakpZoKIkjpZY1SdCadNK3Ab3IazhowzQ2/ES0MVFIYSwpucbvxA/qJXO5FsldlKr8qDxL8EKW7kEQAQsLtapyC1gRkq3vp217mOccwf8wwLksRSlYIoMvCNkOarmEahyODAT2J4yGgtFzhx8UDf5/r6c4NEs7CNqnpxkvbO0kcVjNhCyh5AJe/SW9pBPOV3DJGvu3dsKFaiyxf8qTW9gheQwVs4Z90BDm5oF47cF/Ht4aZC75argxUmD61g9ktJC14hXoN2U5ZmJ0TILitbyq5O889KxuoB/7xRqKnwv9jdn5HqPvGnDVWwTpNJvrFSCul2efi4DeiRigqdB9RfwAI6vGM+5tj41YIvaJL9C+hOfNxerLzHYWhImhPKh3uuBnFJ/A05XoR9zRcBTOMeGo+wcs+yse)

<details>
<summary>৩.৪ এর পূর্বের ব্যবহার</summary>

```vue
<script setup>
defineProps({
  firstName: String,
  lastName: String
})

defineEmits(['update:firstName', 'update:lastName'])
</script>

<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqNUc1qwzAMfhVjCk6hTdg1pGWD7bLDGIydlh1Cq7SGxDaOEjaC332yU6cdFNpLsPRJ348y8idj0qEHnvOi21lpkHWAvdmWSrZGW2Qjs1Azx2qrWyZoVMzQZwf2rWrhhKVZbHhGGivVTqsOWS0tfTeeKBGv+qjEMkJNdUaeNXigyCYjZIEKhNY0FQJVjBXHh+04nvicY/QOBM4VGUFhJHrwBWPDutV7aPKwslbU35Q8FCX/P+GJ4oB/T3hGpEU2m+ArfpnxytX2UEsF71abLhk9QxDzCzn7QCvVYeW7XuGyWSpH0eP6SyuxS75Eb/akOpn302LFYi8SiO8bJ5PK9DhFxV/j0yH8zOnzoWr6+SbhbifkMSwSsgByk1zzsoABFKZY2QNgGpiW57Pdrx2z3JCeI99Svvxh7g8muf2x)

</details>
</div>
<div class="options-api">

```vue
<script>
export default {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName']
}
</script>

<template>
  <input
    type="text"
    :value="firstName"
    @input="$emit('update:firstName', $event.target.value)"
  />
  <input
    type="text"
    :value="lastName"
    @input="$emit('update:lastName', $event.target.value)"
  />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqNkk1rg0AQhv/KIAETSJRexYYWeuqhl9JTt4clmSSC7i7rKCnif+/ObtYkELAiujPzztejQ/JqTNZ3mBRJ2e5sZWgrVNUYbQm+WrQfskE4WN1AmuXRwQmpUELh2Qv3eJBdTTAIBbDTLluhoraA4VpjXHNwL0kuV0EIYJE6q6IFcKhsSwWk7/qkUq/nq5be+aa5JztGfrmHu8t8GtoZhI2pJaGzAMrT03YYQk0YR3BnruSOZe5CXhKnC3X7TaP3WBc+ZaOc/1kk3hDJvYILRQGfQzx3Rct8GiJZJ7fA7gg/AmesNszMrUIXFpxbwCfZSh09D0Hc7tbN6sAWm4qZf6edcZgxrMHSdA3RF7PTn1l8lTIdhbXp1/CmhOeJRNHLupv4eIaXyItPdJEFD7R8NM0Ce/d/ZCTtESnzlVZXhP/vHbeZaT0tPdf59uONfx7mDVM=)

</div>

## Handling `v-model` modifiers {#handling-v-model-modifiers}

আমরা যখন ফর্ম ইনপুট বাইন্ডিং সম্পর্কে শিখছিলাম, তখন আমরা দেখেছিলাম যে `v-model`-এ রয়েছে [বিল্ট-ইন মডিফায়ার](/guide/essentials/forms#modifiers) - `.trim`, `.number` এবং `.lazy`। কিছু ক্ষেত্রে, আপনি কাস্টম সংশোধককে সমর্থন করার জন্য আপনার কাস্টম ইনপুট কম্পোনেন্টটিতে `v-model`ও চাইতে পারেন।

আসুন একটি উদাহরণ তৈরি করি কাস্টম মডিফায়ার, `capitalize`, যা `v-model` বাইন্ডিং দ্বারা প্রদত্ত স্ট্রিংয়ের প্রথম অক্ষরকে বড় করে তোলে:

```vue-html
<MyComponent v-model.capitalize="myText" />

```

<div class="composition-api">

একটি কম্পোনেন্ট `v-model`-এ যোগ করা সংশোধকগুলিকে এইভাবে `defineModel()` রিটার্ন মানকে ধ্বংস করে চাইল্ড কম্পোনেন্টে অ্যাক্সেস করা যেতে পারে:

```vue{4}
<script setup>
const [model, modifiers] = defineModel()

console.log(modifiers) // { capitalize: true }
</script>

<template>
  <input type="text" v-model="model" />
</template>
```

মডিফায়ারের উপর ভিত্তি করে মান কীভাবে পড়া/লিখতে হবে তা শর্তসাপেক্ষে সামঞ্জস্য করতে, আমরা `get` এবং `set` বিকল্পগুলিকে `defineModel()`-এ পাস করতে পারি। এই দুটি বিকল্প মডেল রেফের গেট/সেটের মান পায় এবং একটি রূপান্তরিত মান ফেরত দেয়। এইভাবে আমরা `capitalize` মডিফায়ার বাস্তবায়নের জন্য `set` বিকল্পটি ব্যবহার করতে পারি:

```vue{6-8}
<script setup>
const [model, modifiers] = defineModel({
  set(value) {
    if (modifiers.capitalize) {
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
    return value
  }
})
</script>

<template>
  <input type="text" v-model="model" />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9UsFu2zAM/RVClzhY5mzoLUgHdEUPG9Bt2LLTtIPh0Ik6WxIkyosb5N9LybFrFG1OkvgeyccnHsWNtXkbUKzE2pdOWQKPFOwnqVVjjSM4gsMKTlA508CMqbMRuu9uDd80ajrD+XISi3WZDCB1abQnaLoNHgiuY8VsNptLvV72TbkdPwgbWxeE/ALY7JUHpW0gKAurqKjVI3rAFl1He6V30JkA3AbdKvLXUzXt+8Zssc6fM6+l6NtLAUtusF6O3cRCvFB9yY2SiYFw+8KSYcY/qfEC+FCVQuf/8rxbrJTG+4hkxyiWq2ZtUQecQ3oDqAqyMWeieyQAu0bBaUh5ebkv3A1lH+Y5md/WorstPGZzeHfGfa1KzD6yxzH11B/TCjHC4dPlX1j3P0CdjQ5S79/Z3WhpPF91lDz7Uald/uCNZj/TFFJE91SN7rslxX5JsRrmk6Koa/P/a4qRC7gY4uUey3+vxB/8Icak+OHQo2tRihGjwu2QtUb47te3pHsEWXWomX0B/Ine1CFq7Gmfg96y7Akvqf2StoKXcePvDoTaD0NFocnhxJeClyRu2FujP8u9yq+GnxGnJxSEO+M=)

<details>
<summary>৩.৪ এর পূর্বের ব্যবহার</summary>

```vue{11-13}
<script setup>
const props = defineProps({
  modelValue: String,
  modelModifiers: { default: () => ({}) }
})

const emit = defineEmits(['update:modelValue'])

function emitValue(e) {
  let value = e.target.value
  if (props.modelModifiers.capitalize) {
    value = value.charAt(0).toUpperCase() + value.slice(1)
  }
  emit('update:modelValue', value)
}
</script>

<template>
  <input type="text" :value="props.modelValue" @input="emitValue" />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNp9Us1Og0AQfpUJF5ZYqV4JNTaNxyYmVi/igdCh3QR2N7tDIza8u7NLpdU0nmB+v5/ZY7Q0Jj10GGVR7iorDYFD6sxDoWRrtCU4gsUaBqitbiHm1ngqrfuV5j+Fik7ldH6R83u5GaBQlVaOoO03+Emw8BtFHCeFyucjKMNxQNiapiTkCGCzlw6kMh1BVRpJZSO/0AEe0Pa0l2oHve6AYdBmvj+/ZHO4bfUWm/Q8uSiiEb6IYM4A+XxCi2bRH9ZX3BgVGKuNYwFbrKXCZx+Jo0cPcG9l02EGL2SZ3mxKr/VW1hKty9hMniy7hjIQCSweQByHBIZCDWzGDwi20ps0Yjxx4MR73Jktc83OOPFHGKk7VZHUKkyFgsAEAqcG2Qif4WWYUml3yOp8wldlDSLISX+TvPDstAemLeGbVvvSLkncJSnpV2PQrkqHLOfmVHeNrFDcMz3w0iBQE1cUzMYBbuS2f55CPj4D6o0/I41HzMKsP+u0kLOPoZWzkx1X7j18A8s0DEY=)

</details>
</div>

<div class="options-api">

একটি `v-model` কম্পোনেন্টে যোগ করা সংশোধকগুলিকে `modelModifiers` বৈশিষ্ট্যের মাধ্যমে কম্পোনেন্টটিতে সরবরাহ করা হবে। নিম্নলিখিত উদাহরণে, আমরা একটি কম্পোনেন্ট তৈরি করেছি যাতে একটি `modelModifiers` প্রপ রয়েছে যা ডিফল্টরূপে একটি খালি বস্তু:

```vue{11}
<script>
export default {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  created() {
    console.log(this.modelModifiers) // { capitalize: true }
  }
}
</script>

<template>
  <input
    type="text"
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

লক্ষ্য করুন কম্পোনেন্টের `modelModifiers` প্রপে `capitalize` আছে এবং এর মান `true` - কারণ এটি `v-model` বাইন্ডিং `v-model.capitalize="myText"`-এ সেট করা হয়েছে।

এখন যেহেতু আমরা আমাদের প্রপ সেট আপ করেছি, আমরা `modelModifiers` অবজেক্ট কীগুলি পরীক্ষা করতে পারি এবং নির্গত মান পরিবর্তন করতে একটি হ্যান্ডলার লিখতে পারি। নিচের কোডে যখনই `<input />` কম্পোনেন্টটি একটি `input` ইভেন্ট ফায়ার করে তখনই আমরা স্ট্রিংটিকে বড় আকারে ব্যবহার করব।

```vue{13-15}
<script>
export default {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  methods: {
    emitValue(e) {
      let value = e.target.value
      if (this.modelModifiers.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }
      this.$emit('update:modelValue', value)
    }
  }
}
</script>

<template>
  <input type="text" :value="modelValue" @input="emitValue" />
</template>
```

[চেষ্টা করুন](https://play.vuejs.org/#eNqFks1qg0AQgF9lkIKGpqa9iikNOefUtJfaw6KTZEHdZR1DbPDdO7saf0qgIq47//PNXL2N1uG5Ri/y4io1UtNrUspCK0Owa7aK/0osCQ5GFeCHq4nMuvlJCZCUeHEOGR5EnRNcrTS92VURXGex2qXVZ4JEsOhsAQxSbcrbDaBo9nihCHyXAaC1B3/4jVdDoXwhLHQuCPkGsD/JCmSpa4JUaEkilz9YAZ7RNHSS5REaVQPXgCay9vG0rPNToTLMw9FznXhdHYkHK04Qr4Zs3tL7g2JG8B4QbZS2LLqGXK5PkdcYwTsZrs1R6RU7lcmDRDPaM7AuWARMbf0KwbVdTNk4dyyk5f3l15r5YjRm8b+dQYF0UtkY1jo4fYDDLAByZBxWCmvAkIQ5IvdoBTcLeYCAiVbhvNwJvEk4GIK5M0xPwmwoeF6EpD60RrMVFXJXj72+ymWKwUvfXt+gfVzGB1tzcKfDZec+o/LfxsTdtlCj7bSpm3Xk4tjpD8FZ+uZMWTowu7MW7S+CWR77)

</div>

### Modifiers for `v-model` with arguments {#modifiers-for-v-model-with-arguments}

<div class="options-api">

আর্গুমেন্ট এবং মডিফায়ার উভয়ের সাথে `v-model` বাইন্ডিংয়ের জন্য, জেনারেট করা প্রপের নাম হবে `arg + "Modifiers"`। উদাহরণ স্বরূপ:

```vue-html
<MyComponent v-model:title.capitalize="myText">
```

সংশ্লিষ্ট ঘোষণাগুলি হওয়া উচিত:

```js
export default {
  props: ['title', 'titleModifiers'],
  emits: ['update:title'],
  created() {
    console.log(this.titleModifiers) // { capitalize: true }
  }
}
```

</div>

এখানে বিভিন্ন আর্গুমেন্ট সহ একাধিক `v-model` সহ মডিফায়ার ব্যবহার করার আরেকটি উদাহরণ রয়েছে:

```vue-html
<UserName
  v-model:first-name.capitalize="first"
  v-model:last-name.uppercase="last"
/>
```

<div class="composition-api">

```vue
<script setup>
const [firstName, firstNameModifiers] = defineModel('firstName')
const [lastName, lastNameModifiers] = defineModel('lastName')

console.log(firstNameModifiers) // { capitalize: true }
console.log(lastNameModifiers) // { uppercase: true }
</script>
```

<details>
<summary>৩.৪ এর পূর্বের ব্যবহার</summary>

```vue{5,6,10,11}
<script setup>
const props = defineProps({
firstName: String,
lastName: String,
firstNameModifiers: { default: () => ({}) },
lastNameModifiers: { default: () => ({}) }
})
defineEmits(['update:firstName', 'update:lastName'])

console.log(props.firstNameModifiers) // { capitalize: true }
console.log(props.lastNameModifiers) // { uppercase: true }
</script>
```

</details>
</div>
<div class="options-api">

```vue{15,16}
<script>
export default {
  props: {
    firstName: String,
    lastName: String,
    firstNameModifiers: {
      default: () => ({})
    },
    lastNameModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:firstName', 'update:lastName'],
  created() {
    console.log(this.firstNameModifiers) // { capitalize: true }
    console.log(this.lastNameModifiers) // { uppercase: true }
  }
}
</script>
```

</div>
