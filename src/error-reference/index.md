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

## রানটাইম এরর {#runtime-errors}  

প্রোডাকশন বিল্ডে, নিম্নলিখিত এরর হ্যান্ডলার API-তে পাস করা হয়, আর্গুমেন্টটি সম্পূর্ণ তথ্যের স্ট্রিংয়ের পরিবর্তে একটি সংক্ষিপ্ত কোড হিসেবে প্রদর্শিত হবে:  

- [`app.config.errorHandler`](/api/application#app-config-errorhandler)  
- [`onErrorCaptured`](/api/composition-api-lifecycle#onerrorcaptured) (Composition API)  
- [`errorCaptured`](/api/options-lifecycle#errorcaptured) (Options API)  

নিম্নের সারণীতে প্রতিটি কোড তাদের সংশ্লিষ্ট সম্পূর্ণ তথ্যের স্ট্রিংয়ের সাথে ম্যাপ করা হয়েছে।  

<ErrorsTable kind="runtime" :errors="data.runtime" :highlight="highlight" />  

## কম্পাইলার এরর {#compiler-errors}  

নিচের সারণীতে উৎপন্ন কম্পাইলার এরর কোড এবং তাদের মূল বার্তার মধ্যে ম্যাপিং প্রদান করা হয়েছে।  

<ErrorsTable kind="compiler" :errors="data.compiler" :highlight="highlight" />
