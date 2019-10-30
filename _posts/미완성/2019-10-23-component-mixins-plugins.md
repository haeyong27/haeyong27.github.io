---
layout: post
title:  "Component vs Mixins vs Plugins in Vue.js"
date:   2019-10-23
categories: Vuejs
tag: [Vuejs]
---


# Component vs Mixins vs Plugins in Vue.js
참고 : [Component vs Mixins vs Plugins in Vue.js](https://medium.com/@victorlucss/component-vs-mixins-vs-plugins-in-vue-js-6a8bce29d6ec)	
## Introduction
### Components
> They help you extend basic HTML elements to encapsulate reusable code.
> They are the high level of a Vue application and compose layout or functionalities to your program. In the most times, developers opt for that because of the reuse or facility to replicate in others parts. Components can be extended getting the aspects of a parent component to the child.

HTML elements를 캡슐화하여 재사용할 수 있도록 도와준다. component는 high level의 뷰 어플리케이션이고, layout이나 프로그램의 기능을 구성한다. 개발자들은 쉽게 복제할 수 있고 재사용성이 높기 때문에 이를 활용합니다. 또한 부모 component의 기능을 가져와 확장할 수 있습니다. 


### Mixins
> Mixins are a flexible way to distribute reusable functionalities for Vue components.
> Mixins are options of a component that can be accessed in any component, in several times this avoids the rework and allow to write less. They can be local, that is created and accessed only once in a component or global, it will affect every Vue instances. Already ahead that as a personal option, I choose to use plugins instead of mixins.

어떤 component에서도 접근할 수 있는 옵션이다. 반복되는 일을 줄일 수 있고 더 적게 코드를 작성하도록 해준다. 전역적으로 사용할 수 도 있고, 지역적으로 사용할 수 있다. 

### Plugins
> Plugins usually add global-level functionality to Vue
> Plugins are uses that can be accessed in any component and depends on your use. Things like router, state management and even actions that are realized can be a plugin that will be imported and used globally. For example, you can write a global method as a plugin and, any component will have access to this functionality.

Plugins는 어떤 component에서도 사용할 수 있습니다. router, state management과 같은것들을 전역적으로 사용합니다. 예를들어 전역적으로 사용할 method를 플러그인으로 사용하면 어떤 component에서도 접근할 수 있습니다. 

## document
### Vue.js
[Plugins — Vue.js](https://vuejs.org/v2/guide/plugins.html)

### Nuxt.js
[Plugins - NuxtJS](https://nuxtjs.org/guide/plugins)

> It is important to know that in any Vue  [instance lifecycle](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram) , only beforeCreate and created hooks are called **both, from client-side and server-side**. All other hooks are called only from the client-side.

오직 beforeCreate와 created만이 클라이언트 사이드와 서버사이드 모두에서 호출됩니다.  이외의 훅들은 오직 클라이언트 사이드에서만 호출됩니다. 


#### Client-side only 
> Some plugins might work **only in the browser** because they lack SSR support. In these situations you can use the mode: client option in plugins to add the plugin only on the client-side.

서버에 영향을 최대한 덜 주기 위해 브라우저에서만 플러그인이 작동하도록 할 수 있습니다. mode: client 옵션을 사용하면 됩니다. 
```javascript
// nuxt.config.js:
export default {
  plugins: [
    { src: ‘~/plugins/vue-notifications’, mode: ‘client’ }
  ]
}
```


```javascript
// plugins/vue-notifications.js:
import Vue from ‘vue’
import VueNotifications from ‘vue-notifications’

Vue.use(VueNotifications)
```

