# Componentes de un Solo Archivo

## Introducción

En muchos proyectos Vue, los componentes globales serán definidos utilizando `app.component()`, seguido por `app.mount('#app')` para apuntar a un elemento contenedor en el cuerpo de cada página.

Esto puede funcionar muy bien desde proyectos pequeños a proyectos medianos, donde JavaScript solo es utilizado para mejorar ciertas vistas. Sin embargo, en proyectos más complejos, o donde su frontend está manejado completamente por JavaScript, estas desventajas se vuelven claras:

- **Las definiciones Globales** obligan nombres únicos para cada componente
- **Las plantillas de cadenas de caracteres** carecen de resaltado de sintaxis y requieren slashes poco atractivos para HTML multilínea
- **Sin soporte CSS** significa que mientras el HTML y JavaScript estén modularizados en componentes, el CSS es dejado fuera conspicuamente
- **Sin paso de compilación** nos restringe a HTML y JavaScript ES5, en vez de preprocesadores como Pug (formalmente Jade) y Babel

Todas estas son resueltas por **componentes de un solo archivo** con una extensión `.vue`, hecho posible con herramientas de compilación como Webpack o Browserify.

Aquí está un ejemplo de un archivo al que llamaremos `Hello.vue`:

<a href="https://codepen.io/team/Vue/pen/3de13b5cd0133df4ecf307b6cf2c5f94" target="_blank" rel="noopener noreferrer"><img src="/images/sfc.png" width="403" alt="Single-file component example (click for code as text)" style="display: block; margin: 15px auto; max-width: 100%"></a>

Ahora obtenemos:

- [Resaltado de sintaxis completa](https://github.com/vuejs/awesome-vue#source-code-editing)
- [Módulos CommonJS](https://webpack.js.org/concepts/modules/#what-is-a-webpack-module)
- [CSS con alcance por componente](https://vue-loader.vuejs.org/en/features/scoped-css.html)

Como se prometió, también podemos utilizar preprocesadores como Pug, Babel (con módulos ES2015), y Stylus para componentes más claros y completos en características.

<a href="https://codesandbox.io/s/vue-single-file-component-with-pre-processors-mr3ik?file=/src/App.vue" target="_blank" rel="noopener noreferrer"><img src="/images/sfc-with-preprocessors.png" width="563" alt="Single-file component with pre-processors example (click for code as text)" style="display: block; margin: 15px auto; max-width: 100%"></a>

Estos lenguajes específicos son únicamente ejemplos. Puede utilizar fácilmente TypeScript, SCSS, PostCSS, o cualquier otro preprocesador que le ayude a ser productivo. Si utiliza Webpack con `vue-loader`, también tiene soporte de primera clase para Módulos CSS.

### ¿Qué hay sobre la Separación de Responsabilidades?

Una cosa importante a notar es que **la separación de responsabilidades no es igual a la separación de tipos de archivo.** En el desarrollo de interfaces de usuario modernas, hemos encontrado que en vez de dividir el código base en tres grandes capas que se entrelazan una con otra, tiene mucho más sentido dividirlas en componentes con bajo acoplamiento y componer los mismos. Dentro de un componente, su plantilla, lógica y estilos están acoplados inherentemente, y colocándolos de hecho hace al componente más cohesivo y mantenible.

Incluso si no le gusta la idea de Componentes de un Solo Archivo, todavía puede aprovechar sus características de recarga en caliente y precompilación separando su JavaScript y CSS en archivos separados:

``` html
<!-- my-component.vue -->
<template>
  <div>Esto será precompilado</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

## Empezando

### Ejemplo en Sandbox

Si quiere adentrarse y empezar jugando con componentes de un solo archivo, vea [esta aplicación simple todo](https://codesandbox.io/s/vue-todo-list-app-with-single-file-component-vzkl3?file=/src/App.vue) en CodeSandbox.

### Para Usuarios Nuevos en Sistemas de Compilación de Módulos en JavaScript

Con componentes `.vue`, estamos entrando al reino de aplicaciones avanzadas de JavaScript. Esto significa aprender a utilizar algunas herramientas adicionales si aún no lo ha hecho:

- **Manejador de Paquetes de Node (NPM)**: Lea la sección de [la Guía de Inicio](https://docs.npmjs.com/packages-and-modules/getting-packages-from-the-registry) sobre cómo obtener paquetes del registro.

- **JavaScript Moderno con ES2015/16**: Lea parte a parte la [Guía de Aprendizaje de ES2015](https://babeljs.io/docs/en/learn) de Babel. No tiene que memorizar cada característica ahora mismo, pero tenga esta página como referencia a la que puede volver.

Despúes de que ha tomado un día para adentrarse en estos recursos, recomendamos ver [Vue CLI](https://cli.vuejs.org/). ¡Siga las instrucciones y debería tener un proyecto Vue con componentes `.vue`, ES2015, webpack y recarga en caliente al instante!

### Para Usuarios Avanzados

La interfaz de línea de comandos se ocupa de la mayoría de configuraciones de herramientas por usted, pero también permite buen grado de personalización a través de sus [opciones de configuración ](https://cli.vuejs.org/config/).

En caso de que prefiera preparar su propia configuración de compilación de cero, tendrá que configurar webpack manualmente con [vue-loader](https://vue-loader.vuejs.org). Para aprender más sobre webpack, vea [su documentación oficial](https://webpack.js.org/configuration/) y la [academia de aprendizaje de webpack](https://webpack.academy/p/the-core-concepts).
