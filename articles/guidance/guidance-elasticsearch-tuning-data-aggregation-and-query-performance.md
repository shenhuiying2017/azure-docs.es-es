<properties
   pageTitle="Ajuste de la agregación de datos y del rendimiento de qQuery con Elasticsearch en Azure | Microsoft Azure"
   description="Un resumen de las consideraciones al optimizar el rendimiento de consultas y de búsquedas en Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Ajuste de la agregación de datos y del rendimiento de qQuery con Elasticsearch en Azure

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

Un motivo principal para usar Elasticsearch es admitir búsquedas a través de los datos. Los usuarios deberían poder encontrar rápidamente la información que están buscando. Además, el sistema debe permitir a los usuarios formular preguntas de los datos, buscar correlaciones y llegar a conclusiones que puedan impulsar decisiones empresariales. Este procesamiento es lo que diferencia los datos de la información.

Este documento resume las opciones que puede tener en cuenta al determinar la mejor forma de optimizar el sistema para el rendimiento de las búsquedas y las consultas.

Todas las recomendaciones de rendimiento dependen en gran medida de los escenarios aplicables a su situación, del volumen de los datos que se van a indexar y la velocidad a la que las aplicaciones y los usuarios consultan los datos. Debe probar con cuidado los resultados de cualquier cambio en la configuración o en la estructura de indexación mediante sus propios datos y cargas de trabajo para evaluar las ventajas para sus escenarios específicos. Para ello, este documento también describe una serie de pruebas comparativas realizadas para un escenario específico que se implementa mediante diferentes configuraciones. Puede adaptar el enfoque tomado para evaluar el rendimiento de sus propios sistemas.

## Consideraciones sobre rendimiento de índices y consultas

En esta sección se describen algunos de los factores comunes que debe tener en cuenta al diseñar índices que tienen que admitir consultas y búsquedas rápidas.

### Almacenamiento de varios tipos en un índice

Un índice de Elasticsearch puede contener varios tipos. Es mejor evitar este enfoque y crear un índice independiente para cada tipo. Considere los siguientes puntos:

- Los diferentes tipos pueden especificar distintos analizadores y no siempre está claro qué analizador debe usar Elasticsearch si se realiza una consulta en el nivel de índice en lugar de en el nivel de tipo. Consulte la sección [Avoiding Type Gotchas](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) (Evitar trampas de tipos) para más información.

- Las particiones de índices que contienen varios tipos probablemente serán mayores que los de índices que contengan un único tipo. Cuanto mayor sea una partición, más esfuerzo requiere Elasticsearch para filtrar los datos cuando se realizan consultas.

- Si hay una discrepancia significativa entre los volúmenes de datos de los tipos, la información para un tipo puede estar escasamente distribuida entre varias particiones, lo que reduce la eficacia de las búsquedas que recuperan estos datos.

![](./media/guidance-elasticsearch/query-performance1.png)

***Figura 1. Efectos de compartir un índice entre tipos***

En la figura 1 se describe este escenario. En la parte superior del diagrama, documentos de tipo A y B comparten el mismo índice. Hay más documentos de tipo A que de tipo B. Las búsquedas del tipo A implicarán consultas en las cuatro particiones. La parte inferior del diagrama muestra el efecto si se crean índices independientes para cada tipo. En este caso, las búsquedas del tipo A solo requieren acceso a dos particiones.

Las particiones pequeñas se pueden distribuir más uniformemente que las particiones grandes, con lo que a Elasticsearch le resulta más sencillo distribuir la carga entre los nodos.

Los distintos tipos pueden tener diferentes períodos de retención. Puede ser difícil de archivar los datos antiguos que comparten particiones con datos activos.

Sin embargo, en algunas circunstancias el uso compartido de un índice entre los tipos puede ser eficaz si:

- Las búsquedas abarcan regularmente tipos contenidos en el mismo índice.

- Los tipos tienen solo un número pequeño de documentos cada uno; el mantenimiento de un conjunto independiente de particiones para cada tipo puede ser una sobrecarga significativa en este caso.

### Optimización de tipos de índices

