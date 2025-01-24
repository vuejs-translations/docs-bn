# Custom Renderer API {#custom-renderer-api}

## createRenderer() {#createrenderer}

একটি কাস্টম রেন্ডারার তৈরি করে। আপনি  Vue-এর মূল রানটাইম ব্যবহার করতে পারেন non-DOM এনভারনমেন্ট লক্ষ্য করার জন্য এবং প্ল্যাটফর্ম-নির্দিষ্ট নোড তৈরি এবং ম্যানিপুলেশন API প্রদান করে ।

- **Type**

  ```ts
  function createRenderer<HostNode, HostElement>(
    options: RendererOptions<HostNode, HostElement>
  ): Renderer<HostElement>

  interface Renderer<HostElement> {
    render: RootRenderFunction<HostElement>
    createApp: CreateAppFunction<HostElement>
  }

  interface RendererOptions<HostNode, HostElement> {
    patchProp(
      el: HostElement,
      key: string,
      prevValue: any,
      nextValue: any,
      namespace?: ElementNamespace,
      parentComponent?: ComponentInternalInstance | null,
    ): void
    insert(el: HostNode, parent: HostElement, anchor?: HostNode | null): void
    remove(el: HostNode): void
    createElement(
      type: string,
      namespace?: ElementNamespace,
      isCustomizedBuiltIn?: string,
      vnodeProps?: (VNodeProps & { [key: string]: any }) | null,
    ): HostElement
    createText(text: string): HostNode
    createComment(text: string): HostNode
    setText(node: HostNode, text: string): void
    setElementText(node: HostElement, text: string): void
    parentNode(node: HostNode): HostElement | null
    nextSibling(node: HostNode): HostNode | null
    querySelector?(selector: string): HostElement | null
    setScopeId?(el: HostElement, id: string): void
    cloneNode?(node: HostNode): HostNode
    insertStaticContent?(
      content: string,
      parent: HostElement,
      anchor: HostNode | null,
      namespace: ElementNamespace,
      start?: HostNode | null,
      end?: HostNode | null,
    ): [HostNode, HostNode]
  }
  ```

- **Example**

  ```js
  import { createRenderer } from '@vue/runtime-core'

  const { render, createApp } = createRenderer({
    patchProp,
    insert,
    remove,
    createElement
    // ...
  })

  // `render` is the low-level API
  // `createApp` returns an app instance
  export { render, createApp }

  // re-export Vue core APIs
  export * from '@vue/runtime-core'
  ```

Vue-এর নিজস্ব `@vue/runtime-dom` হল [implemented using the same API](https://github.com/vuejs/core/blob/main/packages/runtime-dom/src/index.ts)। সহজ বাস্তবায়নের জন্য, দেখুন [`@vue/runtime-test`](https://github.com/vuejs/core/blob/main/packages/runtime-test/src/index.ts) যা একটি প্রাইভেট প্যাকেজ Vue এর নিজস্ব ইউনিট টেস্টের জন্য।
