# Instalación

## Notas de Lanzamiento

Última Versión: ![npm](https://img.shields.io/npm/v/vue/next.svg)

Las notas de lanzamiento detalladas para cada versión se encuentran en [GitHub](https://github.com/vuejs/vue-next/blob/master/CHANGELOG.md).

## Vue Devtools

> Actualmente en fase Beta - Vuex y la integración con Router aún son trabajo en progreso.

Cuando utiliza Vue, recomendamos instalar también las [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) en su navegador, permitiéndole inspeccionar y depurar sus aplicaciones desarrolladas con Vue mediante una interfaz más amigable al usuario.

[Obtener la Extensión para Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg)

[Obtener el Addon para Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)

[Obtener la aplicación de Electron](https://github.com/vuejs/vue-devtools/blob/dev/packages/shell-electron/README.md)

## CDN

Para prototipos o con fines de aprendizaje, puede utilizar la última versión de la siguiente forma:

```html
<script src="https://unpkg.com/vue@next"></script>
```

Para producción, recomendamos vincular a una versión específica para evitar rupturas inesperadas de nuevas versiones.

## NPM

NPM es el método recomendado de instalación cuando se está construyendo aplicaciones a gran escala con Vue. Se empareja bien con los de empaquetadores de módulos como [Webpack](https://webpack.js.org/) o [Rollup](https://rollupjs.org/). Vue también provee herramientas para la creación de [Single File Components](../guide/single-file-component.html).

```bash
# latest stable
$ npm install vue@next
```

## CLI

Vue provee una [interfaz de línea de comandos (CLI) oficial](https://github.com/vuejs/vue-cli) para realizar la creación rápida de la estructura base para las Single Page Applications. Esta provee _setups_ con baterías incluidas para un flujo de trabajo de frontend moderno. Solo toma unos minutos configurar y ejecutarlo con _hot-reload_, _lint-on-save_, y compilaciones listas para producción. Véase [the Vue CLI docs](https://cli.vuejs.org) para más detalles.

::: tip
La interfaz de línea de comandos (CLI) asume que usted posee conocimiento previo de Node.js y las herramientas de compilación asociadas. Si usted es nuevo en Vue o en el manejo de las herramientas de compilación de front-end, le recomendamos leer <a href="./">la guía</a> sin ninguna herramienta de compilación antes de usar la interfaz de línea de comandos.
:::

Para Vue 3, usted debería utilizar Vue CLI v4.5 disponible en `npm` bajo el nombre de `@vue/cli`. Para actualizar, ustd necesita reinstalar la última versión de `@vue/cli` globalmente:

```bash
yarn global add @vue/cli
# O
npm install -g @vue/cli
```

Luego, en los proyectos de Vue, ejecute

```bash
vue upgrade --next
```

## Vite

[Vite](https://github.com/vitejs/vite) es una herramienta de desarrollo web que le permite el rápido despliegue del código debido a su enfoque nativo para importación de Módulos ES.

Los proyectos de Vue pueden ser rápidamente configurados con Vite ejecutando los siguientes comandos en su terminal.

Con NPM:

```bash
$ npm init vite-app <nombre-de-proyecto>
$ cd <nombre-de-proyecto>
$ npm install
$ npm run dev
```

O con Yarn:

```bash
$ yarn create vite-app <nombre-de-proyecto>
$ cd <nombre-de-proyecto>
$ yarn
$ yarn dev
```

## Explicación de las Distintas Compilaciones.

En el [directorio `dist/` del paquete NPM](https://cdn.jsdelivr.net/npm/vue@3.0.0-rc.1/dist/) encontrará varias compilaciones de Vue.js. Aquí está una visión general de cuál archivo del directorio `dist` debería ser utilizado dependiendo del caso de uso.

### Desde un CDN o sin un Empaquetador

#### `vue(.runtime).global(.prod).js`:

- Para uso directo mediante `<script src="...">` en el navegador, expone la variable Vue globalmente.
- Compilación de plantillas dentro del navegador:
  - `vue.global.js` es la compilación completa que incluye ambos, el compilador y el entorno de ejecución, así que soporta compilar plantillas sobre la marcha.
  - `vue.runtime.global.js` contiene solo el entorno de ejecución y requiere que las plantillas sean pre-compiladas durante la etapa de compilación.
- Todos los paquetes internos del núcleo de Vue en líneas, p. ej. un único archivo sin dependencias de otros. Esto significa que debe importar todo solo desde este archivo para garantizar que está obteniendo la misma instancia de código.
- Contiene las ramas prod/dev codificadas, y la compilación de prod está pre-minificada. Utilice los archivos `*.prod.js` para producción.

:::tip Nota
Las compilaciones globales no son compilaciones [UMD](https://github.com/umdjs/umd). Están compiladas como [IIFEs](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) y solo están destinadas para uso directo mediante `<script src="...">`.
:::

#### vue(.runtime).esm-browser(.prod).js:

- Para uso mediante la importación de módulos ES nativos (en el navegador mediante `<script type="module">`).
- Comparte la misma compilación del entorno de ejecución, declaración de dependencias en línea y el comportamiento codificado prod/dev con la compilación global.

### Con un Empaquetador

#### vue(.runtime).esm-bundler.js:

- Para uso con empaquetadores como `webpack`, `rollup` y `parcel`.
- Deja las ramas prod/dev con `process.env.NODE_ENV guards` (debe ser reemplazado por el empaquetador)
- No entrega las compilaciones minificadas (para ser minificado junto con el resto del código después de empaquetar)
- Importa las dependencias (p. ej. `@vue/runtime-core`, `@vue/runtime-compiler`)
  - Las dependencias importadas son también compilaciones _esm-bundler_ y es por esto que importarán sus dependencias (e.g. @vue/runtime-core imports @vue/reactivity)
  - Esto significa que usted **puede** instalar/importar esas dependencias sin terminar con diferentes instancias de ellas, pero debe estar seguro de que todas ellas se resuelven a la misma versión.
- Compilación de plantillas dentro del navegador:
  - `vue.runtime.esm-bundler.js` **(por defecto)** es solo entorno de ejecución, y requiere que todas las plantillas sean pre-compiladas. Esta es la entrada por defecto para los empaquetadores (mediante el campo módulo en `package.json`) debido a que cuando se utiliza un empaquetador las plantillas son típicamente pre-compiladas (p. ej. en los archivos `*.vue`).
  - `vue.esm-bundler.js`: incluye el compilador del entorno de ejecución. Use esto si está empleando un empaquetador pero quiere la compilación de plantillas en tiempo de ejecución (p. ej. plantillas dentro del DOM o plantillas mediante _strings_ en línea con JavaScript). Necesitará configurar su empaquetador para establecer el alias vue a este archivo.

### Para el Renderizado en el lado del Servidor

#### `vue.cjs(.prod).js`:

- Para uso en renderizado en el lado del servidor con Node.js mediante `require()`.
- Si usted empaqueta su aplicación con webpack utilizando `target: 'node'` y externaliza apropiadamente `vue`, esta será la compilación que será cargada.
- Los archivos dev/prod son pre-compilados, pero el archivo apropiado es requerido automáticamente basado en `process.env.NODE_ENV`.

## Entorno en tiempo de Ejecución + Compilador vs. Solo Entorno en tiempo de Ejecución

Si usted necesita compilar plantillas en el cliente (p. ej. pasando un parámetro a la opción de plantilla, o montando en un elemento utilizando su HTML dentro del DOM como la plantilla), necesitará el compilador y por ello la compilación completa:

```js
// esto requiere el compilador
Vue.createApp({
  template: '<div>{{ hola }}</div>'
})

// esto no
Vue.createApp({
  render() {
    return Vue.h('div', {}, this.hola)
  }
})
```

Cuando se esté utilizando `vue-loader`, las plantillas dentro de archivos `*.vue` son pre-compiladas a JavaScript en tiempo de compilación. Usted no necesita realmente el compilador en el empaquetado final y, por lo tanto, puede utilizar la compilación solo entorno en tiempo de ejecución.
