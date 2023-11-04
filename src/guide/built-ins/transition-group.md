<script setup>
import ListBasic from './transition-demos/ListBasic.vue'
import ListMove from './transition-demos/ListMove.vue'
import ListStagger from './transition-demos/ListStagger.vue'
</script>

# TransitionGroup {#transitiongroup}

`<TransitionGroup>` হল একটি অন্তর্নির্মিত কম্পোনেন্ট যা একটি তালিকায় রেন্ডার করা কম্পোনেন্ট বা কম্পোনেন্টগুলির সন্নিবেশ, অপসারণ এবং অর্ডার পরিবর্তন অ্যানিমেট করার জন্য ডিজাইন করা হয়েছে।

## Differences from `<Transition>` {#differences-from-transition}

`<TransitionGroup>` একই প্রপস, CSS ট্রানজিশন ক্লাস, এবং JavaScript হুক শ্রোতাদেরকে `<Transition>` হিসেবে সমর্থন করে, নিম্নলিখিত পার্থক্য সহ:

- ডিফল্টরূপে, এটি একটি মোড়ক কম্পোনেন্ট রেন্ডার করে না। কিন্তু আপনি `tag` প্রপের সাথে রেন্ডার করার জন্য একটি কম্পোনেন্ট নির্দিষ্ট করতে পারেন।

