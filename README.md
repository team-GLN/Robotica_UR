# Proyecto Final del curso de Robotica Industrial Aplicada
## Universal Robots
*Master Universitario en Digital Manufacturing*

Desarrollado por:
* Nekane Berasategui
* Goiatz Ezpeleta
* Laura Zambrano

### Contenido
- [Introducción](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#introducci%C3%B3n)
- [Programación mediante script del robot UR5](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#programaci%C3%B3n-mediante-script-del-robot-ur5)
  - [Ejercicio 1](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-1)
  - [Ejercicio 2](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-2)
  - [Ejercicio 3](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-3)
  - [Ejercicio 4](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-4)
- [Programación mediante easy programming del robot UR5](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#Programación-mediante-easy-programming-del-robot-UR5)

### Introducción
La práctica desarrollada a continuación, se ha llevado a cabo mediante el simulador “SW 5.9 OFFLINE SIMULATOR - E-SERIES - UR SIM FOR NON LINUX 5.9.4” de Universal Robot. Además ha sido necesaria la instalación de VirtualBox 6.1 y el Extension Pack. Dentro del simulador se encuentran diferentes modelos de robots, el número que acompaña al nombre del modelo indica la carga máxima qeu puede soportar. En este caso se va a emplear el modelo UR5, este modelo puede soportar una carga máxima de 5kg, se ha elegido este modelo ya que es el que se encuentra en el laboratorio. 

Universal Robot es un fabricante referente de robots colaborativos. Asimismo, desde el punto de vista de programación, ofrece la posibilidad de programar tanto en scripts como mediante un asistente de programación sencilla. Si bien es cierto que la práctica se ha tenido que desarrollar mediante scripts, también se ha trabajado la segunda opción.



### Programación mediante script del robot UR5 
Antes de comenzar con la explicación de los ejercicios propuestos, se va a hacer una pequeña explicación de la estructura del script. Se acalara que todos los scripts se han escrito con la misma estructura. 

En primer lugar se encuentra la definición del setup del robot. Aunque el setup se pueda definir desde el programa mismo, es aconsejable definir el setup del robot dentro del script, de este modo al ejecutar el programa tendrá prioridad el setup del script, evitando así errores por modificaciones no deseadas del setup desde el programa.

Despues se realiza una declaración de variables, donde se definen las posiciones y parámetros a utilizar durante la ejecución del programa, con el fin de facilitar la sintaxis del código en caso de que se requiera realizar una modificación de un valor.

Seguido a ello, se muestran los subprocesos, métodos o funciones (si son necesarias) que se utilicen en el código. Y por último el principal, donde se invocan los metodos y realizan el resto de acciones.


### Ejercicio 1
El setup de la célula es el siguiente:
* El robot está colocado sobre una mesa de trabajo con la base del robot apoyada en la mesa.
* Se ha añadido un gripper de 1.5kg y su TCP está en la posición XYZ [0, 0, 150] mm y RxRyRz [0, 0, 90] grados.

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

Primero, se utiliza una ventana popup para mostrar cuando se comienza el programa. Y el robot se mueve en espacio joints a la posición segura para iniciar el programa.
```py
#popup inicio
popup("Welcome. Ready?", title="Start", warning=False, error=False, blocking=True)
movej(SafePoint, 1, 1) #iniciar en Posicion segura
```

En el programa se quiere simular una aplicación sencilla de pick&place donde el robot coge una pieza en una posición conocida y la deja en el punto deseado. Siguiendo la siguiente secuencia de pasos:
1. El robot vaya a la posición XYZ [500, 0, 250] RxRyRz [0, 180, 0], baje 250mm hasta quedarse en Z=0, espere 1 segundo y vuelva a subir a la posición previa.
2. El robot vaya a la posición XYZ [500, 250, 250] RxRyRz [0, 180, 0], baje 250mm hasta quedarse en Z=0, espere 1 segundo y vuelva a subir a la posición previa.
3. El robot vuelva a la posición segura.

Por lo cual se definen las siguientes variables que hacen referencia a esas posiciones descritas anteriormente en espacio cartesiano. 
```py
#Declaracion de posiciones globales
posPrePick = p[0.500, 0.000, 0.250, 0, pi, 0] #herramienta arriba en el punto de pick
posPick = p[0.500, 0.000, 0.000, 0, pi, 0] #herramienta abajo en el punto de pick
posPrePlace = p[0.500, 0.250, 0.250, 0, pi, 0]#herramienta arriba en el punto de place
posPlace = p[0.500, 0.250, 0.000, 0, pi, 0]#herramienta abajo en el punto de place
```
Se realiza el primer paso donde se recoge la "pieza" (pick). El robot se desplaza con un ```movej``` a la posición previa al pick. Después baja en línea recta con un ```movel``` a la posición de pick y espera un segundo para "recoger la pieza". Sube nuevamente a la posición arriba del punto de pick y muestra un mensaje por consola LOG que ha realizado el pick. La velocidad y aceleración en todos los movimientos será de 1 m/s y m/s2 respectivamente al ser el valor por defecto para estas aplicaciones.
```py
#Accion 1, Pick
movej(posPrePick, 1, 1) #velocidad y aceleracion = 1
movel(posPick, 1, 1)
sleep(1)
movel(posPrePick, 1, 1)
textmsg("Pick")
```
Posteriormente se realiza el segundo paso donde se coloca la "pieza" (place). El robot se desplaza con un ```movej```a la posición previa al place. Después baja en línea recta con un ```movel``` a la posición de place, donde suelta  la pieza y espera un segundo. Sube nuevamente a la posición arriba del punto de place y muestra un mensaje por consola LOG que ha realizado el place.
```py
#Accion 2, place
movej(posPrePlace, 1, 1)
movel(posPlace, 1, 1)
sleep(1)
movel(posPrePlace, 1, 1)
textmsg("Place")
```
Por ultimo, el robot regresa con un ```movej```a la posición segura y se muestra una ventana popup para dar por finalizado el programa. En caso que se desee volver a ejecutar se selecciona que si desea continuar para repetir el código presentado. 
```py
#Finalizar posicion segura
movej(SafePoint, 1, 1)
popup("Su codigo ha terminado. Desea continuar?", title="Finish", blocking=True)
```
### Ejercicio 2
Para este segundo ejercicio se implementa un programa para el desbarbado de unas rejillas, en el cual el robot se desplaza a las intersecciones de la rejilla para realizar el desbarbado, segun el número de filas, columnas y distancia entre filas/columna definidas por el cliente. 

El setup de la célula es el siguiente:
- El robot está colocado sobre una mesa de trabajo con la base del robot apoyada en la mesa.
- Se ha añadido una herramienta de desbarbado de 2.0kg y su TCP está en la posición XYZ [0, 25, 175] mm y RxRyRz [0, 0, 90] grados. 
- La posición segura de inicio y final de programa, en espacio de joints, es [0º, -45º, -90º, -135º, 90º, 0º].

Por lo que se realiza el siguiente script para configurar el robot, donde en se define la gravedad y las caracteristicas de la herramienta tanto su posición (en metros y pi radianes) como su peso (en Kg). Tambien se declara la posición segura descrita anteriormente.

```py
#Setup robot
set_gravity([0, 0, 9.82])
set_tcp(p[0, 0.025, 0.175, 0, 0, 1.5708])
set_payload(2.0) #peso 2kg
    
#Declarar posicion segura
SafePoint = [0, -0.785398, -1.5708, 2.35619, 1.5708, 0
```
De acuerdo con los siguientes requisitos se tienen diferentes tipos de rejillas por lo que el número de filas, columnas y distancia entre filas/columnas es configurable y se define en el siguiente script. 
```py
#Declarar parametros de la rejilla
filas = 2
columnas = 3
distancia = 0.01
```
En el programa se quiere que el robot pase por todos los puntos de intersección a partir de los parámetros decididos(filas, columnas, distancia). Antes del desbarbado de cada una de las intersecciones, el robot deberá ir a una posición 50mm por encima de la pose de desbarbado y bajar de forma lineal para asegurar que el robot entra de forma perpendicular. Una vez llegado a la posición el robot realizará el desbarbado durante 2.5 segundos. Dichos parametros son definidos en el siguiente script.

```py    
#Parametros desbarbado
h_desbarbado = 0.05
t_desbarbado = 2.5
```
Ademas, al ser un proceso ciclico se ha definido un subproceso donde se realice el desbarbado, el cual esta integrado por dos ciclos (uno para filas y otro colummnas).
El robot volverá a la posición segura al finalizar el proceso. Durante la ejecución del programa queremos que el programa registre en el LOG del robot cuándo alcanza las poses de desbarbado, además de utilizar una ventana popup que muestre cuando comienza y termina el programa

- La posición de la primera intersección siempre será la misma XYZ [500, -150, 0] RxRyRz [0, 180, 0]
- .

```py
#funcion desbarbado
def desbarbado(F, C, D, H, T):
pi = 3.14159  
i = 0
   
    while i < F:
        j = 0

        while j < C:
           puntoArriba = p[0.500+i*D, -0.150+j*D, H, 0, pi, 0]
           movej(puntoArriba, 1, 1)
           puntoAbajo = p[0.500+i*D, -0.150+j*D, 0, 0, pi, 0]
           movel(puntoAbajo, 1, 1)
           textmsg("Start desbarbado, fila ", i+1)
           textmsg("columna ", j+1)
           set_digital_out(0, True)
           sleep(T)
           set_digital_out(0, False)
           textmsg("Stop desbarbado")
           movel(puntoArriba, 1, 1)
           j = j + 1
        end

        i = i + 1

        if(i< F):
           j = 0
           pose = p[0.500+i*D, -0.150+j*D, H, 0, pi, 0]
           movej(pose, 1, 1)
        end
    end        
end
```

### Ejercicio 3

### Ejercicio 4


