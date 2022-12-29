---
author: JackMortDT
title: Vinculación de variables en Elixir
date: 2021-04-23T12:00:47+09:00
description: Como vincula Elixir valores con variables
type: post
---

## Pattern Matching

#### Como vincula Elixir valores con variables.

```elixir
number = 1
number + 3
```

La mayoría de los programadores verían el código y dirían.

_"Ok, estamos asignando el valor de 1 a la variable de numero y al sumar 3 nos da como resultado 4"_

Esto en Elixir es incorrecto. En Elixir el signo de igual **(=)** no es una asignación. Es una aserción. Esta aserción es verdadera cuando Elixir logra igualar el lado derecho con el lado izquierdo.

En Elixir se llama **match operator "="**.

En este caso del lado izquierdo tenemos una variable y del lado derecho un valor entero.

<script id="asciicast-tBhaT047pEdUrnYSHLRpQSLqd" src="https://asciinema.org/a/tBhaT047pEdUrnYSHLRpQSLqd.js" data-size="medium"></script>

Tenemos que **(10 = number)**, es otra forma de hacer match, y esto pasa en Elixir
por lo cual no nos arroja una excepción.

Pero continuando con el ejemplo, vemos que tenemos un error, el lado izquierdo no contiene el mismo valor que el lado derecho, ahí es donde nos arroja una excepción ya que es como si estuvieramos haciendo lo siguiente:

```elixir
1 = 10
** (MatchError) no match of right hand side value: 10
```

## Matches más complejos

Una lista puede ser creada con los valores separados por comas entre corchetes

```elixir
["Jack", "Luis", "Enrique"]
["Jack", ["Luis", "José", 123]]
```

Regresando al match operator

<script id="asciicast-um29urLNG68jomrDgmnwhDdaH" src="https://asciinema.org/a/um29urLNG68jomrDgmnwhDdaH.js" async data-size="medium"></script>

A su vez podemos tener matches más complejos, como podría ser el siguiente:

```elixir
list = [1, 2, [3, 4, 5]]
[a, b, c] = list
c
```

En el valor **c** del match tenemos una lista dentro de la lista, por lo cual la **c** = **[3, 4, 5]**

Al igual, podemos obtener una excepción si el match no se cumple.

```elixir
list = [1, 2, 3]
[a, 1, c] = list
```

### Ignorando valores con **\_ (Underscore)**

Si no necesitamos capturar el valor en un match, podemos usar la variable ( **\_** ).
Esta variable actua como un descarte del valor en un pattern match.

```elixir
[1, _, _] = [1, 2, 3]
[1, _, _] = [1, "cat", "dog"]
```

### Variables usadas una vez por Match

Al momento de hacer un proceso de match no podemos tener la misma variable más de una vez con diferentes valores

```elixir
[a, a] = [1, 1]
[b, b] = [1, 2]
```

En este caso, el primer ejemplo va a ser un match exitoso ya que el valor de a las dos veces es 1. En caso contrario del segundo ejemplo ya que el valor de b es 1, al momento de intentar hacer el match por segunda vez se esta igualando **1 = 2** lo cual es incorrecto.

Si lo que quieres es forzar Elixir para usar el valor existente en una variable en el pattern matching se usa el prefijo **^**, en Elixir se llama **pin operator**

```elixir
a = 1
a = 2
^a = 1
```

Nos arroja un error en el match por que no es el mismo valor del lado derecho como izquierdo.

Esto funciona de la misma manera si la variable es un componente en el pattern

<script id="asciicast-m0fvllBtbCy06iFcIshly08MB" src="https://asciinema.org/a/m0fvllBtbCy06iFcIshly08MB.js" async data-size="small"></script>

### Otra forma de ver el signo de igual

Joe Armstrong, Creador de Erlang, compara el signo de igual en Erlang al usado en algebra.

Cuando escribes una ecuación como:

```
x = a + 1
```

Tu no estas asignado el valor de **a + 1** a **x**. En lugar de eso estas afirmando que las expresiones **x** y **a + 1** tienen el mismo valor. Si conoces el valor de **x**, puedes tener el valor de **a** y vice versa.
