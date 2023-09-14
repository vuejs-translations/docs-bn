# Server-Side Rendering API {#server-side-rendering-api}

## renderToString() {#rendertostring}

- **`vue/server-renderer` থেকে ইমপোর্ট করা হয়েছে**

- **প্রকার**

  ```ts
  function renderToString(
    input: App | VNode,
    context?: SSRContext
  ): Promise<string>
  ```

- **উদাহরন**

  ```js
  import { createSSRApp } from 'vue'
  import { renderToString } from 'vue/server-renderer'

  const app = createSSRApp({
    data: () => ({ msg: 'hello' }),
    template: `<div>{{ msg }}</div>`
  })

  ;(async () => {
    const html = await renderToString(app)
    console.log(html)
  })()
  ```

  ### SSR Context {#ssr-context}

  আপনি একটি অপশনাল কন্টেক্সট অবজেক্ট পাস করতে পারেন, যা রেন্ডারের সময় অতিরিক্ত ডেটা রেকর্ড করতে ব্যবহার করা যেতে পারে, উদাহরণস্বরূপ [টেলিপোর্টের কনটেন্ট অ্যাক্সেস করা](/guide/scaling-up/ssr#teleports):

  ```js
  const ctx = {}
  const html = await renderToString(app, ctx)

  console.log(ctx.teleports) // { '#teleported': 'teleported content' }
  ```

  এই পেইজের বেশিরভাগ অন্যান্য SSR APIগুলিও ঐচ্ছিকভাবে একটি কন্টেক্সট অবজেক্ট গ্রহণ করে। কন্টেক্সট অবজেক্টটি [useSSRContext](#usessrcontext) হেল্পার মাধ্যমে কম্পোনেন্ট কোডে অ্যাক্সেস করা যেতে পারে।

- **আরো দেখুন** [Guide - Server-Side Rendering](/guide/scaling-up/ssr)

## renderToNodeStream() {#rendertonodestream}

একটি [Node.js Readable stream](https://nodejs.org/api/stream.html#stream_class_stream_readable) হিসাবে ইনপুট রেন্ডার করে।

- **`vue/server-renderer` থেকে এক্সপোর্ট করা হয়েছে**

- **প্রকার**

  ```ts
  function renderToNodeStream(
    input: App | VNode,
    context?: SSRContext
  ): Readable
  ```

- **উদাহরন**

  ```js
  // inside a Node.js http handler
  renderToNodeStream(app).pipe(res)
  ```

  :::tip নোট
  এই মেথডটি `vue/server-renderer`-এর ESM বিল্ডে সাপোর্টেড নয়, যা Node.js এনভারনমেন্ট থেকে ডিকপল করা হয়েছে। পরিবর্তে [`pipeToNodeWritable`](#pipetonodewritable) ব্যবহার করুন।
  :::

## pipeToNodeWritable() {#pipetonodewritable}

একটি বিদ্যমান [Node.js Writable stream](https://nodejs.org/api/stream.html#stream_writable_streams) উদাহরণে রেন্ডার এবং পাইপ করুন।

- **`vue/server-renderer` থেকে এক্সপোর্ট করার হয়েছে**

- **প্রকার**

  ```ts
  function pipeToNodeWritable(
    input: App | VNode,
    context: SSRContext = {},
    writable: Writable
  ): void
  ```

- **উদাহরন**

  ```js
  // inside a Node.js http handler
  pipeToNodeWritable(app, {}, res)
  ```

## renderToWebStream() {#rendertowebstream}

একটি [Web ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) হিসাবে ইনপুট রেন্ডার করে।

- **`vue/server-renderer` থেকে এক্সপোর্ট করা হয়েছে**

- **প্রকার**

  ```ts
  function renderToWebStream(
    input: App | VNode,
    context?: SSRContext
  ): ReadableStream
  ```

- **উদাহরন**

  ```js
  // inside an environment with ReadableStream support
  return new Response(renderToWebStream(app))
  ```

  :::tip নোট
  যে ইনভারনমেন্টগুলি গ্লোবাল স্কোপে `ReadableStream` কনস্ট্রাক্টরকে প্রকাশ করে না, সেখানে [`pipeToWebWritable()`](#pipetowebwritable) ব্যবহার করা উচিত।
  :::

## pipeToWebWritable() {#pipetowebwritable}

একটি বিদ্যমান [Web WritableStream](https://developer.mozilla.org/en-US/docs/Web/API/WritableStream) উদাহরণে রেন্ডার এবং পাইপ করুন।

- **`vue/server-renderer` থেকে এক্সপোর্ট করা হয়েছে**

- **প্রকার**

  ```ts
  function pipeToWebWritable(
    input: App | VNode,
    context: SSRContext = {},
    writable: WritableStream
  ): void
  ```

- **উদাহরন**

  এটি সাধারণত [`TransformStream`](https://developer.mozilla.org/en-US/docs/Web/API/TransformStream) এর সাথে ব্যবহার করা হয়:

  ```js
  // TransformStream is available in environments such as CloudFlare workers.
  // in Node.js, TransformStream needs to be explicitly imported from 'stream/web'
  const { readable, writable } = new TransformStream()
  pipeToWebWritable(app, {}, writable)

  return new Response(readable)
  ```

## renderToSimpleStream() {#rendertosimplestream}

একটি সহজ রিডএ্যাবল ইন্টারফেস ব্যবহার করে স্ট্রিমিং মোডে ইনপুট রেন্ডার করে।

- **`vue/server-renderer` থেকে এক্সপোর্ট করা হয়েছে**

- **প্রকার**

  ```ts
  function renderToSimpleStream(
    input: App | VNode,
    context: SSRContext,
    options: SimpleReadable
  ): SimpleReadable

  interface SimpleReadable {
    push(content: string | null): void
    destroy(err: any): void
  }
  ```

- **উদাহরন**

  ```js
  let res = ''

  renderToSimpleStream(
    app,
    {},
    {
      push(chunk) {
        if (chunk === null) {
          // done
          console(`render complete: ${res}`)
        } else {
          res += chunk
        }
      },
      destroy(err) {
        // error encountered
      }
    }
  )
  ```

## useSSRContext() {#usessrcontext}

একটি রানটাইম API যা `renderToString()` বা অন্যান্য সার্ভার রেন্ডার API-এ পাস করা কনটেক্স অবজেক্ট পুনরুদ্ধার করতে ব্যবহৃত হয়।

- **প্রকার**

  ```ts
  function useSSRContext<T = Record<string, any>>(): T | undefined
  ```

- **উদাহরন**

  পুনরুদ্ধার করা কনটেক্সটি চূড়ান্ত HTML রেন্ডার করার জন্য প্রয়োজনীয় তথ্য সংযুক্ত করতে ব্যবহার করা যেতে পারে (যেমন হেড মেটাডেটা)।

  ```vue
  <script setup>
  import { useSSRContext } from 'vue'

  // make sure to only call it during SSR
  // https://vitejs.dev/guide/ssr.html#conditional-logic
  if (import.meta.env.SSR) {
    const ctx = useSSRContext()
    // ...attach properties to the context
  }
  </script>
  ```
