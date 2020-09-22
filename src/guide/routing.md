# Enrutamiento

## Enrutador Oficial

Para la mayoría de Single Page Applications, es recomendable utilizar la librería oficialmente soportada [vue-router](https://github.com/vuejs/vue-router). Para más detalles, véase la [documentación de Vue Router](https://router.vuejs.org/).

## Enrutamiento Simple desde Cero

Si usted solo necesita enrutamiento simple y no desea involucrarse con una librería de enrutamiento con demasiadas características, puede hacerlo renderizando dinámicamente un componente a nivel de página como se muestra a continuación:

```js
const NotFoundComponent = { template: '<p>Page not found</p>' }
const HomeComponent = { template: '<p>Home page</p>' }
const AboutComponent = { template: '<p>About page</p>' }

const routes = {
  '/': HomeComponent,
  '/about': AboutComponent
}

const SimpleRouter = {
  data: () => ({
    currentRoute: window.location.pathname
  }),

  computed: {
    CurrentComponent() {
      return routes[this.currentRoute] || NotFoundComponent
    }
  },

  render() {
    return Vue.h(this.CurrentComponent)
  }
}

Vue.createApp(SimpleRouter).mount('#app')
```

Combinado con la [API de Historial](https://developer.mozilla.org/en-US/docs/Web/API/History_API/Working_with_the_History_API), puede construir un enrutador básico pero completamente funcional en el cliente. Para ver eso en práctica, visite [esta aplicación de ejemplo](https://github.com/phanan/vue-3.0-simple-routing-example).

## Integrando Enrutadores de Terceros

Si existe un enrutador de terceros que prefiera utilizar, como [Page.js](https://github.com/visionmedia/page.js) o [Director](https://github.com/flatiron/director), la integración es [sencillamente similar](https://github.com/phanan/vue-3.0-simple-routing-example/compare/master...pagejs). Aquí encontrará un [ejemplo completo](https://github.com/phanan/vue-3.0-simple-routing-example/tree/pagejs) empleando Page.js.
