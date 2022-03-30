# SIMUPY

Simupy (nombre provisional) es un paquete que permite simular la evolucion de una poblacion mediante una simulacion de tipo 'forward time'. El paquete consta (de momento) de dos clases Population e Individual, estas clases contienen una serie de metodos que permiten llevar a cabo la creacion de 0 de una poblacion, la evolucion de esta y la obtencion de unos informes, graficos y resultados de la evolcion.




# Population
Population es la clase principal, a partir de esta el usuario interactuara con ella instanciando un objeto y llamando a sus metodos para llevar a cabo la simulacion. Dentro de esta clase, utiliando la *agregacion* de clases, se instanciaran multitud de **Individual** para llevar a cabo su evolucion.

## Funciones
### init
Inicializa los atributos, que son
- **name** :(str) nombre de la poblacion, no relevante
- **size** :(int) tamaño de la poblacion, utilizado para recorrer posteriormente los individuos entre otras
- **ploidy** :(int) indica si la pob esta formada por individuos haploides(1) o diploides(2)
- **vida media** : (int) todavia sin uso, se implementara posteriormente para generar una dist de edades y para hacerla evolucionar.
- **R**: (float [0,0.5]) frecuencia de recombinacion
- **mut**: (N-size tuple [0,1]) frecuencia de mutacion de cada gen 
- **freq**: (N-size dict tuples) frecuencia alelica, se expresa en un diccionario donde la key es el genX y los valores son la frecuencia de su alelo dominante y el recesivo

Estos son los atributos que el usuario debera pasar (tiene valores por defecto), ademas se encuentran otros como:

- **genotypeFreq**:(N-size dict tuples) frecuencias genotipicas, se calculan llamando al metodo genotFreq
- **gen**: este atributo indica la generacion en la que se encuentra la poblacion

### generateIndividuals()
No se le pasa ningun parametro (posiblemente se cambie ya que el usuario deberia poder elegir ciertas cosas), tampoco devuelve ninguno.
Dentro, genera **indiv** que es una lista de objetos de clase **individuo** (composicion de clases) de tamaño size, ademas printa por pantalla que se ha completado el proceso.

La lista de individuos (**indiv**) sera la lista padre original, a partir de la cual se generaran las hijas.

### printIndiv(show,children)
A esta funcion se le pasan dos parametros, **show**, que permite elegir cuantos individuos se muestran y children, un boobleano que elige si se enseña la generacion padre o la hija (esto habra que cambiarlo mas adelante)
recorre la lista en un bucle y printa los atributos de cada instancia de individuo en la lista.
### genotFreq()
Genera las frecuencias genotipicas a partir de las alelicas **freq** (pasadas por el usuario o calculadas a partir de los padres), se guardan en un diccionario **genotypeFreq** que contiene las frecuencias genotipicas para cada gen.
### getMeanAge()
Calcula la edad media de la poblacion, de momento siempre 0

### getGenotype()
Crea un diccionario llamado Counter, donde se ira almacenando valores numericos segun aparezcan en el genotipo de los individuos.
- esto habra que cambiarlo para que se muestre 1. en forma de tabla (pandas maybe) 2. muestre la frecuencia de un genotipo completo (gen1 y gen2) 3. calcule frecuencias relativas.

### evolvePop(gens,k,time)
Este metodo lleva a cabo el proceso de evolucion, sera el usuario quien la llame, a partir de ese momento y hasta un numero X de generaciones (pasadas por el usuario o predeterminadas) la poblacion comenzara a evolucionar, esto es:
- se dara un proceso de 'mating', donde 2 individuos de la lista de objetos Individual se seleccionan (en concreto sus genotipos **genotype**) y se produce su 'recombinacion'
- su sexo **sex** se escogera de forma aleatoria (en un principio, mas adelante podria verse algun gen de det del sexo gen3=XX o XY)
- la edad **age** se ignorara de momento, 
- tambien podra producirse una mutacion de uno de sus alelos (dominante a recesivo y viceversa), de momento la mutacion no podra crear nuevos alelos (ej. A,a y nuevo c)
- como el tama;o **size** de la poblacion debe mantenerse constante la nueva lista de Individual sera igual que la anterior en tama;o, asi que:
    1. Por cada pareja debe surgir 2 hijos (monogamia) --> sencillo
    *2. Un individuo puede juntarse con mas de un individuo(poligamia simple) --> ligeramente menos sencillo*
    3. Que exista un parametro extra similar a la eficacia biologica, determinado por el genotipo, que afectara al numero de descendientes de un individuo. FALTA desarrollo pero esta seria la forma ideal. --> complejo pero importante