Un índice de Elasticsearch contiene una copia de los documentos JSON originales que se utilizaron para rellenarlo. Esta información se almacena en el campo [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) de cada elemento indexado. Estos datos no se pueden buscar pero, de forma predeterminada, los devuelven las solicitudes *get* y *search*. Sin embargo, este campo produce una sobrecarga y ocupa almacenamiento, con lo que las particiones son más grandes y se aumenta el volumen de E/S realizada. Puede deshabilitar el campo *\_source* por tipo:

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

Si se deshabilita este campo, también se quita la capacidad de realizar las siguientes operaciones:

- Actualización de datos en el índice con la API *update*.
- Realización de búsquedas que devuelven datos resaltados.
- Reindexación de un índice de Elasticsearch directamente en otro.
- Cambio de las asignaciones o configuración de análisis.
- Depuración de consultas mediante consulta del documento original.
 
### Reindexación de los datos

Finalmente, el número de particiones disponibles para un índice determina la capacidad del índice. Puede realizar una estimación inicial (e informada) de cuántas particiones serán necesarias, pero siempre debe considerar de antemano la estrategia de reindexación de documentación. En muchos casos, la reindexación puede ser una tarea pensada cuando aumentan los datos; no puede asignar inicialmente un gran número de particiones a un índice, en aras de la optimización de la búsqueda, pero asigne nuevas particiones a medida que se expanda el volumen de datos. En otros casos la reindexación debe realizarse de forma ad hoc si simplemente se muestra que las estimaciones sobre el crecimiento del volumen de datos son inexactas.

> [AZURE.NOTE] La reindexación podría no ser necesaria para aquellos datos que envejecen rápidamente. En este caso, una aplicación puede crear un nuevo índice para cada período de tiempo. Entre los ejemplos se incluyen registros de rendimiento o de datos de auditoría que podrían almacenarse en un índice nuevo cada día.

La reindexación implica eficazmente la creación de un nuevo índice de datos en uno anterior y, a continuación, la supresión del índice antiguo. Si un índice es grande, este proceso puede tardar tiempo y puede que necesite asegurarse de que los datos se pueden seguir buscando durante este período. Por este motivo, debe crear un [alias para cada índice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) y las consultas deben recuperar los datos a través de estos alias. Al reindexar, mantenga el alias apuntando al índice antiguo y luego cámbielo para que haga referencia al nuevo índice una vez completada la reindexación. Este enfoque también es útil para tener acceso a datos basados en el tiempo que crean un índice nuevo cada día. Para acceder a los datos actuales, utilice un alias que continuará en el nuevo índice cuando se crea.

### Administración de asignaciones

Elasticsearch utiliza asignaciones para determinar cómo interpretar los datos que se producen en cada campo en un documento. Cada tipo tiene su propia asignación, que define eficazmente un esquema para ese tipo. Elasticsearch utiliza esta información para generar índices invertidos para cada campo en los documentos de un tipo. En cualquier documento, cada campo tiene un tipo de datos (como *string*, *date* o *long*) y un valor. Puede especificar las asignaciones de un índice cuando se crea el índice por primera vez o pueden deducirlas Elasticsearch cuando se agregan nuevos documentos a un tipo. Sin embargo, considere los siguientes puntos:

- Las asignaciones generadas dinámicamente pueden producir errores dependiendo de cómo se interpretan los campos cuando se agregan documentos a un índice. Por ejemplo, el documento 1 podría contener un campo A con un número y hace que Elasticsearch agregue una asignación que especifica que este campo es de tipo *long*. Si se agrega otro documento en el cual el campo A contiene datos no numéricos, se producirá un error. En este caso, el campo A debería probablemente interpretarse como una cadena cuando se agregó el primer documento. Especificar esta asignación cuando se crea el índice puede ayudar a evitar estos problemas.

- Diseñe sus documentos para evitar que se generen asignaciones excesivamente grandes, ya que se puede agregar una importante sobrecarga al realizar búsquedas, consumir mucha memoria y también hacer que las consultas no puedan encontrar datos. Adopte una convención de nomenclatura coherente para los campos de los documentos que comparten el mismo tipo. Por ejemplo, no utilice nombres de campos como "first\_name", "FirstName" y "forename" en distintos documentos. Utilice el mismo nombre de campo en cada documento. Además, no intente utilizar valores como claves (esto es un enfoque común en las bases de datos de familia de columnas, pero puede producir errores e ineficiencias con Elasticsearch). Para más información, consulte [Mapping Explosion](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion) (Explosión de asignación).

