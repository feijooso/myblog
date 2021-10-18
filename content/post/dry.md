+++
title = "DRY Principle"
description = ""
tags = [
    "DRY",
]
date = "2021-10-18"
categories = [
    "Development",
]
author = "Sofia Feijoo"
+++


Muchxs conocerán el principio DRY (Don't Repeat Yourself). Para quienes no, es un principio de software que nos ayuda a luchar contra el código repetido. Así tendremos código limpio, mantenible y fácil de leer. Pareciera ser simple, hasta acá todo bien. 

Lo que nadie nos dijo es que Andy Hunt y Dave Thomas, los creadores de DRY, se referían a duplicaciones de conocimiento, no de código en sí. Esto llevó a que muchas veces lo apliquemos de manera incorrecta.  

A ver. Existe lo que se llama **incidental duplication**. Es código que se ve casi igual pero representa diferentes comportamientos del sistema. En términos de código sí está duplicado, pero en términos de comportamiento no. Por ejemplo:  

    fun moveForward(position: Pair<Int, Int>) = Pair(position.first - 1, position.second)
    fun moveBackwards(position: Pair<Int, Int>) = Pair(position.first + 1, position.second)

Poniendonos en contexto, la idea era armar una api para enviarle comandos a un robot y que este se mueva. Obviemos el resto de la implementación y los posibles errores del modelo. Vemos que la primera función avanza un paso hacia adelante, y la segunda retrocede un paso hacia atrás. Son casi iguales. Apenas las vi dije "código duplicado, esto está mal" y me precipité a armar algo de este estilo:

    fun move(position: Pair<Int, Int>, step: Int) = Pair(position.first + step, position.second)

Me pareció una buena idea. También pensé en agregarle otro step para caminar hacia los costados (y modificar position.second). Listo, 4 pájaros de un tiro.

El problema empezó cuando quise agregar más funcionalidades. La función unificada *move* me empezó a molestar, era poco clara. Estaba esforzándome en mantener una unión sintáctica pero cada movimiento quería seguir evolucionando de manera distinta. No debería haber fusionado esas funciones para cada dirección de movimiento ya que si bien eran muy similares, no repetían comportamiento. 

En conclusión, hay que saber distinguir en qué momento estamos repitiendo sintaxis y en qué momento el comportamiento cambia (aunque sea similar) para tener código mantenible.
