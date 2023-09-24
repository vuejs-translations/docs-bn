<script setup>
import SwitchComponent from './keep-alive-demos/SwitchComponent.vue'
</script>

# KeepAlive {#keepalive}

`<KeepAlive>` হল একটি অন্তর্নির্মিত কম্পোনেন্ট যা আমাদেরকে শর্তসাপেক্ষে কম্পোনেন্ট ইনস্ট্যান্স ক্যাশে করার অনুমতি দেয় যখন গতিশীলভাবে একাধিক উপাদানের মধ্যে স্যুইচ করা হয়।

## Basic Usage {#basic-usage}

কম্পোনেন্ট বেসিক অধ্যায়ে, আমরা `<component>` বিশেষ উপাদান ব্যবহার করে [Dynamic Components](/guide/essentials/component-basics#dynamic-components) এর জন্য সিনট্যাক্স প্রবর্তন করেছি:

```vue-html
<component :is="activeComponent" />
```

ডিফল্টরূপে, একটি সক্রিয় কম্পোনেন্ট ইন্সট্যান্স এটি থেকে স্যুইচ করার সময় আনমাউন্ট করা হবে। এর ফলে এটিতে থাকা যেকোনো পরিবর্তিত স্থিতি হারিয়ে যাবে। যখন এই উপাদানটি আবার প্রদর্শিত হবে, শুধুমাত্র প্রাথমিক অবস্থার সাথে একটি নতুন উদাহরণ তৈরি করা হবে।

নীচের উদাহরণে, আমাদের দুটি রাষ্ট্রীয় উপাদান রয়েছে - A-তে একটি কাউন্টার রয়েছে, যখন B-তে একটি বার্তা রয়েছে যা `v-model` এর মাধ্যমে একটি ইনপুটের সাথে সিঙ্ক করা হয়েছে৷ তাদের মধ্যে একটির অবস্থা আপডেট করার চেষ্টা করুন, স্যুইচ করুন, এবং তারপরে এটিতে ফিরে যান :

<SwitchComponent />

আপনি লক্ষ্য করবেন যে যখন ফিরে সুইচ করা হবে, পূর্ববর্তী পরিবর্তিত অবস্থা পুনরায় সেট করা হবে।

স্যুইচে নতুন কম্পোনেন্ট ইন্সট্যান্স তৈরি করা সাধারণত উপকারী আচরণ, কিন্তু এই ক্ষেত্রে, আমরা সত্যিই চাই যে দুটি কম্পোনেন্ট ইনস্ট্যান্স নিষ্ক্রিয় থাকা সত্ত্বেও সংরক্ষণ করা হোক। এই সমস্যা সমাধানের জন্য, আমরা আমাদের ডায়নামিক কম্পোনেন্টকে `<KeepAlive>` -এর অন্তর্নির্মিত উপাদান:

```vue-html
<!-- Inactive components will be cached! -->
<KeepAlive>
  <component :is="activeComponent" />
</KeepAlive>
```

এখন, state কম্পোনেন্ট সুইচ জুড়ে থাকবে:

<SwitchComponent use-KeepAlive />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqtUsFOwzAM/RWrl4IGC+cqq2h3RFw495K12YhIk6hJi1DVf8dJSllBaAJxi+2XZz8/j0lhzHboeZIl1NadMA4sd73JKyVaozsHI9hnJqV+feJHmODY6RZS/JEuiL1uTTEXtiREnnINKFeAcgZUqtbKOqj7ruPKwe6s2VVguq4UJXEynAkDx1sjmeMYAdBGDFBLZu2uShre6ioJeaxIduAyp0KZ3oF7MxwRHWsEQmC4bXXDJWbmxpjLBiZ7DwptMUFyKCiJNP/BWUbO8gvnA+emkGKIgkKqRrRWfh+Z8MIWwpySpfbxn6wJKMGV4IuSs0UlN1HVJae7bxYvBuk+2IOIq7sLnph8P9u5DJv5VfpWWLaGqTzwZTCOM/M0IaMvBMihd04ruK+lqF/8Ajxms8EFbCiJxR8khsP6ncQosLWnWV6a/kUf2nqu75Fby04chA0iPftaYryhz6NBRLjdtajpHZTWPio=)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqtU8tugzAQ/JUVl7RKWveMXFTIseofcHHAiawasPxArRD/3rVNSEhbpVUrIWB3x7PM7jAkuVL3veNJmlBTaaFsVraiUZ22sO0alcNedw2s7kmIPHS1ABQLQDEBAMqWvwVQzffMSQuDz1aI6VreWpPCEBtsJppx4wE1s+zmNoIBNLdOt8cIjzut8XAKq3A0NAIY/QNveFEyi8DA8kZJZjlGALQWPVSSGfNYJjVvujIJeaxItuMyo6JVzoJ9VxwRmtUCIdDfNV3NJWam5j7HpPOY8BEYkwxySiLLP1AWkbK4oHzmXOVS9FFOSM3jhFR4WTNfRslcO54nSwJKcCD4RsnZmJJNFPXJEl8t88quOuc39fCrHalsGyWcnJL62apYNoq12UQ8DLEFjCMy+kKA7Jy1XQtPlRTVqx+Jx6zXOJI1JbH4jejg3T+KbswBzXnFlz9Tjes/V/3CjWEHDsL/OYNvdCE8Wu3kLUQEhy+ljh+brFFu)

</div>