- Utilice *not\_analyzed* evitar la tokenización donde corresponda. Por ejemplo, si un documento contiene un campo de cadena denominado *datos* que contiene el valor "ABC-DEF", puede intentar realizar una búsqueda de todos los documentos que coinciden con este valor como sigue:

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

Sin embargo, esta búsqueda no devolverá los resultados esperados debido a la manera en que la cadena ABC-DEF se tokeniza cuando se indexa; se dividirá eficazmente en dos tokens, ABC y DEF, por el guion. Esta característica está pensada para admitir la búsqueda de texto completo, pero si desea que la cadena se interprete como un solo elemento atómico, debe deshabilitar la tokenización cuando el documento se agregue al índice. Puede usar una asignación como la siguiente:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

Para más información, consulte la sección [Finding Exact Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text) (Búsqueda de valores exactos).

### Uso de los valores de documentos

Muchas de las consultas y agregaciones requieren que los datos se ordenen como parte de la operación de búsqueda. La ordenación requiere poder asignar uno o más términos a una lista de documentos. Para ayudar en este proceso, Elasticsearch puede cargar todos los valores de un campo usado como criterio de ordenación en memoria. Esta información se conoce como *fielddata*. La intención es que el almacenamiento en caché de la estructura fielddata en memoria consume menos E/S y podría ser más rápido que leer repetidamente los mismos datos desde el disco. Sin embargo, si un campo tiene cardinalidad alta, almacenar la estructura fielddata en memoria puede consumir mucho espacio de montón, posiblemente que afecta a la capacidad de realizar otras operaciones simultáneas o incluso agotar el almacenamiento causando un error en Elasticsearch.

Como alternativa, Elasticsearch también admite los *valores de documentos*. Un valor de documento es similar a una estructura fielddata en memoria, salvo que se almacenen en disco y se crea cuando los datos se almacenan en un índice (la estructura fielddata se construye dinámicamente cuando se realiza una consulta). Los valores de documento no consumen espacio de montón y, por tanto, son útiles para las consultas que ordenan o agregan datos por los campos que pueden contener un gran número de valores únicos. Además, la presión reducida del montón puede ayudar a compensar las diferencias de rendimiento entre recuperar datos del disco y leer de la memoria; es probable que se produzca con menos frecuencia una colección de elementos no utilizados y es menos probable que se vean afectadas otras operaciones simultáneas que usan la memoria.

Puede habilitar o deshabilitar los valores de documento en función de cada propiedad de un índice con el atributo *doc\_values*, tal como se muestra en el ejemplo siguiente:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] Los valores de documento están habilitados de forma predeterminada a partir de Elasticsearch versión 2.0.0.

El impacto exacto del uso de valores de documento suele ser muy específico para sus propios datos y escenarios de consulta, por tanto hay que estar preparado para llevar a cabo pruebas de rendimiento para establecer su utilidad. También hay que tener en cuenta que los valores de documento no funcionan con campos de cadenas analizadas. Para más información, consulte [Doc Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values) (Valores de documento).

### Uso de nodos de cliente

Todas las consultas se procesan mediante el nodo que primero recibe la solicitud. Este nodo envía más solicitudes a todos los demás nodos que contienen particiones para los índices que se consultan y, después, acumula los resultados para devolver la respuesta. Si una consulta implica agregar datos o realizar cálculos complejos, el nodo inicial es responsable de realizar el procesamiento adecuado. Si el sistema tiene que admitir un número relativamente pequeño de consultas complejas, considere la posibilidad de crear un grupo de nodos de cliente para aliviar la carga de los nodos de datos. Por el contrario, si el sistema tiene que administrar un gran número de consultas sencillas, envíe estas solicitudes directamente a los nodos de datos y utilice un equilibrador de carga para distribuir las solicitudes de manera uniforme.

### Uso de réplicas para reducir la contención de consultas

