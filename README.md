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
  - [Ejercicio 5](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-5)
- [Programación mediante easy programming del robot UR5](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#Programación-mediante-easy-programming-del-robot-UR5)
  - [Ejercicio 1 Easy Programming](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-1-easy-programming)
  - [Ejercicio 2 Easy Programming](https://github.com/team-GLN/Robotica_UR/blob/UR/README.md#ejercicio-2-easy-programming)

### Introducción
La práctica desarrollada a continuación, se ha llevado a cabo mediante el simulador “SW 5.9 OFFLINE SIMULATOR - E-SERIES - UR SIM FOR NON LINUX 5.9.4” de Universal Robot. Además ha sido necesaria la instalación de VirtualBox 6.1 y el Extension Pack. Dentro del simulador se encuentran diferentes modelos de robots, el número que acompaña al nombre del modelo indica la carga máxima qeu puede soportar. En este caso se va a emplear el modelo UR5, este modelo puede soportar una carga máxima de 5kg, se ha elegido este modelo ya que es el que se encuentra en el laboratorio. 

Universal Robot es un fabricante referente de robots colaborativos. Asimismo, desde el punto de vista de programación, ofrece la posibilidad de programar tanto en scripts como mediante un asistente de programación sencilla. Si bien es cierto que la práctica se ha tenido que desarrollar mediante scripts, también se ha trabajado la segunda opción.



### Programación mediante script del robot UR5 
Antes de comenzar con la explicación de los ejercicios propuestos, se va a hacer una descripción de la estructura del script. Se acalara que todos los scripts se han escrito con la misma estructura. 

En primer lugar se encuentra la definición del setup del robot. Aunque el setup se pueda definir desde el programa mismo, es aconsejable definir el setup del robot dentro del script, de este modo al ejecutar el programa tendrá prioridad el setup del script, evitando así errores por modificaciones no deseadas del setup desde el programa.

Despues se realiza una declaración de variables, donde se definen las posiciones y parámetros a utilizar durante la ejecución del programa, con el fin de facilitar la sintaxis del código en caso de que se requiera realizar una modificación de un valor.

Seguido a ello, se muestran los subprocesos, métodos o funciones (si son necesarias) que se utilicen en el código. Y por último el principal, donde se invocan los metodos y realizan el resto de acciones.


#### Ejercicio 1
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
#### Ejercicio 2
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
En el programa se quiere que el robot pase por todos los puntos de intersección a partir de los parámetros decididos(filas, columnas, distancia). Antes del desbarbado de cada una de las intersecciones, el robot deberá ir a una posición 50mm por encima de la pose de desbarbado y bajar de forma lineal para asegurar que el robot entra de forma perpendicular. Una vez llegado a la posición el robot realizará el desbarbado durante 2.5 segundos. Dichos parametros son definidos en el siguiente script. Se consigue que el robor permanezca en el punto de desbarbado durante el tiempo definido mediante la función predeterminada ```sleep( )```.

```py    
#Parametros desbarbado
h_desbarbado = 0.05
t_desbarbado = 2.5
```
Ademas, al ser un proceso ciclico se ha definido un subproceso donde se realice el desbarbado, el cual esta integrado por dos ciclos (uno para filas y otro colummnas).
El robot volverá a la posición segura al finalizar el proceso. Durante la ejecución del programa queremos que el programa registre en el LOG del robot cuándo alcanza las poses de desbarbado, además de utilizar una ventana popup que muestre cuando comienza y termina el programa

- La posición de la primera intersección siempre será la misma XYZ [500, -150, 0] RxRyRz [0, 180, 0]
- Cuando un punto de la rejilla se ha mecanizado pasa al siguiente, desplazandose la distancia que se ha definido en las variables del principio, tiene que recorrer todos los puntos de una fila para que pueda saltar al primer punto de la siguiente fila.

Para este ejercicio se ha tomado como base el ejercicio anterior, agregando dos ciclos para recorrer las filas y columnas mientras se llegando a los puntos de arriba y abajo para el desbarbado.

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
Cuando se han mecanizado todos los puntos de la rejilla, es decir, los ciclos definidos han llegado al limite establecido por las variables ```filas```y ```columnas```, el robot vuelve a la posición de seguridad y finaliza el programa. 

#### Ejercicio 3
Este ejercicio es muy parecido al Ejercicio 2, con la diferencia de que en este caso contamos con una herramienta de doble cabezal en el extremo del robot, uno para el desbarbado y otro para el pulido, cada cabezal tiene su TCP. Partiendo del script del Ejercicio 2 y haciendo unas modificaciones se puede programar facilmete el robot para este caso.

El setup de la célula es el siguiente:
- El robot está colocado sobre una mesa de trabajo con la base del robot apoyada en la mesa.
- Se ha añadido una herramienta de 3.0kg con un cabezal de desbarbado con TCP XYZ [50, 25, 175] mm y RxRyRz [0, 0, 90] el cual se activa con la salida digital 0, y un cabezal de pulido con TCP XYZ [-50, 25, 175] mm y RxRyRz [0, 0, 90] el cual se activa con la salida digital 1. 
- La posición segura de inicio y final de programa, en espacio de joints, es [0º, -45º, -90º, -135º, 90º, 0º].
```py
#Setup robot
set_gravity([0, 0, 9.82])
set_payload(3.0) #peso 3kg
    
#Declarar posicion segura
SafePoint=[0,-pi/4,-pi/2,-3*pi/4,pi/2,0]
```

Los parametros de la rejilla no varían. Lo que sí es diferente respecto al anterior ejercicio son los parametros del mecanizado ya que en este caso hay que añadir también los parametros correspondientes al proceso de pulido que dura 1 segundo.
```py
#Parametros desbarbado
h_desbarbado = 0.05
t_desbarbado = 2.5
t_pulido = 1
```

En el programa se quiere que el robot pase por todos los puntos de intersección a partir de los parámetros decididos(filas, columnas, distancia). Antes del mecanizado de cada una de las intersecciones, el robot deberá ir a una posición 50mm por encima de la pose de la rejilla y bajar de forma lineal para asegurar que el robot entra de forma perpendicular, este movimiento lo tendrá que repetir dos veces, una para el desbarbado y otra para el pulido. Una vez llegado a la posición el robot realizará el desbarbado durante 2.5 segundos, subira a la posición previa y volverá a bajar para hacer el pulido durante 1 segundo. Antes de que el robot baje al punto de la rejilla para realizar cada acción hay que llamar al TCP correspondiente de cada cabezal.

Es un proceso cíclico que está compuesto por un ciclo dentro del otro, el más exterior permite desplazarse al robot por las diferentes filas de la rejilla y el ciclo interior hace que el robot se mueva por los punto de una misma fila.
```py
def desbarbado(F, C, D, H, T_d, T_p):
    i = 0
    while i < F:
        j = 0
        while j < C:
           puntoArriba = p[0.500+i*D, -0.150+j*D, H, 0, pi, 0]
           movej(puntoArriba, 1, 1)
           #desbarbado
           set_tcp(tcp_des)
           puntoAbajo = p[0.500+i*D, -0.150+j*D, 0, 0, pi, 0]
           movel(puntoAbajo, 1, 1)
           textmsg("Start desbarbado, fila ", i+1)
           textmsg("columna ", j+1)
           set_digital_out(0, True)
           sleep(T_d)
           set_digital_out(0, False)
           textmsg("Stop desbarbado")
           movel(puntoArriba, 1, 1)
           #pulido
           set_tcp(tcp_pul)
           puntoAbajo = p[0.500+i*D, -0.150+j*D, 0, 0, pi, 0]
           movel(puntoAbajo, 1, 1)
           textmsg("Start pulido, fila ", i+1)
           textmsg("columna ", j+1)
           set_digital_out(1, True)
           sleep(T_p)
           set_digital_out(1, False)
           textmsg("Stop pulido")
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

#### Ejercicio 4
El ejercicio 4 parte el Ejercicio 3, en este caso, por limitaciones en el espacio, el robot está colgado en el techo y como medida de seguridad no se pondrá en marcha hasta que reciba la señal de la entrada digital.
Para situar el robot en el techo, en la sección *Installation* del simulador se especifica el modo de montaje colgado del techo.

El setup de la célula es el siguiente:
- El robot está colgado del techo. Esto hace que la gravedad que afecta al robot se considere negativa en Z.
- Se ha añadido una herramienta de 3.0kg con un cabezal de desbarbado con TCP XYZ [50, 25, 175] mm y RxRyRz [0, 0, 90] el cual se activa con la salida digital 0, y un cabezal de pulido con TCP XYZ [-50, 25, 175] mm y RxRyRz [0, 0, 90] el cual se activa con la salida digital 1. 
- La posición segura de inicio y final de programa, en espacio de joints, es  [0º, -22.5º, -112.5º, 45º, 90º, 0º].
 ```py
#Setup robot
set_gravity([0, 0, -9.82])
set_payload(3)
    
#Posicion segura
SafePoint=[0, -pi/8, -5*pi/8, pi/4, pi/2, 0]
```
Para que el robot empiece con el desbarbado tiene que recivir la señal de la entrada digital 0. Esto se consigue añadiendo un nodo tipo *WAIT* desde la lista de nodos antes del nodo *SCRIPT*. Dentro del nodo Wait se elige la opción de "Wait for digital input" seleccionando la entrada digital 0 de la lista y la señal "High". 
Para poder activar la entrada digital hay que trabajar en modo *Simulation* en la barra inferior del software. Cunado este modo está activado el entorno negro se vuelve azul y se habilita la selección de las entradas digitles en la pestaña *I/O*.

Dentro del script, la escritura de la función de desbarbado es igual que en el ejercicio 3 con la única diferencia de que como el origen del robot está a una altura diferente, la altura en la que se situa el punto que hay que mecanizar es diferente respecto al otro caso. De este modo el punto previo al desbarbado se define como ```puntoArriba=p[-0.25+i*D, -0.15+j*D, H-0.05, 0, 0, 0]``` y la posición donde se encuentra el punto a mecanizar se define como ```puntoAbajo=p[-0.25+i*D, -0.15+j*D, H, 0, 0, 0]``` siendo ```j``` e ```ì``` variables que hacen que el ciclo no se ejecute de forma infinita, ```D``` la distancia entre los puntos de la rejilla y ```H``` la  distancia en el eje Z desde la base del robot hata la rejilla, en este caso 1000mm.

#### Ejercicio 5
Este ejercicio, que se basa en el ejercicio 2, trata de detectar los puntos que el brazo no puede alcanzar. Los puntos inalcanzables tienen que quedar registrados en los LOG del robot. Estos puntos inalcanzables pueden aparecer cuando la distancia entre los puntos es muy grande o cuando, teniendo una distncia relativamente pequeña, tiene muchos puntos que mecanizar.

El setup y el punto de seguridad se mantienen respecto al Ejercicio 2, también el punto de inicio del desbarbado y los parametros de desbarbado.

Antes de empezar con el desbarbado hay que hacer un chequeo del alcance de los puntos empleando la función ```is_within_safety_limits( )``` dentro de un ciclo que lo va comprobando punto por punto. Se define la variable ```error``` que irá aumentando su valor por cada punto inalcanzble que detecte, estos puntos se guardan en los LOG.

```py
def desbarbado(F,C,d,h,t):
  	#Comprobacion de los puntos conflictivos, por cada punto conflictivo que calcule el contador error aumentara
   	i=0
  	error=0
   	while i<F:
   		j=0
   		while j<C:
   			puntoArriba=p[0.5+i*d, -0.15+j*d, h, 0, pi, 0]
   			if is_within_safety_limits(puntoArriba)==False:
   				textmsg("punto no alcanzable ",  puntoArriba)
  				error=error+1
  			end
       		j=j+1
   		end
       	i=i+1
   	end
	#si no hay puntos conflictivos el programa de desbarbado se ejecutara
	if error!=0:
		popup("Hay puntos conflictivos, no se puede seguir con el programa", warning=True, error=False, blocking=True)
	else:
		i=0
		while i<F:
			j=0
			while j<C:
				puntoArriba=p[0.5+i*d, -0.15+j*d, h, 0, pi, 0]
				movej(puntoArriba,1,1)
				puntoAbajo=p[0.5+i*d, -0.15+j*d, 0, 0, pi, 0]
				movel(puntoAbajo,1,1)
				textmsg(puntoAbajo) #Guarda un LOG por cada punto que hace
				set_digital_out(0,True)
				sleep(t)
				set_digital_out(0,False)
				movel(puntoArriba,1,1)
				j=j+1
			end
			i=i+1
		end
	end
end
```

Si en la comprobación de los puntos se encuentra con al menos un punto conflictivo, aparecerá un mensaje de advertencia y el robot no se pondrá en movimiento. En caso contrario, si los puntos de la rejilla están a una distancia alcanzable por el robot, el robot se pondrá en marcha y y guardara un LOG por cada punto en el que haga el desbarbado.

### Programación mediante easy programming del robot UR5
UR robots contiene un modo de programación fácil que añadiendo nodos al arbol de programación se consigue programar el robot. Estos nodos tienen diferentes funcionalidades y complementandose entre ellos se puede conseguir el mismo nivel de programación que escribiendo un script.

En esta forma de programar es menos probable cometer errores ya que, si alguno de los nodos está mal definido se colorea de amarillo el parámetro que hay que definir. Además, al establecer el valor para una variable, el recuadro donde se especifica el valor se colorea si el formato no es válido con el tipo de variable que calcula. 

![](/img/UR_EP_1.PNG)

Una utilidad interesante del easy programing es que durante la ejecución del programa, en la vista *Variables* se pueden observar los valores que obtienen las variables en todo momento, interesante en el caso de las variables dinámicas.

![](/img/EASY_VARIABLES.png)

A diferencia de la programación mediante script, en la programación con *easy programming* hay que definir la instalación antes de ejecutar el programa. En la definición de la instalación se definen tanto la posición del montaje del robot como el TCP de las herramientas que se van a emplear.

Todos los ejercicios tienen la misma esteuctura: Al inicio del programa se recogen las variables fijas en una carpeta, para definir las variables se ha empleado el nodo *Assignment*. En segundo lugar se establece un mensaje que indica que se va a dar comienzo al programa. Después está el cuerpo del programa. El cuerpo del programa está compuesto por los movimientos, bucles y condiciones que se tienen que cumplir. Los nodos correspondientes a una misma acción, como un ciclo para moverse por varios puntos, están recopilados en una carpeta para poder ser identificado y replicado con mayor facilidad. Por último, cuando el robot haya realizado todo el programa, un mensaje emerge para dar fin a la ejecución.

A continuación se mostrarán los árboles de programación que se han creado para cada ejercicio y expresando las partes más remarcables en cada uno de ellos.

#### Ejercicio 1 Easy Programming
Es un ejercicio típico de pick and place, en el que el brazo robótico coge un objeto de un punto y lo deposita en otro.

Para este ejercicio se han definido una variable por cada punto del pick anda place: ```pick_arriba=p[0.5, 0, 0.25, 0, pi, 0]```,  ```pick_abajo=p[0.5, 0, 0, 0 , pi, 0]```, ```place_arriba=p[0.5, 0.25, 0.25, 0, pi, 0]```, ```place_abajo=p[0.5, 0.25, 0, 0, pi, 0]```. También se ha creado una variable que recoje la posición segura del robot ```safe_point=p[0.5, 0.25, 0.25, 0, pi, 0]```.

![](/img/EASY_1.1.png)
![](/img/EASY_1.2.png)

El robot comienza a moverse desde la posición segura y se desplaza a la posición previa del pick mediante el nodo de movimiento ```movej```, después baja con el nodo de movimiento lineal ```movel``` hasta el punto de pick, espera durante un segundo con el nodo ```wait``` y vuelve a subir con un movimiento lineal. Después se mueve hacia las coordenadas del place y repite el mismo movimiento de bajar-subir en las coordinadas de place.

#### Ejercicio 2 Easy Programming
Es un ejercicio en el que el robot recorre los diferentes puntos de una rejilla y ejecuta un desbarbado en cada uno de ellos. Los parámetros de la rejilla están definidos al principio del programa dentro de la carpeta de variables.

Un mensaje advierte del inicio del programa y el robot se mueve a la posición segura. Una vez aqui se empieza a desplazar al primer punto de la rejilla y por medio de un ciclo limitado se ejecuta el desbarbado por todos los puntos de la rejilla. Por último, el robot vuelve a la posición segura y un mensaje informa del final del programa.

![](/img/EASY_2.1.png)

Los nodos correspondientes al desbarbado se recojen en una carpeta. El desplazamiento por los diferentes pontos de la rejilla se consigue mediante un ciclo dentro de otro. El ciclo exterios se ejecuta mientras que la variable ```i``` tenga un valor menor a las filas establecida, y el ciclo interior se ejecutará  mientras que la variable ```j```tenga un valor menor a las columnas establecidas, reiniciandose cada vez que pase a una nueva fila. Dentro del ciclo interior se definen las variables ```arriba``` y ```abajo``` que hacen referencia a los puntos previos de desbarbado y los puntos de desbarbado, estos puntos van variando dependiendo de los valores de ```i``` y ```j```, es decir, dependen de la fila y la columna. Cuando el robot alcanza el punto ```abajo```, entra en un reposo de 2.5 segundos en los que se activa la salida digital 0 mediante el nodo ```set Digital Output``` con el valor HIGH (True en escritura de script), una vez pasado ese tiempo la salida digital se desactiva adquiriendo el valor LOW (False en escritura de script).

![](/img/EASY_2.2.png)

#### Ejercicio 3 Easy Programming
Este ejercicio está vasado en el Ejercicio 2, en este caso en cada punto de la rejilla hay que efectuar un desbarbado de 2.5 segundos y un pulido de 1 segundo de duración. Como se emplean dos herramientas, se define un TCP por cada una de ellas en la instalación del robot, el de desbarbado es TCP_3_D con XYZ [50, 25, 175] mm y RxRyRz [0, 0, 90] grados y el de pulido TCP_3_P con XYZ [-50, 25, 175] mm y RxRyRz [0, 0, 90] grados.

![](/img/EASY_3.1.png)
![](/img/EASY_3.2.png)

Al igual que en el Ejercicio 2, el movimiento por los puntos de la rejilla se consigue mediante un ciclo dentro de otro. El movimiento de subir y bajar la herramienta en un mismo punto se hace dos veces, uno por el desbarbado y otro por el pulido. Como se ha comentado, la extensión acoplada al robot está compuesta por dos herramientas, para poder bajar la herramienta correspondiente en cada caso la programación por easy programming permite definir el TCP en los nodos de movimiento.

![](/img/EASY_3.3.png)
