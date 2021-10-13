+++
title = "Clean Code Basics"
description = "Qué, cómo y por qué"
tags = [
    "cleancode",
    "coding",
    "codesmells",
    "oop",
]
date = 2021-10-07T17:20:50Z
author = "Sofia Feijoo"
+++


## Código limpio

Existe una teoría llamada "Broken Window Theory", formulada en los ochenta por James Q. Wilson y George Kelling, que intentaba comprender el constante crecimiento del vandalismo y la inseguridad en varios barrios de la ciudad de Nueva York. Esta teoría sostiene que el delito es mayor en las zonas con altos niveles de descuido, suciedad y desorden.

En definitiva, el desorden invita a más desorden. Y exactamente esto sucede cuando programamos: un código desprolijo probablemente continúe empeorando con el tiempo. En cambio, si trabajamos con un código limpio es mucho más fácil seguir las mismas lógicas.

Pero, ¿qué es el código limpio? Como casi todo en la vida, su definición es un poco ambigua y depende generalmente del contexto. Pero para comenzar, podemos establecer algunos puntos clave para identificarlo:

1. Es fácil de comprender, tanto para unx como para otrxs.
2. No está duplicado.
3. Es corto y simple.
4. Funciona.

La mayor ventaja de seguir estas reglas es que hace que nuestro código sea mucho más facil (y barato) de mantener.


## Ejemplos comunes

Además de los casos más conocidos de mal código como variables mal nombradas, abuso de comentarios, funciones que tienen más de una responsabilidad, etc, hay algunos conceptos claves que explican muchos code smells.


**Step down rule**

La regla Step Down fomenta la legibilidad del código. Dice que debería poder leerse desde arriba hacia abajo, con abstracción descendiente (es decir, si A llama a B, primero debería aparecer la función A y luego la B).

Por ejemplo, el siguiente código no cumple con la step down rule 

    private void serve() {
        ...
    }
        
    private void toastBread() {
        ...
    }
        
    private void prepareCoffee() {
        ...
    }
        
    private void makeBreakfast() {
        prepareCoffee();
        toastBread();
        serve();
    }

Para cumplirla debería verse así

    private void makeBreakfast() {
        prepareCoffee();
        toastBread();
        serve();
    }
        
    private void prepareCoffee() {
        ...
    }
        
    private void toastBread() {
        ...
    }
        
    private void serve() {
        ...
    }

Ahora sí, la función más abstracta se encuentra arriba de todo y a medida que vamos leyendo aparecen las funciones invocadas.

**Defensive programming**

El concepto de defensive programming se refiere al constante chequeo de tipos. Si bien tiene como objetivo prevenir bugs, parte desde la desconfianza y suele ser una pérdida de tiempo y generar código repetido. 

Por ejemplo

    public void doSomething (int count) {
        if (count > 0) throw new CountIsNegativeException();
        //do something
    }
    public void doSomethingElse (int count) {
        if (count > 0) throw new CountIsNegativeException();
        //do something else
    }

El parámetro count que reciben ambas funciones no puede ser negativo. El problema es que cada vez que se lo utiliza, se chequea que cumpla esta condición. En cambio la variable count nunca debería siquiera existir siendo negativa, es decir, es una condición que debería chequearse al momento de crearla. Podría crearse por ejemplo una clase Count que se encargue de dicho comportamiento. 

Esto está muy relacionado con un code smell llamado Primitive Obsession.

**Command Query Separaton**

Las funciones deben cambiar estados o devolver valores, pero no ambas a la vez. Un error muy común es que una función intente modificar un estado y devuelva null en caso de no poder hacerlo. Debería, por ejemplo, modificar el estado o devolver una excepción en caso de no poder hacerlo. 
Mezclar estos dos tipos de funciones puede ser peligroso porque podríamos estar haciendo queries dentro de if statments sin darnos cuenta de que en verdad este llamado implica un cambio de estado de alguna clase. Y por supuesto, dificulta la legibilidad.

**Ley de Demeter**

La ley de Demeter intenta combatir el acomplamiento entre componentes. Básicamente dice que solo debemos llamar a los métodos de los objetos pasados como argumentos, creados localmente, o globales.

Si por ejemplo queremos hacer algo con un objeto, pero para ello necesitamos algunos de sus atributos primero. Una opción sería hacer: 

    object.getX().getY().doSomething()

Pero podemos ver que el acoplamiento es alto. Para evitar estos returns encadenados, podemos simplemente delegar la responsabilidad de obtener los parámetros necesarios a la función que llamaremos luego. 

    object.doSomething()

Esto está relacionado también con el principio de Tell, don't ask.

## Conclusiones

Desarrollar software de calidad no es fácil, ni hay una receta mágica a seguir. Es necesario conocer técnicas y reglas, para poder decidir cuando utilizarlas y cuando no. 

Algunas veces el uso excesivo de algunas leyes lleva a problemas típicos como el God Object, o incluso hace que el código sea más difícil de comprender. Está en el criterio del programadorx la ponderación de algunos valores por sobre otros. 

