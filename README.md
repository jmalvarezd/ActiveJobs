# ActiveJobs
Actividad para la exposicion de Active Jobs, Ingenieria de Software 2 UNAL
Ejercicio: Crear un Job basico que elimine todos los registros que cumplan cierta propiedad

# Pasos iniciales

En una consola vacia de Cloud 9, ejecutar los comandos iniciales usuales

```
$ nvm install v8.9.4
```

```
$ gem install rails
```

```
$ rails new jobsactivity
```

```
$ cd jobsactivity
```

Una vez tengamos la aplicacion base creada, generamos un nuevo scaffold sobre el cual trabajar

```
$ rails generate scaffold User moneyspent:integer
```

Nos aseguramos que la base de datos se actualice

```
$ rails db:migrate
```

Generamos el nuevo Job que tendra la logica a ejecutar.

```
$ rails generate job usercleaner
```

Se habra creado entonces un nuevo archivo: /app/jobs/usercleaner_job.rb , inicialmente asi:

```
class UsercleanerJob < ApplicationJob
  queue_as :default

  def perform(*args)
    # Do something later
  end
end
```

Implementamos el metodo perform con lo que deseamos que se realice al llamar este job:

```
  def perform(*args)
    Users.find(
  end
```
