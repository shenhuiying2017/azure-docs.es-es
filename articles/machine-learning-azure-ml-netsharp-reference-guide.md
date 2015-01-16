<properties title="Guide to the Net# Neural Networks Specification Language for Azure ML" pageTitle="Guía sobre el lenguaje de especificación de redes neuronales de Net# para el Aprendizaje automático de Azure " description="Sintaxis para el lenguaje de especificación de redes neuronales de Net#, junto con ejemplos de la manera de crear un modelo de red neuronal personalizada en el aprendizaje automático de Microsoft Azure con Net# " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jeannt" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/08/2014" ms.author="jeannt" />



# Guía sobre el lenguaje de especificación de redes neuronales de Net#


Net#(c) es un lenguaje desarrollado por Microsoft que se utiliza para definir arquitecturas de redes neuronales para módulos de redes neuronales en el Aprendizaje automático de Microsoft Azure. En este artículo, aprenderá lo siguiente:  

-	Conceptos básicos relacionados con redes neuronales
-	Requisitos de red neuronal y definición de componentes principales
-	Sintaxis y palabras clave del lenguaje de especificación Net# 
-	Ejemplos de redes neuronales personalizadas creadas mediante Net#  

###Conceptos básicos sobre redes neuronales
Una estructura de red neuronal consta de ***nodos*** que se disponen en ***capas*** y ***conexiones*** ponderadas (o ***bordes***) entre los nodos. Las conexiones son direccionales, y cada una de ellas tiene un nodo de ***origen*** y un nodo de ***destino***.  

Cada una de las ***capas entrenables*** (concretamente una capa oculta o de salida) tiene uno o varios ***conjuntos de conexiones***. Un conjunto de conexiones consta de una capa de origen y una especificación de las conexiones que salen de esa capa de origen. Todas las conexiones de un conjunto determinado comparten la misma ***capa de origen*** y la misma ***capa de destino***. En Net#, se considera que un conjunto de conexiones pertenece a la capa de destino del conjunto.  
 
Net# admite varios tipos de conjuntos de conexiones, lo que le permite personalizar la manera en que las entradas se asignan a capas ocultas y posteriormente a salidas.   

El conjunto estándar o predeterminado es un **conjunto completo**, en el cual cada nodo de la capa de origen se conecta a cada uno de los nodos de la capa de destino.  

Además, Net# admite los siguientes cuatro tipos de conjuntos de conexiones avanzados:  

-	**Conjuntos filtrados**. El usuario puede definir un predicado mediante las ubicaciones del nodo de la capa de origen y el nodo de la capa de destino. Los nodos se conectan siempre que el predicado sea verdadero.
-	**Conjuntos convolucionales**. El usuario puede definir pequeñas vecindades de nodos en la capa de origen. Cada nodo de la capa de destino se conecta con una vecindad de nodos en la capa de origen.
-	**Conjuntos de agrupación** y **conjuntos de normalización de respuesta**. Son similares a los conjuntos convolucionales en cuanto que el usuario define pequeñas vecindades de nodos en la capa de origen. La diferencia es que las ponderaciones en los bordes de estos conjuntos no son entrenables, y en su lugar se aplica una función predefinida a los valores del nodo de origen para determinar el valor del nodo de destino.  

El uso de Net# para definir la estructura de una red neuronal posibilita la definición de estructuras complejas como redes neuronales profundas (DNN) o convoluciones de dimensiones arbitrarias, que se sabe que mejoran el aprendizaje en datos tales como imágenes, audio o vídeo.  

###Personalizaciones compatibles
La arquitectura de los modelos de redes neuronales creados en el Aprendizaje automático de Microsoft Azure se puede personalizar en gran medida mediante el uso de Net#. Puede:  

-	Crear capas ocultas y controlar el número de nodos de cada capa.
-	Especificar cómo se van a conectar las capas entre ellas.
-	Definir estructuras de conectividad especial como convoluciones y conjuntos ponderados de uso compartido.
-	Especificar diferentes funciones de activación.  

Para obtener detalles sobre la sintaxis del lenguaje de especificación, consulte Especificación de estructura.  
 
Para consultar ejemplos de definición de redes neuronales para algunas tareas habituales de aprendizaje automático, desde lo simple a lo complejo, consulte Ejemplos.  

