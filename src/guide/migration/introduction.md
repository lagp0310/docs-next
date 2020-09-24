# Introducción

::: info 
¿Nuevo en Vue.js? Revise nuestra [Guía Escencial](/guide/introduction.html) para iniciar. 
:::

Esta guía es principalmente para usuarios con experiencia previa con Vue 2 que quieren aprender sobre las nuevas características y cambios que hay en Vue 3. **Esto no es algo que debe leer completo antes de trabajar con Vue 3.** Aunque parezca que muchas cosas han cambiado, mucho de lo que conoce y ama de Vue sigue igual; pero queríamos ser tan completos como sea posible y proveer explicaciones detalladas y ejemplos para cada cambio documentado. 

- [Inicio Rápido](#quickstart)
- [Características Nuevas Notables](#notable-new-features)
- [Cambios que afectan a desarrollos previos](#breaking-changes)
- [Librerías de Soporte](#supporting-libraries)

## Vista General

<br>
<iframe src="https://player.vimeo.com/video/440868720" width="640" height="360" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>

Empiece aprendiendo Vue 3 en [Vue Mastery](https://www.vuemastery.com/courses-path/vue3).

## Inicio Rápido

- A través de un CDN: `<script src="https://unpkg.com/vue@next"></script>`
- Entorno de pruebas en el navegador en [Codepen](https://codepen.io/yyx990803/pen/OJNoaZL)
- Generado mediante [Vite](https://github.com/vitejs/vite):

  ```bash
  npm init vite-app hello-vue3 # OR yarn create vite-app hello-vue3
  ```

- Generado mediante [vue-cli](https://cli.vuejs.org/):

  ```bash
  npm install -g @vue/cli # OR yarn global add @vue/cli
  vue create hello-vue3
  # seleccione valores preestablecidos para su proyecto con vue 3
  ```

## Características Nuevas Notables

Algunas de las nuevas características que merece la pena ver en Vue 3 incluye:

- [API de Composición](/guide/composition-api-introduction.html)
- [Teleport](/guide/teleport.html)
- [Fragmentos](/guide/migration/fragments.html)
- [Opción de Emisión en Componentes](/guide/component-custom-events.html)
- [API `createRenderer` de `@vue/runtime-core`](https://github.com/vuejs/vue-next/tree/master/packages/runtime-core) to create custom renderers
- [Sintaxis de la API de Composición para SFC (`<script setup>`)](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md) <Badge text="experimental" type="warning" />
- [Variables CSS manejadas por estado para SFC (`<style vars>`)](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md) <Badge text="experimental" type="warning" />
- [El `<style scoped>` en SFC puede incluir ahora reglas globales o reglas que tienen como objetivo solo contenido en slots](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0023-scoped-styles-changes.md)

## Cambios que afectan a desarrollos previos

::: info INFO
Aún estamos trabajando en una guía de migración de Vue 3 con comportamiento compatible con Vue 2 y advertencias en tiempo de ejecución del uso incompatible. Si está planeando migrar una aplicación compleja desarrollada con Vue 2, le recomendamos esperar por la guía de migración para una mejor experiencia.
:::

Lo siguiente constituye una lista de los cambios que afectan a los desarrollos realizados con Vue 2.x:

### API Global

- [La API Global de Vue fue cambiada para utilizar la instancia de una aplicación](/guide/migration/global-api.html)
- [Las APIs globales e internas han sido reestructuradas para cumplir con "eliminación de código muerto"](/guide/migration/global-api-treeshaking.html)

### Directivas de Plantillas

- [El uso de `v-model` en componentes ha sido trabajado nuevamente](/guide/migration/v-model.html)
- [El uso de `key` en nodos `<template v-for>` y nodos que no son `v-for` ha cambiado](/guide/migration/key-attribute.html)
- [La precedencia de `v-if` y `v-for` cuando son usadas en el mismo elemento ha cambiado](/guide/migration/v-if-v-for.html)
- [`v-bind="object"` ahora respeta el ordenamiento](/guide/migration/v-bind.html)
- [`ref` dentro de `v-for` no registra un array de refs](/guide/migration/array-refs.html)

### Componentes

- [Los componentes funcionales solo pueden ser creados utilizando una función simple](/guide/migration/functional-components.html)
- [El atributo `functional` de la etiqueta `<template>` en SFC y la opción de componente `functional` están obsoletas](/guide/migration/functional-components.html)
- [Los componentes asíncronos ahora requieren el método `defineAsyncComponent` para ser creados](/guide/migration/async-components.html)

### Función de Renderizado

- [La API de funciones de renderizado cambió](/guide/migration/render-function-api.html)
- [La propiedad `$scopedSlots` fue eliminada y todos los _slots_ son expuestos mediante `$slots` como funciones](/guide/migration/slots-unification.html)

### Elementos Modificados

- [La lista permitida de elementos modificados ahora es realizada durante la compilación de plantillas](/guide/migration/custom-elements-interop.html)
- [El uso de la propiedad especial `is` está restringido únicamente para la etiqueta reservada `<component>`](/guide/migration/custom-elements-interop.html#customized-built-in-elements)

### Otros Cambios Menores

- La etapa de ciclo de vida `destroyed` ha sido renombrada a `unmounted`
- La etapa de ciclo de vida `beforeDestroy` ha sido renombrada a `beforeUnmount`
- [El argumento _props_ en la función de fábrica `default` ya no tiene acceso al contexto `this`](/guide/migration/props-default-this.html)
- [La API de directivas modificadas fue cambiada para alinearse con el ciclo de vida de los componentes](/guide/migration/custom-directives.html)
- [La opción `data` siempre debería ser declarada como una función](/guide/migration/data-option.html)
- [La opción `data` de _mixins_ ahora es fusionada superficialmente](/guide/migration/data-option.html#mixin-merge-behavior-change)
- [La estrategia de coerción de atributos ha cambiado](/guide/migration/attribute-coercion.html)
- [Algunas clases de transición fueron renombradas](/guide/migration/transition.html)
- [Cuando utiliza _watch_ para un array, la función _callback_ solo será ejecutada cuando el array es reemplazado. Si necesita ejecutarla cuando ocurra una mutación en su estado, la opción `deep` debe ser especificada.](/guide/migration/watch.html)
- Las etiquetas `<template>` sin directivas especiales (`v-if/else-if/else`, `v-for`, o `v-slot`) son tratadas ahora como elementos simples y resultarán en un elemento `<template>` nativo en vez de renderizar su contenido interno.
- En Vue 2.x, el `outerHTML` del contenedor raíz de la aplicación fue reemplazado con el componente raíz de la plantilla (o eventualmente compilado a una plantilla, si el componente raíz no tiene opción de plantilla/renderizado). En su lugar, Vue 3.x ahora utiliza el contenedor de la aplicación `innerHTML`, esto significa que, ahora, el contenedor por sí solo no es considerado una parte de la plantilla.

### APIs Eliminadas

- [Soporte de `keyCode` como modificadores de `v-on`](/guide/migration/keycode-modifiers.html)
- [Métodos de instancia $on, $off y \$once](/guide/migration/events-api.html)
- [Filtros](/guide/migration/filters.html)
- [Atributos de plantillas en línea](/guide/migration/inline-template-attribute.html)
- Método de instancia `$destroy`. Los usuarios ya no deberían manejar manualmente el ciclo de vida de componentes individuales de Vue.

## Librerías Soportadas

Todas nuestras librerías oficiales ahora soportan Vue 3, pero la mayor parte de ellas siguen en fase beta y distribuidas bajo la etiqueta de distribución `next` en NPM. **Estamos planeando estabilizar y cambiar todos los proyectos para utilizar la etiqueta de distribución `latest` para el final del año 2020.**

### Interfaz de Línea de Comandos de Vue (Vue CLI)

<a href="https://www.npmjs.com/package/@vue/cli" target="_blank" noopener noreferrer><img src="https://img.shields.io/npm/v/@vue/cli"></a>

A partir de la versión 4.5.0, `vue-cli` ahora provee una opción incorporada para elegir Vue 3 cuando se esté creando un nuevo proyecto. Puede actualizar `vue-cli` y ejecutar `vue create` para crear un proyecto con Vue 3 ahora.

- [Documentación](https://cli.vuejs.org/)
- [GitHub](https://github.com/vuejs/vue-cli)

### Enrutador de Vue (Vue Router)

<a href="https://www.npmjs.com/package/vue-router/v/next" target="_blank" noopener noreferrer><img src="https://img.shields.io/npm/v/vue-router/next.svg"></a>

Vue Router 4.0 provee soporte para Vue 3 y posee un número de cambios que afectan a desarrollos con versiones previas. Véase su [README](https://github.com/vuejs/vue-router-next#vue-router-next-) para detalles completos.

- [GitHub](https://github.com/vuejs/vue-router-next)
- [RFCs](https://github.com/vuejs/rfcs/pulls?q=is%3Apr+is%3Amerged+label%3Arouter)

### Vuex

<a href="https://www.npmjs.com/package/vuex/v/next" target="_blank" noopener noreferrer><img src="https://img.shields.io/npm/v/vuex/next.svg"></a>

Vuex 4.0 provee soporte para Vue 3 con casi la misma API que la versión 3.x. El único cambio que afecta a los desarrollos con versiones previas es [cómo el plugin es instalado](https://github.com/vuejs/vuex/tree/4.0#breaking-changes).

- [GitHub](https://github.com/vuejs/vuex/tree/4.0)

### Extensión de Herramientas para Desarrollador

Estamos trabajando en una nueva versión de las herramientas para desarrollador con una nueva interfaz gráfica de usuario y comportamiento interno refactorizado para dar soporte a múltiples versiones de Vue. La nueva versión se encuentra en fase beta y solo soporta Vue 3 (por ahora). La integración con Vuex y Router es también un trabajo en progreso.

- Para Chrome: [Instalar desde la Chrome web store](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg?hl=en)

  - Nota: el canal de entrega beta puede presentar conflictos con la versión estable de la herramienta para desarrollador así que es posible que deba deshabilitar la versión estable para que el canal de entrega beta funcione adecuadamente.

- Para Firefox: [Descargar la extensión firmada](https://github.com/vuejs/vue-devtools/releases/tag/v6.0.0-beta.2) (archivo `.xpi` en la carpeta Assets)

### Soporte para Entornos de Desarrollo Integrados (IDE)

Es recomendado el uso de [VSCode](https://code.visualstudio.com/) con nuetra extensión oficial [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur), la cual provee soporte integral de entorno de desarrollo intergrado para Vue 3.

### Otros Proyectos

| Proyecto               | NPM                            | Repo                 |
| ---------------------  | -----------------------------  | -------------------- |
| @vue/babel-plugin-jsx  | [![rc][jsx-badge]][jsx-npm]    | [[GitHub][jsx-code]] |
| eslint-plugin-vue      | [![beta][epv-badge]][epv-npm]  | [[GitHub][epv-code]] |
| @vue/test-utils        | [![beta][vtu-badge]][vtu-npm]  | [[GitHub][vtu-code]] |
| vue-class-component    |  [![beta][vcc-badge]][vcc-npm] | [[GitHub][vcc-code]] |
| vue-loader             | [![beta][vl-badge]][vl-npm]    | [[GitHub][vl-code]]  |
| rollup-plugin-vue      | [![beta][rpv-badge]][rpv-npm]  | [[GitHub][rpv-code]] |

[jsx-badge]: https://img.shields.io/npm/v/@vue/babel-plugin-jsx.svg
[jsx-npm]: https://www.npmjs.com/package/@vue/babel-plugin-jsx
[jsx-code]: https://github.com/vuejs/jsx-next
[vd-badge]: https://img.shields.io/npm/v/@vue/devtools/beta.svg
[vd-npm]: https://www.npmjs.com/package/@vue/devtools/v/beta
[vd-code]: https://github.com/vuejs/vue-devtools/tree/next
[epv-badge]: https://img.shields.io/npm/v/eslint-plugin-vue/next.svg
[epv-npm]: https://www.npmjs.com/package/eslint-plugin-vue/v/next
[epv-code]: https://github.com/vuejs/eslint-plugin-vue
[vtu-badge]: https://img.shields.io/npm/v/@vue/test-utils/next.svg
[vtu-npm]: https://www.npmjs.com/package/@vue/test-utils/v/next
[vtu-code]: https://github.com/vuejs/vue-test-utils-next
[jsx-badge]: https://img.shields.io/npm/v/@ant-design-vue/babel-plugin-jsx.svg
[jsx-npm]: https://www.npmjs.com/package/@ant-design-vue/babel-plugin-jsx
[jsx-code]: https://github.com/vueComponent/jsx
[vcc-badge]: https://img.shields.io/npm/v/vue-class-component/next.svg
[vcc-npm]: https://www.npmjs.com/package/vue-class-component/v/next
[vcc-code]: https://github.com/vuejs/vue-class-component/tree/next
[vl-badge]: https://img.shields.io/npm/v/vue-loader/next.svg
[vl-npm]: https://www.npmjs.com/package/vue-loader/v/next
[vl-code]: https://github.com/vuejs/vue-loader/tree/next
[rpv-badge]: https://img.shields.io/npm/v/rollup-plugin-vue/next.svg
[rpv-npm]: https://www.npmjs.com/package/rollup-plugin-vue/v/next
[rpv-code]: https://github.com/vuejs/rollup-plugin-vue/tree/next
