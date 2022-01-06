# Visión Artificial: Metal defects detector 

Aplicación de visión artificial para la detección de defectos en superficies de metales.

El trabajo combina varias tecnologías:
- Clasificación de los distintos tipos de defectos presentes en las imágenes, realizado con una red neuronal convolucional, implementada en Python con Tensorflow.
- Detección de defectos (lugares en la imagen donde apararecen las imperfecciones), implementada en Python con OpenCv, mediante técnicas clásicas de Visión Artificial.
- Servidor HTTP implementado en Python para recibir peticiones de detección sobre imágenes.
- Test de integración continua para el código Python realizados con la librería *unittest*.
- Librería Java para integrar la solución en una aplicación de cliente realizada en este lenguaje.
- Creación de un Docker para el despliegue de la aplicación.

A continuación, se desarrolla la explicación del trabajo

## Introducción

Esta aplicación se desarrolla para la asignatura de *Aplicaciones Industriales y Comerciales* del Máster de Visión Artificial de la Universidad Rey Juan Carlos de Madrid. 

Un supuesto cliente, frabricante de láminas metálicas, desea contratar los servicios de una empresa de Visión Artificial para realizar el control de calidad de las láminas. Las láminas que fabrica pueden presentar 6 tipos de defectos o imperfecciones:
- Moho (patches) 
- Arañazos (scratches) 
- Suciedad (inclusion) 
- Resquebrajadura (crazing)
- Corrosión (pitted_surface)
- Escama laminada (rolled-in_scale)

De estos defectos, el cliente quiere que se clasifiquen adecuadamente los tres primeros. Los otros tres, deben clasificarse como una categoría distinta (other). Además, se debe detectar la posición concreta de los defectos en los dos primeros casos, esto es, moho y arañazos.

