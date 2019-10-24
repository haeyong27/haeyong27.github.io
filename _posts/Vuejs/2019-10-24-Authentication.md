---
layout: post
title:  "Authentication"
date:   2019-10-24
categories: Vuejs
tag: [Vuejs, Nuxtjs, routes]
---
- [routing](#routing)
  - [midleware](#midleware)
  - [example](#example)
- [API: The middleware Property](#api-the-middleware-property)
  - [Named middleware](#named-middleware)
- [Auth External API : JWT](#auth-external-api--jwt)

Vuejs Authentication

# routing
## midleware
[Routing - NuxtJS](https://nuxtjs.org/guide/routing#middleware)
> Middleware lets you define custom functions that can be run before rendering either a page or a group of pages.

미들웨어는 페이지가 렌더링 되기 전에 실행되야하는 함수를 정의할 수 있도록 해줍니다. 

> Every middleware should be placed in the middleware/directory. The filename will be the name of the middleware (middleware/auth.js will be the auth middleware). 
> A middleware receives  [the context](https://nuxtjs.org/api/context)  as first argument:

미들웨어는 컨텍스트를 첫번째 인자로 받습니다. 

---

context 란?
> The context provides additional objects/params from Nuxt to Vue components and is available in special nuxt lifecycle areas like* [asyncData](https://nuxtjs.org/api) *,* [fetch](https://nuxtjs.org/api/pages-fetch) *,* [plugins](https://nuxtjs.org/guide/plugins) *,* [middleware](https://nuxtjs.org/guide/routing#middleware) *and* [nuxtServerInit](https://nuxtjs.org/guide/vuex-store#the-nuxtserverinit-action) *.*

 

컨텍스트는 객체나 매개변수를 넉스트에서 vue components로 전달하고 asyncData, fetch 등과 같은 lifecycle areas에서 사용가능합니다. 

---

> In universal mode, middlewares will be called server-side once (on the first request to the Nuxt app or when page refreshes) and client-side when navigating to further routes. In SPA mode, middlewares will be called client-side on the first request and when navigating to further routes.

- universal 모드 : 다른 경로로 이동할 때 미들웨어는 서버사이드(첫번 째 요청이나 page 새로고침하는 경우)와 클라이언트 사이드에서 호출됩니다. 
- SPA 모드 : 다른 경로로 이동할 때 미들웨어는 클라이언트 사이드(첫번째 리퀘스트)에서 호출됩니다. 


> The middleware will be executed in series in this order:
미들웨어는 다음 순서로 순차적으로 실행됩니다. 

1. nuxt.config.js (in the order within the file)
2. Matched layouts
3. Matched pages

> A middleware can be asynchronous. To do this, simply return a Promise or use the 2nd callback argument:

미들웨어는 비동기입니다.

## example
```javascript
// middleware/stats.js
import axios from ‘axios’

export default function ({ route }) {
  return axios.post(‘http://my-stats-api.com’, {
    url: route.fullPath
  })
}
```


Then, in your nuxt.config.js , use the router.middleware key:

Then, in your **`nuxt.config.js`** , use the **`router.middleware`** key:

```javascript
//nuxt.config.js
export default {
  router: {
    middleware: ‘stats’
  }
}
```

> Now the stats middleware will be called for every route change.

이제 stats midleware는 라우트가 바뀔 때마다 실행됩니다.

> You can add your middleware to a specific layout or page as well: **`pages/index.vue`** or **`layouts/default.vue`** 
특정 페이지나 레이아웃에만 미들웨어를 추가할 수 있습니다. 

```javascript
export default {
  middleware: ‘stats’
}
```
# API: The middleware Property

참고 : [API: The middleware Property - NuxtJS](https://nuxtjs.org/api/pages-middleware#named-middleware)

## Named middleware
> You can create named middleware by creating a file inside the `middleware/` directory, the file name will be the middleware name.

```javascript
//middleware/authenticated.js
export default function ({ store, redirect }) {
  // If the user is not authenticated
  if (!store.state.authenticated) {
    return redirect(‘/login’)
  }
}
```

```javascript
//pages/secret.vue
<template>
  <h1>Secret page</h1>
</template>

<script>
export default {
  middleware: ‘authenticated’
}
</script>
```

# Auth External API : JWT
[Auth External API (JWT) - NuxtJS](https://nuxtjs.org/examples/auth-external-jwt)
