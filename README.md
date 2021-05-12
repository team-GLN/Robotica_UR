# Proyecto Final del curso de Robotica Industrial Aplicada
## Universal Robots
*Master Universitario en Digital Manufacturing*

Desarrollado por:
* Nekane Berasategui
* Goiatz Ezpeleta
* Laura Zambrano

La práctica desarrollada a continuación, se ha llevado a cabo mediante el simulador “SW 5.9 OFFLINE SIMULATOR - E-SERIES - UR SIM FOR NON LINUX 5.9.4” de Universal Robot. Además ha sido necesaria la instalación de VirtualBox 6.1 y el Extension Pack. Dentro del simulador se encuentran diferentes modelos de robots, el número que acompaña al nombre del modelo indica la carga máxima qeu puede soportar. En este caso se va a emplear el modelo UR5, este modelo puede soportar una carga máxima de 5kg, se ha elegido este modelo ya que es el que se encuentra en el laboratorio. 

Universal Robot es un fabricante referente de robots colaborativos. Asimismo, desde el punto de vista de programación, ofrece la posibilidad de programar tanto en scripts como mediante un asistente de programación sencilla. Si bien es cierto que la práctica se ha tenido que desarrollar mediante scripts, también se ha trabajado la segunda opción.



### Programación mediante script del robot UR5 
Antes de comenzar con lal explicación de los ejercicios propuestos, se va a hacer una pequeña explicación de la estructura del script.

Todos los scripts se han escrito con la misma estructura. 
En primer lugar se encuentra la definición del setup del robot. Aunque el setup se pueda definir desde el programa mismo, es aconsejable definir el setup del robot dentro del script, de este modo al ejecutar el programa tendrá prioridad el setup del script, evitando así errores por modificaciones no deseadas del setup desde el programa.


### Ejercicio 1
El setup de la célula es el siguiente:
• El robot está colocado sobre una mesa de trabajo con la base del robot apoyada en la mesa.
• Se ha añadido un gripper de 1.5kg y su TCP está en la posición XYZ [0, 0, 150] mm y RxRyRz [0, 0, 90] grados.

Por lo que se realiza el siguiente script para configurar el robot, donde en se define la gravedad y las caracteristicas de la herramienta tanto su posición (en metros y pi radianes) como su peso (en Kg). Tambien ha definido la variable pi, que se utilizara a lo largo del codigo.

```py
pi = 3.14159

#Setup robot
set_gravity([0, 0, 9.82])
set_tcp(p[0, 0, 0.15, 0, 0, pi/2])
set_payload(1.5)
```

Despues se declara la posición segura de inicio y final de programa, la cual en espacio de joints es [0º, -45º, -90º, -135º, 90º, 0º]. En el código se expresa su equivalente en pi radianes.
```py
#Declarar posicion segura
SafePoint=[0, -pi/4, -pi/2, -3*pi/4, pi/2, 0]
```
En el programa queremos que:
1. El robot vaya a la posición XYZ [500, 0, 250] RxRyRz [0, 180, 0], baje 250mm hasta quedarse en Z=0, espere 1 segundo y vuelva a subir a la posición previa.
2. El robot vaya a la posición XYZ [500, 250, 250] RxRyRz [0, 180, 0], baje 250mm hasta quedarse en Z=0, espere 1 segundo y vuelva a subir a la posición previa.
3. El robot vuelva a la posición segura.

Por lo cual se definen las siguientes variables que hacen referencia a esas posiciones escritas en espacio cartesiano. 
```py
#Declaracion de posiciones globales
posPrePick = p[0.500, 0.000, 0.250, 0, pi, 0]
posPick = p[0.500, 0.000, 0.000, 0, pi, 0]
posPrePlace = p[0.500, 0.250, 0.250, 0, pi, 0]
posPlace = p[0.500, 0.250, 0.000, 0, pi, 0]
```

De esta forma simularemos una aplicación sencilla de pick&place donde el robot coge una pieza en una posición conocida y la deja en el punto deseado. Durante la ejecución del programa queremos que el programa registre en el LOG del robot cuándo alcanza las poses de pick y place, además de utilizar una ventana popup que muestre cuando comienza y termina el programa

