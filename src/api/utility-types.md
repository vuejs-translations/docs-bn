# Utility Types {#utility-types}

:::info
এই পেইজটি শুধুমাত্র কয়েকটি সাধারণভাবে ব্যবহৃত ইউটিলিটি টাইপের লিস্ট করে যেগুলির ব্যবহারের জন্য ব্যাখ্যার প্রয়োজন হতে পারে। এক্সপোর্টেড করা টাইপের সম্পূর্ণ লিস্টের জন্য, [source code](https://github.com/vuejs/core/blob/main/packages/runtime-core/src/index.ts#L131) দেখুন।
:::

## PropType\<T> {#proptype-t}

রানটাইম প্রপ ডিক্লেয়ার ব্যবহার করার সময় আরও উন্নত ধরনের সহ একটি প্রপ টীকা করতে ব্যবহৃত হয়।

- **উদাহরন**

  ```ts
  import type { PropType } from 'vue'

  interface Book {
    title: string
    author: string
    year: number
  }

  export default {
    props: {
      book: {
        // provide more specific type to `Object`
        type: Object as PropType<Book>,
        required: true
      }
    }
  }
  ```

- **আরো দেখুন** [Guide - Typing Component Props](/guide/typescript/options-api#typing-component-props)

## MaybeRef\<T> {#mayberef}

`T | এর এলিয়াস রেফারেন্স<T>`। [Composables](/guide/reusability/composables.html) এর আর্গুমেন্ট টীকা করার জন্য দরকারী।

- শুধুমাত্র 3.3+ এ সমর্থিত।

## MaybeRefOrGetter\<T> {#maybereforgetter}

`T | এর এলিয়াস রেফারেন্স<T>`। [Composables](/guide/reusability/composables.html) এর আর্গুমেন্ট টীকা করার জন্য দরকারী।

- শুধুমাত্র 3.3+ এ সমর্থিত।

## ExtractPropTypes\<T> {#extractproptypes}

রানটাইম প্রপ অপশন অবজেক্ট থেকে প্রপ টাইপগুলি এক্সট্রাক্ট করুন। এক্সট্রাক্টেড টাইপগুলি অভ্যন্তরীণ মুখোমুখি - অর্থাৎ কম্পোনেন্ট দ্বারা প্রাপ্ত সমাধানকৃত প্রপস৷ এর অর্থ হল বুলিয়ান প্রপস এবং ডিফল্ট ভ্যালুসহ প্রপগুলি সর্বদা ডিফাইনড করা হয়, এমনকি তাদের প্রয়োজন না হলেও।

পাবলিক ফেসিং প্রপস বের করতে, যেমন প্রপস যা প্যারেন্টকে পাস করার এ্যালাউ করা হয়েছে, [`ExtractPublicPropTypes`](#extractpublicproptypes) ব্যবহার করুন।

- **উদাহরন**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar: boolean,
  //   baz: number,
  //   qux: number
  // }
  ```

## ExtractPublicPropTypes\<T> {#extractpublicproptypes}

রানটাইম প্রপ অপশন অবজেক্ট থেকে প্রপ টাইপগুলি বের করুন। এক্সট্রাক্টেড টাইপগুলি পাবলিক ফেসিং - যেমন প্রপস যা প্যারেন্টকে পাস করার অনুমতি দেওয়া হয়েছে৷

- **উদাহরন**

  ```ts
  const propsOptions = {
    foo: String,
    bar: Boolean,
    baz: {
      type: Number,
      required: true
    },
    qux: {
      type: Number,
      default: 1
    }
  } as const

  type Props = ExtractPublicPropTypes<typeof propsOptions>
  // {
  //   foo?: string,
  //   bar?: boolean,
  //   baz: number,
  //   qux?: number
  // }
  ```

## ComponentCustomProperties {#componentcustomproperties}

কাস্টম গ্লোবাল প্রোপার্টি সমর্থন করার জন্য কম্পোনেন্ট ইন্সট্যান্স টাইপ বৃদ্ধি ব্যবহৃত হয়।

- **উদাহরন**

  ```ts
  import axios from 'axios'

  declare module 'vue' {
    interface ComponentCustomProperties {
      $http: typeof axios
      $translate: (key: string) => string
    }
  }
  ```

  :::tip
  অগমেন্টেশনগুলি অবশ্যই একটি মডিউল `.ts` বা `.d.ts` ফাইলে স্থাপন করতে হবে। আরো বিস্তারিত জানার জন্য [Type Augmentation Placement](/guide/typescript/options-api#augmenting-global-properties) দেখুন।
  :::

- **আরো দেখুন** [Guide - Augmenting Global Properties](/guide/typescript/options-api#augmenting-global-properties)

## ComponentCustomOptions {#componentcustomoptions}

কাস্টম টাইপগুলিকে সমর্থন করার জন্য কম্পোনেন্ট অপশনের টাইপ বৃদ্ধি করতে ব্যবহৃত হয়।

- **উদাহরন**

  ```ts
  import { Route } from 'vue-router'

  declare module 'vue' {
    interface ComponentCustomOptions {
      beforeRouteEnter?(to: any, from: any, next: () => void): void
    }
  }
  ```

  :::tip
  অগমেন্টেশনগুলি অবশ্যই একটি মডিউল `.ts` বা `.d.ts` ফাইলে স্থাপন করতে হবে। আরো বিস্তারিত জানার জন্য [Type Augmentation Placement](/guide/typescript/options-api#augmenting-global-properties) দেখুন।
  :::

- **আরো দেখুন** [Guide - Augmenting Custom Options](/guide/typescript/options-api#augmenting-custom-options)

## ComponentCustomProps {#componentcustomprops}

Used to augment allowed TSX props in order to use non-declared props on TSX elements.

- **Example**

  ```ts
  declare module 'vue' {
    interface ComponentCustomProps {
      hello?: string
    }
  }

  export {}
  ```

  ```tsx
  // now works even if hello is not a declared prop
  <MyComponent hello="world" />
  ```

  :::tip
  Augmentations must be placed in a module `.ts` or `.d.ts` file. See [Type Augmentation Placement](/guide/typescript/options-api#augmenting-global-properties) for more details.
  :::

## CSSProperties {#cssproperties}

Used to augment allowed values in style property bindings.

- **Example**

  Allow any custom CSS property

  ```ts
  declare module 'vue' {
    interface CSSProperties {
      [key: `--${string}`]: string
    }
  }
  ```

  ```tsx
  <div style={ { '--bg-color': 'blue' } }>
  ```

  ```html
  <div :style="{ '--bg-color': 'blue' }"></div>
  ```

:::tip
Augmentations must be placed in a module `.ts` or `.d.ts` file. See [Type Augmentation Placement](/guide/typescript/options-api#augmenting-global-properties) for more details.
:::

:::info See also
SFC `<style>` tags support linking CSS values to dynamic component state using the `v-bind` CSS function. This allows for custom properties without type augmentation.

- [v-bind() in CSS](/api/sfc-css-features#v-bind-in-css)
  :::
