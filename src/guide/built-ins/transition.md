<script setup>
import Basic from './transition-demos/Basic.vue'
import SlideFade from './transition-demos/SlideFade.vue'
import CssAnimation from './transition-demos/CssAnimation.vue'
import NestedTransitions from './transition-demos/NestedTransitions.vue'
import JsHooks from './transition-demos/JsHooks.vue'
import BetweenElements from './transition-demos/BetweenElements.vue'
import BetweenComponents from './transition-demos/BetweenComponents.vue'
</script>

# Transition {#transition}

Vue দুটি অন্তর্নির্মিত কম্পোনেন্ট অফার করে যা পরিবর্তন এবং অ্যানিমেশনের সাথে কাজ করতে সাহায্য করতে পারে পরিবর্তিত অবস্থার প্রতিক্রিয়ায়:

- `<Transition>` অ্যানিমেশন প্রয়োগ করার জন্য যখন কোনো কম্পোনেন্ট বা কম্পোনেন্ট DOM-এ প্রবেশ করছে এবং ছেড়ে যাচ্ছে। এটি এই পৃষ্ঠায় আচ্ছাদিত করা হয়.

- `<TransitionGroup>` অ্যানিমেশন প্রয়োগ করার জন্য যখন কোনো কম্পোনেন্ট বা কম্পোনেন্ট একটি `v-for` তালিকার মধ্যে ঢোকানো, সরানো বা সরানো হয়। এটি [পরবর্তী অধ্যায়](/guide/built-ins/transition-group) এ কভার করা হয়েছে।

এই দুটি কম্পোনেন্ট ছাড়াও, আমরা অন্যান্য কৌশল যেমন CSS ক্লাস টগল করা বা স্টাইল বাইন্ডিংয়ের মাধ্যমে রাষ্ট্র-চালিত অ্যানিমেশন ব্যবহার করে Vue-তে অ্যানিমেশন প্রয়োগ করতে পারি। এই অতিরিক্ত কৌশলগুলি [অ্যানিমেশন টেকনিক](/guide/extras/animation) অধ্যায়ে কভার করা হয়েছে।

## The `<Transition>` Component {#the-transition-component}

`<Transition>` হল একটি অন্তর্নির্মিত কম্পোনেন্ট: এর মানে এটি নিবন্ধন না করেই যেকোনো কম্পোনেন্টের টেমপ্লেটে উপলব্ধ। এটির ডিফল্ট স্লটের মাধ্যমে এটিতে পাস করা কম্পোনেন্ট বা কম্পোনেন্টগুলিতে এন্টার এবং ছেড়ে অ্যানিমেশন প্রয়োগ করতে ব্যবহার করা যেতে পারে। এন্টার বা লিভ নিম্নলিখিতগুলির একটি দ্বারা ট্রিগার করা যেতে পারে:

- `v-if` এর মাধ্যমে শর্তসাপেক্ষ রেন্ডারিং
- `v-শো` এর মাধ্যমে শর্তসাপেক্ষ প্রদর্শন
- গতিশীল কম্পোনেন্ট `<component>` বিশেষ কম্পোনেন্টের মাধ্যমে টগল করা
- বিশেষ `key` বৈশিষ্ট্য পরিবর্তন করা হচ্ছে

এটি সবচেয়ে মৌলিক ব্যবহারের একটি উদাহরণ:

```vue-html
<button @click="show = !show">Toggle</button>
<Transition>
  <p v-if="show">hello</p>
</Transition>
```

```css
/* we will explain what these classes do next! */
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
```

<Basic />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNpVkEFuwyAQRa8yZZNWqu1sunFJ1N4hSzYUjRNUDAjGVJHluxcCipIV/OG/pxEr+/a+TwuykfGogvYEEWnxR2H17F0gWCHgBBtMwc2wy9WdsMIqZ2OuXtwfHErhlcKCb8LyoVoynwPh7I0kzAmA/yxEzsKXMlr9HgRr9Es5BTue3PlskA+1VpFTkDZq0i3niYfU6anRmbqgMY4PZeH8OjwBfHhYIMdIV1OuferQEoZOKtIJ328TgzJhm8BabHR3jeC8VJqusO8/IqCM+CnsVqR3V/mfRxO5amnkCPuK5B+6rcG2fydshks=)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNpVkMFuAiEQhl9lyqlNuouXXrZo2nfwuBeKs0qKQGBAjfHdZZfVrAmB+f/M/2WGK/v1vs0JWcdEVEF72vQWz94Fgh0OMhmCa28BdpLk+0etAQJSCvahAOLBnTqgkLA6t/EpVzmCP7lFEB69kYRFAYi/ROQs/Cij1f+6ZyMG1vA2vj3bbN1+b1Dw2lYj2yBt1KRnXRwPudHDnC6pAxrjBPe1n78EBF8MUGSkixnLNjdoCUMjFemMn5NjUGacnboqPVkdOC+Vpgus2q8IKCN+T+suWENwxyWJXKXMyQ5WNVJ+aBqD3e6VSYoi)

</div>

:::tip
`<Transition>` শুধুমাত্র একটি কম্পোনেন্ট বা কম্পোনেন্টকে এর স্লট সামগ্রী হিসাবে সমর্থন করে। বিষয়অবজেক্ট একটি কম্পোনেন্ট হলে, কম্পোনেন্টের শুধুমাত্র একটি একক মূল কম্পোনেন্ট থাকতে হবে।
:::

যখন একটি `<Transition>` কম্পোনেন্টের একটি কম্পোনেন্ট সন্নিবেশিত বা সরানো হয়, তখন এটি ঘটে:

1. টার্গেট এলিমেন্টে সিএসএস ট্রানজিশন বা অ্যানিমেশন প্রয়োগ করা হয়েছে কিনা তা স্বয়ংক্রিয়ভাবে শুঁকে যাবে। যদি তা হয়, উপযুক্ত সময়ে বেশ কয়েকটি [CSS ট্রানজিশন ক্লাস](#transition-classes) যোগ/সরানো হবে।

2. [JavaScript hooks](#javascript-hooks) এর শ্রোতা থাকলে, এই হুকগুলিকে উপযুক্ত সময়ে ডাকা হবে।

3. যদি কোন CSS ট্রানজিশন/অ্যানিমেশন সনাক্ত না করা হয় এবং কোন জাভাস্ক্রিপ্ট হুক প্রদান করা না হয়, তাহলে সন্নিবেশ এবং/অথবা অপসারণের জন্য DOM অপারেশন ব্রাউজারের পরবর্তী অ্যানিমেশন ফ্রেমে সম্পাদিত হবে।

## CSS-Based Transitions {#css-based-transitions}

### Transition Classes {#transition-classes}

এন্টার/লিভ ট্রানজিশনের জন্য অ্যাপ্লিকেশন করা হয়েছে ছয়টি ক্লাস।

![Transition Diagram](./images/transition-classes.png)

<!-- https://www.figma.com/file/rlOv0ZKJFFNA9hYmzdZv3S/Transition-Classes -->

1. `v-enter-from`: এন্টারের জন্য শুরুর অবস্থা। কম্পোনেন্ট ঢোকানোর আগে যোগ করা হয়েছে, কম্পোনেন্ট ঢোকানোর পরে একটি ফ্রেম সরানো হয়েছে।

2. `v-enter-active`: প্রবেশের জন্য সক্রিয় অবস্থা। পুরো প্রবেশ পর্বের সময় প্রয়োগ করা হয়। কম্পোনেন্টটি ঢোকানোর আগে যোগ করা হয়, যখন রূপান্তর/অ্যানিমেশন শেষ হয় তখন সরানো হয়। এই শ্রেণীটি প্রবেশের স্থানান্তরের সময়কাল, বিলম্ব এবং সহজীকরণ বক্ররেখা নির্ধারণ করতে ব্যবহার করা যেতে পারে।

3. `v-enter-to`: এন্টারের জন্য শেষ অবস্থা। কম্পোনেন্ট ঢোকানোর পরে একটি ফ্রেম যোগ করা হয়েছে (একই সময়ে `v-enter-from` সরানো হয়েছে), ট্রানজিশন/অ্যানিমেশন শেষ হলে সরানো হয়েছে।

4. `v-leave-from`: ছুটির জন্য শুরুর অবস্থা। একটি ছেড়ে যাওয়া স্থানান্তর ট্রিগার হলে অবিলম্বে যোগ করা হয়, এক ফ্রেমের পরে সরানো হয়।

5. `v-leave-active`: ছুটির জন্য সক্রিয় অবস্থা। পুরো ছেড়ে যাওয়ার পর্যায়ে প্রয়োগ করা হয়েছে। একটি ছেড়ে যাওয়া স্থানান্তর ট্রিগার হলে অবিলম্বে যোগ করা হয়, যখন রূপান্তর/অ্যানিমেশন শেষ হয় তখন সরানো হয়। এই শ্রেণীটি চলে যাওয়ার স্থানান্তরের সময়কাল, বিলম্ব এবং সহজীকরণ বক্ররেখা নির্ধারণ করতে ব্যবহার করা যেতে পারে।

6. `v-leave-to`: ছুটির জন্য শেষ অবস্থা। একটি ছেড়ে যাওয়া স্থানান্তর ট্রিগার হওয়ার পরে একটি ফ্রেম যুক্ত করা হয়েছে (একই সময়ে `v-leave-from` সরানো হয়েছে), যখন ট্রানজিশন/অ্যানিমেশন শেষ হয় তখন সরানো হয়।

`v-enter-active` এবং `v-leave-active` আমাদের এন্টার/লিভ ট্রানজিশনের জন্য বিভিন্ন সহজ বক্ররেখা নির্দিষ্ট করার ক্ষমতা দেয়, যা আমরা নিম্নলিখিত বিভাগে একটি উদাহরণ দেখতে পাব।

### Named Transitions {#named-transitions}

একটি রূপান্তরের নাম `name` প্রপের মাধ্যমে নামকরণ করা যেতে পারে:

```vue-html
<Transition name="fade">
  ...
</Transition>
```

একটি নামযুক্ত ট্রানজিশনের জন্য, এর ট্রানজিশন ক্লাসগুলি এর নামের সাথে `v` এর পরিবর্তে প্রিফিক্স করা হবে। উদাহরণ স্বরূপ, উপরের ট্রানজিশনের জন্য প্রয়োগকৃত ক্লাস হবে `v-enter-active` এর পরিবর্তে `fade-enter-active`। ফেইড ট্রানজিশনের জন্য CSS দেখতে এইরকম হওয়া উচিত:

```css
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```

### CSS Transitions {#css-transitions}

`<Transition>` সাধারণত [নেটিভ সিএসএস ট্রানজিশন](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions) এর সাথে ব্যবহার করা হয়, যেমনটি উপরের মৌলিক উদাহরণে দেখা গেছে . 'ট্রানজিশন' সিএসএস প্রপার্টি হল একটি সংক্ষিপ্ত হস্ত যা আমাদেরকে একটি ট্রানজিশনের একাধিক দিক নির্দিষ্ট করতে দেয়, যার মধ্যে অ্যানিমেটেড হওয়া উচিত এমন বৈশিষ্ট্য, ট্রানজিশনের সময়কাল এবং [easing curves](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function)।

এখানে একটি আরও উন্নত উদাহরণ রয়েছে যা একাধিক বৈশিষ্ট্যের স্থানান্তর করে, বিভিন্ন সময়কাল সহ এবং প্রবেশ এবং ছেড়ে যাওয়ার জন্য বক্ররেখা সহজ করে:

```vue-html
<Transition name="slide-fade">
  <p v-if="show">hello</p>
</Transition>
```

```css
/*
  Enter and leave animations can use different
  durations and timing functions.
*/
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}
```

<SlideFade />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqFkc9uwjAMxl/F6wXQKIVNk1AX0HbZC4zDDr2E4EK0NIkStxtDvPviFQ0OSFzyx/m+n+34kL16P+lazMpMRBW0J4hIrV9WVjfeBYIDBKzhCHVwDQySdFDZyipnY5Lu3BcsWDCk0OKosqLoKcmfLoSNN5KQbyTWLZGz8KKMVp+LKju573ivsuXKbbcG4d3oDcI9vMkNiqL3JD+AWAVpoyadGFY2yATW5nVSJj9rkspDl+v6hE/hHRrjRMEdpdfiDEkBUVxWaEWkveHj5AzO0RKGXCrSHcKBIfSPKEEaA9PJYwSUEXPX0nNlj8y6RBiUHd5AzCOodq1VvsYfjWE4G6fgEy/zMcxG17B9ZTyX8bV85C5y1S40ZX/kdj+GD1P/zVQA56XStC9h2idJI/z7huz4CxoVvE4=)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqFkc1uwjAMgF/F6wk0SmHTJNQFtF32AuOwQy+hdSFamkSJ08EQ776EbMAkJKTIf7I/O/Y+ezVm3HvMyoy52gpDi0rh1mhL0GDLvSTYVwqg4cQHw2QDWCRv1Z8H4Db6qwSyHlPkEFUQ4bHixA0OYWckJ4wesZUn0gpeainqz3mVRQzM4S7qKlss9XotEd6laBDu4Y03yIpUE+oB2NJy5QSJwFC8w0iIuXkbMkN9moUZ6HPR/uJDeINSalaYxCjOkBBgxeWEijnayWiOz+AcFaHNeU2ix7QCOiFK4FLCZPzoALnDXHt6Pq7hP0Ii7/EGYuag9itR5yv8FmgH01EIPkUxG8F0eA2bJmut7kbX+pG+6NVq28WTBTN+92PwMDHbSAXQhteCdiVMUpNwwuMassMP8kfAJQ==)

