# ActiveJobs
Actividad para la exposicion de Active Jobs, Ingenieria de Software 2 UNAL

Ejercicio: Crear un Job basico que elimine los registros que cumplan cierta propiedad

# Pasos iniciales

0. En una consola vacia de Cloud 9, ejecutar los comandos iniciales usuales

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
# Ejercicio:

1. Una vez tengamos la aplicacion base creada, generamos un nuevo scaffold sobre el cual trabajar

```
$ rails generate scaffold User moneyspent:integer
```

2. Nos aseguramos que la base de datos se actualice

```
$ rails db:migrate
```

3. Generamos el nuevo Job que tendra la logica a ejecutar.

```
$ rails generate job usercleaner
```

4. Se habra creado entonces un nuevo archivo: /app/jobs/usercleaner_job.rb , inicialmente asi:

```
class UsercleanerJob < ApplicationJob
  queue_as :default

  def perform(*args)
    # Do something later
  end
end
```

5. Implementamos el metodo perform con lo que deseamos que se realice al llamar este job:

```
  def perform(*args)
    @user = User.find_by "moneyspent < 10"
    @user.destroy
    puts "Destroying non-spender account"
  end
```
#Testing

6. En una nueva consola ejecutamos la aplicacion y observamos los resultado en http://workspace-username.c9users.io/users/

```
$ rails server -b 0.0.0.0
```

7. Abrimos la consola de Rails y creamos algunos registros de prueba

```
$ rails console
```

```
> u1 = User.create(moneyspent: 5)
```

```
> u2 = User.create(moneyspent: 12)
```

```
> u3 = User.create(moneyspent: 3)
```

8. Ejecutamos el Job :

```
>  UsercleanerJob.perform_later
```

8.5 Tambien podemos ejecutarlo luego de pasado un intervalo

```
> UsercleanerJob.set(wait: 30.second).perform_later
```

Conclusion: Los jobs pueden ejecutar cualquier codigo de Ruby, y son utiles para enviar correos frecuentes, programar tareas, ejecutar limpiezas, crear resumenes de actividad, entre otros.