El cliente proporciona un conjunto de imágenes de ejemplo, junto con sus anotaciones, accesibles en el siguiente [enlace](https://www.kaggle.com/kaustubhdikshit/neu-surface-defect-database). Para mayor comodidad a la hora de utilizar este software, se incluyen las imágenes en el GitHub.

La solución debe poder integrarse fácilmente en una aplicación que utiliza el cliente, desarrollada en Java por los programadores de su empresa, con la que se gestiona el proceso productivo y con la que se realiza la captura de las imágenes.

## Diseño de la aplicación

La clasificación se realiza con una red neuronal convolucional, mientras que la detección en los defectos moho y arazaño se realiza con técnicas clásicas de visión artificial. 

Para la red neuronal, a su vez, se ha implementado código, tanto para la fase de entrenamiento, como para la fase de clasificación. En la fase de entrenamiento se crea un modelo de red, que será utilizado por la fase de clasificación. La aplicación almacena el mejor modelo (los mejores pesos) que la red va encontrando en la carpeta *models*. NOTA: El mejor será el último que se ha guardado. Además, crea dos ficheros *.png* con las gráficas de precisión y pérdida del entrenamiento.

Si el software clasifica el defecto como moho, se manda la imagen al código que detecta la presencia de moho. Si, por su parte, clasifica el defecto como arañazo, se manda la imagen al detector de arañazos, que hace lo propio. En el resto de los casos no es necesario el proceso de detección.

Además, se ha implementado un servidor HTTP capaz de recibir una imagen y develver el resultado de la clasificación, y en su caso, detección. 

Adicionalmente, se ha creado una librería en Java que actúa como cliente del servidor HTTP, mandando una imagen y recibiendo el resultado de la clasificación y, en su caso, detección. De esta manera, la aplicación puede ser integrada fácilmente en la aplicación principal de la empresa metalúrgica, que está desarrollada en este lenguaje. Junto con la librería, se ha creado en Java un pequeño programa de demostración para poder ser utilizado como demostración a la empresa contratante.

Por último, para facilitar el despliegue del software Python en el cliente, se ha empaquetado la solución en un Docker.


## Resultados

La clasificación tiene una tasa de acierto aproximada del 95% sobre un conjunto de test elegido al azar. En cuanto a la detección en los casos de Mohos y Arañazos, se ha podido medir una IoU aproximada del 58%. Estos resultados están en consonancia con las especificaciones del cliente.

## Utilización

La explicación se hará pensando que se está utilizando linux. No obstante, para otras sistemas operativos los pasos serán similares. Además, se presupone que se tienen correctamente instalados: *git*, *python3*, *pip*, *Java-jdk* y *docker*.

La aplicación puede ser utilizada de tres formas distintas:
- Con descarga del docker desde DorcerHub
- Creando el docker 
- Sin docker

En los siguientes apartados se ofrece más información.

### Utilización con descarga del docker desde DorcerHub

Este es el procedimiento que requiere menos pasos:

1. Si no se tiene habilitada la conexión del docker con la pantalla de la máquina, es necesario ejecutar este comando: `xhost +"local:docker@"`
2. Ejecución del docker: `sudo docker run --rm -it -p 9000:9000 -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix juanluiscarrillo/metaldefects`. Si el docker no se encuentra en el sistema, se descargará del repositorio docker.
3. Ejecución del servidor en segundo plano: `python server.py 2> log.txt &`
4. Ejecución de la aplicación de demostración: `java -jar metaldetector.jar`

También, es posible ejecutar la aplicación de demostración desde un terminal distinto al del docker. Para ello, una vez se ha ejecutado el servidor dentro del contenedor, hay que abrir un terminal seguir los siguientes pasos:
- Clonar el proyecto: `git clone https://github.com/juanluiscarrillo/CV-Metal-Defects-Detector.git`
- Acceder a la carpeta del proyecto: `cd CV-Metal-Defects-Detector/`
- Compilar el código Java: 
    ```
    javac -d ./classes -cp ./classes java-src/detect/*.java
    javac -d ./classes -cp ./classes java-src/*.java
    ```
- Crear el fichero *metaldetector.jar*:
    ```
    cp -r ./META-INF/ ./classes/
    cd ./classes/
    jar cmf META-INF/MANIFEST.MF metaldetector.jar *.class detect
    mv metaldetector.jar ../
    cd ..
    ```
- Ejecutar la aplicación de demostración: `java -jar metaldetector.jar`


### Utilización creando el docker

En el repositorio se guardan los ficheros fuentes y las imágenes, por lo que es necesario realizar una serie de pasos para poner en funcionamiento la aplicación. Los primeros 12 pasos son idénticos al caso de *utilización sin docker*. El resto son propios de este método. A continuación, se detallan todos los pasos:
1. Clonación del proyecto: `git clone https://github.com/juanluiscarrillo/CV-Metal-Defects-Detector.git`
2. Acceso a la carpeta del proyecto: `cd CV-Metal-Defects-Detector/`
3. Creación de un entorno *venv*: `python3 -m venv ./venv`
4. Activación del entorno: `source ./venv/bin/activate`
5. Instalación de dependencias: `pip3 install -r requirements.txt` 
6. Creación de las muestras de entrenamiento y de test: `python MetalDefectsUtil.py`
7. Entrenamiento de la red neuronal: `python MetalDefectsTrainer.py`
8. Creación de la carpeta *CNN_UTIL*: `mkdir CNN_UTIL`
9. Copia del mejor modelo (el más reciente) a la carpeta *CNN_UTIL*: `cp ./models/weights_improvement.*.h5 ./CNN_UTIL/` **NOTA:** Sustituir el * por el valor que corresponda
10. Cambio de nombre del modelo de la carpeta *CNN_UTIL* a *weights_improvement.h5*: `mv ./CNN_UTIL/weights_improvement.*.h5 ./CNN_UTIL/weights_improvement.h5`

11. Compilación del código Java: 
    ```
    javac -d ./classes -cp ./classes java-src/detect/*.java
    javac -d ./classes -cp ./classes java-src/*.java
    ```
12. Creación del fichero *metaldetector.jar*:
    ```
    cp -r ./META-INF/ ./classes/
    cd ./classes/
    jar cmf META-INF/MANIFEST.MF metaldetector.jar *.class detect
    mv metaldetector.jar ../
    cd ..
    ```
13. Creación de la imagen del docker: `sudo docker build -t metaldefects:local .`
14. En algunos entornos es necesario ejecutar el siguiente comando: `xhost +"local:docker@"`
15. Creación de un contenedor: `docker run --rm -it -p 9000:9000 -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix metaldefects:local`
16. Ejecución del servidor en segundo plano: `python server.py 2> log.txt &`
17. Ejecución de la aplicación de demostración: `java -jar metaldetector.jar`

También, es posible ejecutar la aplicación de demostración desde un terminal distinto al del docker. Para ello, una vez se ha ejecutado el servidor dentro del contenedor, hay que abrir un nuevo terminal y situarse en la carpeta *CV-Metal-Defects-Detector/*. Desde ahí, se lanza la aplicación de demostración `java -jar metaldetector.jar`.




### Utilización sin docker

Si se quiere utilizar la aplicación sin el uso de docker, hay que proceder de la siguiente forma:
1. Realizar los 12 primeros pasos indicados en el apartado *Utilización creando el docker*
2. Lanzar el servidor: `python server.py 2> log.txt &` **NOTA:** En el fichero *log.txt* se guardan los fallos del servidor
3. Ejecutar la aplicación de demostración en Java: `java -jar metaldetector.jar`

Además, es posible utilizar otros scripts python para comprobar el desempeño de la solución. A continuación, se indicará cómo hacerlo, pero previamente es necesario copiar el fichero *mi_test.csv* en la carpeta *CNN_UTIL*: `cp mi_test.csv ./CNN_UTIL/`.

Una vez realizado este paso, se detallan las posibilidades del software:
- Pasar manualmente los test de *unittest*: `python MetalDefects_test.py NEU-DET`. 
- Clasificar una a una todas las imágenes: `python Application.py NEU-DET`. Hay que pulsar la letra *q* para salir de la aplicación y cualquier otra letra para avanzar a la siguiente imagen.

**NOTA:** En este caso *NEU-DET* es la ruta donde se encuentran las imágenes, junto con las anotaciones. Si éstas se encuentran en otro sitio, hay que indicar la ruta correspondiente.
