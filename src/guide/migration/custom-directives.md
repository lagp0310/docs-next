---
badges:
  - cambio importante
---

# Directivas Modificadas <MigrationBadges :badges="$frontmatter.badges" />

## Vistazo General

Aquí hay un resumen breve de lo que cambió:

- La API ha sido renombrada para alinearse mejor con el ciclo de vida de componentes
- Las directivas modificadas serán controladas por componentes hijos a través de `v-bind="$attrs"`

Para mayor información, ¡siga leyendo!

## Sintaxis de la versión 2.x

En Vue 2, las directivas modificadas fueron creadas utilizando los _hooks_ listados a continuación para apuntar al ciclo de vida de un elemento, los cuales son opcionales:

- **bind** - Ocurre una vez que la directiva es vinculada al elemento. Ocurre una sola vez.
- **inserted** - Ocurre una vez que el elemento es insertado en el DOM padre.
- **update** - Este _hook_ es llamado cuando el elemento se actualiza, pero los hijos no han sido actualizados aún.
- **componentUpdated** - Este _hook_ es llamado una vez que el componente y los hijos han sido actualizados.
- **unbind** - Este _hook_ es llamado una vez que la directiva es eliminada. También es llamado una única vez.

Aquí hay un ejemplo de esto:

```html
<p v-highlight="yellow">Highlight this text bright yellow</p>
```

```js
Vue.directive('highlight', {
  bind(el, binding, vnode) {
    el.style.background = binding.value
  }
})
```

Aquí, en la preparación inicial para este elemento, la directiva vincula un estilo pasando un valor, que puede ser actualizado a valores diferentes a través de la aplicación.

## Sintaxis de la verisión 3.x

En Vue 3, sin embargo, hemos creado una API más cohesiva para las directivas modificadas. Como puede observar, estas difieren en gran medida de nuestros métodos de ciclo de vida de componente aunque estamos hablando de eventos similares. Ahora los hemos unificado como sigue a continuación:

- bind → **beforeMount**
- inserted → **mounted**
- **beforeUpdate**: ¡Nuevo! Este es llamado antes de que el elemento en sí sea actualizado, similar a los _hooks_ del ciclo de vida del componente.
- update → ¡Eliminado! Existían muchas similitudes a _update_, por lo que este es redundante. Por favor, en su lugar, utilice _updated_.
- componentUpdated → **updated**
- **beforeUnmount** ¡Nuevo! Similar a los _hooks_ del ciclo de vida de componentes, esto será llamado antes de que un elemento sea desmontado.
- unbind -> **unmounted**

La API final es como se muestra a continuación:

```js
const MyDirective = {
  beforeMount(el, binding, vnode, prevVnode) {},
  mounted() {},
  beforeUpdate() {},
  updated() {},
  beforeUnmount() {}, // new
  unmounted() {}
}
```

La API resultante puede ser utilizada así, reflejando el ejemplo de antes:

```html
<p v-highlight="yellow">Highlight this text bright yellow</p>
```

```js
const app = Vue.createApp({})

app.directive('highlight', {
  beforeMount(el, binding, vnode) {
    el.style.background = binding.value
  }
})
```

Ahora que los _hooks_ del ciclo de vida de directivas modificadas reflejan los de los componentes en sí, ¡estos se vuelven más fáciles de entender y recordar!

## Detalles de Implementación

In Vue 3, we're now supporting fragments, which allow us to return more than one DOM node per component. You can imagine how handy that is for something like a component with multiple `<li>` elements or the children elements of a table:

```html
<template>
  <li>¡Hola</li>
  <li>Vue</li>
  <li>Devs!</li>
</template>
```

Tan maravillosamente flexible como es esto, podemos encontrar un problema potencial con una directiva modificada que pueda tener múltiples nodos raíz.

Como resultado, las directivas modificadas ahora son incluidas como parte de los datos de un nodo de DOM virtual. Cuando una directiva modificada es utilizada en un componente, los _hooks_ son pasados al componente como propiedades extrañas y terminan en `this.$attrs`.

Esto también significa que es posible enganchar directamente en el ciclo de vida de un elemento así como en la plantilla, lo que puede ser práctico cuando una directiva modificada está muy involucrada:

```html
<div @vnodeMounted="myHook" />
```

Esto es consistente con el comportamiento del atributo de caída a través, así que cuando un componente hijo utilice `v-bind="$attrs"` en un elemento interno, este aplicará también cualquier directiva modificada utilizada en él.
