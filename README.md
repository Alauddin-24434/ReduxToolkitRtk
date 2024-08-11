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

    // এখানে credentials: "include" করা হয়েছে ব্যাকেন্ড থেকে পাঠানো refreshToken Cookie এর ভিতরে পাঠানোর জন্য। তাই credentials: "include" অবশ্যই লিখতে হবে যদি cookie তে টোকেন পাঠানো হয়। আর কুকিতে কোন টোকেন না পাঠালে   credentials: "include", না লিখলেও চলবে।
    credentials: "include",
  }),
  endpoints: () => ({}),
});
</code></pre>

6. features/auth/authApi this file code inject with base api
  <pre> <code>
    import { baseApi } from "../../api/baseApi";


const authApi=baseApi.injectEndpoints({
    endpoints:(builder)=>({
        login: builder.mutation({
            query:(userInfo)=>({
                url:'/auth/login-user',
                method:'POST',
                body:userInfo,
            })
        })
    })
})


export const {useLoginMutation}= authApi;
  </code></pre>

7. features/auth/authSlice this file code represent redux local state 
<pre> <code>
  import { createSlice } from "@reduxjs/toolkit";
import { RootState } from "../../store";

type TAuthSate={
    user:null | object;
    token:null | string;
}

const initialState:TAuthSate={
    user:null,
    token:null,
}

const authSlice= createSlice({
    name:'auth',
    initialState,
    reducers:{
        setUser: (state, action)=>{
            const {user, token}= action.payload;
            state.user=user;
            state.token=token;
        },
        logout: (state)=>{
            state.user= null;
            state.token=null;
        }
    }

})

export const {setUser,logout}= authSlice.actions;

export default authSlice.reducer;
// নিচের দুই লাইনের কোডগুলি লিখা হয়েছে redux এর loacl state এর auth নামক state থেকে token এবং user কে নেয়ার জন্য, সাথে ২ টি কেই  export  করা হয়েছে  অন্য ফাইল থেকে এ ২ টি কেই import করার জন্য।
export const useCurrentToken=(state:RootState)=>state.auth.token;
export const useCurrenUser=(state:RootState)=>state.auth.user;
</code></pre>
8. redix persit use inside store.ts file code
# important 
### Redux Persist হলো একটি লাইব্রেরি যা Redux স্টোরের অবস্থা (state) local storage বা session storage এর মতো স্টোরেজে সংরক্ষণ করতে এবং অ্যাপ্লিকেশন রিফ্রেশ বা পুনরায় চালু হওয়ার সময় সেই সংরক্ষিত অবস্থা পুনরুদ্ধার করতে সাহায্য করে। এটি নিশ্চিত করে যে অ্যাপ্লিকেশন রিফ্রেশ হলেও Redux স্টোরের অবস্থা হারিয়ে না যায়।
<pre> <code>
  import { configureStore } from "@reduxjs/toolkit";

import authReducer from "./features/auth/authSlice";
import { baseApi } from "./api/baseApi";
  
import {
  persistStore,
  persistReducer,
  FLUSH,
  REHYDRATE,
  PAUSE,
  PERSIST,
  PURGE,
  REGISTER,
} from "redux-persist";
import storage from "redux-persist/lib/storage"; // defaults to localStorage for web

// redux persist  রিলেটেড কোড
const persistConfig = {
  key: "auth",
  storage,
};

  // redux persist  রিলেটেড কোড
const persistedAuthReducer = persistReducer(persistConfig, authReducer);

export const store = configureStore({
  reducer: {
    [baseApi.reducerPath]: baseApi.reducer,
// auth: authReducer এট ডিফল্ট ছিল, যদি redux persist add না করা হতো।  কিন্তু auth: persistedAuthReducer প্রয়জন হয়েছে  auth  নামাক  sate এ যাতে করে persist/persit or perst/rehidret হয় তার কারনে auth: authReducer রিপ্লেস করে লিখা হয়েছে
    auth: persistedAuthReducer,
  },
  middleware: (getDefaultMiddlewares) =>
    getDefaultMiddlewares({
      serializableCheck: {
  //Redux Persist কিছু অভ্যন্তরীণ অ্যাকশন (যেমন FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER) প্রেরণ করে যেগুলো সাধারণত ডেভেলপারদের সরাসরি পরিচালনা করতে হয় না। ignoredActions ব্যবহার করে এই অ্যাকশনগুলোকে Redux স্টোরের লগ বা অন্যান্য ম্যানেজমেন্ট টুল থেকে বাদ দেওয়া হয়, যাতে অপ্রয়োজনীয় অ্যাকশনগুলি অ্যাপ্লিকেশনের পারফরম্যান্স বা ডিবাগিংয়ে প্রভাব না ফেলে।

এটি স্টোরের অ্যাকশনগুলিকে পরিচ্ছন্ন এবং সহজবোধ্য রাখে, এবং শুধুমাত্র প্রয়োজনীয় অ্যাকশনগুলিকে গুরুত্ব দিয়ে দেখা যায়।
        ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
      },
    }).concat(baseApi.middleware),
});

// Infer the `RootState` and `AppDispatch` types from the store itself
export type RootState = ReturnType<typeof store.getState>;
// Inferred type: {posts: PostsState, comments: CommentsState, users: UsersState}
export type AppDispatch = typeof store.dispatch;
export const persistor = persistStore(store);

</code></pre>

*. main.tsx file setup flow main.png image screenShort



<pre> <code></code></pre>
