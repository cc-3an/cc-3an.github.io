---
layout: post
title:  "Laboratorio 6"
date:   2017-01-07
category: lab
description: >
    En este laboratorio van aprender una técnica de optimización y conocerán como se manejan los datos internamente.
---

#### Un poco de Información

**Multiplicación de Matrices**

Si se recuerdan, las matrices son estructuras de 2 dimensiones, donde uno tiene acceso a cada elemento vía 2 índices. Para multiplicar dos matrices, se puede utilizar simplemente 3 ciclos anidados, asumiendo que las matrices A, B y C son cuadradas (nxn) y están guardadas en un arreglo de una dimensión:

```c
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++)
        for (int k = 0; k < n; k++)
            C[i+j*n] += A[i+k*n] * B[k+j*n];
```

La multiplicación de matrices son el core de muchos algoritmos de álgebra, y una eficiente multiplicación de matrices es crucial para muchas aplicaciones.

En el código de arriba, noten que los ciclos están ordenados por i, j, k. Si examinamos el ciclo interno (k), vemos que este se mueve a través de B con 1 paso, a través de A con n pasos y a través de C con 0 pasos. Para calcular la multiplicación de matrices correctamente, el orden de los ciclos no importa. Sin embargo, el orden en el que eligen acceder a los elementos de las matrices puede tener un mayor impacto en la performance del programa. El cache funciona mejor (hay mas cache hit, y menos cache misses) cuando se accede a la memoria tomando en cuenta la localidad temporal y espacial. Optimizar el acceso a memoria es esencial para obtener una buena performance en la jerarquía de memoria.

**Transponer Matrices**

Algunas veces, se necesita intercambiar en una matriz filas por columnas. A esto se le llama "transponer" y una implementación eficiente puede ser bien útil, al utilizar operaciones de álgebra lineal más complicadas. La transpuesta de una matriz A se denota como A<sup>T</sup>.

![fig1](/assets/img/labs/matTnorm.png)

**Cache Blocking**

En el código de arriba de multiplicación de matrices, noten que para calcular un valor de C necesitamos atravesar las matrices A y B completamente. Nosotros estamos constantemente accediendo a valores nuevos de la memoria y reusando muy poco los datos que están en el cache. Podemos mejorar el reuso de esos datos en cache, implementando una técnica llamada cache blocking. Más formalmente, el cache blocking es una técnica que trata de reducir el “cache miss rate” mejorando la localidad temporal y/o espacial de los accesos a memoria. En el caso de la transpuesta de una matriz nosotros consideramos un bloque pequeño en cada tiempo.

![fig2](/assets/img/labs/matTblock.png)

En la imagen de arriba, nosotros transponemos cada bloque Aij de la matriz A a su posición final en la matriz de salida, un bloque a la vez. Con este esquema, nosotros podemos reducir el trabajo de llenar el cache durante el procesamiento de cualquier bloque. Esto (si está implementado correctamente) va resultar en un performance bastante significativo. En este lab van a implementar la técnica del cache blocking para calcular la transpuesta de una matriz y verificar su performance.

***

#### Ejercicio 1: Multiplicación de Matrices

Échenle un vistazo a matrixMultiply.c. Van a notar que el archivo contiene múltiples implementaciones de una multiplicación de matrices con 3 ciclos anidados.

Compilen y corran su código con el siguiente comando:

```shell
$ make ex1
```

Noten que el comando de compilación en el Makefile usa la bandera “-O3”. Es importante que aquí utilicemos la bandera “-O3” para que el compilador haga optimizaciones. El Makefile va a correr matrixMultiply dos veces. Copien los resultados en algún lugar para que ustedes no tengan que correr el programa otra vez y utilícenlos para responder a las siguientes preguntas:

1. ¿Por qué el perfomance de Gflops/s baja con valores de n grandes?
2. ¿Qué orden de los ciclos tiene el mejor perfomance en las matrices de 1000x1000?
3. ¿Qué orden tiene el peor performance?
4. ¿Cómo la manera en que avanzamos en las matrices con respecto al ciclo interno afecta el performance?

***

#### Ejercicio 2: Transpuesta de una Matriz

Compilen y corran la implementación (no muy buena) de la transpuesta de una matriz en transpose.c

```shell
$ make ex2
```

Lo siguiente, completar los siguientes ejercicios.

1. Noten el tiempo requerido para realizar la transpuesta de una matriz de tamaño 2000x2000
2. Modifiquen la función llamada tranpose en transpose.c para implementar un nivel de cache blocking. Esto es, hacer un ciclo que tome pequeños bloques de una matriz, a ese pequeño bloque hacerle la transpuesta y guardarlo en la matriz de destino. Asegúrense de manejar los casos especiales de la transpuesta: ¿Qué pasa si tratamos de aplicarle la transpuesta a una matriz de 5x5 con un tamaño de bloque de 2?
3. Prueben tamaños de bloque de 2x2, 100x100, 200x200, 400x400 y 1000x1000. ¿Cuál tiene la mejor performance en una matriz de 2000x2000? ¿Cuál tiene el peor performance?
