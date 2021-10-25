+++
title = "Single Responsibility Principle"
description = ""
tags = [
    "SRP",
    "SOLID",
]
date = "2021-10-25"
categories = [
    "Development",
]
author = "Sofia Feijoo"
+++

El Single Responsibility Principle nos dice que las clases y funciones solo deberían tener una sola razón para cambiar (es decir, una única responsabilidad).

Veamos un ejemplo. 
![srp-example](/srp-1.jpg)

La clase alumno es capaz de darnos su promedio o de guardar la información en la base de datos. Es decir, el alumno está teniendo dos responsabilidades. Y cada responsabilidad está ligada a un actor distinto. ¿Cómo podemos hacer que cumpla el SRP? Hay dos posibles maneras.

La primera es el **Facade design pattern**. Facade es un patrón de diseño estructural que proporciona una interfaz simplificada a una biblioteca, un framework o cualquier otro grupo complejo de clases. En nuestro caso sería algo así:

![srp-facade](/srp2.jpg)

Lo bueno de este patrón es que podemos aislar el código de la complejidad de un subsistema: separa la implementación de las responsabilidades. Lo malo es que una fachada puede convertirse rápidamente en un God Object, y además los actores están muy acoplados entre sí: cualquier cambio en una función puede afectar a todos los actores. 


Otro posible diseño nace de la **implementación de interfaces**. Podemos crear una interfaz para cada responsabilidad.

![srp-interfaces](/srp-3.jpg)

Esto mantiene a los actores desacoplados, ya que cada uno dependerá únicamente de su interfaz. Sin embargo es poco práctico tener todas las responsabilidades desparramadas para quienes programamos.

Como todo, es un trade-off. Un diseño será mejor que otro dependiendo el escenario. 