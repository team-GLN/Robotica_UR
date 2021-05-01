# Proyecto Final del curso de Robotica Industrial Aplicada
## Universal Robots
*Master Universitario en Digital Manufacturing*

Desarrollado por:
* Nekane Berasategui
* Goiatz Ezpeleta
* Laura Zambrano

La práctica desarrollada a continuación, se ha llevado a cabo mediante el simulador “SW 5.9 OFFLINE SIMULATOR - E-SERIES - UR SIM FOR NON LINUX 5.9.4” de Universal Robot. Además ha sido necesaria la instalación de VirtualBox 6.1 y el Extension Pack. Dentro del simulador se encuentran diferentes modelos de robots, el número que acompaña al nombre del modelo indica la carga máxima qeu puede soportar. En este caso se va a emplear el modelo UR5, este modelo puede soportar una carga máxima de 5kg, se ha elegido este modelo ya que es el que se encuentra en el laboratorio. 

Universal Robot es un fabricante referente de robots colaborativos. Asimismo, desde el punto de vista de programación, ofrece la posibilidad de programar tanto en scripts como mediante un asistente de programación sencilla. Si bien es cierto que la práctica se ha tenido que desarrollar mediante scripts, también se ha trabajado la segunda opción.



### Programación mediante script del robot UR 
Antes de comenzar con lal explicación de los ejercicios propuestos, se va a hacer una pequeña explicación de la estructura del script.

Todos los scripts se han escrito con la misma estructura. 
En primer lugar se encuentra la definición del setup del robot. Aunque el setup se pueda definir desde el programa mismo, es aconsejable definir el setup del robot dentro del script, de este modo al ejecutar el programa tendrá prioridad el setup del script, evitando así errores por modificaciones no deseadas del setup desde el programa.
