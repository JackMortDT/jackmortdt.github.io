---
author: JackMortDT
title: Procesos en Elixir
date: 2022-01-24T09:45:33-06:00
description: Como se crean nuevos procesos en Elixir
type: post
---

## Procesos

En Elixir, todo el código corre dentro de procesos. Los procesos son aislados unos de otros, se ejecutan simultáneamente entre sí y se comunican a través del paso de mensajes. Los procesos no solo sirven como base de la concurrencia en Elixir, sino que también proporcionan los medios para crear programas distribuidos y tolerantes a fallos.

Los procesos de Elixir no son los mismos del sistema operativo. Los procesos de Elixir son extremadamente livianos, de hecho, la máquina virtual de Erlang admite hasta 134 millones de procesos. Los procesos creados por la maquina virtual son independientes del sistema operativo; son más livianos y su creación toma solo microsegundos.

Elixir usa un modelo de concurrencia de actores, lo que significa lo siguiente:

- Cada actor es un proceso
- Cada proceso realiza una tarea específica
- Para decirle a un proceso que haga algo, es necesario mandarle un mensaje. El proceso puede responder de vuelta con otro mensaje
- Los tipos de mensajes sobre los que puede actuar el proceso son específicos del proceso mismo.
- Los procesos no comparten información con otros procesos

El proceso es una estructura simple: tienes un buzón, creado dentro de una función y por el bloque **receive**, puedes gestionar diferentes mensajes y con base a eso hacer algo. Cuando crea un proceso con **spawn**, obtendrá un ID de proceso y también podrá saber si este proceso esta vivo o no.

Cuando tienes un proceso vivo, puedes enviarle mensajes, para ello solo tienes que enviar el ID del proceso y un mensaje.

## Ejemplo de procesos ping/pong

He estado leyendo el libro de [The Little Elixir & OTP Guidebook](https://www.manning.com/books/the-little-elixir-and-otp-guidebook), en uno de los capítulos hay un ejericio que se me hizo muy interesante, un simple ping/pong de procesos, en este caso tener un proceso en el cual le envías el mensaje ping y te responde con un mensaje pong y viceversa.

Para esto se crea un programa el cual va a tener una función con el bloque receive junto con el la lógica que se usara, en este caso mandar ping o pong y obtener un mensaje de respuesta.

```elixir
defmodule PingPong do
  def mailbox do
    receive do
      :ping ->
        IO.puts("pong!")

      :pong ->
        IO.puts("ping!")
    end
  end
end
```

En este caso, funcionaría de la siguiente manera.

<script id="asciicast-9eEyJRYldZDPOGjuFyPuRPI9V" src="https://asciinema.org/a/9eEyJRYldZDPOGjuFyPuRPI9V.js" data-size="medium"></script>

Cuál es el problema con este ejemplo, que cuando recibimos un mensaje de vuelta, el proceso muere.

En general, el proceso debería poder manejar más de un mensaje, esto se solucionaría mandando llamar la función de manera recursiva.

```elixir
def mailbox do
  receive do
    ...
  end
  mailbox()
end
```

<script id="asciicast-97E7XWAXZ0lfTvg8Bd4gKPEge" src="https://asciinema.org/a/97E7XWAXZ0lfTvg8Bd4gKPEge.js" data-size="medium"></script>

De esta manera nuestro proceso ya puede procesar más de un mensaje, pero que pasa si queremos que el proceso se comunique con sigo mismo sin tener la necesidad de estar mandando ping o pong cada vez.

```elixir
def mailbox do
  receive do
    {:ping, pid} ->
      IO.puts("pong!")
      :timer.sleep(1_000)
      send(pid, {:pong, pid})
    {:pong, pid} ->
      IO.puts("ping!")
      :timer.sleep(1_000)
      send(pid, {:ping, pid})
  end
  mailbox()
end
```

De esta manera al momento que manda un mensaje de vuelta, se vuelve a llamar el proceso mandando diferente mensaje, ya que tenemos el ID del proceso podemos hacer esto indefinidamente, coloqué un timer.sleep de un segundo ya que los mensajes si no se verían como salen de una manera desmesurada.

<script id="asciicast-08y7Bfccxbjg2fhFc7Zx0oiMs" src="https://asciinema.org/a/08y7Bfccxbjg2fhFc7Zx0oiMs.js" data-size="medium"></script>

Con este ejemplo, se pude ver el como crear diferentes procesos, mandar y recibir mensajes.
