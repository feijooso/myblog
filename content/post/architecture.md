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

A este objeto LoginHandler lo diseñamos usando **TDD**. Verificamos que llame al usecase y sus ouputs según si puedo loguearse o no.


    class LoginHandlerShould {
        @Test
        fun `call Login with given username and password`() {
            loginHandler.handle(Command("login", listOf(alice, alicePassword)))

            verify { login.execute(alice, alicePassword) }
        }

        @Test
        fun `return success message when login succeeds`() {
            every { login.execute(any(), any()) } returns true

            val output = loginHandler.handle(loginCommand)

            assertThat(output).isEqualTo("Login successful")
        }

        @Test
        fun `return fail message when login fails`() {
            every { login.execute(any(), any()) } returns false

            val output = loginHandler.handle(loginCommand)

            assertThat(output).isEqualTo("Login fail")
        }

        @BeforeEach
        fun beforeEach() {
            every { login.execute(any(), any()) } returns true
        }
        private val alice = "@alice"
        private val alicePassword = "1234"
        private val loginCommand = Command("login", listOf(alice, alicePassword))
        private val login = mockk<Login>()
        private val loginHandler = LoginHandler(login)
    }

Creando este LoginHandler aparece el objeto Login. El usuario es capaz de ingresar el comando, y la consola es capaz de interpretarlo. Cuando lo interprete llamará al caso de uso Login que es quien conoce a las entidades del domain necesarias para ejecutarlo. 

Diseñamos el use case Login con TDD y de paso creamos el User en el domain. También nos topamos con el repositorio (que será InMemory por ahora).

    class LoginShould {
        @Test
        fun `return true if credentials are valid`() {
            userRepository.add(User(alice, alicePassword))

            val isSuccess = login.execute(alice, alicePassword)

            assertThat(isSuccess).isTrue
        }

        @Test
        fun `return false if user doesnt exist`() {
            val isSuccess = login.execute(nonExistentUser, alicePassword)

            assertThat(isSuccess).isFalse
        }
        @Test
        fun `return false if password is invalid`() {
            userRepository.add(User(alice, alicePassword))

            val isSuccess = login.execute(alice, invalidPassword)

            assertThat(isSuccess).isFalse
        }

        private val alice = "@alice"
        private val alicePassword = "1234"
        private val nonExistentUser = "@bob"
        private val invalidPassword = "invalid"
        private val userRepository = InMemoryUserRepository()
        private val login = Login(userRepository)
    }


Ahora sí podemos volver al test end-to-end con el que iniciamos y dejamos en rojo, para ahora hacerlo pasar. 

## Conclusión

Para cada caso de uso nuevo que queramos agregar (por ejemplo sign up, post, log out, etc) podemos seguir los mismos pasos: crear un test end-to-end que recorra todos los layers de la aplicación y a partir de ahí, siguiendo una lógica outside-in, ir creando los objetos que vayamos necesitando usando TDD. 

El código completo lo podés ver acá: https://github.com/feijooso/social-network/tree/main/src