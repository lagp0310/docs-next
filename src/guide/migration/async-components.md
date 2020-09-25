---
badges:
  - nuevo
---

# Componentes Asíncronos <MigrationBadges :badges="$frontmatter.badges" />

## Vistazo General

A continuación un vistazo general de alto nivel a los cambios hechos:

- Nuevo método de ayuda `defineAsyncComponent` que define explícitamente a los componentes asíncronos
- Opción `component` renombrada a `loader`
- La función cargador no recibe inherentemente los argumentos `resolve` y `reject` y debe retornar una Promesa

Para una explicación más detallada, ¡siga leyendo!

## Introducción

Previamente, los componentes asíncronos fueron creados simplemente definiendo un componente como una función que retornaba una promesa, como se muestra a continuación:

```js
const asyncPage = () => import('./NextPage.vue')
```

O, para la sintaxis de componentes máx avanzada utilizando opciones:

```js
const asyncPage = {
  component: () => import('./NextPage.vue'),
  delay: 200,
  timeout: 3000,
  error: ErrorComponent,
  loading: LoadingComponent
}
```

## Sintaxis 3.x

Ahora, en Vue 3, como los componentes funcionales son definidos como funciones simples, las definiciones de componentes asíncronos necesitan ser explícitamente definidas envolviéndolas en una nueva función de ayuda `defineAsyncComponent`:

```js
import { defineAsyncComponent } from 'vue'
import ErrorComponent from './components/ErrorComponent.vue'
import LoadingComponent from './components/LoadingComponent.vue'

// Async component without options
const asyncPage = defineAsyncComponent(() => import('./NextPage.vue'))

// Async component with options
const asyncPageWithOptions = defineAsyncComponent({
  loader: () => import('./NextPage.vue'),
  delay: 200,
  timeout: 3000,
  errorComponent: ErrorComponent,
  loadingComponent: LoadingComponent
})
```

Otro cambio que se ha hecho de la versión 2.x es que la opción `component` ahora fue renombrada a `loader` para comunicar con precisión que la definición de un componente no puede ser proporcionada directamente.

```js{4}
import { defineAsyncComponent } from 'vue'

const asyncPageWithOptions = defineAsyncComponent({
  loader: () => import('./NextPage.vue'),
  delay: 200,
  timeout: 3000,
  error: ErrorComponent,
  loading: LoadingComponent
})
```

Además, a diferencia de la versión 2.x, la función cargador ya no recibe los argumentos `resolve` ni `reject` y siempre debe retornar una Promesa.

```js
// 2.x version
const oldAsyncComponent = (resolve, reject) => {
  /* ... */
}

// 3.x version
const asyncComponent = defineAsyncComponent(
  () =>
    new Promise((resolve, reject) => {
      /* ... */
    })
)
```

Para más información en el uso de los componentes asíncronos, véase:

- [Guía: Componentes Dinámicos y Asíncronos](/guide/component-dynamic-async.html#dynamic-components-with-keep-alive)
