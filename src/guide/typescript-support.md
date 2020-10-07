# Soporte para TypeScript

> [Vue CLI](https://cli.vuejs.org) provee soporte integrado para herramientas de TypeScript.

## Declaración Oficial en Paquetes NPM

Un sistema de tipos estático ayuda a prevenir muchos errores potenciales en tiempo de ejecución a medida que crecen las aplicaciones, razón por la cual Vue 3 está escrito en TypeScript. Esto significa que no necesita ninguna herramienta adicional para utilizar TypeScript con Vue, debido a que posee soporte de primera clase para su uso.

## Configuración Recomendada

```js
// tsconfig.json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    // esto habilita la inferencia estricta para las propiedades de datos en `this`
    "strict": true,
    "jsx": "preserve",
    "moduleResolution": "node"
  }
}
```

Note que tiene que incluir `strict: true` (o al menos `noImplicitThis: true` que es parte de la bandera `strict`) para apalancar la revisión de tipos de `this` en métodos de componente, de lo contrario es siempre tratado como tipo `any`.

Vea [documentación de opciones del compilador de TypeScript](https://www.typescriptlang.org/docs/handbook/compiler-options.html) para más detalles.

## Herramientas de Desarrollo

### Creación de Proyecto

[Vue CLI](https://github.com/vuejs/vue-cli) puede generar nuevos proyectos que utilicen TypeScript. Para empezar:

```bash
# 1. Instale Vue CLI, si aún no está instalado
npm install --global @vue/cli

# 2. Cree un nuevo proyecto, luego elija la opción "Seleccionar características manualmente"
vue create my-project-name

# Si ya tiene un proyecto Vue CLI sin TypeScript, por favor añada un plugin apropiado de Vue CLI:
vue add typescript
```

Asegúrese de que la parte `script` del componente tiene TypeScript como lenguaje:

```html
<script lang="ts">
  ...
</script>
```

### Soporte para Editores de Código

Para desarrollar aplicaciones Vue con TypeScript, recomendamos fuertemente utilizar [Visual Studio Code](https://code.visualstudio.com/), que provee soporte genial integrado para TypeScript. Si está utilizando [componentes de un solo archivo](./single-file-component.html) (SFCs), obtenga la asombrosa [extensión Vetur](https://github.com/vuejs/vetur), que provee inferencia de TypeScript dentro de SFCs y muchas otras características geniales.

[WebStorm](https://www.jetbrains.com/webstorm/) también provee soporte integrado para ambos, TypeScript y Vue.

## Definiendo Componentes Vue

Para dejar que TypeScript infiera apropiadamente los tipos dentro de opciones de componentes Vue, necesita definir componentes con el método global `defineComponent`:

```ts
import { defineComponent } from 'vue'

const Component = defineComponent({
  // inferencia de tipos habilitada
})
```

## Utilizando con la API de Opciones

TypeScript debería ser capaz de inferir la mayoría de los tipos sin definirlos explícitamente. Por ejemplo, si tiene un componente con una propiedad numérica `count`, tendrá un error si intenta llamar un método específico de cadenas de caracteres en ella:

```ts
const Component = defineComponent({
  data() {
    return {
      count: 0
    }
  },
  mounted() {
    const result = this.count.split('') // => La propiedad 'split' no existe en el tipo 'number'
  }
})
```

Si tiene un tipo o interfaz compleja, puede realizar un cast utilizando [aserción de tipos](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions):

```ts
interface Book {
  title: string
  author: string
  year: number
}

const Component = defineComponent({
  data() {
    return {
      book: {
        title: 'Guía de Vue 3',
        author: 'Equipo Vue',
        year: 2020
      } as Book
    }
  }
})
```

### Anotando Tipos de Dato de Retorno

Debido a la naturaleza circular de los archivos de declaración de Vue, TypeScript puede tener dificultades infiriendo los tipos de valores calculados. Por esta razón, puede tener que anotar el tipo de dato de retorno de propiedades calculadas.

```ts
import { defineComponent } from 'vue'

const Component = defineComponent({
  data() {
    return {
      message: 'Hello!'
    }
  },
  computed: {
    // necesita una anotación
    greeting(): string {
      return this.message + '!'
    }

    // en un valor calculado con un setter, el getter necesita ser anotado
    greetingUppercased: {
      get(): string {
        return this.greeting.toUpperCase();
      },
      set(newValue: string) {
        this.message = newValue.toUpperCase();
      },
    },
  }
})
```

### Anotando Propiedades

Vue realiza una validación en tiempo de ejecución en propiedades con un `type` definido. Para proporcionar estos tipos a TypeScript, necesitamos hacer cast al constructor con `PropType`:

```ts
import { defineComponent, PropType } from 'vue'

interface ComplexMessage {
  title: string
  okMessage: string
  cancelMessage: string
}
const Component = defineComponent({
  props: {
    name: String,
    success: { type: String },
    callback: {
      type: Function as PropType<() => void>
    },
    message: {
      type: Object as PropType<ComplexMessage>,
      required: true,
      validator(message: ComplexMessage) {
        return !!message.title
      }
    }
  }
})
```

Si encuentra que la función que valida no obtiene la inferencia de tipos o la completación de miembros no está funcionando, anotando el argumento con el tipo esperado puede ayudar a abordar estos problemas.

## Utilizando la API de Composición

En la función `setup()`, no necesita pasar un tipo al parámetro `props` porque este inferirá tipos de la opción `props` del componente.

```ts
import { defineComponent } from 'vue'

const Component = defineComponent({
  props: {
    message: {
      type: String,
      required: true
    }
  },

  setup(props) {
    const result = props.message.split('') // correcto, el tipo de 'message' es string
    const filtered = props.message.filter(p => p.value) // un error será lanzado: La propiedad 'filter' no existe en el tipo 'string'
  }
})
```

### Tipos con `ref`s

Los Refs infieren el tipo del valor inicial:

```ts
import { defineComponent, ref } from 'vue'

const Component = defineComponent({
  setup() {
    const year = ref(2020)

    const result = year.value.split('') // => La propiedad 'split' no existe en el tipo 'number'
  }
})
```

Algunas veces es posible que necesitemos especificar tipos complejos para un valor interno de un ref's. Podemos hacer eso simplemente pasando un argumento genérico cuando llamemos a ref para anular la inferencia por defecto:

```ts
const year = ref<string | number>('2020') // tipo de year: Ref<string | number>

year.value = 2020 // ok!
```

::: tip Nota
Si el tipo del genérico es desconocido, es recomendado hacer el cast de `ref` a `Ref<T>`.
:::

### Tipos con `reactive`

Cuando se refiere a tipos con una propiedad `reactive`, podemos utilizar interfaces:

```ts
import { defineComponent, reactive } from 'vue'

interface Book {
  title: string
  year?: number
}

export default defineComponent({
  name: 'HelloWorld',
  setup() {
    const book = reactive<Book>({ title: 'Guía de Vue 3' })
    // or
    const book: Book = reactive({ title: 'Guía de Vue 3' })
    // or
    const book = reactive({ title: 'Guía de Vue 3' }) as Book
  }
})
```

### Tipos con `computed`

Los valores calculados automáticamente inferirán el tipo del valor retornado

```ts
import { defineComponent, ref, computed } from 'vue'

export default defineComponent({
  name: 'CounterButton',
  setup() {
    let count = ref(0)

    // solo lectura
    const doubleCount = computed(() => count.value * 2)

    const result = doubleCount.value.split('') // => La propiedad 'split' no existe en el tipo 'number'
  }
})
```
