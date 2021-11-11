+++
title = "Social Network - Architecture"
description = ""
tags = [
    "Architecture",
    "Clean",
    "TDD",
    "Kotlin",
    "Design"
]
date = "2021-11-11"
categories = [
    "Development",
]
author = "Sofia Feijoo"
+++

## Escenario

Llegó el día de la kata de arquitectura. Menos mal que ya leí Clean Architecture de Robert Martin ¿qué tan difícil puede ser?

La kata consiste en implementar una especie de red social por consola. Tiene varios escenarios pero esta vez solo me voy a concentrar en el login. Los usuarios pueden ingresar el comando "login @user password" por consola y así loguearse en la red social. 


## Resolución

¿Por dónde arrancamos? Como sugiere el libro Growing Object-oriented Software Guided by tests, podemos armar un primer **esqueleto**. Por ejemplo:

![architecture-skeleton](/social-network-architecture.png)

Analicemos las partes: 

1. Domain: es donde viven las entidades de negocio, por ejemplo los usuarios.
2. Infrastructure: adapters para la base de datos
3. Use Cases: son las reglas de negocio de la aplicación. Por ejemplo Login, o Sign Up en este caso. 
4. UI: interpreta el input del usuario e identifica el caso de uso a llamar. En este caso la UI es la consola
5. Main: inicializa la aplicación

Es importante que el esquema tenga las dependencias bien direccionadas. 

Ahora seguimos con los tests.

Empezamos con un test **end-to-end** que testee el login. El test debe verificar que cuando se introduzca un comando de login con usuario y contraseña, el output sea un mensaje de éxito. Algo así: 

    @Test
    fun login() {
        input.willRead("login user password")
    
        console.run()
    
        assertThat(output.content).isEqualTo("Login successful")
    }

En primer lugar, tenemos que crear un FakeInput y FakeOutput para simular el comportamiento de la consola. También se puede hacer utilizando Mocks. 

Pero para hacer pasar este test, hay que primero crear varias clases. Empezando desde afuera hacia adentro, primero vamos a necesitar que la UI sepa interpretar los comandos. Para ello creamos un login handler.

A este objeto LoginHandler lo diseñamos usando TDD. 

Luego de que crear el LoginHandler, volvemos a donde estabamos antes. El usuario ahora es capaz de ingresar el comando, y la consola es capaz de interpretarlo. Cuando lo interprete llamará al caso de uso Login que es quien conoce a las entidades del domain necesarias para ejecutarlo. 

Diseñamos el use case Login con TDD y de paso creamos el User en el domain.

Ahora sí podemos volver al test end-to-end con el que iniciamos y dejamos en rojo, para ahora hacerlo pasar. 
