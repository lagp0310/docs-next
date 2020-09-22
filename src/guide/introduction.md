# Introducción

::: tip NOTA
¿Ya conoce Vue 2 y solo quiere aprender sobre lo nuevo que hay en Vue 3? Visite la [Guía de Migración](/guide/migration/introduction.html)!
:::

## ¿Qué es Vue.js?

Vue (pronunciado /vjuː/, como **view**) es un **framework progresivo** para la construcción de interfaces de usuario. A diferencia de otros frameworks monolíticos, Vue está diseñado desde cero para ser incrementalmente adoptable. La librería base está enfocada solo en la capa de vista, y es sencillo integrarla con otras librerías o proyectos existentes. Por otro lado, Vue es también perfectamente capaz de ejecutar sofisticadas Single-Page Applications cuando es utilizado en combinación con [herramientas modernas](../guide/single-file-component.html) y [librerías de soporte](https://github.com/vuejs/awesome-vue#components--libraries).

Si a usted le gustaría aprender más sobre Vue antes de adentrarse, nosotros <a id="modal-player" class="vuemastery-trigger"  href="#">creamos un video</a> que lo guiará a través de los principios básicos y un proyecto de ejemplo.

<common-vuemastery-video-modal/>

## Empezando

<p>
  <ActionLink class="primary" url="installation.html">
    Instalación
  </ActionLink>
</p>

::: tip
La guía oficial asume un conocimiento intermedio de HTML, CSS y JavaScript. Si usted es completamente nuevo en el desarrollo de frontend, puede no ser la mejor idea ir directo a un framework como su primer paso, ¡aprenda lo básico y luego vuelva! La experiencia previa con otros frameworks ayuda, pero no es requerida.
:::

La manera más fácil de probar Vue.js es utilizando el [ejemplo Hola Mundo](https://codepen.io/team/Vue/pen/KKpRVpx). Siéntase libre de abrirlo en otra pestaña y sígalo mientras vamos mostrando algunos ejemplos básicos.

La página de [Instalación](installation.md) proporciona más opciones para instalar Vue. Nota: **No** recomendamos que los principiantes empiecen con `vue-cli`, especialmente si no está familiarizado aún con las herramientas de compilación basadas en Node.js.

## Renderizado Declarativo

En el núcleo de Vue.js se encuentra un sistema que nos permite renderizar datos declarativamente en el DOM utilizando una sintaxis de plantilla simple:

```html
<div id="counter">
  Contador: {{ counter }}
</div>
```

```js
const Counter = {
  data() {
    return {
      counter: 0
    }
  }
}

Vue.createApp(Counter).mount('#counter')
```

¡Hemos creado nuestra primera aplicación Vue! Esto luce muy similar a renderizar una plantilla que contiene cadenas de caracteres, pero Vue ha hecho mucho trabajo detrás de escenas. Los datos y el DOM ahora están vinculados, y todo es **reactivo**. ¿Cómo lo sabemos? Vea el ejemplo siguiente donde la propiedad `counter` incrementa cada segundo y verá como cambia el DOM renderizado:

```js{8-10}
const CounterApp = {
  data() {
    return {
      counter: 0
    }
  },
  mounted() {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
```

<FirstExample />

Además de la interpolación de texto, también podemos vincular atributos de elementos como sigue:

```html
<div id="bind-attribute">
  <span v-bind:title="message">
    ¡Pase el cursor sobre mí por unos segundos para observar el título vinculado dinámicamente!
  </span>
</div>
```

```js
const AttributeBinding = {
  data() {
    return {
      message: 'Cargó esta página en ' + new Date().toLocaleString()
    }
  }
}

Vue.createApp(AttributeBinding).mount('#bind-attribute')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="KKpRVvJ" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Attribute dynamic binding">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/KKpRVvJ">
  Attribute dynamic binding</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Here we're encountering something new. The `v-bind` attribute you're seeing is called a **directive**. Directives are prefixed with `v-` to indicate that they are special attributes provided by Vue, and as you may have guessed, they apply special reactive behavior to the rendered DOM. Here we are basically saying "_keep this element's `title` attribute up-to-date with the `message` property on the current active instance._"

## Manejando la Entrada del Usuario

Para permitir a los usuarios interactuar con su aplicación, podemos utilizar la directiva `v-on` para vincular eventos de escucha que invoquen métodos en nuestras instancias:

```html
<div id="event-handling">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Mensaje Invertido</button>
</div>
```

```js
const EventHandling = {
  data() {
    return {
      message: '¡Hola Vue.js!'
    }
  },
  methods: {
    reverseMessage() {
      this.message = this.message
        .split('')
        .reverse()
        .join('')
    }
  }
}

Vue.createApp(EventHandling).mount('#event-handling')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="dyoeGjW" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Event handling">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/dyoeGjW">
  Event handling</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Note que en este método actualizamos el estado de nuestra aplicación son tocar el DOM, todas las manipulaciones del DOM son manejadas por Vue, y el código que usted escribe está enfocado en la lógica que hay debajo.

Vue también provee la directiva `v-model` que hace muy sencilla la vinculación de doble dirección entre el formulario de entrada y el estado de la aplicación:

```html
<div id="two-way-binding">
  <p>{{ message }}</p>
  <input v-model="message" />
</div>
```

```js
const TwoWayBinding = {
  data() {
    return {
      message: '¡Hola Vue!'
    }
  }
}

Vue.createApp(TwoWayBinding).mount('#two-way-binding')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="poJVgZm" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Two-way binding">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/poJVgZm">
  Two-way binding</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Condicionales y Ciclos

Es sencillo también alternar la presencia de un elemento:

```html
<div id="conditional-rendering">
  <span v-if="seen">Ahora me ve</span>
</div>
```

```js
const ConditionalRendering = {
  data() {
    return {
      seen: true
    }
  }
}

Vue.createApp(ConditionalRendering).mount('#conditional-rendering')
```

Este ejemplo demuestra que podemos vincular datos no solo a texto y atributos, sino también a la **estructura** del DOM. Además, Vue provee también un poderoso sistema de transición de efectos que puede aplicar automáticamente [transition effects](transitions-enterleave.md) cuando los elementos son insertados/actualizados/eliminados por Vue.

Puede cambiar `seen` de `true` a `false` en el _sandbox_ que sigue para observar lo que ocurre:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="oNXdbpB" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Conditional rendering">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/oNXdbpB">
  Conditional rendering</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Existen otras directivas, cada una con su funcionalidad especial. Por ejemplo, la directiva `v-for` puede ser utilizada para mostrar una lista de ítems empleando datos de un Array:

```html
<div id="list-rendering">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```js
const ListRendering = {
  data() {
    return {
      todos: [
        { text: 'Aprender JavaScript' },
        { text: 'Aprender Vue' },
        { text: 'Construir algo asombroso' }
      ]
    }
  }
}

Vue.createApp(ListRendering).mount('#list-rendering')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="mdJLVXq" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="List rendering">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/mdJLVXq">
  List rendering</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Composición mediante Componentes

El sistema de componentes es otro concepto importante en Vue, porque es una abstracción que nos permite construir aplicaciones a gran escala compuestos de componentes pequeños, auto-contenidos, y usualmente reutilizables. Si lo pensamos, casi todo tipo de interfaz de aplicación puede ser abstraída en un árbol de componentes:

![Árbol de Componentes](/images/components.png)

En Vue, un componente es esencialmente una instancia con opciones predefinidas. Registrar un componente en Vue es sencillo: creamos un objeto componente como hicimos con los objetos `App` y lo definimos en la opción `components` en su padre:

```js
// Crear aplicación Vue
const app = Vue.createApp(...)

// Definir un nuevo componente llamado todo-item
app.component('todo-item', {
  template: `<li>Esto es un pendiente</li>`
})

// Montar la aplicación Vue
app.mount(...)
```

Ahora puede componerlo en la plantilla de otro componente:

```html
<ol>
  <!-- Crea una instancia del componente todo-item -->
  <todo-item></todo-item>
</ol>
```

Pero esto va a renderizar el mismo texto para cada pendiente, lo cual no es muy interesante. Deberíamos ser capaces de pasar datos a los componentes hijo desde el padre. Vamos a modificar la definición del componente para que acepte un [prop](component-basics.html#passing-data-to-child-components-with-props):

```js
app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})
```

Ahora podemos pasar el pendiente en cada componente repetido utilizando `v-bind`:

```html
<div id="todo-list-app">
  <ol>
    <!--
      Ahora proveemos cada todo-item con el objeto pendiente
      que está representando, con lo que su contenido puede ser dinámico.
      También, necesitamos proveer cada componente con una "key",
      lo cual explicaremos luego.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```

```js
const TodoList = {
  data() {
    return {
      groceryList: [
        { id: 0, text: 'Vegetales' },
        { id: 1, text: 'Queso' },
        { id: 2, text: 'Cualquier otra cosa que se supone que coman los humanos' }
      ]
    }
  }
}