Una estrategia habitual para mejorar el rendimiento de las consultas es crear varias réplicas de cada índice. Las operaciones de recuperación de datos se pueden satisfacer mediante la captura de datos de una réplica. Sin embargo, esta estrategia puede afectar gravemente el rendimiento de las operaciones de ingesta de datos, por lo que debe utilizarse con cuidado en escenarios que impliquen cargas de trabajo mixtas. Además, esta estrategia solo es beneficiosa si las réplicas se distribuyen entre los nodos y no compiten por los recursos con particiones principales que forman parte del mismo índice. Recuerde que es posible aumentar o disminuir el número de réplicas de un índice de forma dinámica.

### Uso de la memoria caché de la solicitud de particiones

Elasticsearch puede almacenar en caché los datos locales solicitados por las consultas en cada partición de la memoria. Esto permite que las consultas que tienen acceso a los mismos datos se ejecuten con más rapidez; los datos pueden recuperarse desde la memoria en lugar del almacenamiento en disco. Los datos en la memoria caché se invalidan cuando se actualiza la partición y los datos cambian; la frecuencia de las actualizaciones se rige por el valor de la configuración de *refresh\_interval* del índice. De forma predeterminada, la memoria caché de la solicitud de particiones de un índice está deshabilitada, pero puede habilitarla de la forma siguiente:

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

La memoria caché de la solicitud de particiones es más adecuada para la información que permanece relativamente estática, como datos históricos o de registro.

## Pruebas y análisis de rendimiento de agregación y de búsqueda
En esta sección se describen los resultados de una serie de pruebas que se realizaron en distintas configuraciones de índice y de clúster. Cada prueba se inició con un índice vacío que se rellenaba a medida que continuaba la prueba mediante la realización operaciones de inserción masiva (cada operación agregaba 1000 documentos). Al mismo tiempo, se repetían cada 5 segundos un número de consultas diseñadas para buscar datos específicos y para generar agregaciones. El propósito de las pruebas fue establecer cómo se veía afectado el rendimiento de las consultas por el volumen de datos.

Cada documento del índice tenía el mismo esquema. Esta tabla resume los campos del esquema:

 Nombre | Tipo | Notas |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organización | Cadena | La prueba genera 200 organizaciones únicas. |
 CustomField1 - CustomField5 |String |Hay cinco campos de cadena que se establecen en la cadena vacía.|
 DateTimeRecievedUtc |Timestamp |La fecha y la hora en que se agregó el documento.|
 Host |String |Este campo se establece en una cadena vacía.|
 HttpMethod |String |Este campo se establece en uno de los siguientes valores: "POST", "GET", "PUT".|
 HttpReferrer |String |Este campo se establece en una cadena vacía.|
 HttpRequest |String |Este campo se rellena con texto aleatorio entre 10 y 200 caracteres de longitud.|
 HttpUserAgent |String |Este campo se establece en una cadena vacía.|
 HttpVersion |String |Este campo se establece en una cadena vacía.|
 OrganizationName |Cadena |Este campo se establece en el mismo valor que el campo de la organización.|
 SourceIp |IP |Este campo contiene una dirección IP que indica el "origen" de los datos. |
 SourceIpAreaCode |Largo |Este campo se establece en 0.|
 SourceIpAsnNr |Cadena |Este campo se establece en "AS#####".|
 SourceIpBase10 |Largo |Este campo se establece en 500.|
 SourceIpCountryCode |Cadena |Este campo contiene un código de país de dos caracteres. |
 SourceIpCity |String |Este campo contiene una cadena que identifica una ciudad de un país. |
 SourceIpLatitude |Doble |Este campo contiene un valor aleatorio.|
 SourceIpLongitude |Doble |Este campo contiene un valor aleatorio.|
 SourceIpMetroCode |Largo |Este campo se establece en 0.|
 SourceIpPostalCode |Cadena |Este campo se establece en una cadena vacía.|
 SourceLatLong |Punto geográfico |Este campo se establece en un punto geográfico aleatorio.|
 SourcePort |String |Este campo se rellena con la representación de cadena de un número aleatorio.|
 TargetIp |IP |Se rellenará con una dirección IP aleatoria en el intervalo de 0.0.100.100 a 255.9.100.100.|
 SourcedFrom |String |Este campo se establece en la cadena "MonitoringCollector".|
 TargetPort |String |Este campo se rellena con la representación de cadena de un número aleatorio.|
 Rating |String |Este campo se rellena con uno de los 20 valores de cadena diferentes seleccionados al azar.|
 UseHumanReadableDateTimes |Booleano |Este campo se establece en false.|

