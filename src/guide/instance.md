# Application & Component Instances

## Creating an Application Instance

Cada aplicación Vue empieza con la creación de una **instancia de aplicación** con la función `createApp`:

```js
const app = Vue.createApp({ /* options */ })
```

The application instance is used to register 'globals' that can then be used by components within that application. We'll discuss that in detail later in the guide but as a quick example:

```js
const app = Vue.createApp({})
app.component('SearchInput', SearchInputComponent)
app.directive('focus', FocusDirective)
app.use(LocalePlugin)
```

Most of the methods exposed by the application instance return that same instance, allowing for chaining:

```js
Vue.createApp({})
  .component('SearchInput', SearchInputComponent)
  .directive('focus', FocusDirective)
  .use(LocalePlugin)
```

You can browse the full application API in the [API reference](../api/application-api.html).

## The Root Component

The options passed to `createApp` are used to configure the **root component**. That component is used as the starting point for rendering when we **mount** the application.

An application needs to be mounted into a DOM element. For example, if we want to mount a Vue application into `<div id="app"></div>`, we should pass `#app`:

```js
const RootComponent = { /* options */ }
const app = Vue.createApp(RootComponent)
const vm = app.mount('#app')
```

Unlike most of the application methods, `mount` does not return the application. Instead it returns the root component instance.

Although not strictly associated with the [MVVM pattern](https://en.wikipedia.org/wiki/Model_View_ViewModel), Vue's design was partly inspired by it. As a convention, we often use the variable `vm` (short for ViewModel) to refer to a component instance.

While all the examples on this page only need a single component, most real applications are organized into a tree of nested, reusable components. For example, a Todo application's component tree might look like this:

```
Root Component
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Each component will have its own component instance, `vm`. For some components, such as `TodoItem`, there will likely be multiple instances rendered at any one time. All of the component instances in this application will share the same application instance.

We'll talk about [the component system](component-basics.html) in detail later. For now, just be aware that the root component isn't really any different from any other component. The configuration options are the same, as is the behavior of the corresponding component instance.

## Component Instance Properties

Earlier in the guide we met `data` properties. Properties defined in `data` are exposed via the component instance:

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4
```

There are various other component options that add user-defined properties to the component instance, such as `methods`, `props`, `computed`, `inject` and `setup`. We'll discuss each of these in depth later in the guide. All of the properties of the component instance, no matter how they are defined, will be accessible in the component's template.

Vue also exposes some built-in properties via the component instance, such as `$attrs` and `$emit`. These properties all have a `$` prefix to avoid conflicting with user-defined property names.

## Lifecycle Hooks

Each component instance goes through a series of initialization steps when it's created - for example, it needs to set up data observation, compile the template, mount the instance to the DOM, and update the DOM when data changes. Along the way, it also runs functions called **lifecycle hooks**, giving users the opportunity to add their own code at specific stages.

Por ejemplo, el _hook_ [created](../api/options-lifecycle-hooks.html#created) puede ser utilizado para ejecutar código después de que una instancia fue creada:

```js
Vue.createApp({
  data() {
    return { count: 1 }
  },
  created() {
    // `this` points to the vm instance
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

Existen también otros _hooks_ los cuales serán llamados en etapas diferentes del ciclo de vida de la instancia, como [mounted](../api/options-lifecycle-hooks.html#mounted), [updated](../api/options-lifecycle-hooks.html#updated), y [unmounted](../api/options-lifecycle-hooks.html#unmounted). Todos los _hooks_ del ciclo de vida son llamados con su contexto `this` apuntando a la instancia actual y activa que los invoca.

::: tip
No utilice [arrow functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) en la propiedad de opciones o _callback_, como las `created: () => console.log(this.a)` o `vm.$watch('a', newValue => this.myMethod())`. Como una _arrow function_ no tiene un `this`, `this` será tratado como cualquier otra variable y buscada léxicamente hacia los alcances del elemento padre hasta ser encontrada, usualmente resultando en errores como `Uncaught TypeError: Cannot read property of undefined` o `Uncaught TypeError: this.myMethod is not a function`.
:::

## Diagrama de Ciclo de Vida

A continuación se muestra un diagrama del ciclo de vida de la instancia. No necesita entender en este momento todo lo que se muestra, pero conforme aprenda y construya más, será una referencia útil.

<img src="/images/lifecycle.png" width="840" height="auto" style="margin: 0px auto; display: block; max-width: 100%;" loading="lazy" alt="Instance lifecycle hooks">
