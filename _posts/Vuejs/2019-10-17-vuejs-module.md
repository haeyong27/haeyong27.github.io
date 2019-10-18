---
layout: post
title:  "Vuejs Module"
date:   2019-10-17
categories: Vuejs
tag: [Vuejs, Nuxtjs, terminology]
---

# Nuxt, Vue Module
https://nuxtjs.org/guide/modules
## Introduction
> While developing production-grade applications with Nuxt, you’ll soon discover that the framework’s core functionality is not enough. Nuxt can be extended with configuration options and plugins, but maintaining these customizations across multiple projects is tedious, repetitive and time-consuming. On the other hand, supporting every project’s needs out of the box would make Nuxt very complex and hard to use.

프로덕션 수준의 어플리케이션을 만들 때 프레임워크의 코어 기능만으로는 충분하지 않다는것을 느끼게됩니다.  Nuxt 프로젝트는 설정 옵션과 플러그인들로 확장할 수 있지만 이러한 여러 프로젝트를 관리하면서 커스터마이징하는것은 반복적이고 소모적이며 번거롭습니다. 

> This is one of the reasons why Nuxt provides a higher-order module system that makes it easy to extend the core. 

그래서 Nuxt는 이런것들을 쉽게 할 수 있도록 해주는 higher-order module system을 제공합니다. 

> Modules are simply **functions** that are called sequentially when booting Nuxt. The framework waits for each module to finish before continuing. In this way, modules can customize almost any aspect of Nuxt. Thanks to Nuxt’s modular design (based on webpack’s  [Tapable](https://github.com/webpack/tapable) ), modules can easily register hooks for certain entry points like the builder initialization.

The Nuxt.js team offers **official** modules:

*  [@nuxt/http](https://http.nuxtjs.org/) : Light and universal way to make HTTP requests, based on  [ky-universal](https://github.com/sindresorhus/ky-universal) 
*  [@nuxtjs/axios](https://axios.nuxtjs.org/) : Secure and Easy Axios integration with Nuxt.js to make HTTP requests
*  [@nuxtjs/pwa](https://pwa.nuxtjs.org/) : Supercharge Nuxt with a heavily tested, updated and stable PWA solution
*  [@nuxtjs/auth](https://auth.nuxtjs.org/) : Authentication module for Nuxt.js, offering different schemes and strategies
A list of Nuxt.js modules made by the community is available on  [https://github.com/topics/nuxt-module](https://github.com/topics/nuxt-module) 


```javascript
// nuxt.config.js 
export default {
  modules: [
    // Simple usage
    ‘~/modules/simple’

    // Passing options directly
    [‘~/modules/simple’, { token: ‘123’ }]
  ]
}
```
We then tell Nuxt to load some specific modules for a project with optional parameters as options. Please refer to  [modules configuration](https://nuxtjs.org/api/configuration-modules)  docs for more info!

## Async Modules
Not all modules will do everything synchronous. For example you may want to develop a module which needs fetching some API or doing async IO. For this, Nuxt supports async modules which can return a Promise or call a callback.

## Build-only Modules
Usually, modules are only required during development and build time. Using buildModules helps to make production startup faster and also significantly decreasing node_modules size for production deployments. If you are a module author, It is highly recommended to suggest users installing your package as a devDependency and use buildModules instead of modules for nuxt.config.js.

Your module is a buildModule unless:

* It is providing a serverMiddleware
* It has to register a Node.js runtime hook (Like sentry)
* It is affecting vue-renderer behavior or using a hook from server: or vue-renderer: namespace
* Anything else that is outside of webpack scope (Hint: plugins and templates are compiled and are in webpack scope)

보통 모듈은 개발과정이나 빌드타임에서만 필요합니다. buildModules는 production startup를 더 빠르게 할 수 있도록 도와주고 production 배포시 node_moduels 사이즈를 감소시켜줍니다. 

---
#study/programming/vue
#date/2019/10/17
17 Oct 2019 3:20 PM