Se realizaron las siguientes consultas como un lote por cada iteración de la prueba (los nombres en cursiva se usan para hacer referencia a estas consultas en el resto de este documento):

- ¿Cuántos documentos con cada valor de *Rating* se han incluido en los últimos 15 minutos (*Recuento por clasificación*)?

- ¿Cuántos documentos se agregaron en cada intervalo de 5 minutos durante los últimos 15 minutos (*Recuento a lo largo del tiempo*)?

- ¿Cuántos documentos de cada valor de *Rating* se han agregado para cada país en los últimos 15 minutos (*Visitas por país*)?

- ¿Qué 15 organizaciones aparecen con más frecuencia en los documentos agregados en los últimos 15 minutos (*15 organizaciones principales*)?

- ¿Cuántas organizaciones distintas aparecen en los documentos agregados en los últimos 15 minutos (*Organizaciones de recuento único*)?

- ¿Cuántos documentos se agregaron en los últimos 15 minutos (*Recuento total de visitas*)?

- ¿Cuántos valores de *SourceIp* diferentes aparecen en los documentos agregados en los últimos 15 minutos (*Recuento de IP único*)?

Las pruebas se realizaron para comprender los efectos de las variables siguientes:

- **Tipo de disco** La prueba se realizó en un clúster de seis nodos de máquinas virtuales D4 mediante un almacenamiento estándar (HDD) y se repitió en un clúster de seis nodos de máquinas virtuales DS4 con almacenamiento premium (SSD).

- **Tamaño de la máquina: escalado vertical**. La prueba se realizó en un clúster de seis nodos que incluyen máquinas virtuales DS3 (designadas como el clúster *pequeño*), repetida en un clúster de máquinas virtuales DS4 (el clúster *mediano*) y se repitió en un clúster de máquinas DS14 (el clúster *grande*). En la tabla siguiente se resumen las características clave de cada SKU de máquina virtual:

Clúster |SKU de la máquina virtual |Número de núcleos |Número de discos de datos |RAM (GB)|
--------- |--------------- |----------------- |---------------------- |--------|
Pequeña |DS3 estándar |4 |8 |14 |
Mediano |DS4 estándar |8 |16 |28 |
Grande |DS14 estándar |16 |32 |112 |

- **Valores de documentos**. Inicialmente las pruebas se realizaron con la configuración de *doc\_values* del índice establecida en *true*. Las pruebas seleccionadas se repitieron con *doc\_values* establecido en *false*.

## Resultados de rendimiento: tipo de disco

En la tabla siguiente se resumen los tiempos de respuesta del trabajo realizado al ejecutar de la prueba en el clúster de 6 nodos de máquinas virtuales de D4 (con HDD) y en el clúster de 6 nodos de máquinas virtuales de DS4 (con SSD). La configuración de Elasticsearch en ambos clústeres era la misma. Los datos se difundieron por los 16 discos en cada nodo y cada nodo tenía 14 GB de memoria RAM asignada a JVM que ejecutaba Elasticsearch; se dejó la memoria restante (también 14 GB) para uso del sistema operativo. Cada prueba se ejecutó durante 24 horas. Este período se seleccionó para habilitar los efectos del volumen creciente de datos que se volvió evidente y para permitir que el sistema se estabilice:

 Clúster |Operación o consulta |Tiempo de respuesta promedio (ms)|
 -----------|---------------------------- |--------------------------|
 D4 |Ingesta de datos |978 |
 |Recuento por clasificación |103 |
 |Recuento a lo largo del tiempo |134 |
 |Visitas por país |199 |
 |15 organizaciones principales |137 |
 |Organizaciones de recuento único |139 |
 |Recuento de IP único |510 |
 |Recuento total de visitas |89 |
 DS4 |Ingesta de datos |511 |
 |Recuento por clasificación |187 |
 |Recuento a lo largo del tiempo |411 |
 |Visitas por país |402 |
 |15 organizaciones principales |307 |
 |Organizaciones de recuento único |320 |
 |Recuento de IP único |841 |
 |Recuento total de visitas |236 |