###Requisitos generales
-	Debe haber exactamente una capa de salida, al menos una capa de entrada y ninguna o varias capas ocultas. 
-	Cada capa tiene un número fijo de nodos, ordenados conceptualmente en una matriz rectangular de dimensiones arbitrarias. 
-	Las capas de entrada no tienen parámetros entrenables asociados y representan el punto en que los datos de la instancia entran en la red. 
-	Las capas entrenables (es decir, las capas ocultas y de salida) tienen parámetros entrenables asociados, conocidos como ponderaciones y sesgos. 
-	Los nodos de origen y de destino deben encontrarse en capas separadas. 
-	Las conexiones deben ser acíclicas; en otras palabras, no puede haber una cadena de conexiones que conduzca de nuevo al nodo de origen inicial.
-	La capa de salida no puede ser la capa de origen de un conjunto de conexiones.  

##Especificación de estructura
La especificación de estructura de una red neuronal está formada por tres secciones: la **declaración constante**, la **declaración de capas**, la **declaración de conexiones** y una **declaración de recursos compartidos** opcional. Estas secciones se pueden especificar en cualquier orden.  

###Declaración constante 
Una declaración constante es opcional y proporciona un medio para definir valores que se utilizan en cualquier parte de la definición de red neuronal. La instrucción de la declaración consta de un identificador seguido por un signo igual y una expresión de valor.   

Por ejemplo, la siguiente declaración define una constante **x**:  


    Const X = 28;  

Para definir dos o varias constantes de manera simultánea, encierre los nombres de identificador y los valores entre llaves y sepárelos utilizando puntos y coma, como en este ejemplo:  

    Const { X = 28; Y = 4; }  

La parte derecha de cada expresión de asignación puede ser un entero, un número real, un valor booleano (verdadero/falso) o una expresión matemática. Por ejemplo:  
 

	Const { X = 17 * 2; Y = true; }  

###Declaración de capas
La declaración de capas es obligatoria, y define el tamaño y el origen de la capa, incluidos sus conjuntos de conexiones y atributos. La instrucción de la declaración empieza por el nombre de la capa (de entrada, oculta o de salida) seguida por las dimensiones de la capa (una tupla de enteros positivos). Por ejemplo:  

	input Data[784];
	hidden Hidden[5,20] from Data all;
	output Result[2] from Hidden all;  

-	El producto de las dimensiones es el número de nodos de la capa. En este ejemplo, hay dos dimensiones [5,20], lo que significa que hay  100 nodos en la capa.
-	Las capas se pueden declarar en cualquier orden, con una excepción: si se define más de una capa de entrada, el orden en que se declaran debe coincidir con el orden de las características de los datos de entrada.  

<!-- REMOVED THIS CONTENT UNTIL THIS FEATURE IS SUPPORTED IN THE PRODUCT
To specify that the number of nodes in a layer be determined automatically, use the **auto** keyword. The **auto** keyword has different effects, depending on the layer:  

-	In an input layer declaration, the number of nodes is the number of features in the input data.
-	In a hidden layer declaration, the number of nodes is the number specified by the parameter value for **Number of hidden nodes**. 
-	In an output layer declaration, the number of nodes is 2 for two-class classification, 1 for regression, and equal to the number of output nodes for multi-class classification.   

For example, the following network definition allows the size of all layers to be automatically determined:  

	input Data auto;
	hidden Hidden auto from Data all;
	output Result auto from Hidden all;  
-->

Una declaración de capas para una capa entrenable (es decir, las capas de salida u ocultas) pueden incluir opcionalmente la función de salida (también llamada función de activación), que se establece de manera predeterminada en **sigmoid**. Se admiten las siguientes funciones de salida:  

-	sigmoid
-	linear
-	softmax
-	rlinear
-	square
-	sqrt
-	srlinear
-	abs
-	tanh 
-	brlinear  

Por ejemplo, la siguiente declaración utiliza la función **softmax**:  

	output Result [100] softmax from Hidden all;  

###Declaración de conexiones
Inmediatamente después de definir la capa entrenable, debe declarar conexiones entre las capas que ha definido. La declaración del conjunto de conexiones empieza por la palabra clave **from**, seguida del nombre de la capa de origen del conjunto y el tipo de conjunto de conexiones que se va a crear.   

Actualmente se admiten cinco tipos de conjuntos de conexiones:  

