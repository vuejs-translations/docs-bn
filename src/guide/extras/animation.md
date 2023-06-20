<script setup>
import ElasticHeader from './demos/ElasticHeader.vue'
import DisabledButton from './demos/DisabledButton.vue'
import Colors from './demos/Colors.vue'
import AnimateWatcher from './demos/AnimateWatcher.vue'
</script>

# Animation Techniques {#animation-techniques}

Vue এন্টার/লিভ এবং লিস্ট ট্রানজিশন পরিচালনার জন্য [`<Transition>`](/guide/built-ins/transition) এবং [`<TransitionGroup>`](/guide/built-ins/transition-group) উপাদান প্রদান করে। যাইহোক, ওয়েবে অ্যানিমেশন ব্যবহার করার আরও অনেক উপায় রয়েছে, এমনকি Vue অ্যাপ্লিকেশনেও। এখানে আমরা কয়েকটি অতিরিক্ত কৌশল নিয়ে আলোচনা করব।

## Class-based Animations {#class-based-animations}

যে উপাদানগুলি DOM এ প্রবেশ করছে না / ছাড়ছে না, আমরা গতিশীলভাবে একটি CSS ক্লাস যোগ করে অ্যানিমেশন ট্রিগার করতে পারি:

<div class="composition-api">

```js
const disabled = ref(false)

function warnDisabled() {
  disabled.value = true
  setTimeout(() => {
    disabled.value = false
  }, 1500)
}
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      disabled: false
    }
  },
  methods: {
    warnDisabled() {
      this.disabled = true
      setTimeout(() => {
        this.disabled = false
      }, 1500)
    }
  }
}
```

</div>

```vue-html
<div :class="{ shake: disabled }">
  <button @click="warnDisabled">Click me</button>
  <span v-if="disabled">This feature is disabled!</span>
</div>
```

```css
.shake {
  animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
  transform: translate3d(0, 0, 0);
}

@keyframes shake {
  10%,
  90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%,
  80% {
    transform: translate3d(2px, 0, 0);
  }

  30%,
  50%,
  70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%,
  60% {
    transform: translate3d(4px, 0, 0);
  }
}
```

<DisabledButton />

## State-driven Animations {#state-driven-animations}

কিছু ট্রানজিশন ইফেক্ট মান ইন্টারপোলেটিং দ্বারা প্রয়োগ করা যেতে পারে, উদাহরণস্বরূপ একটি উপাদানের সাথে একটি স্টাইল আবদ্ধ করে যখন একটি মিথস্ক্রিয়া ঘটে। উদাহরণস্বরূপ এই উদাহরণ নিন:

<div class="composition-api">

```js
const x = ref(0)

function onMousemove(e) {
  x.value = e.clientX
}
```

</div>
<div class="options-api">

```js
export default {
  data() {
    return {
      x: 0
    }
  },
  methods: {
    onMousemove(e) {
      this.x = e.clientX
    }
  }
}
```

</div>

```vue-html
<div
  @mousemove="onMousemove"
  :style="{ backgroundColor: `hsl(${x}, 80%, 50%)` }"
  class="movearea"
>
  <p>Move your mouse across this div...</p>
  <p>x: {{ x }}</p>
</div>
```

```css
.movearea {
  transition: 0.3s background-color ease;
}
```

<Colors />

রঙ ছাড়াও, আপনি রূপান্তর, প্রস্থ বা উচ্চতা অ্যানিমেট করতে শৈলী বাইন্ডিংগুলিও ব্যবহার করতে পারেন। এমনকি আপনি স্প্রিং ফিজিক্স ব্যবহার করে এসভিজি পাথগুলিকে অ্যানিমেট করতে পারেন - সর্বোপরি, এগুলি সমস্ত ডেটা বাইন্ডিং বৈশিষ্ট্য:

<ElasticHeader />

## Animating with Watchers {#animating-with-watchers}

কিছু সৃজনশীলতার সাথে, আমরা কিছু সংখ্যাগত অবস্থার উপর ভিত্তি করে যে কোনও কিছুকে অ্যানিমেট করতে পর্যবেক্ষকদের ব্যবহার করতে পারি। উদাহরণস্বরূপ, আমরা সংখ্যাটি নিজেই অ্যানিমেট করতে পারি:

<div class="composition-api">

```js
import { ref, reactive, watch } from 'vue'
import gsap from 'gsap'

const number = ref(0)
const tweened = reactive({
  number: 0
})

watch(number, (n) => {
  gsap.to(tweened, { duration: 0.5, number: Number(n) || 0 })
})
```

```vue-html
Type a number: <input v-model.number="number" />
<p>{{ tweened.number.toFixed(0) }}</p>
```

</div>
<div class="options-api">

```js
import gsap from 'gsap'

export default {
  data() {
    return {
      number: 0,
      tweened: 0
    }
  },
  watch: {
    number(n) {
      gsap.to(this, { duration: 0.5, tweened: Number(n) || 0 })
    }
  }
}
```

```vue-html
Type a number: <input v-model.number="number" />
<p>{{ tweened.toFixed(0) }}</p>
```

</div>

<AnimateWatcher />

<div class="composition-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNpNUstygzAM/BWNLyEzBDKd6YWSdHrpsacefSGgJG7xY7BImhL+vTKv9ILllXYlr+jEm3PJpUWRidyXjXIEHql1e2mUdrYh6KDBY8yfoiR1wRiuBZVn6OHYWA0r5q6W2pMv3ISHkBPSlNZ4AtPqAzawC2LRdj3DdEU0WA34qB910sBUnsFWmp6LpRmaRo9UHMLIrGG3h4EBQ/OEbDRpxjx51TYFKWtYKHmOF9WP4Qzs+x22EDoA9NLwmaejC/x+vhBqVxeEfAPIK3WBsi6830lRobZSDDjA580hFIt8roxrCS4bbSuskxFmzhhIAenEy92id1CnzZzfd91szETmZ72rH6zYOej7PA3rYXrKE3GUp//m5KunWx3C5CE6enS0hjZXVKczZXCwdfWyoF79YgZPqBliJ9iGSUTEYlzuRrO9X94a/lUGNTklvBTZvAMpwhYCIMWZyPksTVvjvk9JaXUacq9sSlujFJPnvej/AElH3FQ=)

</div>
<div class="options-api">

[চেষ্টা করুন](https://play.vuejs.org/#eNpNUctugzAQ/JWVLyESj6hSL5Sm6qXHnnr0xYENuAXbwus8Svj3GlxIJEvendHMvgb2bkx6cshyVtiyl4b2XMnO6J6gtsLAsdcdbKZwwxVXeJmpCo/CtQQDVwCVIBFtQwzQI7leLRmAct0B+xx28YLQGVFh5aGAjNM3zvRZUNnkizhII7V6w9xTSjqiRtoYBqhcL0hq5c3S5/hu/blKbzfYwbh9LMWVf0W2zusTws60gnDK6OtqEMTaeSGVcQSnpNMVtmmAXzkLAWeQzarCQNkKaz1zkHWysPthWNryjX/IC1bRbgvjWGTG64rssbQqLF3bKUzvHmH6o1aUnFHWDeVw0G31sqJW/mIOT9h5KEw2m7CYhUsmnV/at9XKX3n24v+E5WxdNmfTbieAs4bI2DzLnDI/dVrqLpu4Nz+/a5GzZYls/AM3dcFx)

</div>
