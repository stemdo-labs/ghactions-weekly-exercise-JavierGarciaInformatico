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

Vamos a ver nuestro `yaml` de `prueba_bruta.yaml` en el que simplemente probamos a subir una imagen en `docker` para ver su funcionamiento

```yaml
name: funcionamiento en bruto

on:
  workflow_dispatch:
    inputs:
      PRODUCCION:
        description: 'desplegar en producción (si/no)'
        required: true
        default: 'no'
      VERSION:
        description: 'versión de la imagen Docker'
        required: true
        default: '1'

#   push:
#     branches:
#       - main
#       - development

jobs:
  job-ci:
    runs-on: [weeklyjaviergarcia]
    steps:
      - name: Checkout del código
        uses: actions/checkout@v4
      - name: construir la imagen Docker
        run: |
          docker build -t ${{vars.DOCKERUSER}}/enbruto:${{ github.event.inputs.VERSION }} .

#https://github.com/docker/login-action

      - name: Login a Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ vars.DOCKERUSER }}
          password: ${{ secrets.DOCKERTOKEN }}
      
      - name: Subir la imagen Docker a Docker Hub
        run: |
          docker push ${{vars.DOCKERUSER}}/enbruto:${{ github.event.inputs.VERSION }}

  job-cd:
    runs-on: [weeklyjaviergarcia]
    needs: job-ci
    steps:
      - name: Desplegar (simulado)
        run: |
          if [ "${{ github.event.inputs.PRODUCCION }}" == "si" ]; then
            echo "desplegando a Producción con la imagen ${{vars.DOCKERUSER}}/enbruto:${{ github.event.inputs.VERSION }}"
          else
            echo "desplegando a UAT con la imagen ${{vars.DOCKERUSER}}/enbruto:${{ github.event.inputs.VERSION }}"
          fi
```

Realmente aquí sólo hago la imagen, no compruebo que luego se pueda usar bien, eso lo voy a hacer manualmente una vez se suba, lo importante de este `yaml` sería la parte de `docker/login-action@v3` que es una action personalizada oficial de `docker` par loguearse de forma segura, así cuando termina el `workflow` borra la sesión para prevenir ataques. [aquí dejo la documentación](https://github.com/docker/login-action)

Probemos si crea bien la imagen, para ello debemos de ejecutar el `workflow` manualmente pues es `dispatch` con modo producción:

![](/solucion/imagenes/weekly_10.png)

![](/solucion/imagenes/weekly_9.png)

Vemos que se subió correctamente, por lo cual la parte de simular un despliegue salió bien

![](/solucion/imagenes/weekly_11.png)

Veamos si se subió en `docker hub`

![](/solucion/imagenes/weekly_12.png)

Por último como prueba final vamos a ver que pasa si arrancamos el contenedor con el comando:

```bash
docker run -p 8080:8080 javiergarciainformatico/enbruto:1
```

![](/solucion/imagenes/weekly_13.png)

Bien! todo salio correcto, Ahora podremos probar con la versión completa de CI osea arrancando el contenedor y comprobando 2 tests que me he inventado para eso lo ponemos en modo `producción`

![](/solucion/imagenes/weekly_14.png)

Vemos que me da un fallo en el test 2 es porque en el `html` está como `Luisk` con mayúsculas, este test lo dejare a propósito para cuando veamos el uso diferentes `workflows` se pueda ver un test fallido

![](/solucion/imagenes/weekly_15.png)

Hay que tener en cuenta una cosa **MUY IMPORTANTE** y es que el contenedor si no lo paramos en el `workflow` seguirá en funcionamiento, y también sus imágenes descargadas.

![](/solucion/imagenes/weekly_16.png)

esto hay que tenerlo muy en cuenta para borrar todo lo que queramos antes de que finalice el `workflow` o de lo contrario tendremos problemas en siguiente ejecuciones con el nombre del contenedor, las imagenes da un poco más de igual pero aún así también lo limpiaré, esto lo haremos en un `job` que dependa del de `cd` pero que de igual si ha fallado o no así se hará la limpieza necesaria si o si al finalizar el `workflow`

Lo conseguimos con estas líneas

```yaml
    needs: job-cd
    if: always() 
```

con `if: always()` ponemos que da igual si ha fallado el `needs` ya que por defecto si falla no se hace, forcé un error poniendo que exista ya el contenedor de `pruebas` así se ve el error real de por qué es necesaria la limpieza

![](/solucion/imagenes/weekly_18.png)

![](/solucion/imagenes/weekly_17.png)




















