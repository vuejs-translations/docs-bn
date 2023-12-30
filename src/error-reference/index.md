<script setup>
import { ref, onMounted } from 'vue'
import { data } from './errors.data.ts'
import ErrorsTable from './ErrorsTable.vue'

const highlight = ref()
onMounted(() => {
  highlight.value = location.hash.slice(1)
})
</script>

# Production Error Code Reference {#error-reference}

## Runtime Errors {#runtime-errors}

প্রোডাকশন বিল্ডে, নিম্নলিখিত ত্রুটি হ্যান্ডলার API-এ পাস করা 3য় আর্গুমেন্ট সম্পূর্ণ তথ্যের স্ট্রিংয়ের পরিবর্তে একটি ছোট কোড হবে:

- [`app.config.errorHandler`](/api/application#app-config-errorhandler)
- [`onErrorCaptured`](/api/composition-api-lifecycle#onerrorcaptured) (Composition API)
- [`errorCaptured`](/api/options-lifecycle#errorcaptured) (Options API)

নিম্নলিখিত সারণীটি কোডগুলিকে তাদের মূল সম্পূর্ণ তথ্যের স্ট্রিংগুলিতে ম্যাপ করে৷

<ErrorsTable kind="runtime" :errors="data.runtime" :highlight="highlight" />

## Compiler Errors {#compiler-errors}

নিম্নলিখিত টেবিলটি তাদের মূল বার্তাগুলিতে উত্পাদন কম্পাইলার ত্রুটি কোডগুলির একটি ম্যাপিং প্রদান করে।

<ErrorsTable kind="compiler" :errors="data.compiler" :highlight="highlight" />
