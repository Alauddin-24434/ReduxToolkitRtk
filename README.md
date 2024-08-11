# ReduxToolkitRtk with typescript react

1. install reduxjs/toolkit
  <pre>
    <code>  npm install @reduxjs/toolkit</code>
  </pre>

2. If you need React bindings:

  <pre>
     <code> npm install react-redux</code>
  </pre>

  3. create folder and file

<pre><code>
├── src
│   ├── redux
│   │   ├── api
│   │   │   └── baseApi.ts
│   │   ├── features
│   │   │   ├── auth
│   │   │   │   ├── authApi.ts
│   │   │   │   └── authSlice.ts
│   │   ├── hooks.ts
│   │   └── store.ts
│   ├── utils
│   └── main.tsx



</code></pre>

4. store.ts besic bryler plate
<pre> <code>
  import { configureStore } from '@reduxjs/toolkit'
// ...

export const store = configureStore({
  reducer: {
    posts: postsReducer,
    comments: commentsReducer,
    users: usersReducer,
  },
})

// Infer the `RootState` and `AppDispatch` types from the store itself
export type RootState = ReturnType<typeof store.getState>
// Inferred type: {posts: PostsState, comments: CommentsState, users: UsersState}
export type AppDispatch = typeof store.dispatch
</code></pre>

5. api/baseApi.ts besic broyler plate

<pre> <code>
  import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

export const baseApi = createApi({
  reducerPath: "baseApi",
  baseQuery: fetchBaseQuery({
    baseUrl: "http://localhost:5000/api",

    // এখানে credentials: "include" করা হয়েছে ব্যাকেন্ড থেকে পাঠানো refreshToken Cookie এর ভিতরে পাঠানোর জন্য।
    // তাই credentials: "include" অবশ্যই লিখতে হবে যদি cookie তে টোকেন পাঠানো হয়। আর কুকিতে কোন টোকেন না পাঠালে   credentials: "include", না লিখলেও চলবে।
    credentials: "include",
  }),
  endpoints: () => ({}),
});


</code></pre>
*. main.tsx file setup flow main.png image screenShort
<pre> <code>

</code></pre>

<pre> <code></code></pre>
<pre> <code></code></pre>