</div>

### CSS Animations {#css-animations}

[নেটিভ CSS অ্যানিমেশন](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations) CSS ট্রানজিশনের মতো একইভাবে প্রয়োগ করা হয়, পার্থক্য হল `*-enter- from` কম্পোনেন্টটি সন্নিবেশিত হওয়ার সাথে সাথে সরানো হয় না, তবে একটি `animationend` ইভেন্টে।

বেশিরভাগ CSS অ্যানিমেশনের জন্য, আমরা সেগুলিকে `*-enter-active` এবং `*-leave-active` ক্লাসের অধীনে ঘোষণা করতে পারি। এখানে একটি উদাহরণ:

```vue-html
<Transition name="bounce">
  <p v-if="show" style="text-align: center;">
    Hello here is some bouncy text!
  </p>
</Transition>
```

```css
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
```

<CssAnimation />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqNksGOgjAQhl9lJNmoBwRNvCAa97YP4JFLbQZsLG3TDqzG+O47BaOezCYkpfB9/0wHbsm3c4u+w6RIyiC9cgQBqXO7yqjWWU9wA4813KH2toUpo9PKVEZaExg92V/YRmBGvsN5ZcpsTGGfN4St04Iw7qg8dkTWwF5qJc/bKnnYk7hWye5gm0ZjmY0YKwDlwQsTFCnWjGiRpaPtjETG43smHPSpqh9pVQKBrjpyrfCNMilZV8Aqd5cNEF4oFVo1pgCJhtBvnjEAP6i1hRN6BBUg2BZhKHUdvMmjWhYHE9dXY/ygzN4PasqhB75djM2mQ7FUSFI9wi0GCJ6uiHYxVsFUGcgX67CpzP0lahQ9/k/kj9CjDzgG7M94rT1PLLxhQ0D+Na4AFI9QW98WEKTQOMvnLAOwDrD+wC0Xq/Ubusw/sU+QL/45hskk9z8Bddbn)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqNUs2OwiAQfpWxySZ66I8mXioa97YP4LEXrNNKpEBg2tUY330pqOvJmBBgyPczP1yTb2OyocekTJirrTC0qRSejbYEB2x4LwmulQI4cOLTWbwDWKTeqkcE4I76twSyPcaX23j4zS+WP3V9QNgZyQnHiNi+J9IKtrUU9WldJaMMrGEynlWy2em2lcjyCPMUALazXDlBwtMU79CT9rpXNXp4tGYGhlQ0d7UqAUcXOeI6bluhUtKmhEVhzisgPFPKpWhVCTUqQrt6ygD8oJQajmgRhAOnO4RgdQm8yd0tNzGv/D8x/8Dy10IVCzn4axaTTYNZymsSA8YuciU6PrLL6IKpUFBkS7cKXXwQJfIBPyP6IQ1oHUaB7QkvjfUdcy+wIFB8PeZIYwmNtl0JruYSp8XMk+/TXL7BzbPF8gU6L95hn8D4OUJnktsfM1vavg==)

</div>

### Custom Transition Classes {#custom-transition-classes}

এছাড়াও আপনি `<Transition>`-এ নিম্নলিখিত প্রপস পাস করে কাস্টম ট্রানজিশন ক্লাস নির্দিষ্ট করতে পারেন:

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

এগুলি প্রচলিত শ্রেণির নামগুলিকে অগ্রাহ্য করবে। এটি বিশেষভাবে উপযোগী যখন আপনি একটি বিদ্যমান CSS অ্যানিমেশন লাইব্রেরির সাথে Vue-এর ট্রানজিশন সিস্টেমকে একত্রিত করতে চান, যেমন [Animate.css](https://daneden.github.io/animate.css/):

```vue-html
<!-- assuming Animate.css is included on the page -->
<Transition
  name="custom-classes"
  enter-active-class="animate__animated animate__tada"
  leave-active-class="animate__animated animate__bounceOutRight"
>
  <p v-if="show">hello</p>
</Transition>
```

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqNUctuwjAQ/BXXF9oDsZB6ogbRL6hUcbSEjLMhpn7JXtNWiH/vhqS0R3zxPmbWM+szf02pOVXgSy6LyTYhK4A1rVWwPsWM7MwydOzCuhw9mxF0poIKJoZC0D5+stUAeMRc4UkFKcYpxKcEwSenEYYM5b4ixsA2xlnzsVJ8Yj8Mt+LrbTwcHEgxwojCmNxmHYpFG2kaoxO0B2KaWjD6uXG6FCiKj00ICHmuDdoTjD2CavJBCna7KWjZrYK61b9cB5pI93P3sQYDbxXf7aHHccpVMolO7DS33WSQjPXgXJRi2Cl1xZ8nKkjxf0dBFvx2Q7iZtq94j5jKUgjThmNpjIu17ZzO0JjohT7qL+HsvohJWWNKEc/NolncKt6Goar4y/V7rg/wyw9zrLOy)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqNUcFuwjAM/RUvp+1Ao0k7sYDYF0yaOFZCJjU0LE2ixGFMiH9f2gDbcVKU2M9+tl98Fm8hNMdMYi5U0tEEXraOTsFHho52mC3DuXUAHTI+PlUbIBLn6G4eQOr91xw4ZqrIZXzKVY6S97rFYRqCRabRY7XNzN7BSlujPxetGMvAAh7GtxXLtd/vLSlZ0woFQK0jumTY+FJt7ORwoMLUObEfZtpiSpRaUYPkmOIMNZsj1VhJRWeGMsFmczU6uCOMHd64lrCQ/s/d+uw0vWf+MPuea5Vp5DJ0gOPM7K4Ci7CerPVKhipJ/moqgJJ//8ipxN92NFdmmLbSip45pLmUunOH1Gjrc7ezGKnRfpB4wJO0ZpvkdbJGpyRfmufm+Y4Mxo1oK16n9UwNxOUHwaK3iQ==)