-	Conjuntos **completos**, indicados por la palabra clave **all**.
-	Conjuntos **filtrados**, indicados por la palabra clave **where** seguida por una expresión de predicado.
-	Conjuntos **convolucionales**, indicados por la palabra clave **convolve** seguida por los atributos de convolución.
-	Conjuntos de **agrupación**, indicados por la palabra clave **max pool** o **mean pool**.
-	Conjuntos de **normalización de respuesta**, indicados por la palabra clave **response norm**.  	

####Conjuntos completos  

Un conjunto de conexiones completo incluye una conexión desde cada nodo de la capa de origen a cada nodo de la capa de destino. Se trata del tipo de conexión de red predeterminado.  

####Conjuntos filtrados
Una especificación de conjunto de conexiones filtrado incluye un predicado, expresado sintácticamente de manera muy similar a una expresión lambda de C#. El ejemplo siguiente define dos conjuntos filtrados:  

	input Pixels [10, 20];
	hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-	En el predicado de ByRow, **s** es un parámetro que representa un índice en la matriz rectangular de nodos de la capa de entrada Pixels, mientras que **d** es un parámetro que representa un índice en la matriz de nodos de la capa oculta ByRow. El tipo tanto de **s** como de **d** es una tupla de enteros de longitud dos. Conceptualmente, **s** oscila entre todos los pares de enteros con 0 <= s[0] < 10 y 0 <= s[1] < 20, mientras que **d** oscila entre todos los pares de enteros con 0 <= d[0] < 10 y 0 <= d[1] < 12. 
-	En la parte derecha de la expresión del predicado hay una condición. En este ejemplo, para cada valor de **s** y **d** en los que esa condición sea verdadera, existe un borde que va desde el nodo de la capa de origen al nodo de la capa de destino. Por tanto, esta expresión de filtro indica que el conjunto incluye una conexión desde el nodo definido por **s** hasta el nodo definido por **d** en todos aquellos casos en que s[0] sea igual a d[0].  

También tiene la posibilidad de especificar un conjunto de ponderaciones para un conjunto filtrado. El valor del atributo **Weights** debe ser una tupla de valores de puntos flotantes cuya longitud coincida con el número de conexiones definidas por el conjunto. De manera predeterminada, las ponderaciones se generan de manera aleatoria.  

Los valores ponderados se agrupan según el índice del nodo de destino. Esto es, si el primer nodo de destino está conectado por K nodos de origen, los primeros K elementos de la tupla **Weights** son las ponderaciones del primer nodo de destino, en orden del índice de origen. Lo mismo se aplica para los nodos de destino restantes.  

####Conjuntos convolucionales
Cuando los datos entrenables tienen una estructura homogénea, las conexiones convoluciones se suelen utilizar para aprender características de alto nivel de los datos. Por ejemplo, en datos de imagen, audio o vídeo, la dimensionalidad espacial o temporal puede ser bastante homogénea.  

Los conjuntos convolucionales utilizan **kernels** que se deslizan a través de las dimensiones. Básicamente, cada kernel define un conjunto de ponderaciones aplicado en vecindades locales, conocidas como **aplicaciones de kernel**. Cada aplicación de kernel se corresponde con un nodo en la capa de origen, conocido como **nodo central**. Las ponderaciones de un kernel se comparten entre varias conexiones. En un conjunto convolucional, cada kernel es rectangular y todas las aplicaciones de kernel tienen el mismo tamaño.  

Los conjuntos convolucionales admiten los siguientes atributos: **InputShape** define la dimensionalidad de la capa de origen para los fines de este conjunto convolucional. El valor debe ser una tupla de enteros positivos. El producto de los enteros debe equivaler al número de nodos de la capa de origen, pero no es necesario que coincida con la dimensionalidad declarada para la capa de origen. La longitud de esta tupla se convierte en el valor de **aridad** del conjunto convolucional. (La aridad normalmente hace referencia al número de argumentos u operandos que puede asumir una función).  

Para definir la forma y las ubicaciones de los kernels, utilice los atributos **KernelShape**, **Stride**, **Padding**, **LowerPad** y **UpperPad**:   