A primera vista, parecería que el clúster DS4 ha realizado consultas con menos eficiencia que el clúster D4, a veces duplicando (o incluso peor) el tiempo de respuesta. Sin embargo, esto no muestra la imagen completa. En la tabla siguiente se muestra el número de operaciones de ingesta realizadas por cada clúster (recuerde que cada operación carga 1000 documentos):

 Clúster | Operaciones de ingesta
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

El clúster DS4 fue capaz de cargar casi dos veces más datos que el clúster D4 durante la prueba. Por lo tanto, al analizar los tiempos de respuesta para cada operación, también debe tener en cuenta cuántos documentos tiene que examinar cada consulta y cuántos documentos se devuelven.

Estas son cifras dinámicas, ya que el volumen de los documentos en el índice aumenta continuamente. No se puede dividir simplemente 503137 por 264769 (el número de operaciones de ingesta realizadas por cada clúster) y después multiplicar el resultado por el tiempo medio de respuesta para cada consulta realizada por el clúster D4 para dar una cifra comparativa, ya que se ignora la cantidad de E/S que se realiza simultáneamente por la operación de ingesta.

En su lugar, debe medir la cantidad física de los datos que se escriben en el disco, y se leen de él, mientras se realiza la prueba. El plan de pruebas JMeter captura esta información para cada nodo. Los resultados resumidos fueron los siguientes:

 Clúster |Promedio de bytes escritos o leídos por cada operación|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

Estas ilustraciones muestran que el clúster DS4 fue capaz de soportar una tasa de E/S aproximadamente 1,8 veces la del clúster D4. Dado que, además de la naturaleza de los discos, todos los demás recursos son iguales, la diferencia puede deberse al uso de SSD en lugar de HDD.

Para ayudar a justificar esta conclusión, los gráficos siguientes muestran cómo se realizó la E/S a lo largo del tiempo por cada clúster:

![](./media/guidance-elasticsearch/query-performance2.png)

El gráfico del clúster D4 muestra una variación considerable, especialmente durante la primera mitad de la prueba. Esto fue probablemente debido a la limitación para reducir la velocidad de E/S. En las fases iniciales de la prueba, las consultas pueden ejecutarse rápidamente, ya que hay pocos datos para analizar. Los discos del clúster D4, por tanto, suelen estar funcionando cerca de su capacidad IOPS, aunque cada operación de E/S no puede devolver demasiados datos. El clúster DS4 es capaz de admitir una mayor tasa de IOPS y no presenta el mismo grado de limitación; las tasas de E/S son más regulares.

Para ilustrar esta teoría, el siguiente par de gráficos muestra cómo la E/S del disco ha bloqueado la CPU a lo largo del tiempo (los tiempos de espera de disco que se muestran en los gráficos son la proporción de tiempo que la CPU ha dedicado a esperar la E/S):

![](./media/guidance-elasticsearch/query-performance3.png)

Es importante comprender que, en este escenario de prueba, hay dos motivos predominantes para que las operaciones de E/S bloqueen la CPU:

- El subsistema de E/S se podría leer o escribir los datos hacia o desde el disco.

- El subsistema de E/S podría estar limitado por el entorno de host. Los discos de Azure respaldados por el almacenamiento estándar tienen un rendimiento máximo de 500 IOPS, mientras que los respaldados por el almacenamiento premium tienen un rendimiento máximo de IOPS de 5000.

Para el clúster D4, el tiempo dedicado a esperar por la E/S durante la primera mitad de la prueba se correlaciona estrechamente de manera invertida con el gráfico que muestra las tasas de E/S; los períodos de baja E/S se corresponden a los períodos de tiempo que la CPU se encuentra bloqueada.

