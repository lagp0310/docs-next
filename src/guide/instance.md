# La instancia de la Aplicación

## Creando una Instancia

Cada aplicación Vue empieza con la creación de una **instancia de aplicación** con la función `createApp`:

```js
Vue.createApp(/* opciones */)
```

Después de que la instancia es creada, podemos _montarla_, pasando un contenedor al método `mount`. Por ejemplo, si queremos monstar una aplicación Vue en `<div id="app"></div>`, deberíamos pasarle `#app`:

```js
Vue.createApp(/* opciones */).mount('#app')
```

Aunque no está estrictamente asociado al [patrón MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), el diseño de Vue fue parcialmente inspirado por él. Como una convención, usualmente utilizamos la variable `vm` (abreviatura para ViewModel) para referirnos a nuestra instancia.

Cuando usted crea una instancia, le pasa un **objeto de opciones**. La mayor parte de esta guía describe cómo puede usar estas opciones para crear el comportamiento deseado. Para referencia, también puede buscar la lista completa de opciones en la [referencia de la API](../api/options-data.html).

Una aplicación Vue consiste en una **instancia raíz** creada con `createApp`, opcionalmente organizada en un árbol de componentes anidados reutilizables. Por ejemplo, un árbol de componentes para una aplicación `todo` puede verse de la siguiente forma:

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Después hablaremos del [sistema de componentes](component-basics.html) en detalle. Por ahora, solo debe saber que todos los componentes Vue son también instancias, y por ello aceptan el mismo objeto de opciones.

## Datos y Métodos

Cuando una instancia es creada, agrega todas las propiedades que encontró en su `data` al [**sistema reactivo** de Vue](reactivity.html). Cuando los valores de esas propiedades cambian, la vista "reaccionará", actualizándose para coincidir con los nuevos valores.

```js
// Nuestro objeto de datos
const data = { a: 1 }

// El objeto es añadido a la instancia raíz
const vm = Vue.createApp({
  data() {
    return data
  }
}).mount('#app')

// Obtener la propiedad de la instancia
// retorna la que se encuentra en los datos originales
vm.a === data.a // => true

// Asignar la propiedad en la instancia
// también afecta a los datos originales
vm.a = 2
data.a // => 2
```

Cuando estos datos cambian, la vista se renderizará de nuevo. Debe notarse que las propiedades en `data` solo son **reactivas** si estas existían cuando la instancia fue creada. Esto significa que si usted agrega una nueva propiedad, así:

```js
vm.b = 'hola'
```

Entonces los cambios de `b` no harán que la vista se actualice. Si usted sabe que necesitará una propiedad luego, pero en el momento de la creación no se conoce qué valor tendrá, deberá asignarle algún valor inicial. Por ejemplo:

```js
data() {
  return {
    newTodoText: '',
    visitCount: 0,
    hideCompletedTodos: false,
    todos: [],
    error: null
  }
}
```

La única excepción a esto es con el uso de `Object.freeze()`, que previene que sean cambiadas las propiedades existentes, lo que también significa que el sistema reactivo no puede _hacer seguimiento_ a los cambios.

```js
const obj = {
  foo: 'bar'
}

Object.freeze(obj)

const vm = Vue.createApp({
  data() {
    return obj
  }
}).mount('#app')
```

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- esto no actualizará más la variable `foo`! -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

Además de las propiedades de los datos, las instancias exponen un número de propiedades y métodos de útiles. Estas tienen el prefijo `$` para diferenciarlas de las propiedades definidas por el usuario. Por ejemplo:

```js
const vm = Vue.createApp({
  data() {
    return {
      a: 1
    }
  }
}).mount('#example')

vm.$data.a // => 1
```

En el futuro, puede consultar la [referencia de la API](../api/instance-properties.html) para una lista completa de las propiedades y métodos de instancia.

## _Hooks_ de Instancia de Ciclo de Vida

Cada instancia pasa a través de una serie de pasos de inicialización cuando es creada, por ejemplo, necesita preparar la observación de datos, compilar la plantilla, montar la instancia al DOM, y actualizar el DOM cuando los datos cambian. Durante este proceso, también ejecuta funciones llamadas **_hooks_ de ciclo de vida**, dando a los usuarios la oportunidad para agregar su propio código en etapas específicas.

Por ejemplo, el _hook_ [created](../api/options-lifecycle-hooks.html#created) puede ser utilizado para ejecutar código después de que una instancia fue creada:

```js
Vue.createApp({
  data() {
    return {
      a: 1
    }
  },
  created() {
    // `this` apunta a la instancia de vm
    console.log('a is: ' + this.a) // => "a is: 1"
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