</div>

### Using Transitions and Animations Together {#using-transitions-and-animations-together}

একটি রূপান্তর কখন শেষ হয়েছে তা জানার জন্য Vue ইভেন্ট শ্রোতাদের সংযুক্ত করতে হবে। এটি হয় `transitionend` বা `animationend` হতে পারে, প্রয়োগ করা CSS নিয়মের প্রকারের উপর নির্ভর করে। আপনি যদি শুধুমাত্র একটি বা অন্যটি ব্যবহার করেন তবে Vue স্বয়ংক্রিয়ভাবে সঠিক প্রকার সনাক্ত করতে পারে।

যাইহোক, কিছু ক্ষেত্রে আপনি উভয়ই একই কম্পোনেন্টে রাখতে চাইতে পারেন, উদাহরণস্বরূপ, হোভারে একটি CSS ট্রানজিশন প্রভাব সহ Vue দ্বারা ট্রিগার করা একটি CSS অ্যানিমেশন। এই ক্ষেত্রে, `animation` বা `transition`-এর মান সহ `type` প্রপ পাস করার মাধ্যমে আপনি Vue কে যে ধরনের যত্ন নিতে চান তা আপনাকে স্পষ্টভাবে ঘোষণা করতে হবে:

```vue-html
<Transition type="animation">...</Transition>
```

### Nested Transitions and Explicit Transition Durations {#nested-transitions-and-explicit-transition-durations}

যদিও ট্রানজিশন ক্লাসগুলি শুধুমাত্র `<Transition>`-এ সরাসরি চাইল্ড এলিমেন্টে প্রয়োগ করা হয়, আমরা নেস্টেড সিএসএস সিলেক্টর ব্যবহার করে নেস্টেড এলিমেন্ট ট্রানজিশন করতে পারি:

```vue-html
<Transition name="nested">
  <div v-if="show" class="outer">
    <div class="inner">
      Hello
    </div>
  </div>
</Transition>
```

```css
/* rules that target nested elements */
.nested-enter-active .inner,
.nested-leave-active .inner {
  transition: all 0.3s ease-in-out;
}

.nested-enter-from .inner,
.nested-leave-to .inner {
  transform: translateX(30px);
  opacity: 0;
}

/* ... other necessary CSS omitted */
```

এমনকি আমরা এন্টার নেস্টেড এলিমেন্টে একটি ট্রানজিশন বিলম্ব যোগ করতে পারি, যা একটি স্তব্ধ এন্টার অ্যানিমেশন সিকোয়েন্স তৈরি করে:

```css{3}
/* delay enter of nested element for staggered effect */
.nested-enter-active .inner {
  transition-delay: 0.25s;
}
```

যাইহোক, এটি একটি ছোট সমস্যা তৈরি করে। ডিফল্টরূপে, রুট ট্রানজিশন এলিমেন্টে **প্রথম** `transitionend` বা `animationend` ইভেন্টটি শুনে রূপান্তর শেষ হলে `<Transition>` কম্পোনেন্ট স্বয়ংক্রিয়ভাবে বের করার চেষ্টা করে। একটি নেস্টেড ট্রানজিশনের সাথে, সমস্ত অভ্যন্তরীণ কম্পোনেন্টগুলির রূপান্তর শেষ না হওয়া পর্যন্ত পছন্দসই আচরণটি অপেক্ষা করা উচিত।

এই ধরনের ক্ষেত্রে আপনি `<transition>` কম্পোনেন্টে `duration` প্রপ ব্যবহার করে একটি সুস্পষ্ট রূপান্তর সময়কাল (মিলিসেকেন্ডে) নির্দিষ্ট করতে পারেন। মোট সময়কাল অভ্যন্তরীণ কম্পোনেন্টের বিলম্ব এবং স্থানান্তর সময়কালের সাথে মেলে:

```vue-html
<Transition :duration="550">...</Transition>
```

<NestedTransitions />

