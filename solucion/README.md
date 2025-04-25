# Solución

## índice


Primero antes de nada, yo en otros ejercicios estaba siempre trabajando con la rama de `JavierGarciaExercises` y en este caso vamos a trabajar con la rama de `development` que es un requisito de una de las ramas que nos piden, asi mato 2 pajaros de un tiro

![](/solucion/imagenes/weekly_1.png)

He creado una imagen para ver visualmente mi idea del ejercicio y como lo voy a plantear:

![](/solucion/imagenes/weekly_2.png)

Antes comentar que todo esto lo hice en un runner propio, es sencillo de enlazar veamos cómo, nos vamos a `settings > runners > y le damos a añadir nuevo`

![](/solucion/imagenes/weekly_3.png)

realizamos todos esos comandos en nuestra máquina virtual

![](/solucion/imagenes/weekly_4.png)

Configuramos el runner

![](/solucion/imagenes/weekly_5.png)

Y por último lo iniciamos con el siguiente comando (comando importante ya que si apagamos la máquina deberemos de volver a ejecutarlo, la configuración ya si está guardada)

```bash
./run.sh
```

![](/solucion/imagenes/weekly_6.png)

**IMPORTANTE** si vamos a usar `docker` deberemos instalarlo antes en el `runner` de lo contrario no nos funcionará ya que no encontrará el comando, y con el usuario que ejecutemos el `run.sh` habrá que darle permisos metiendolo en el grupo de `docker` para que use el `docker engine` con el siguiente comando:

```bash
sudo usermod -aG docker $USER
```

Cerramos sesión y volvemos a iniciar para que se carguen los cambios

Probamos con un `workflow` básico para ver que usa el `runner`, dejaré el `workflow` comentado 

![](/solucion/imagenes/weekly_7.png)

Vale pues así ya podemos usar nuestro propio `runner` así no uso horas de `ubuntu_latest`

Procedemos a generar el tocken de docker

![](/solucion/imagenes/weekly_8.png)

De momento voy a poner el token y el nombre de usuario en variables, no de entorno, cuando vea que funciona correctamente pondré el repositorio público y procederé a crear los `environments` 

Probemos si crea bien la imagen:

![](/solucion/imagenes/weekly_9.png)

documentación docker login https://github.com/docker/login-action

















