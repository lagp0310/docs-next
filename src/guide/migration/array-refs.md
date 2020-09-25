---
title: Refs de Arreglos v-for
badges:
- cambio importante
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

En Vue 2, el uso del atributo `ref` dentro de `v-for` asignará la propiedad correspondiente en `$refs` con un array de refs. Este comportamiento se vuelve ambiguo e ineficiente cuando están presentes `v-for`s anidados.

En Vue 3, dicho uso ya no creará automáticamente un array en `$refs`. Para obtener múltiples refs de una única vinculación, vincule `ref` a una función que provea mayor flexibilidad (esto es una nueva característica):

```html
<div v-for="item in list" :ref="setItemRef"></div>
```

Con la API de Opciones:

```js
export default {
  data() {
    return {
      itemRefs: []
    }
  },
  methods: {
    setItemRef(el) {
      this.itemRefs.push(el)
    }
  },
  beforeUpdate() {
    this.itemRefs = []
  },
  updated() {
    console.log(this.itemRefs)
  }
}
```

Con la API de Composición:

```js
import { ref, onBeforeUpdate, onUpdated } from 'vue'

export default {
  setup() {
    let itemRefs = []
    const setItemRef = el => {
      itemRefs.push(el)
    }
    onBeforeUpdate(() => {
      itemRefs = []
    })
    onUpdated(() => {
      console.log(itemRefs)
    })
    return {
      itemRefs,
      setItemRef
    }
  }
}
```

Nótese que:

- `itemRefs` no tiene que ser un array: puede ser también un objeto donde las refs son asignadas por sus claves de iteración.

- Esto también permite hacer reactivo y observado a `itemRefs`, en caso de ser necesario.