- [Transition modes](./transition#transition-modes) উপলভ্য নয়, কারণ আমরা আর পারস্পরিক একচেটিয়া কম্পোনেন্টগুলির মধ্যে পরিবর্তন করছি না।

- ভিতরের কম্পোনেন্টগুলির একটি অনন্য `key` বৈশিষ্ট্য থাকতে **সর্বদা প্রয়োজন**।

- সিএসএস ট্রানজিশন ক্লাসগুলি তালিকার পৃথক কম্পোনেন্টগুলিতে প্রয়োগ করা হবে, **নই** গ্রুপ/কন্টেইনারে।

:::tip
যখন [in-DOM টেমপ্লেট](/guide/essentials/component-basics#in-dom-template-parsing-caveats) ব্যবহার করা হয়, তখন এটিকে `<transition-group>` হিসেবে উল্লেখ করা উচিত।
:::

## Enter / Leave Transitions {#enter-leave-transitions}

এখানে `<TransitionGroup>` ব্যবহার করে একটি `v-for` তালিকায় এন্টার/লিভ ট্রানজিশন প্রয়োগ করার একটি উদাহরণ দেওয়া হল:

```vue-html
<TransitionGroup name="list" tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</TransitionGroup>
```

```css
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
```

<ListBasic />

## Move Transitions {#move-transitions}

উপরের ডেমোতে কিছু সুস্পষ্ট ত্রুটি রয়েছে: যখন একটি আইটেম ঢোকানো বা সরানো হয়, তখন তার আশেপাশের আইটেমগুলি মসৃণভাবে সরানোর পরিবর্তে অবিলম্বে "জাম্প" করে। আমরা কিছু অতিরিক্ত CSS নিয়ম যোগ করে এটি ঠিক করতে পারি:

```css{1,13-17}
.list-move, /* চলমান কম্পোনেন্টগুলিতে রূপান্তর প্রয়োগ করুন */
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

/* নিশ্চিত করুন যে ছেড়ে যাওয়া আইটেমগুলি লেআউট প্রবাহের বাইরে নেওয়া হয়েছে যাতে চলন্ত হয়
    অ্যানিমেশন সঠিকভাবে গণনা করা যেতে পারে। */
.list-leave-active {
  position: absolute;
}
```

এখন এটি আরও ভাল দেখায় - এমনকি পুরো তালিকাটি এলোমেলো হয়ে গেলে মসৃণভাবে অ্যানিমেটিং করা:

<ListMove />

[Full Example](/examples/#list-transition)

## Staggering List Transitions {#staggering-list-transitions}

ডাটা অ্যাট্রিবিউটের মাধ্যমে জাভাস্ক্রিপ্ট ট্রানজিশনের সাথে যোগাযোগ করার মাধ্যমে, একটি তালিকায় ট্রানজিশন স্তব্ধ করাও সম্ভব। প্রথমত, আমরা DOM এলিমেন্টের ডেটা অ্যাট্রিবিউট হিসেবে একটি আইটেমের সূচী রেন্ডার করি:

```vue-html{11}
<TransitionGroup
  tag="ul"
  :css="false"
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @leave="onLeave"
>
  <li
    v-for="(item, index) in computedList"
    :key="item.msg"
    :data-index="index"
  >
    {{ item.msg }}
  </li>
</TransitionGroup>
```

তারপর, জাভাস্ক্রিপ্ট হুকগুলিতে, আমরা ডেটা অ্যাট্রিবিউটের উপর ভিত্তি করে একটি বিলম্ব সহ কম্পোনেন্টটিকে অ্যানিমেট করি। এই উদাহরণটি অ্যানিমেশন করতে [GreenSock লাইব্রেরি](https://greensock.com/) ব্যবহার করছে:

```js{5}
function onEnter(el, done) {
  gsap.to(el, {
    opacity: 1,
    height: '1.6em',
    delay: el.dataset.index * 0.15,
    onComplete: done
  })
}
```

<ListStagger />

<div class="composition-api">

[সম্পূর্ণ উদাহরণ চেষ্টা করুন](https://play.vuejs.org/#eNqlVMuu0zAQ/ZVRNklRm7QLWETtBW4FSFCxYkdYmGSSmjp28KNQVfl3xk7SFyvEponPGc+cOTPNOXrbdenRYZRHa1Nq3lkwaF33VEjedkpbOIPGeg6lajtnsYIeaq1aiOlSfAlqDOtG3L8SUchSSWNBcPrZwNdCAqVqTZND/KxdibBDjKGf3xIfWXngCNs9k4/Udu/KA3xWWnPz1zW0sOOP6CcnG3jv9ImIQn67SvrpUJ9IE/WVxPHsSkw97gbN0zFJZrB5grNPrskcLUNXac2FRZ0k3GIbIvxLSsVTq3bqF+otM5jMUi5L4So0SSicHplwOKOyfShdO1lariQo+Yy10vhO+qwoZkNFFKmxJ4Gp6ljJrRe+vMP3yJu910swNXqXcco1h0pJHDP6CZHEAAcAYMydwypYCDAkJRdX6Sts4xGtUDAKotIVs9Scpd4q/A0vYJmuXo5BSm7JOIEW81DVo77VR207ZEf8F23LB23T+X9VrbNh82nn6UAz7ASzSCeANZe0AnBctIqqbIoojLCIIBvoL5pJw31DH7Ry3VDKsoYinSii4ZyXxhBQM2Fwwt58D7NeoB8QkXfDvwRd2XtceOsCHkwc8KCINAk+vADJppQUFjZ0DsGVGT3uFn1KSjoPeKLoaYtvCO/rIlz3vH9O5FiU/nXny/pDT6YGKZngg0/Zg1GErrMbp6N5NHxJFi3N/4dRkj5IYf5ULxCmiPJpI4rIr4kHimhvbWfyLHOyOzQpNZZ57jXNy4nRGFLTR/0fWBqe7w==)

</div>
<div class="options-api">

[সম্পূর্ণ উদাহরণ চেষ্টা করুন](https://play.vuejs.org/#eNqtVE2P0zAQ/SujXNqgNmkPcIjaBbYCJKg4cSMcTDJNTB07+KNsVfW/M3aabNpyQltViT1vPPP8Zian6H3bJgeHURatTKF5ax9yyZtWaQuVYS3stGpg4peTXOayUNJYEJwea/ieS4ATNKbKYPKoXYGwRZzAeTYGPrNizxE2NZO30KZ2xR6+Kq25uTuGFrb81vrFyQo+On0kIJc/PCV8CmxL3DEnLJy8e8ksm8bdGkCjdVr2O4DfDvWRgtGN/JYC0SOkKVTTOotl1jv3hi3d+DngENILkey4sKinU26xiWH9AH6REN/Eqq36g3rDDE7jhMtCuBLN1NbcJIFEHN9RaNDWqjQDAyUfcac0fpA+CYoRCRSJsUeBiWpZwe2RSrK4w2rkVe2rdYG6LD5uH3EGpZI4iuurTdwDNBjpRJclg+UlhP914UnMZfIGm8kIKVEwciYivhoGLQlQ4hO8gkWyfD1yVHJDKgu0mAUmPXLuxRkYb5Ed8H8YL/7BeGx7Oa6hkLmk/yodBoo21BKtYBZpB7DikroKDvNGUeZ1HoVmyCNIO/ibZtJwy5X8pJVru9CWVeTpRB51+6wwhgw7Jgz2tnc/Q6/M0ZeWwKvmGZye0Wu78PIGexC6swdGxEnw/q6HOYUkt9DwMwhKxfS6GpY+KPHc45G8+6EYAV7reTjucf/uwUtSmvvTME1wDuISlVTwTqf0RiiyrtKR0tEs6r5l84b645dRkr5zoT8oXwBMHg2Tlke+jbwhj2prW5OlqZPtvkroYqnH3lK9nLgI46scnf8Cn22kBA==)

</div>

---

**সম্পর্কিত**

- [`<TransitionGroup>` API রেফারেন্স](/api/built-in-components#transitiongroup)