const app = Vue.createApp(TodoList)

app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})

app.mount('#todo-list-app')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="VwLxeEz" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Intro-Components-1">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/VwLxeEz">
  Intro-Components-1</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Este es un ejemplo inventado, pero hemos logrado separar nuestra aplicación en dos unidades pequeñas, y el hijo está bien desacoplado del padre mediante el uso de la interfaz props. Ahora podemos mejorar nuestro componente `<todo-item>` con una plantilla y lógica más compleja sin afectar la aplicación padre.

En una aplicación grande, es necesario dividir toda la aplicación en componentes para hacer el desarrollo manejable. Hablaremos más sobre componentes [más adelante en esta guía](component-basics.md), pero aquí brindamos un ejemplo (imaginario) de cómo se vería una plantilla construida a base de componentes:

```html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Relación con los Elementos Modificados

Puede haber notado que los componentes de Vue son muy similares a los **Elementos Modificados**, los cuales son una parte de [la especificación de Componentes Web](https://www.w3.org/wiki/WebComponents/). Esto es porque la sintaxis de componentes de Vue está modelada de cierta forma basada en dicha especificación. Por ejemplo, los componentes de Vue implementan la [API de Slots](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) y el atributo especial `is`. Sin embargo, existen unas diferencias clave:

1. La especificación de Componentes Web ha sido finalizada pero no está implementada de forma nativa en cada navegador. Safari 10.1+, Chrome 54+ y Firefox 63+ soportan de forma nativa los componentes web. En comparación, los componentes de Vue trabajan consistentemente en todos los navegadores soportados (IE11 con compilación de compatibilidad y versiones posteriores). Cuando es requerido, los componentes de Vue pueden ser también envueltos dentro de un elemento nativo modificado.

[//]: # 'TODO: link to compatibility build'

2. Vue components provide important features that are not available in plain custom elements, most notably cross-component data flow, custom event communication and build tool integrations.

Although Vue doesn't use custom elements internally, it has [great interoperability](https://custom-elements-everywhere.com/#vue) when it comes to consuming or distributing as custom elements. Vue CLI also supports building Vue components that register themselves as native custom elements.

## ¿Listo para Más?

Hemos introducido brevemente las características más básicas del núcleo de Vue.js, el resto de esta guía cubrirá las mismas y otras características avanzadas con mayor detalle, ¡así que asegúrese de leerla toda!
