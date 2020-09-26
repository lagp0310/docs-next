---
badges:
  - cambio importante
---

# Comportamiento de Coerción de Atributo <MigrationBadges :badges="$frontmatter.badges" />

::: info Info
Est es un cambio en una API interna de bajo nivel y no afecta a la mayoría de los desarrolladores.
:::

## Vistazo General

Aquí se presenta un resumen de alto nivel sobre los cambios:

- Retirar el concepto interno de atributos enumerados y tratar dichos atributos igual que a los atributos no booleanos.
- **IMPORTANTE**: Ya no se elimina el atributo si el valor booleano es `false`. En su lugar, se asigna como attr="false". Para eliminar el atributo, use `null` o `undefined`.

Para mayor información, ¡siga leyendo!

## Sintaxis 2.x

En la versión 2.x, teníamos las siguientes estrategias para forzar los valores de `v-bind`:

- Para algunos pares atributo/elemento, Vue siempre utiliza el atributo IDL correspondiente (propiedad): [como `value` de `<input>`, `<select>`, `<progress>`, etc](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L11-L18).

- Para "[atributos booleanos](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L33-L40)" y [xlinks](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L44-L46), Vue los elimina si son "falsos" ([`undefined`, `null` o `false`](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L52-L54)) y los añade en caso contrario (véase [aquí](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/runtime/modules/attrs.js#L66-L77) y [aquí](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/runtime/modules/attrs.js#L81-L85)).

- Para "[atributos enumerados](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L20)" (actualmente `contenteditable`, `draggable` y `spellcheck`), Vue intenta [obligarlos](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L24-L31) a ser cadenas de caracteres (con tratamiento especial para `contenteditable` por ahora, para solucionar [vuejs/vue#9397](https://github.com/vuejs/vue/issues/9397)).

- Para otros atributos, eliminamos los valores "falsos" (`undefined`, `null`, o `false`) y les asignamos otros valores como es (véase [aquí](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/runtime/modules/attrs.js#L92-L113)).

La siguiente tabla describe como Vue obliga a los "atributos enumerados" de forma diferente con atributos normales no booleanos:

| Expresión de Vinculación | `foo` <sup>normal</sup> | `draggable` <sup>enumerated</sup> |
| ------------------------ | ----------------------- | --------------------------------- |
| `:attr="null"`           | /                       | `draggable="false"`               |
| `:attr="undefined"`      | /                       | /                                 |
| `:attr="true"`           | `foo="true"`            | `draggable="true"`                |
| `:attr="false"`          | /                       | `draggable="false"`               |
| `:attr="0"`              | `foo="0"`               | `draggable="true"`                |
| `attr=""`                | `foo=""`                | `draggable="true"`                |
| `attr="foo"`             | `foo="foo"`             | `draggable="true"`                |
| `attr`                   | `foo=""`                | `draggable="true"`                |

Podemos observar de la tabla anterior que la implementación actual obliga `true` a `'true'` pero elimina el atributo si es `false`. Esto también llevó a inconsistencia y requería a los usuarios obligar manualmente los valores booleanos a ser cadenas de caracteres en casos muy comunes como en atributos `aria-*` como `aria-selected`, `aria-hidden`, etc.

## Sintaxis 3.x

Intentamos eliminar este concepto interno de "atributos enumerados" y tratarlos como atributos de HTML no booleanos.

- Esto resuelve la inconsistencia entre los atributos normales no booleanos y “atributos enumerados”
- También hace posible usar valores diferentes a `'true'` y `'false'`, e incluso palabras claves que aún están por venir, para atributos como `contenteditable`

Para atributos no booleanos, Vue, en su lugar, dejará de eliminarlos si son `false` y obligarlos a ser `'false'`.

- Esto resuelve la inconsistencia entre `true` y `false` y hace más fácil la salida de atributos `aria-*`

La siguiente tabla describe el nuevo comportamiento:

| Expresión de Vinculación  | `foo` <sup>normal</sup>    | `draggable` <sup>enumerated</sup> |
| ------------------------- | -------------------------- | --------------------------------- |
| `:attr="null"`            | /                          | / <sup>†</sup>                    |
| `:attr="undefined"`       | /                          | /                                 |
| `:attr="true"`            | `foo="true"`               | `draggable="true"`                |
| `:attr="false"`           | `foo="false"` <sup>†</sup> | `draggable="false"`               |
| `:attr="0"`               | `foo="0"`                  | `draggable="0"` <sup>†</sup>      |
| `attr=""`                 | `foo=""`                   | `draggable=""` <sup>†</sup>       |
| `attr="foo"`              | `foo="foo"`                | `draggable="foo"` <sup>†</sup>    |
| `attr`                    | `foo=""`                   | `draggable=""` <sup>†</sup>       |

<small>†: cambiado</small>

La coerción para atributos booleanos se dejó sin modificar.

## Estrategia de Migración

### Atributos Enumerados

La ausencia de atributos enumerados y `attr="false"` puede producir diferentes valores de atributos IDL (los cuales reflejan el estado actual), descrito como sigue a continuación:

| Atributo Enumerado Ausente | Atributo IDL y su valor              |
| -------------------------- | ------------------------------------ |
| `contenteditable`          | `contentEditable` &rarr; `'inherit'` |
| `draggable`                | `draggable` &rarr; `false`           |
| `spellcheck`               | `spellcheck` &rarr; `true`           |

Para que siga funcionando el comportamiento anterior, y como vamos a obligar `false` a `'false'`, en la versión 3.x los desarrolladores de Vue necesitan hacer que la expresión `v-bind` resuelva `false` o `'false'` para `contenteditable` y `spellcheck`.

En la versión 2.x, los valores inválidos eran obligados a `'true'` para atributos enumerados. Esto era, generalmente, involuntario y poco probable de ser dependiente a larga escala. En la versión 3.x `true` o `'true'` debería ser especificado explícitamente.

### Obligando `false` a `'false'` en vez de eliminar el atributo

En la versión 3.x, `null` o `undefined` debería ser utilizado para eliminar un atributo explícitamente.

### Comparación entre el comportamiento de la versión 2.x y 3.x

<table>
  <thead>
    <tr>
      <th>Attribute</th>
      <th><code>v-bind</code> value <sup>2.x</sup></th>
      <th><code>v-bind</code> value <sup>3.x</sup></th>
      <th>HTML output</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3">2.x “Enumerated attrs”<br><small>i.e. <code>contenteditable</code>, <code>draggable</code> and <code>spellcheck</code>.</small></td>
      <td><code>undefined</code>, <code>false</code></td>
      <td><code>undefined</code>, <code>null</code></td>
      <td><i>removed</i></td>
    </tr>
    <tr>
      <td>
        <code>true</code>, <code>'true'</code>, <code>''</code>, <code>1</code>,
        <code>'foo'</code>
      </td>
      <td><code>true</code>, <code>'true'</code></td>
      <td><code>"true"</code></td>
    </tr>
    <tr>
      <td><code>null</code>, <code>'false'</code></td>
      <td><code>false</code>, <code>'false'</code></td>
      <td><code>"false"</code></td>
    </tr>
    <tr>
      <td rowspan="2">Other non-boolean attrs<br><small>eg. <code>aria-checked</code>, <code>tabindex</code>, <code>alt</code>, etc.</small></td>
      <td><code>undefined</code>, <code>null</code>, <code>false</code></td>
      <td><code>undefined</code>, <code>null</code></td>
      <td><i>removed</i></td>
    </tr>
    <tr>
      <td><code>'false'</code></td>
      <td><code>false</code>, <code>'false'</code></td>
      <td><code>"false"</code></td>
    </tr>
  </tbody>
</table>