[চেষ্টা করুন](https://play.vuejs.org/#eNqVVd9v0zAQ/leO8LAfrE3HNKSFbgKmSYMHQNAHkPLiOtfEm2NHttN2mvq/c7bTNi1jgFop9t13d9995ziPyfumGc5bTLJkbLkRjQOLrm2uciXqRhsHj2BwBiuYGV3DAUEPcpUrrpUlaKUXcOkBh860eJSrcRqzUDxtHNaNZA5pBzCets5pBe+4FPz+Mk+66Bf+mSdXE12WEsdphMWQiWHKCicoLCtaw/yKIs/PR3kCitVIG4XWYUEJfATFFGIO84GYdRUIyCWzlra6dWg2wA66dgqlts7c+d8tSqk34JTQ6xqb9TjdUiTDOO21TFvrHqRfDkPpExiGKvBITjdl/L40ulVFBi8R8a3P17CiEKrM4GzULIOlFmpQoSgrl8HpKFpX3kFZu2y0BNhJxznvwaJCA1TEYcC4E3MkKp1VIptjZ43E3KajDJiUMBqeWUBmcUBUqJGYOT2GAiV7gJAA9Iy4GyoBKLH2z+N0W3q/CMC2yCCkyajM63Mbc+9z9mfvZD+b071MM23qLC69+j8PvX5HQUDdMC6cL7BOTtQXCJwpas/qHhWIBdYtWGgtDWNttWTmThu701pf1W6+v1Hd8Xbz+k+VQxmv8i7Fv1HZn+g/iv2nRkjzbd6npf/Rkz49DifQ3dLZBBYOJzC4rqgCwsUbmLYlCAUVU4XsCd1NrCeRHcYXb1IJC/RX2hEYCwJTvHYVMZoavbBI09FmU+LiFSzIh0AIXy1mqZiFKaKCmVhiEVJ7GftHZTganUZ56EYLL3FykjhL195MlMM7qxXdmEGDPOG6boRE86UJVPMki+p4H01WLz4Fm78hSdBo5xXy+yfsd3bpbXny1SA1M8c82fgcMyW66L75/hmXtN44a120ktDPOL+h1bL1HCPsA42DaPdwge3HcO/TOCb2ZumQJtA15Yl65Crg84S+BdfPtL6lezY8C3GkZ7L6Bc1zNR0=)

প্রয়োজনে, আপনি একটি অবজেক্ট ব্যবহার করে প্রবেশ এবং ত্যাগের সময়কালের জন্য পৃথক মানও নির্দিষ্ট করতে পারেন:

```vue-html
<Transition :duration="{ enter: 500, leave: 800 }">...</Transition>
```

### Performance Considerations {#performance-considerations}

আপনি লক্ষ্য করতে পারেন যে উপরে দেখানো অ্যানিমেশনগুলি বেশিরভাগই `ট্রান্সফর্ম` এবং `অপাসিটি` এর মতো বৈশিষ্ট্য ব্যবহার করছে। এই বৈশিষ্ট্যগুলি অ্যানিমেট করার জন্য দক্ষ কারণ:

1. তারা অ্যানিমেশনের সময় ডকুমেন্ট লেআউটকে প্রভাবিত করে না, তাই তারা প্রতিটি অ্যানিমেশন ফ্রেমে ব্যয়বহুল CSS লেআউট গণনা ট্রিগার করে না।

2. 'transform' অ্যানিমেট করার সময় বেশিরভাগ আধুনিক ব্রাউজার GPU হার্ডওয়্যার ত্বরণের সুবিধা নিতে পারে।

তুলনামূলকভাবে, `height` বা `margin` এর মতো বৈশিষ্ট্যগুলি CSS লেআউটকে ট্রিগার করবে, তাই এগুলি অ্যানিমেট করার জন্য অনেক বেশি ব্যয়বহুল, এবং সতর্কতার সাথে ব্যবহার করা উচিত। আমরা [CSS-Triggers](https://csstriggers.com/) এর মতো সংস্থানগুলি পরীক্ষা করে দেখতে পারি যে কোন বৈশিষ্ট্যগুলি আমরা অ্যানিমেট করলে লেআউট ট্রিগার করবে।

## JavaScript Hooks {#javascript-hooks}

`<Transition>` কম্পোনেন্টের ইভেন্টগুলো শুনে আপনি জাভাস্ক্রিপ্টের মাধ্যমে ট্রানজিশন প্রক্রিয়ায় যোগ দিতে পারেন:

```html
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @enter-cancelled="onEnterCancelled"
  @before-leave="onBeforeLeave"
  @leave="onLeave"
  @after-leave="onAfterLeave"
  @leave-cancelled="onLeaveCancelled"
>
  <!-- ... -->
</Transition>
```

<div class="composition-api">

```js
// called before the element is inserted into the DOM.
// use this to set the "enter-from" state of the element
function onBeforeEnter(el) {}

// called one frame after the element is inserted.
// use this to start the entering animation.
function onEnter(el, done) {
  // call the done callback to indicate transition end
  // optional if used in combination with CSS
  done()
}

// called when the enter transition has finished.
function onAfterEnter(el) {}

// called when the enter transition is cancelled before completion.
function onEnterCancelled(el) {}

// called before the leave hook.
// Most of the time, you should just use the leave hook
function onBeforeLeave(el) {}

// called when the leave transition starts.
// use this to start the leaving animation.
function onLeave(el, done) {
  // call the done callback to indicate transition end
  // optional if used in combination with CSS
  done()
}

// called when the leave transition has finished and the
// element has been removed from the DOM.
function onAfterLeave(el) {}

// only available with v-show transitions
function onLeaveCancelled(el) {}
```

</div>
<div class="options-api">

```js
export default {
  // ...
  methods: {
    // called before the element is inserted into the DOM.
    // use this to set the "enter-from" state of the element
    onBeforeEnter(el) {},

    // called one frame after the element is inserted.
    // use this to start the animation.
    onEnter(el, done) {
      // call the done callback to indicate transition end
      // optional if used in combination with CSS
      done()
    },

    // called when the enter transition has finished.
    onAfterEnter(el) {},

    // called when the enter transition is cancelled before completion.
    onEnterCancelled(el) {},

    // called before the leave hook.
    // Most of the time, you should just use the leave hook.
    onBeforeLeave(el) {},

    // called when the leave transition starts.
    // use this to start the leaving animation.
    onLeave(el, done) {
      // call the done callback to indicate transition end
      // optional if used in combination with CSS
      done()
    },

    // called when the leave transition has finished and the
    // element has been removed from the DOM.
    onAfterLeave(el) {},

    // only available with v-show transitions
    onLeaveCancelled(el) {}
  }
}
```

</div>

এই হুকগুলি সিএসএস ট্রানজিশন / অ্যানিমেশনের সাথে বা তাদের নিজস্বভাবে ব্যবহার করা যেতে পারে।

জাভাস্ক্রিপ্ট-অনলি ট্রানজিশন ব্যবহার করার সময়, সাধারণত `:css="false"` প্রপ যোগ করা ভালো ধারণা। এটি স্পষ্টভাবে Vue কে স্বয়ংক্রিয় CSS ট্রানজিশন সনাক্তকরণ এড়িয়ে যেতে বলে। সামান্য বেশি পারফরম্যান্স করার পাশাপাশি, এটি সিএসএস নিয়মগুলিকে ঘটনাক্রমে পরিবর্তনের সাথে হস্তক্ষেপ করা থেকেও বাধা দেয়:

```vue-html{3}
<Transition
  ...
  :css="false"
>
  ...
</Transition>
```

`:css="false"` এর সাথে, রূপান্তর শেষ হলে নিয়ন্ত্রণ করার জন্য আমরা সম্পূর্ণরূপে দায়ী। এই ক্ষেত্রে, `@enter` এবং `@leave` হুকের জন্য `done` কলব্যাক প্রয়োজন। অন্যথায়, হুকগুলিকে সিঙ্ক্রোনাসভাবে বলা হবে এবং রূপান্তর অবিলম্বে শেষ হবে।

এখানে একটি ডেমো রয়েছে অ্যানিমেশনগুলি সম্পাদন করার জন্য [GSAP লাইব্রেরি](https://gsap.com/) ব্যবহার করে ৷ আপনি অবশ্যই অন্য যেকোনো অ্যানিমেশন লাইব্রেরি ব্যবহার করতে পারেন, যেমন [Anime.js](https://animejs.com/) অথবা [Motion One](https://motion.dev/)।

<JsHooks />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqNVMtu2zAQ/JUti8I2YD3i1GigKmnaorcCveTQArpQFCWzlkiCpBwHhv+9Sz1qKYckJ3FnlzvD2YVO5KvW4aHlJCGpZUZoB5a7Vt9lUjRaGQcnMLyEM5RGNbDA0sX/VGWpHnB/xEQmmZIWe+zUI9z6m0tnWr7ymbKVzAklQclvvFSG/5COmyWvV3DKJHTdQiRHZN0jAJbRmv9OIA432/UE+jODlKZMuKcErnx8RrazP8woR7I1FEryKaVTU8aiNdRfwWZTQtQwi1HAGF/YB4BTyxNY8JpaJ1go5K/WLTfhdg1Xq8V4SX5Xja65w0ovaCJ8Jvsnpwc+l525F2XH4ac3Cj8mcB3HbxE9qnvFMRzJ0K3APuhIjPefmTTyvWBAGvWbiDuIgeNYRh3HCCDNW+fQmHtWC7a/zciwaO/8NyN3D6qqap5GfVnXAC89GCqt8Bp77vu827+A+53AJrOFzMhQdMnO8dqPpMO74Yx4wqxFtKS1HbBOMdIX4gAMffVp71+Qq2NG4BCIcngBKk8jLOvfGF30IpBGEwcwtO6p9sdwbNXPIadsXxnVyiKB9x83+c3N9WePN9RUQgZO6QQ2sT524KMo3M5Pf4h3XFQ7NwFyZQpuAkML0doEtvEHhPvRDPRkTfq/QNDgRvy1SuIvpFOSDQmbkWTckf7hHsjIzjltkyhqpd5XIVNN5HNfGlW09eAcMp3J+R+pEn7L)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqNVFFvmzAQ/is3pimNlABNF61iaddt2tukvfRhk/xiwIAXsJF9pKmq/PedDTSwh7ZSFLjvzvd9/nz4KfjatuGhE0ES7GxmZIu3TMmm1QahtLyFwugGFu51wRQAU+Lok7koeFcjPDk058gvlv07gBHYGTVGALbSDwmg6USPnNzjtHL/jcBK5zZxxQwZavVNFNqIHwqF8RUAWs2jn4IffCfqQz+mik5lKLWi3GT1hagHRU58aAUSshpV2YzX4ncCcbjZDp099GcG6ZZnEh8TuPR8S0/oTJhQjmQryLUSU0rUU8a8M9wtoWZTQtIwi0nAGJ/ZB0BwKxJYiJpblFko1a8OLzbhdgWXy8WzP99109YCqdIJmgifyfYuzmUzfFF2HH56o/BjAldx/BbRo7pXHKMjGbrl1IcciWn9fyaNfC8YsIueR5wCFFTGUVAEsEs7pOmDu6yW2f6GBW5o4QbeuScLbu91WdZiF/VlvgEtujdcWek09tx3qZ+/tXAzQU1mA8mCoeicneO1OxKP9yM+4ElmLaEFr+2AecVEn8sDZOSrSzv/1qk+sgAOa1kMOyDlu4jK+j1GZ70E7KKJAxRafKzdazi26s8h5dm+NLpTeQLvP27S6+urz/7T5aaUao26TWATt0cPPsgcK3f6Q1wJWVY4AVJtcmHWhueyo89+G38guD+agT5YBf39s25oIv5arehu8krYkLAs8BeG86DfuANYUCG2NomiTrX7Msx0E7ncl0bnXT04566M4PQPykWaWw==)

</div>

## Reusable Transitions {#reusable-transitions}

Vue এর কম্পোনেন্ট সিস্টেমের মাধ্যমে রূপান্তরগুলি পুনরায় ব্যবহার করা যেতে পারে। একটি পুনঃব্যবহারযোগ্য ট্রানজিশন তৈরি করতে, আমরা একটি কম্পোনেন্ট তৈরি করতে পারি যেটি `<Transition>` কম্পোনেন্টকে মোড়ানো এবং স্লট বিষয়অবজেক্টকে পাস করে:

```vue{5}
<!-- MyTransition.vue -->
<script>
// JavaScript hooks logic...
</script>

<template>
  <!-- wrap the built-in Transition component -->
  <Transition
    name="my-transition"
    @enter="onEnter"
    @leave="onLeave">
    <slot></slot> <!-- pass down slot content -->
  </Transition>
</template>

<style>
/*
  Necessary CSS...
  Note: avoid using <style scoped> here since it
  does not apply to slot content.
*/
</style>
```

এখন `MyTransition` বিল্ট-ইন সংস্করণের মতোই আমদানি এবং ব্যবহার করা যেতে পারে:

```vue-html
<MyTransition>
  <div v-if="show">Hello</div>
</MyTransition>
```

## Transition on Appear {#transition-on-appear}

আপনি যদি একটি নোডের প্রাথমিক রেন্ডারে একটি ট্রানজিশন প্রয়োগ করতে চান তবে আপনি `appear` প্রপ যোগ করতে পারেন:

```vue-html
<Transition appear>
  ...
</Transition>
```

## Transition Between Elements {#transition-between-elements}

একটি কম্পোনেন্টকে `v-if` / `v-show` দিয়ে টগল করার পাশাপাশি, আমরা `v-if` / `v-else` / `v-else-if` ব্যবহার করে দুটি কম্পোনেন্টের মধ্যে স্থানান্তর করতে পারি, যতক্ষণ না আমরা নিশ্চিত করি যে কোনো মুহূর্তে শুধুমাত্র একটি কম্পোনেন্ট দেখানো হচ্ছে:

```vue-html
<Transition>
  <button v-if="docState === 'saved'">Edit</button>
  <button v-else-if="docState === 'edited'">Save</button>
  <button v-else-if="docState === 'editing'">Cancel</button>
</Transition>
```

<BetweenElements />

[চেষ্টা করুন](https://play.vuejs.org/#eNqdk8tu2zAQRX9loI0SoLLcFN2ostEi6BekmwLa0NTYJkKRBDkSYhj+9wxJO3ZegBGu+Lhz7syQ3Bd/nJtNIxZN0QbplSMISKNbdkYNznqCPXhcwwHW3g5QsrTsTGekNYGgt/KBBCEsouimDGLCvrztTFtnGGN4QTg4zbK4ojY4YSDQTuOiKwbhN8pUXm221MDd3D11xfJeK/kIZEHupEagrbfjZssxzAgNs5nALIC2VxNILUJg1IpMxWmRUAY9U6IZ2/3zwgRFyhowYoieQaseq9ElDaTRrkYiVkyVWrPiXNdiAcequuIkPo3fMub5Sg4l9oqSevmXZ22dwR8YoQ74kdsL4Go7ZTbR74HT/KJfJlxleGrG8l4YifqNYVuf251vqOYr4llbXz4C06b75+ns1a3BPsb0KrBy14Aymnerlbby8Vc8cTajG35uzFITpu0t5ufzHQdeH6LBsezEO0eJVbB6pBiVVLPTU6jQEPpKyMj8dnmgkQs+HmQcvVTIQK1hPrv7GQAFt9eO9Bk6fZ8Ub52Qiri8eUo+4dbWD02exh79v/nBP+H2PStnwz/jelJ1geKvk/peHJ4BoRZYow==)

## Transition Modes {#transition-modes}

পূর্ববর্তী উদাহরণে, প্রবেশ করা এবং ছেড়ে যাওয়া কম্পোনেন্টগুলি একই সময়ে অ্যানিমেটেড, এবং উভয় কম্পোনেন্টই DOM-এ উপস্থিত থাকলে লেআউট সমস্যা এড়াতে আমাদের সেগুলিকে `position: absolute` করতে হয়েছিল।

যাইহোক, কিছু ক্ষেত্রে এটি একটি বিকল্প নয়, বা সহজভাবে পছন্দসই আচরণ নয়। আমরা হয়তো চাই যে ছেড়ে যাওয়া কম্পোনেন্টটি প্রথমে অ্যানিমেটেড করা হোক এবং প্রবেশের কম্পোনেন্টটি প্রবেশ করানো **পরে** অ্যানিমেশন শেষ হওয়ার পরে। ম্যানুয়ালি এই জাতীয় অ্যানিমেশনগুলি অর্কেস্ট্রেট করা খুব জটিল হবে - সৌভাগ্যক্রমে, আমরা `<Transition>` একটি `mode` প্রপ পাস করে এই আচরণটি সক্ষম করতে পারি:

```vue-html
<Transition mode="out-in">
  ...
</Transition>
```

এখানে `mode="out-in"` সহ আগের ডেমো আছে:

<BetweenElements mode="out-in" />

`<Transition>` এছাড়াও `mode="in-out"` সমর্থন করে, যদিও এটি প্রায়ই কম ব্যবহৃত হয়।

## Transition Between Components {#transition-between-components}

`<Transition>` [ডাইনামিক কম্পোনেন্ট](/guide/essentials/component-basics#dynamic-components) এর আশেপাশেও ব্যবহার করা যেতে পারে:

```vue-html
<Transition name="fade" mode="out-in">
  <component :is="activeComponent"></component>
</Transition>
```

<BetweenComponents />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqtksFugzAMhl/F4tJNKtDLLoxWKnuDacdcUnC3SCGJiMmEqr77EkgLbXfYYZyI8/v77dinZG9M5npMiqS0dScMgUXqzY4p0RrdEZzAfnEp9fc7HuEMx063sPIZq6viTbdmHy+yfDwF5K2guhFUUcBUnkNvcelBGrjTooHaC7VCRXBAoT6hQTRyAH2w2DlsmKq1sgS8JuEwUCfxdgF7Gqt5ZqrMp+58X/5A2BrJCcOJSskPKP0v+K8UyvQENBjcsqTjjdAsAZe2ukHpI3dm/q5wXPZBPFqxZAf7gCrzGfufDlVwqB4cPjqurCChFSjeBvGRN+iTA9afdE+pUD43FjG/bSHsb667Mr9qJot89vCBMl8+oiotDTL8ZsE39UnYpRN0fQlK5A5jEE6BSVdiAdrwWtAAm+zFAnKLr0ydA3pJDDt0x/PrMrJifgGbKdFPfCwpWU+TuWz5omzfVCNcfJJ5geL8pqtFn5E07u7fSHFOj6TzDyUDNEM=)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNqtks9ugzAMxl/F4tJNamGXXVhWqewVduSSgStFCkkUDFpV9d0XJyn9t8MOkxBg5/Pvi+Mci51z5TxhURdi7LxytG2NGpz1BB92cDvYezvAqqxixNLVjaC5ETRZ0Br8jpIe93LSBMfWAHRBYQ0aGms4Jvw6Q05rFvSS5NNzEgN4pMmbcwQgO1Izsj5CalhFRLDj1RN/wis8olpaCQHh4LQk5IiEll+owy+XCGXcREAHh+9t4WWvbFvAvBlsjzpk7gx5TeqJtdG4LbawY5KoLtR/NGjYoHkw+PTSjIqUNWDkwOK97DHUMjVEdqKNMqE272E5dajV+JvpVlSLJllUF4+QENX1ERox0kHzb8m+m1CEfpOgYYgpqVHOmJNpgLQQa7BOdooO8FK+joByxLc4tlsiX6s7HtnEyvU1vKTCMO+4pWKdBnO+0FfbDk31as5HsvR+Hl9auuozk+J1/hspz+mRdPoBYtonzg==)

</div>

## Dynamic Transitions {#dynamic-transitions}

`<Transition>` প্রপস যেমন `name`ও গতিশীল হতে পারে! এটি আমাদেরকে রাষ্ট্রীয় পরিবর্তনের উপর ভিত্তি করে গতিশীলভাবে বিভিন্ন রূপান্তর প্রয়োগ করতে দেয়:

```vue-html
<Transition :name="transitionName">
  <!-- ... -->
</Transition>
```

আপনি যখন Vue-এর ট্রানজিশন ক্লাস কনভেনশনগুলি ব্যবহার করে CSS ট্রানজিশন / অ্যানিমেশনগুলি সংজ্ঞায়িত করেছেন এবং তাদের মধ্যে স্যুইচ করতে চান তখন এটি কার্যকর হতে পারে।

আপনি আপনার কম্পোনেন্টের বর্তমান অবস্থার উপর ভিত্তি করে জাভাস্ক্রিপ্ট ট্রানজিশন হুকগুলিতে বিভিন্ন আচরণ প্রয়োগ করতে পারেন। অবশেষে, ডাইনামিক ট্রানজিশন তৈরির চূড়ান্ত উপায় হল [reusable transition components](#reusable-transitions) যা ব্যবহার করা রূপান্তর(গুলি) এর প্রকৃতি পরিবর্তন করার জন্য প্রপস গ্রহণ করে। এটা চটুল শোনাতে পারে, কিন্তু একমাত্র সীমা সত্যিই আপনার কল্পনা.

## Transitions with the Key Attribute {#transitions-with-the-key-attribute}

কখনও কখনও একটি পরিবর্তন ঘটানোর জন্য আপনাকে একটি DOM উপাদানের পুনরায় রেন্ডার করতে বাধ্য করতে হবে৷

উদাহরণস্বরূপ এই কাউন্টার উপাদান নিন।

<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue';
const count = ref(0);

setInterval(() => count.value++, 1000);
</script>

<template>
  <Transition>
    <span :key="count">{{ count }}</span>
  </Transition>
</template>
```

</div>
<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 1,
      interval: null 
    }
  },
  mounted() {
    this.interval = setInterval(() => {
      this.count++;
    }, 1000)
  },
  beforeDestroy() {
    clearInterval(this.interval)
  }
}
</script>

<template>
  <Transition>
    <span :key="count">{{ count }}</span>
  </Transition>
</template>
```

</div>

যদি আমরা `key` অ্যাট্রিবিউটটি বাদ দিতাম, তবে শুধুমাত্র টেক্সট নোড আপডেট করা হবে এবং এইভাবে কোনো রূপান্তর ঘটবে না। যাইহোক, 'কী' অ্যাট্রিবিউটের জায়গায়, Vue একটি নতুন `span` উপাদান তৈরি করতে জানে যখনই `count` পরিবর্তিত হয় এবং এইভাবে `Transition` উপাদানটির মধ্যে স্থানান্তরের জন্য ২টি ভিন্ন উপাদান থাকে।

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNp9UsFu2zAM/RVCl6Zo4nhYd/GcAtvQQ3fYhq1HXTSFydTKkiDJbjLD/z5KMrKgLXoTHx/5+CiO7JNz1dAja1gbpFcuQsDYuxtuVOesjzCCxx1MsPO2gwuiXnzkhhtpTYggbW8ibBJlUV/mBJXfmYh+EHqxuITNDYzcQGFWBPZ4dUXEaQnv6jrXtOuiTJoUROycFhEpAmi3agCpRQgbzp68cA49ZyV174UJKiprckxIcMJA84hHImc9oo7jPOQ0kQ4RSvH6WXW7JiV6teszfQpDPGqEIK3DLSGpQbazsyaugvqLDVx77JIhbqp5wsxwtrRvPFI7NWDhEGtYYVrQSsgELzOiUQw4I2Vh8TRgA9YJqeIR6upDABQh9TpTAPE7WN3HlxLp084Foi3N54YN1KWEVpOMkkO2ZJHsmp3aVw/BGjqMXJE22jml0X93STRw1pReKSe0tk9fMxZ9nzwVXP5B+fgK/hAOCePsh8dAt4KcnXJR+D3S16X07a9veKD3KdnZba+J/UbyJ+Zl0IyF9rk3Wxr7jJenvcvnrcz+PtweItKuZ1Np0MScMp8zOvkvb1j/P+776jrX0UbZ9A+fYSTP)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNp9U8tu2zAQ/JUFTwkSyw6aXlQ7QB85pIe2aHPUhZHWDhOKJMiVYtfwv3dJSpbbBgEMWJydndkdUXvx0bmi71CUYhlqrxzdVAa3znqCBtey0wT7ygA0kuTZeX4G8EidN+MJoLadoRKuLkdAGULfS12C6bSGDB/i3yFx2tiAzaRIjyoUYxesICDdDaczZq1uJrNETY4XFx8G5Uu4WiwW55PBA66txy8YyNvdZFNrlP4o/Jdpbq4M/5bzYxZ8IGydloR8Alg2qmcVGcKqEi9eOoe+EqnExXsvTVCkrBkQxoKTBspn3HFDmprp+32ODA4H9mLCKDD/R2E5Zz9+Ws5PpuBjoJ1GCLV12DASJdKGa2toFtRvLOHaY8vx8DrFMGdiOJvlS48sp3rMHGb1M4xRzGQdYU6REY6rxwHJGdJxwBKsk7WiHSyK9wFQhqh14gDyIVjd0f8Wa2/bUwOyWXwQLGGRWzicuChvKC4F8bpmrTbFU7CGL2zqiJm2Tmn03100DZUox5ddCam1ffmaMPJd3Cnj9SPWz6/gT2EbsUr88Bj4VmAljjWSfoP88mL59tc33PLzsdjaptPMfqP4E1MYPGOmfepMw2Of8NK0d238+JTZ3IfbLSFnPSwVB53udyX4q/38xurTuO+K6/Fqi8MffqhR/A==)

</div>

---

**সম্পর্কিত**

- [`<Transition>` API রেফারেন্স](/api/built-in-components#transition)