-	**KernelShape**: (obligatorio) define la dimensionalidad de cada kernel para el conjunto convolucional. El valor debe ser una tupla de enteros positivos cuya longitud equivalga a la aridad del conjunto. Ninguno de los componentes de esta tupla debe superar en tamaño al componente correspondiente de **InputShape**. 
-	**Stride**: (opcional) define los tamaños de los pasos de deslizamiento de la convolución (un tamaño de paso para cada dimensión); esto es, la distancia entre los nodos centrales. El valor debe ser una tupla de enteros positivos cuya longitud sea la aridad del conjunto. Ninguno de los componentes de esta tupla debe superar en tamaño al componente correspondiente de **KernelShape**. El valor predeterminado es una tupla con todos los componentes iguales a uno. 
-	**Padding**: (opcional) determina si la entrada debe completarse utilizando un esquema de completado predeterminado. El valor puede ser un valor booleano sencillo o una tupla de valores booleanos cuya longitud sea la aridad del conjunto. Un valor booleano sencillo se amplía para convertirse en una tupla si la longitud correcta con todos los componentes equivale al valor especificado. Si el valor de una dimensión es verdadero, el origen se completa lógicamente en esa dimensión con celdas de valor cero para atender a las aplicaciones de kernel adicionales, por lo cual los nodos centrales del primer y último kernel de esa dimensión son el primer y último nodo en esa dimensión en la capa de origen. De este modo, el número de nodos "ficticios" de cada dimensión se determina automáticamente, para coincidir exactamente con el número de kernels (InputShape[d] - 1) / Stride[d] + 1 en la capa de origen completada. SI el valor de una dimensión es falso, los kernels se definen de manera que el número de nodos que quedan en cada lado sea el mismo (con una diferencia máxima de 1). El valor predeterminado de este atributo es una tupla con todos los componentes iguales a falso.
-	**UpperPad** y **LowerPad**: (opcional) proporcionan control sobre la cantidad de completado que se utiliza. Estos atributos pueden definirse si y solo si **Padding** ***no*** está definido. Los valores deben ser tuplas de valores enteros cuyas longitudes sean la aridad del conjunto. Cuando se especifican estos atributos, se agregan nodos "ficticios" a los extremos inferior y superior de cada dimensión de la capa de entrada- El número de nodos agregados a los extremos inferior y superior en dimensión está determinado por **LowerPad**[i] y **UpperPad**[i], respectivamente. Para asegurarse de que los kernels se corresponden solo a nodos "reales" y no a nodos "ficticios", deben cumplirse las condiciones siguientes:
	-	Cada componente de **LowerPad** debe ser estrictamente inferior a KernelShape[d]/2. 
	-	Ningún componente de **UpperPad** debe ser superior a KernelShape[d]/2. 
	-	El valor predeterminado de estos atributos es una tupla con todos los componentes iguales a 0. 
-	**Sharing**: (opcional) define el uso compartido de las ponderaciones de cada dimensión de la convolución. El valor puede ser un valor booleano sencillo o una tupla de valores booleanos cuya longitud sea la aridad del conjunto. Un valor booleano sencillo se amplía para convertirse en una tupla si la longitud correcta con todos los componentes equivale al valor especificado. El valor predeterminado es una tupla que tiene todos los valores verdaderos. 
-	**MapCount**: (opcional) define el número de mapas de características para el conjunto convolucional. El valor puede ser un entero positivo sencillo o una tupla de enteros positivos cuya longitud sea la aridad del conjunto. Un valor entero sencillo se amplía para convertirse en una tupla si la longitud correcta con todos los componentes equivale al valor especificado y todos los componentes restantes equivalen a uno. El valor predeterminado es uno. El número total de mapas de características es el producto de los componentes de la tupla. La factorización de este número total entre los componentes determina cómo se agrupan los valores de mapas de características en los nodos de destino. 
-	**Weights**: (opcional) define las ponderaciones iniciales del conjunto. El valor debe ser una tupla de valores de puntos flotantes cuya longitud sea el número de kernels por el número de ponderaciones por kernel, como se define a continuación. De manera predeterminada, las ponderaciones se generan de manera aleatoria.  

Para obtener más información acerca de las redes convolucionales y sus aplicaciones, consulte estos artículos:  