- por ultimo, un parametro **time** que nos diga cada cuantas generaciones queremos guardar informacion sobre la lista de Individuals (para estadisticas y graficos), esto llamaria a otra funcion no definida que iria calculando las estadisticas del individuo que le pasemos en ese momento y las devolveria, este return se almacenaria en una estructura tipo dataframe de tama;o filas: gen//time, col:atributos(sex,genotype,etc) (siendo gen el numero de generaciones que queremos evolucionar la poblacion), que despues se podria representar graficamente.

Este metodo ya genera nuevas generaciones. El proceso actual es el siguiente:
Se crea un bucle que se ejecutara tantas veces como se le indique en el numero de generaciones (gens=20), se trabaja con la variable poblacion que apunta a una lista de **Individual**, se vacia la lista **childrenIndiv** donde se almacenaran los hijos y posteriormente se introducen en esta misma lista llamando al metodo chooseMate, que nos devolvera un individuo nuevo.
Ahora la lista **indiv** pasa a ser la lista de hijos **childrenIndiv**, decidimos cada cuanto y qué mostrar al usuario en el condicional if y por último aumentamos 1 la generacion.

Lo siguiente a hacer es:

1. generar una funcion o conjunto de funciones que permitan obtener estadisticas adecuadas (o grafico o tabla pero que resuman bien la informacion de la poblacion)
2. Introducir el parametro mut a la generacion de genotipos

### chooseMate()
Metodo que se llama dentro de *evolvePop*, toma como parametros x(se usara para formar su **ide**ntificador) y poblacion, que es la lista de **Individuals** de la cual escogeremos dos de ellos de forma aleatoria, despues guardamos ambos padres ind1,ind2 en una variable llamada parents que se pasara como atributo de clase a Individual, despues llamamos a findFreqAlleles() que modifica la variable freq ¡¡hay que cambiar esto!! para que sea la frecuencia alelica de los padres y genotFreq para que genere una nueva lista de frecuencias genotipicas ¡¡hay que cambiarlo tambien!!, despues se instancia un nuevo individuo que se devuelve a evolvePop

### findFreqAlleles(ind1,ind2)
Se le pasan como atributos los dos individuos, de los cuales debera obtener las frecuencias alelicas sumando las ocurrencias de A,a y B,b para ambos. Estos valores sobreescribiran el antiguo diccionario freq, esto puede ser un poco confuso ya que antes freq indicaba una caractersitica de la poblacion y ahora indica la de dos individuos, quiza sea mejor crear un nuevo atributo llamado freqInd pero esto hara que sea necesario cambiar ligeramente el metodo genotFreq().


# Individual
Individual se trata de una clase que se crea de forma indirecta, llamando un metodo de Population, esta clase no es accesible al usuario. 
Esta clase consta de los atributos y metodos necesarios para dar lugar a un individuo que formara parte de la poblacion, los atributos son: 
- **gen1List,gen2List** : (str list) lista con las combinaciones alelicas posibles, es una variable de clase ya que siempre sera igual para todo los invididuos
## atributos
- name,spSize,spPloidy: (str,int,int)nombre, tamaño y ploidia de la poblacion esto puede que no haga falta
- vidaMedia: (int) vida media, tambien inutil de momento
- genotypeFreq: (dict) frecuencias genotipicas de los padres sumadas
- Sex:  (string) male and female
- Ide: identificador, para cada individuo formado por su generacion y su identificador dentro de su generacion (0-size)
- Age: (int), comienza en 0, de momento no usado
- genotype: un diccionario tipo {gen1:'AA',gen2:'Bb'}
- parents: tupla de 2 elementos que contienen el **Individual** del cual se ha obtenido el genotipo de este (sus padres vamos)

## Metodos

### init
Inicializa los atributos vistos anteriormente, la forma de inicializarlos puede no ser la correcta ya que en muchos se esta llamando a un metodo de la clase pero de momento funciona

### sex
Le da un valor de sexo a cada individuo de forma aleatoria al 50%, no requiere ningun parametro, devuelve male o female
### edad
De momento no se utiliza
### firstGenotype
Elige un genotipo de forma aleatoria con pesos dados por **genotFreq**, solo se utiliza la primera vez
### __str__
Es un metodo dunder o magico que permite printar lo que indiques cuando escribas una instancia en lugar de darte la direccion de memoria
### printParents
permite printar los padres del individudo