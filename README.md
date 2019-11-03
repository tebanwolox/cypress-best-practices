# Wolox Cypress Standards and best practices

<h1 align="center">
  <img src="hhttps://www.cypress.io/static/33498b5f95008093f5f94467c61d20ab/c0bf4/cypress-logo.webp" alt="Cypress Best Practices">
</h1>

   - [1- Objective](#1--objective)
   - [2- Selectors](#2--Selectors)
   - [3- Atributo de tipo data](#3--Atributo-de-tipo-data)
   - [4- cy.contains()](#4--cy.contains())
   - [5- Sitios externos](#5--Sitios-externos)
   - [6- Logging](#6--Logging)
   - [7- Independencia de Test](#7--Independencia-de-Test)
   - [8- Usar código compartido en los test](#8--Usar-código-compartido-en-los-test)
   - [9- Referencias](#9--Referencias)

## 1- Objetivo

El propósito de este documento es presentar estándares de uso en Wolox para trabajar con Cypress.

&nbsp;

## 2- Selectors

Para los seleccionar objetos del DOM con cypress, una mala práctica es usar para todo selectores que son susceptibles a cambiar como por ejemplo: id, clases, tags o texto que no es estático. Para solucionar este problema se puede agregar el data-* atributo en el html, esto nos ayuda a obtener los elementos precisos.

```html
<button id="main" class="btn btn-large" role="button" data-cy="submit">Submit</button>
```

Para este ejemplo usar 

- ```javascript cy.get('button').click()``` es el peor de los casos debido a que es demasiado genérico.
- ```javascript cy.get('.btn.btn-large').click()``` las clases son susceptibles a cambiar.
- ```javascript cy.get('#main').click()```	Aunque el id no suele cambiar tanto, también se puede hacer por medio de Javascript. No recomendado
- ```javascript cy.contains('Submit').click()``` Se podría usar si estamos seguros que el contenido no cambia y además es único.
- ```javascript cy.get('[data-cy=submit]').click()``` Esta opción asegura que siempre se tendrá el mismo objeto.

&nbsp;

## 3- Atributo de tipo data

Siempre debemos usar para pruebas con cypress en el html atributos de tipo data, en este caso estos deben estar definidos de así: ```hmtl data-cy=””```

&nbsp;

## 4- cy.contains()

Para saber cuando usar cy.contains(), debemos de preguntarnos lo siguiente. ¿Si el contenido del elemento cambia nos gustaría la prueba falle?. Si la respuesta es positiva entonces debemos usar cy.contains(), en otro caso tratar de usar un atributo de tipo data.

&nbsp;

## 5- Sitios externos

No se debe interactuar con sitios o servidores de los cuales no tenemos control. Para este tipo de situaciones se puede creer que la solución es ```javascript cy.visit()```, pero esto lleva a muchos problemas como de consumo, la prueba dependerá de los cambios de un agente externo o el sitio externo puede bloquear los testing. 

&nbsp;

## 6- Logging

Podemos usar Stub para hacer creer a nuestra aplicación que hicimos un logging exitoso, o si necesitamos un token real del servidor siempre podemos usar ```javascript cy.request()```. (Ver video)[https://www.youtube.com/watch?v=5XQOK0v_YRE].

&nbsp;

## 7- Independencia de Test


Los test siempre deben de ser independientes y no debe importar si otro falla para que este se ejecute de manera normal.

```javascript
   // an example of what NOT TO DO
describe('my form', function () {
  it('visits the form', function () {
    cy.visit('/users/new')
  })

  it('requires first name', function () {
    cy.get('#first').type('Johnny')
  })

  it('requires last name', function () {
    cy.get('#last').type('Appleseed')
  })

  it('can submit a valid form', function () {
    cy.get('form').submit()
  })
})

```

En el ejemplo anterior cada prueba depende del anterior para que esta funcione, lo cual no nos da el componente de independencia que requiere cada vez. En este caso se debería hacer un solo test como el siguiente:

```javascript
   // a bit better
    describe('my form', function () {
    it('can submit a valid form', function () {
        cy.visit('/users/new')

        cy.log('filling out first name') // if you really need this
        cy.get('#first').type('Johnny')

        cy.log('filling out last name') // if you really need this
        cy.get('#last').type('Appleseed')

        cy.log('submitting form') // if you really need this
        cy.get('form').submit()
    })
    })

```

&nbsp;

## 8- Usar código compartido en los test

Siempre debemos tratar de no duplicar código, si cada test necesita ejecutar la misma acción al principio, sería una buena práctica poner estos pasos repetitivos en una función ```javascript beforeEach()``` la cual se ejecutará antes de cada prueba.

&nbsp;


## 9- Referencias

   [Best Pracrices]: <https://docs.cypress.io/guides/references/best-practices.html>
   [I see your point, but]: <https://www.youtube.com/watch?v=5XQOK0v_YRE>
   [Testing The Way It Should Be (aka Intro Into Cypress)]: <https://www.youtube.com/watch?v=pJ349YntoIs&t=1900s>
   [End-to-End testing with Cypress]: <https://medium.com/better-practices/end-to-end-testing-with-cypress-bfcd59633f1a>

## About

This documentation is maintained by [Wolox](https://github.com/wolox) and it was written by [Wolox](http://www.wolox.com.ar).

![Wolox](https://raw.githubusercontent.com/Wolox/press-kit/master/logos/logo_banner.png)