:::tip
যখন [in-DOM টেমপ্লেট](/guide/essentials/component-basics#in-dom-template-parsing-caveats) ব্যবহার করা হয়, তখন এটিকে `<keep-alive>` হিসেবে উল্লেখ করা উচিত।
:::

## Include / Exclude {#include-exclude}

ডিফল্টরূপে, `<KeepAlive>` ভিতরে যে কোনো কম্পোনেন্ট ইন্সট্যান্স ক্যাশে করবে। আমরা এই আচরণটিকে `include` এবং `exclude` প্রপসের মাধ্যমে কাস্টমাইজ করতে পারি। উভয় প্রপস একটি কমা-ডিলিমিটেড স্ট্রিং, একটি `RegExp` বা একটি অ্যারে থাকতে পারে উভয় প্রকার:

```vue-html
<!-- comma-delimited string -->
<KeepAlive include="a,b">
  <component :is="view" />
</KeepAlive>

<!-- regex (use `v-bind`) -->
<KeepAlive :include="/a|b/">
  <component :is="view" />
</KeepAlive>

<!-- Array (use `v-bind`) -->
<KeepAlive :include="['a', 'b']">
  <component :is="view" />
</KeepAlive>
```

মিলটি কম্পোনেন্টের [`name`](/api/options-misc#name) বিকল্পের বিপরীতে চেক করা হয়েছে, তাই যে উপাদানগুলিকে শর্তসাপেক্ষে `KeepAlive` দ্বারা ক্যাশ করতে হবে সেগুলিকে অবশ্যই একটি `name` বিকল্প ঘোষণা করতে হবে।

:::tip
সংস্করণ 3.2.34 থেকে, `<script setup>` ব্যবহার করে একটি একক-ফাইল উপাদান স্বয়ংক্রিয়ভাবে ফাইলের নামের উপর ভিত্তি করে তার `নাম` বিকল্পটি অনুমান করবে, নামটি ম্যানুয়ালি ঘোষণা করার প্রয়োজনীয়তা দূর করে।
:::

## Max Cached Instances {#max-cached-instances}

আমরা সর্বাধিক সংখ্যক উপাদানের দৃষ্টান্ত সীমিত করতে পারি যা `max` প্রপের মাধ্যমে ক্যাশ করা যেতে পারে। যখন `max` নির্দিষ্ট করা হয়, `<KeepAlive>` একটি [LRU cache](<https://en.wikipedia.org/wiki/Cache_replacement_policies#Least_recently_used_(LRU)>): যদি ক্যাশে করা দৃষ্টান্তের সংখ্যা নির্দিষ্ট সর্বোচ্চ গণনাকে অতিক্রম করতে থাকে, তবে নতুনটির জন্য জায়গা তৈরি করতে সর্বনিম্ন সম্প্রতি অ্যাক্সেস করা ক্যাশে করা উদাহরণ ধ্বংস করা হবে।

```vue-html
<KeepAlive :max="10">
  <component :is="activeComponent" />
</KeepAlive>
```

## Lifecycle of Cached Instance {#lifecycle-of-cached-instance}

যখন একটি কম্পোনেন্ট ইন্সট্যান্স DOM থেকে সরানো হয় কিন্তু `<KeepAlive>` দ্বারা ক্যাশ করা একটি কম্পোনেন্ট ট্রির অংশ হয়, তখন এটি আনমাউন্ট না করে একটি **নিষ্ক্রিয়** অবস্থায় চলে যায়। যখন একটি কম্পোনেন্ট ইনস্ট্যান্স অংশ হিসেবে DOM-এ ঢোকানো হয় একটি ক্যাশ করা গাছের, এটি **সক্রিয়**।

<div class="composition-api">

একটি রক্ষিত-জীবিত উপাদান [`onActivated()`](/api/composition-api-lifecycle#onactivated) এবং [`onDeactivated()`](/api/composition-api-lifecycle ব্যবহার করে এই দুটি অবস্থার জন্য জীবনচক্র হুক নিবন্ধন করতে পারে #অনিষ্ক্রিয়):

```vue
<script setup>
import { onActivated, onDeactivated } from 'vue'

onActivated(() => {
  // called on initial mount
  // and every time it is re-inserted from the cache
})

onDeactivated(() => {
  // called when removed from the DOM into the cache
  // and also when unmounted
})
</script>
```

</div>
<div class="options-api">

একটি রক্ষিত-জীবিত উপাদান [`activated`](/api/options-lifecycle#activated) এবং [`deactivated`](/api/options-lifecycle#deactivated) হুক ব্যবহার করে এই দুটি অবস্থার জন্য জীবনচক্র হুক নিবন্ধন করতে পারে:

```js
export default {
  activated() {
    // called on initial mount
    // and every time it is re-inserted from the cache
  },
  deactivated() {
    // called when removed from the DOM into the cache
    // and also when unmounted
  }
}
```

</div>

Note that:

- <span class="composition-api">`onActivated`</span><span class="options-api">`activated`</span> is also called on mount, and <span class="composition-api">`onDeactivated`</span><span class="options-api">`deactivated`</span> on unmount.

- উভয় হুক শুধুমাত্র `<KeepAlive>` দ্বারা ক্যাশে করা রুট কম্পোনেন্টের জন্যই কাজ করে না, কিন্তু ক্যাশে করা ট্রিতে ডিসেন্ডেন্ট কম্পোনেন্টের জন্যও কাজ করে।

---

**Related**

- [`<KeepAlive>` API রেফারেন্স](/api/built-in-components#keepalive)