Esto indica que se está limitando la E/S. A medida que se agregan más datos al clúster, la situación cambia y, en la segunda mitad de los picos de prueba en E/S, los tiempos de espera se corresponden a los picos de rendimiento de E/S. En este punto, la CPU se bloquea al realizar la E/S real. De nuevo, con el clúster DS4, el tiempo dedicado a esperar la E/S es mucho más uniforme y cada pico coincide con un pico de rendimiento de E/S, en lugar de con un valle; esto implica que se produce muy poca limitación, o ninguna.

Hay otro factor a tener en cuenta. Durante la prueba, el clúster D4 genera 10584 errores de ingesta y 21 errores de consulta. La prueba en el clúster DS4 no había producido ningún error.

## Resultados de rendimiento: escalado vertical

En la tabla siguiente se resumen los resultados de las pruebas en los clústeres mediano (DS4) y grande (DS14). Cada máquina virtual usa SSD para almacenar los datos. Cada prueba se ejecutó durante 24 horas:

| Clúster |Operación o consulta |Número de solicitudes |Tiempo de respuesta promedio (ms)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| Mediano (DS4) |Ingesta de datos |503157 |511 |
| |Recuento por clasificación |6958 |187 |
| |Recuento a lo largo del tiempo |6958 |411 |
| |Visitas por país |6958 |402 |
| |15 organizaciones principales |6958 |307 |
| |Organizaciones de recuento único |6956 |320 |
| |Recuento de IP único |6955 |841 |
| |Recuento total de visitas |6958 |236 |
| Grande (DS14) |Ingesta de datos |502714 |511 |
| |Recuento por clasificación |7041 |201 |
| |Recuento a lo largo del tiempo |7040 |298 |
| |Visitas por país |7039 |363 |
| |15 organizaciones principales |7038 |244 |
| |Organizaciones de recuento único |7037 |283 |
| |Recuento de IP único |7037 |681 |
| |Recuento total de visitas |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## Resultados de rendimiento: valores de documentos

Las pruebas de ingesta y de consulta se realizaron con valores de documentos habilitados, causando que Elasticsearch almacenase los datos usados para ordenar los campos en el disco. Las pruebas se repitieron con valores de documentos deshabilitados, por lo que Elasticsearch construyó la estructura fielddata dinámicamente y la almacenó en la memoria caché. Todas las pruebas se ejecutaron durante 24 horas.
 
 En la tabla siguiente se comparan los tiempos de respuesta de las pruebas que se ejecutaron en clústeres de seis nodos construidos con máquinas virtuales D4, DS4 y DS14.

| Clúster |Operación o consulta |Valores de documentos habilitados (ms) | Valores de documentos deshabilitados (ms) | % de diferencia |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |Ingesta de datos | 978 | 835 | -15 % |
| |Recuento por clasificación | 103 | 132 | +28 % |
| |Recuento a lo largo del tiempo | 134 | 189 | +41 % |
| |Visitas por país | 199 | 259 | +30 % |
| |15 organizaciones principales | 137 | 184 | +34 % |
| |Organizaciones de recuento único | 139 | 197 | +42 % |
| |Recuento de IP único | 510 | 604 | +18 % |
| |Recuento total de visitas | 89 | 134 | +51 % |
| DS4 |Ingesta de datos | 511 | 581 | +14 % |
| |Recuento por clasificación | 187 | 190 | +2 % |
| |Recuento a lo largo del tiempo | 411 | 409 | -0,5 % |
| |Visitas por país | 402 | 414 | +3 % |
| |15 organizaciones principales | 307 | 284 | -7 % |
| |Organizaciones de recuento único | 320 | 313 | -2 % |
| |Recuento de IP único | 841 | 955 | +14 % |
| |Recuento total de visitas | 236 | 281 | +19 % |
| DS14 |Ingesta de datos | 511 | 571 | +12 % |
| |Recuento por clasificación | 201 | 232 | +15 % |
| |Recuento a lo largo del tiempo | 298 | 341 | +14 % |
| |Visitas por país | 363 | 457 | +26 % |
| |15 organizaciones principales | 244 | 338 | +39 % |
| |Organizaciones de recuento único | 283 | 350 | +24 % |
| |Recuento de IP único | 681 | 909 | +33 % |
| |Recuento total de visitas | 200 | 245 | +23 % |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->