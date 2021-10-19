+++
title = "Clojure for the win"
description = ""
tags = [
    "Clojure",
    "Funcional"
]
date = "2021-10-18"
categories = [
    "Development",
]
author = "Sofia Feijoo"
+++

## Programación funcional

El paradigma funcional un paradigma de programación declarativo que le intenta escapar a los cambios de estado. Las funciones son puras y las variables no mutan sino que son reemplazadas por otras. Bastante parecido al comportamiento de los Value Objects.

Pero si bien ganamos en legilibildad y precisión, perdemos en performance. Por ejemplo, para modificar un valor en una lista con muchísimos valores deberíamos devolver otra del mismo tamaño. No suena muy performante. Acá es donde aparece Clojure.


## Persistent collections

Clojure entonces trae las persistent collections, que funcionan de la siguiente manera. Imaginate que tenés una lista L = [1, 2, 3, 4]. Ahora querés cambiar el último valor por un 5, pero dejando los otros 3 primeros valores iguales. Normalmente en programación funcional reemplazaríamos la lista [1, 2, 3, 4] por una nueva lista [1, 2, 3, 5]. Pero una persistent collection nos permitirá solamente modificar el último valor. ¿Cómo? Con referencias. Es decir, los tres primeros valores de la lista serán "los mismos" (la misma referencia en memoria). 

Además, utiliza *tries* en su implementación. 

![clojure-data-structure](/clojure-persistent-data-structures.png)

Los tries son similares a los árboles binarios. La diferencia es que los nodos solamente contienen referencias a otros dos nodos y las hojas son las que contienen los elementos ordenados. Y para garantizar la persistencia hace *path copying*: realiza una copia del path de la parte del árbol que permanece inmutable. 

¿Team Clojure?