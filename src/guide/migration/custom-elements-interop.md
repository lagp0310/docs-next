---
badges:
  - cambio importante
---

# Interoperabilidad de Elementos Personalizados <MigrationBadges :badges="$frontmatter.badges" />

# Vistazo General

- **CAMBIO IMPORTANTE:** El listado de elementos personalizados permitidos ahora es realizado durante la compilación de plantillas, y debería ser configurado a través de las opciones del compilador en vez de la configuración en tiempo de ejecución.
- **CAMBIO IMPORTANTE:** El uso de la propiedad especial `is` está restringido solo a la etiqueta reservada `<component>`.
- **NUEVO:** Existe una nueva directiva `v-is` para dar soporte a los casos de uso de la versión 2.x en los que `is` fue usado en elementos nativos para resolver las restricciones de interpretación de HTML nativo.

## Elementos Autónomos Personalizados

Si quisiéramos añadir un elemento personalizado definido fuera de Vue (p. ej. utilizando la API de Componentes Web), necesitamos 'instruir' a Vue para tratarlo como un elemento personalizado. Vamos a utilizar la siguiente plantilla como un ejemplo.

```html
<plastic-button></plastic-button>
```

### Sintaxis de la versión 2.x

En Vue 2.x, la lista de etiquetas permitidas como elementos personalizados era realizada a través de `Vue.config.ignoredElements`:

```js
// Esto hará que Vue ignore el elemento personalizado definido fuera de Vue
// (p. ej., utilizando las APIs de Componentes Web)

Vue.config.ignoredElements = ['plastic-button']
```

### Sintaxis de la versión 3.x

**En Vue 3.0, esta verificación es realizada durante la compilación de la plantilla.** Para instruir al compilador para tratar `<plastic-button>` como un elemento personalizado:

- Si se está utilizando un paso de compilación: pase la opción `isCustomElement` al compilación de plantillas de Vue. Si está utilizando `vue-loader`, este debería ser pasado a través de la opción `compilerOptions` de `vue-loader`:

  ```js
  // en la configuración de webpack
  rules: [
    {
      test: /\.vue$/,
      use: 'vue-loader',
      options: {
        compilerOptions: {
          isCustomElement: tag => tag === 'plastic-button'
        }
      }
    }
    // ...
  ]
  ```

- Si se está utilizando compilación sobre-la-marcha, se pasa a través de `app.config.isCustomElement`:

  ```js
  const app = Vue.createApp({})
  app.config.isCustomElement = tag => tag === 'plastic-button'
  ```

  Es importante notar que la configuración en tiempo de ejecución solo afecta la compilación de plantillas en tiempo de ejecución, no afectará a plantillas pre-compiladas.

## Elementos Personalizados Integrados

La especificación de Elementos Personalizados provee una forma de utilizarlos como [Elemento Personalizado Integrado](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example) añadiendo el atributo `is` a un elemento integrado:

```html
<button is="plastic-button">¡Haga Click!</button>
```

El uso que le da Vue a la propiedad especial `is` era simular lo que hace el atributo nativo antes de que estuviese disponible universalmente en los navegadores. Sin embargo, en la versión 2.x era interpretado como si se estuviese renderizando un componente de Vue con el nombre `plastic-button`. Esto bloquea el uso nativo del Elemento Personalizado Integrado mencionado previamente.

En la versión 3.0, estamos limitando el tratamiento especial de Vue a la propiedad `is` solo a la etiqueta `<component>`.

- Cuando es utilizado en la etiqueta reservada `<component>`, se comportará exactamente de la misma forma que en la versión 2.x;
- Cuando es utilizado en componentes comunes, se comportará como una propiedad común:

  ```html
  <foo is="bar" />
  ```

  - Comportamiento de la versión 2.x: renderiza el componente `bar`.
  - Comportamiento de la versión 3.x: renderiza el componente `foo` y pasa la propiedad `is`.

- Cuando es utilizado en elementos simples, será pasado a la llamada `createElement` como la opción `is`, y también renderizado como un atributo nativo. Esto soporta el uso de elementos integrados personalizados.

  ```html
  <button is="plastic-button">¡Haga Click!</button>
  ```

  - Comportamiento de la versión 2.x: renderiza el componente `plastic-button`.
  - Comportamiento de la versión 3.x: renderiza un botón nativo llamando a

    ```js
    document.createElement('button', { is: 'plastic-button' })
    ```

## `v-is` para soluciones alternativas de interpretación de plantillas declaradas dentro del DOM

> Nota: esta sección solo afecta los casos donde las plantillas Vue son escritas directamente en el HTML de la página.
> Cuando se esté utilizando plantillas declaradas dentro del DOM, la plantilla está sujeta a las reglas de interpretación de HTML nativo. Algunos elementos HTML, como `<ul>`, `<ol>`, `<table>` y `<select>` tienen restricciones en cuáles elementos pueden aparecer dentro de ellos, y algunos elementos como `<li>`, `<tr>`, y `<option>` solo pueden aparecer dentro de ciertos elementos.

### Sintaxis de la versión 2.x

En Vue 2, recomendábamos soluciones alternativas a estas restricciones utilizando la propiedad `is` en una etiqueta nativa:

```html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

### Sintaxis de la versión 3.x

Con el cambio de comportamiento de `is`, se introdujo una nueva directiva `v-is` como solución temporal a estos casos:

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

:::warning
Las funciones `v-is` al igual que una vinculación dinámica de `:is` en la versión 2.x, la cual se utiliza para renderizar un componente por su nombre registrado, debería tener como valor un string de JavaScript:

```html
<!-- Incorrecto, nada será renderizado -->
<tr v-is="blog-post-row"></tr>

<!-- Correcto -->
<tr v-is="'blog-post-row'"></tr>
```

:::

## Estrategia de Migración

- Reemplace `config.ignoredElements` con alguno, `compilerOptions` de `vue-loader` (con el paso de compilación) o con `app.config.isCustomElement` (con la compilación de plantillas sobre-la-marcha)

- Cambie todas las etiquetas que no sean `<component>` con el uso de `is` a `<component is="...">` (para plantillas SFC) o `v-is` (para plantillas declaradas dentro del DOM).
