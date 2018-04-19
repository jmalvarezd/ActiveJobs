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

# Testing

6. En una nueva consola ejecutamos la aplicacion y observamos los resultados en http://workspace-username.c9users.io/users/

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

# Mailers

9. Comenzamos dandole la propiedad email a los Usuarios

```
rails g migration AddEmailToUsers email:string
rails db:migrate
```

10. Creamos un nuevo mailer, que manejara nuestros correos para los usuarios

```
rails g mailer UserMailer
```

11. Esto nos creara unos nuevos archivos, vamos a /app/mailers/user_mailer.rb y lo dejamos asi, definiendo un nuevo correo:

```
class UserMailer < ApplicationMailer
    def welcome_email(user)
        @user = user
        mail(to: @user.email, subject: 'Welcome to jobsActivity')
    end
end
```

12.Creamos el contenido de este nuevo correo en dos archivos:

```
#/app/views/user_mailer/welcome_email.html.erb

<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
    <h1 >Welcome to mailers!</h1>
    <p>
      You have successfully signed up.<br>
    </p>
    <p>Thanks for joining and have a great day!</p>
  </body>
</html>
```

```
# /app/views/user_mailer/welcome_email.text.erb
Welcome to mailers!

You have successfully signed up.

Thanks for joining and have a great day!
```

13. Por ultimo, hacemos que este correo se envie cuando se cree un nuevo usuario, en el controlador:

```
# /app/controllers/users_controller.rb
...
if @user.save
  UserMailer.welcome_email(@user).deliver_later
...
```

14. Cuando se quiere que esto funcione en una aplicacion real, hace falta por ultimo modificar la configuracion smtp de la aplicacion:

```
# /config/enviroments/development.rb
...
config.action_mailer.raise_delivery_errors = true
  config.action_mailer.perform_deliveries = true

  config.action_mailer.perform_caching = false
  
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = {
    :address  => "smtp.gmail.com",
    :port     => 587,
    :domain => 'gmail.com',
    :user_name => Rails.application.secrets.gmail_username,
    :password => Rails.application.secrets.gmail_password,
    :authentication => "plain",
    :enable_starttls_auto => true
  }
...
```

Conclusion: Los jobs pueden ejecutar cualquier codigo de Ruby, y son utiles para enviar correos frecuentes, programar tareas, ejecutar limpiezas, crear resumenes de actividad, entre otros.
