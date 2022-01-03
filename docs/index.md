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

Esta aplicación se desarrolla para la asignatura de *Aplicaciones* del Máster de Visión Artificial de la Universidad Rey Juan Carlos de Madrid. 

Un supuesto cliente, frabricante de láminas metálicas, desea contratar los servicios de una empresa de Visión Artificial para realizar el control de calidad de las láminas. Las láminas que fabrica pueden presentar 6 tipos de defectos o imperfecciones:
- Moho (patches) 
- Arañazos (scratches) 
- Suciedad (inclusion) 
- Resquebrajadura (crazing)
- Corrosión (pitted_surface)
- Escama laminada (rolled-in_scale)

De estos defectos, el cliente quiere que se clasifiquen adecuadamente los tres primeros. Los otros tres, deben clasificarse como un categoría distinta (otra). Además, se debe detectar la posición concreta de los defectos en los dos primeros casos, esto es, moho y arañazos.

El cliente proporciona un conjunto de imágenes de ejemplo, junto con sus anotaciones, accesibles en el siguiente [enlace](https://www.kaggle.com/kaustubhdikshit/neu-surface-defect-database).

La solución debe poder integrarse fácilmente en una aplicación que utiliza el cliente desarrollada en Java por los programadores del cliente, con la que gestiona el proceso productivo y con la que realiza la captura de las imágenes.

## Diseño de la aplicación

La clasificación se realiza con una red neuronal convolucional, mientras que la detección de las clases moho y arazaño se realiza con técnicas clásicas de visión artificial. 

Para la red neuronal, a su vez, se ha implementado código, tanto para la fase de entrenamiento, como para la fase de clasificación. En la fase de entrenamiento se crea un modelo de red, que será utilizado por la fase de clasificación. NOTA: El código relativo al entrenamiento no será aportado al cliente.

Si el software clasifica el defecto como moho, se manda la imagen al código que detecta la presencia de moho. Si, por su parte, clasifica el defecto como arañazo, se manda la imagen al detector de arañazos. En el resto de los casos no es necesario el proceso de detección.

Además, se ha implementado un servidor HTTP capaz de recibir una imagen y develver el resultado de la clasificación, y en su caso, detección. 

Adicionalmente, se ha creado una librería en Java que actúa como cliente, de tal manera, que pueda ser integrada fácilmente en la aplicación principal del cliente que está desarrollada en este lenguaje. El cliente recibe un imagen y lanza una petición al servidor. Éste contesta con el resultado de la clasificación-detección. Junto con la librería, se ha creado en Java un pequeño programa de demostración para poder ser presentado al cliente.

Por último, para facilitar el despliegue del software Python en el cliente, se ha empaquetado la solución en un Docker.


## Resultados

La clasificación tiene una tasa de acierto aproximada del 95% sobre un conjunto de test elegido al azar. En cuanto a la detección en los casos de Mohos y Arañazos, se ha podido medir una IoU aproximada del 58%. Estos resultados están en consonancia con las especificaciones del cliente.

## Utilización

La aplicación puede ser utilizada de dos formas distintas:
- Con docker 
- Sin docker

En los siguientes apartados se ofrece más información.

### Utilización con docker


### Utilización sin docker (para linux)

Si se quiere utilizar la aplicación sin el uso de docker, hay que proceder de la siguiente forma:
- Clonación del proyecto: `git clone https://github.com/juanluiscarrillo/CV-Metal-Defects-Detector.git`
- Acceso a la carpeta del proyecto: `cd CV-Metal-Defects-Detector/`
- Creación de un entorno *venv*: `python3 -m venv ./metaldefects`
- Activación del entorno: `source ./metaldefects/bin/activate`
- Instalación de dependencias: `pip3 install -r requirements.txt` 
- Creación de las muestras de entrenamiento y de test: `python MetalImperfectionsUtil.py`
- Entrenamiento de la red neuronal: `python MetalDefectsTrainer.py`
- Creación de la carpeta *CNN_UTIL*: `mkdir CNN_UTIL`
- Copia del mejor modelo (el más reciente) a la carpeta *CNN_UTIL*: `cp ./models/weights_improvement.*.h5 ./CNN_UTIL/` **NOTA:** Sustituir el * por el valor que corresponda
- Cambio de nombre del modelo de la carpeta *CNN_UTIL* a *weights_improvement.h5*: `mv ./models/weights_improvement.*.h5 ./models/weights_improvement.h5`
- Lanzamiento del servidor: `python server.py 2> log.txt &` **NOTA:** En el fichero *log.txt* se guardan los fallos del servidor
- Compilación del código Java: 
    ```
    javac -d ./classes -cp ./classes java-src/detect/*.java
    javac -d ./classes -cp ./classes java-src/*.java
    ```
- Creación del fichero *metaldetector.jar*:
    ```
    cp -r ./META-INF/ ./classes/
    cd ./classes/
    jar cmf META-INF/MANIFEST.MF metaldetector.jar *.class detect
    mv metaldetector.jar ../
    cd ..
    ```
- Ejecución de la aplicación de demostración en Java: 