-	[http://deeplearning.net/tutorial/lenet.html ](http://deeplearning.net/tutorial/lenet.html )
-	[http://research.microsoft.com/pubs/68920/icdar03.pdf](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-	[http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

####Conjuntos de agrupación
Un **conjunto de agrupación** aplica una geometría similar a la conectividad convolucional, pero utiliza funciones predefinidas sobre los valores de los nodos de origen para derivar el valor del nodo de destino. Por tanto, los conjuntos de agrupación no tienen estado entrenable (ponderaciones o sesgos). Los conjuntos de agrupación admiten todos los atributos convolucionales excepto **Sharing**, **MapCount** y **Weights**.  

Normalmente, los kernels resumidos por las unidades de agrupación adyacentes no se superponen. Si Stride[d] es igual a KernelShape[d] en cada dimensión, la capa obtenida es la capa de agrupación local tradicional, utilizada habitualmente en redes neuronales convolucionales. Cada nodo de destino calcula el valor máximo o la media de las actividades de su kernel en la capa de origen.  

El ejemplo siguiente ilustra un conjunto de agrupación:  

	hidden P1 [5, 12, 12]
	  from C1 max pool {
	    InputShape  = [ 5, 24, 24];
	    KernelShape = [ 1,  2,  2];
	    Stride      = [ 1,  2,  2];
	  }  

-	La aridad del conjunto es 3 (la longitud de las tuplas **InputShape**, **KernelShape** y **Stride**). 
-	El número de nodos de la capa de origen es 5 * 24 * 24 = 2.880. 
-	Se trata de una capa de agrupación local tradicional, ya que **KernelShape** y **Stride** son iguales. 
-	El número de nodos de la capa de destino es 5 * 12 * 12 = 1.440.  
	
Para obtener más información acerca de las capas de agrupación, consulte estos artículos:  

-	[http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sección 3.4)
-	[http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-	[http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
	
####Conjuntos de normalización de respuesta
La **normalización de respuesta** es un esquema de normalización local presentado por primera vez por Geoffrey Hinton et al en un documento titulado "ImageNet Classiﬁcation with Deep Convolutional Neural Networks" (vea la sección 3.3). La normalización de respuesta se utiliza para ayudar a la generalización en redes neuronales. Cuando una neurona se activa a un nivel muy alto, una capa de normalización de respuesta local suprime la capa de activación de las neuronas circundantes. Esto se realiza por medio de tres parámetros ***α***, ***β*** y ***k*** y una estructura convolucional (o forma de vecindad). Cada neurona de la capa de destino ***y*** se corresponde con una neurona ***x*** de la capa de origen. El nivel de activación de ***y*** se determina mediante la fórmula siguiente, donde ***f*** es el nivel de activación de una neurona y ***Nx*** es el kernel, o el conjunto que contiene las neuronas en la vecindad de ***x*** como se define en la estructura convolucional:  

![][1]  

Los conjuntos de normalización de respuesta admiten todos los atributos convolucionales excepto **Sharing**, **MapCount** y **Weights**.  
 
-	Si el kernel contiene neuronas en el mismo mapa que ***x***, , se hace referencia al esquema de normalización como **normalización de mismo mapa**. Para definir la normalización de mismo mapa, el primer coordinado de **InputShape** debe tener un valor de 1.
-	Si el kernel contiene neuronas en la misma posición espacial que ***x***, pero las neuronas se encuentran en otros mapas, el esquema de normalización recibe el nombre de **normalización entre mapas**. El tipo de normalización de respuesta implementa una forma de inhibición lateral inspirada en el tipo encontrado en las neuronas reales, creando competencia para los grandes niveles de activación entre los resultados neuronales calculados en los diferentes mapas. Para definir la normalización entre mapas, el primer coordinado debe ser un entero mayor que uno y no mayor que el número de mapas, y el resto de coordinados debe tener el valor 1.  

Dado que los conjuntos de normalización de respuesta aplican una función predefinida a los valores del nodo de origen para determinar el valor del nodo de destino, no tienen un estado entrenable (ponderaciones o sesgos).   

**Aviso:** los nodos de la capa de destino se corresponden con las neuronas que se encuentran en los nodos centrales de los kernel. Por ejemplo, si KernelShape[d] es impar, KernelShape[d]/2 corresponde al nodo del kernel central. Si KernelShape[d] es par, el nodo central se encuentra en KernelShape[d]/2 - 1. Por tanto, si **Padding**[d] es falso, el primer y el último nodo KernelShape[d]/2 no tienen nodos correspondientes en la capa de destino. Para evitar esta situación, defina **Padding** como [true, true, ..., true].  

Además de los cuatro atributos descritos anteriormente, los conjuntos de normalización de respuesta admiten también los siguientes atributos:  

-	**Alpha**: (obligatorio) especifica un valor de punto flotante que corresponde a ***α*** en la fórmula anterior. 
-	**Beta**: (obligatorio) especifica un valor de punto flotante que corresponde a ***β*** en la fórmula anterior. 
-	**Offset**: (opcional) especifica un valor de punto flotante que corresponde a ***k*** en la fórmula anterior. El valor predeterminado es 1.  

En el ejemplo siguiente se define un conjunto de normalización de respuesta que utiliza estos atributos:  

	hidden RN1 [5, 10, 10]
	  from P1 response norm {
	    InputShape  = [ 5, 12, 12];
	    KernelShape = [ 1,  3,  3];
	    Alpha = 0.001;
	    Beta = 0.75;
	  }  

-	La capa de origen incluye cinco mapas; cada uno con una dimensión de 12x12 (totalizando 1440 nodos). 
-	El valor de **KernelShape** indica que se trata de una capa de normalización de mismo mapa, donde la vecindad es un rectángulo de 3x3. 
-	El valor predeterminado de **Padding** es falso; así pues, la capa de destino tiene solo 10 nodos en cada dimensión. A fin de incluir un nodo en la capa de destino correspondiente a cada uno de los nodos de la capa de origen, agregue Padding = [true, true, true] y cambie el tamaño de RN1 a [5, 12, 12].  

###Declaración de uso compartido 
Net# admite opcionalmente la definición de varios conjuntos con ponderaciones compartidas. Las ponderaciones de dos conjuntos cualesquiera se pueden compartir siempre y cuando sus estructuras sean la misma. La siguiente notación describe la sintaxis para definir conjuntos con ponderaciones compartidas:  

	share-declaration:
	    share    {    layer-list    }
	    share    {    bundle-list    }
	   share    {    bias-list    }
	
	layer-list:
	    layer-name    ,    layer-name
	    layer-list    ,    layer-name
	
	bundle-list:
	   bundle-spec    ,    bundle-spec
	    bundle-list    ,    bundle-spec
	
	bundle-spec:
	   layer-name    =>     layer-name
	
	bias-list:
	    bias-spec    ,    bias-spec
	    bias-list    ,    bias-spec
	
	bias-spec:
	    1    =>    layer-name
	
	layer-name:
	    identifier  

Por ejemplo, la siguiente declaración de uso compartido especifica los nombres de las capas, indicando que tanto las ponderaciones como los sesgos deben compartirse:  

	Const {
	  InputSize = 37;
	  HiddenSize = 50;
	}
	input {
	  Data1 [InputSize];
	  Data2 [InputSize];
	}
	hidden {
	  H1 [HiddenSize] from Data1 all;
	  H2 [HiddenSize] from Data2 all;
	}
	output Result [2] {
	  from H1 all;
	  from H2 all;
	}
	share { H1, H2 } // share both weights and biases  

-	Las características de entrada se particionan en dos capas de entrada de igual tamaño. 
-	Las capas ocultas calculan entonces características de niveles más altos en las dos capas de entrada. 
-	La declaración de uso compartido especifica que H1 y H2 deben calcularse del mismo modo desde sus entradas respectivas.  
 
Es posible también especificarlo con dos declaraciones de uso compartido independientes, del modo siguiente:  

	share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

	share { 1 => H1, 1 => H2 } // share biases  

Puede utilizar el formato resumido solo cuando las capas contengan un único conjunto. En general, el uso compartido es posible solo cuando la estructura relevante sea idéntica; es decir, cuando tenga el mismo tamaño, la misma geometría convolucional, etc.  

##Ejemplos de uso de Net#
En esta sección se ofrecen ejemplos acerca del modo de utilizar Net# para agregar capas ocultas, definir el modo en que las capas ocultas interactúan con otras capas y crear redes convolucionales.   

###Definición de una red neuronal sencilla personalizada: ejemplo "Hello World"
Este sencillo ejemplo demuestra cómo crear un modelo de red neuronal que tenga una única capa oculta.  

	input Data [100];
	hidden H [200] from Data all;
	output Out [10] sigmoid from H all;  

El ejemplo ilustra algunos comandos básicos y su orden:  

-	La primera línea define la capa de entrada, llamada Data, que tiene 100 nodos, cada uno de los cuales representa una característica en los ejemplos de entrada.
-	La segunda línea crea la capa oculta. El nombre H se asigna a la capa oculta, que tiene 200 nodos. Esta capa está conectada por completo a la capa de entrada.
-	La tercera línea define la capa de salida, denominada O, y contiene 10 nodos de salida. En cuanto a la clasificación de redes neuronales, existe un nodo de salida por clase. La palabra clave **sigmoid** indica la función de salida aplicada a la capa de salida.   

###Definición de varias capas ocultas: ejemplo de visión informática
En el ejemplo siguiente se demuestra cómo definir una red neuronal ligeramente más compleja, con varias capas personalizadas ocultas.  

	// Define the input layers 
	input Pixels [10, 20];
	input MetaData [7];
	
	// Define the first two hidden layers, using data only from the Pixels input
	hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
	hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
	
	// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
	hidden Gather [100] 
	{
	  from ByRow all;
	  from ByCol all;
	}
	
	// Define the output layer and its sources
	output Result [10]  
	{
	  from Gather all;
	  from MetaData all;
	}  

Este ejemplo ilustra varias características del lenguaje de especificación de redes neuronal:  

-	La estructura tiene dos capas de entrada: Pixels y MetaData.
-	La capa Pixels es una capa de origen con dos conjuntos de conexiones, con las capas de destino ByRow y ByCol.
-	Las capas Gather y Result son capas de destino en varios conjuntos de conexiones.
-	La capa de salida, Result, es una capa de destino en dos conjuntos de conexiones; uno con la capa oculta de segundo nivel (Gather) como capa de destino, y la otra con la capa de entrada MetaData como capa de destino.
-	Las capas ocultas, ByRow y ByCol, especifican la conectividad filtrada mediante expresiones de predicado. Más precisamente, el nodo en ByRow en [x, y] está conectado a estos nodos en Pixels al tener el primer coordinado de índice igual al primer coordinado del nodo (x). Del mismo modo, el nodo en ByCol en [x, y] está conectado a estos nodos en Pixels al tener el segundo coordinado de índice dentro del segundo coordinado (y) de uno de los nodos.  

###Definición de una red convolucional para clasificación de varias clases: ejemplo de reconocimiento de dígitos
La definición de la siguiente red, diseñada para reconocer números, ilustra algunas técnicas avanzadas para la personalización de una red neuronal.  

	input Image [29, 29];
	hidden Conv1 [5, 13, 13] from Image convolve 
	{
	   InputShape  = [29, 29];
	   KernelShape = [ 5,  5];
	   Stride      = [ 2,  2];
	   MapCount    = 5;
	}
	hidden Conv2 [50, 5, 5]
	from Conv1 convolve 
	{
	   InputShape  = [ 5, 13, 13];
	   KernelShape = [ 1,  5,  5];
	   Stride      = [ 1,  2,  2];
	   Sharing     = [false, true, true];
	   MapCount    = 10;
	}
	hidden Hid3 [100] from Conv2 all;
	output Digit [10] from Hid3 all;  


-	La estructura tiene una sola capa de entrada: Image.
-	La palabra clave **convolve** indica que Conv1 y Conv2 son capas convolucionales. Cada una de estas declaraciones de capa está seguida por una lista de los atributos de convolución.
-	La red tiene una tercera capa oculta (Hid3) que está totalmente conectada a la segunda capa oculta (Conv2).
-	La capa de salida (Digit) está conectada solo a la tercera capa oculta (Hid3). La palabra clave **all** indica que la capa de salida está conectada por completo a Hid3.
-	La aridad de la convolución es tres (la longitud de las tuplas **InputShape**, **KernelShape**, **Stride** y **Sharing**). 
-	El número de ponderaciones por kernel es 1 + **KernelShape**\[0] * **KernelShape**\[1] * **KernelShape**\[2] = 1 + 1 * 5 * 5 = 26. O bien 26 * 50 = 1.300.
-	Puede calcular los nodos en cada capa oculta del modo siguiente:
	-	**NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
	-	**NodeCount**\[1] = (13 - 5) / 2 + 1 = 5. 
	-	**NodeCount**\[2] = (13 - 5) / 2 + 1 = 5. 
-	El número total de nodos puede calcularse utilizando la dimensionalidad declarada de la capa [50, 5, 5] del modo siguiente: **MapCount** * **NodeCount**\[0] * **NodeCount**\[1] * **NodeCount**\[2] = 10 * 5 * 5 * 5
-	Dado que **Sharing**[d] es falso solo para d == 0, el número de los kernel es **MapCount** * **NodeCount**\[0] = 10 * 5 = 50. 

[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
