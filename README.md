#MESOS

##Arquitectura:

	- Nodo 1: 6.3 GB RAM, 4 CPU's, esclavo y maestro (principal).
	- Nodo 2: 6.3 GB RAM, 4 CPU's, esclavo y maestro (secundario).
	- Cluster: 12.6 GB, 8 CPU's
	- Docker: Imagen eduglez/mesostest3
	
##Job de prueba:

Se generó una imagen con en docker con base en python 3 que contenía  se un script que entrenara una red neuronal con la cantidad de 100 tweets positivos y 200 tweets negativos, que a nivel local nos dio un tiempo de 3.8 segundos. También se manejaron tres instancias para ocupar los recursos de ambas maquinasy distribuir el trabajo, buscando un mejor rendimiento.

##Problemas:

Es importante tener bien configurados tus nodos, es difícil detectar en que archivo podría existir un problema dado que son muchos archivos. En nuestro caso el cache del mesos-slave estaba saturado, para arreglar ese error se limpio y reinicio el servicio.

Cuando se quería ejecutar un job que superaba los recursos del nodo o del cluster, ya sea por escalado, aumento de instancias o este definido en la tarea mismas, mesos pondrá en espera ese job hasta que los requisitos de recursos sean cumplidos de forma satisfactoria.

##Comportamiento:

Por otro lado como el job se dejaba de ejecutar una vez entrenada la red neuronal mesos lo volvía a ejecutar pero ahora se redistribuía las instancias borrando las salidas anteriores. Para resolver este problema se añadió un while con un sleep de 5 segundos para que se siguiera ejecutando y evitar la redistribución de las instancias.

El comportamiento del escalado difería del uso en instancias en unas cosa, mientras las instancias usaban los recursos de las maquinas disponible distribuyendo de la forma más equitativa el escalado distribuía en el mismo nodo hasta que se consumieran en total de sus recursos, solo después de eso comenzaba a usar el resto de los nodos.

##Resultados:

Los tiempos después de ejecutar el job mejoraron usando mesos, de 3.8 que dejo la marca local a 2.8 y 1.8 que se registraron en cada uno de los nodos. Por otra parte mejoraba el rendimiento general de los equipos, dado que usa docker esto aísla totalmente los recursos y trabajo que se realiza evitando la interrupción de otro procesos en la maquina local y viceversa. 
