<properties 
	pageTitle="Limitaciones de Stream Analytics en la versión preliminar | Azure" 
	description="Obtener información acerca de las limitaciones en la versión preliminar pública de trabajos de análisis de secuencias de Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015"
	ms.author="jgao"/>

# Problemas conocidos y limitaciones de Azure Stream Analytics en su versión preliminar

En este documento se describen las limitaciones y los problemas conocidos de [Análisis de transmisiones][stream.analytics.documentation] durante la versión preliminar. En la mayoría de los casos estos límites existen con la intención de obtener sus comentarios anticipados o se basan en las restricciones de capacidad actual. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Limitaciones

### Disponibilidad regional
Los trabajos de Análisis de transmisiones solo se pueden aprovisionar en las regiones de Europa occidental y Centro de EE. UU. en la versión preliminar

### Escala 
**Cuota de unidades de streaming**

Debido a las restricciones actuales de capacidad, se aplica una cuota de 12 unidades de streaming por región y suscripción. Para obtener más información, consulte [Escalación de trabajos de Análisis de transmisiones de Azure][stream.analytics.scale.jobs]. Si tiene la necesidad empresarial de flexibilizar este límite, llame al [Servicio de soporte técnico de Microsoft][microsoft.support] y haremos lo posible por adaptarnos dentro de las restricciones de la oferta pública.

**Uso de unidades de streaming**

En esta versión preliminar, el número de unidades de streaming suministradas en un trabajo puede ser a veces mayor que la cantidad seleccionada o facturada. Además, las unidades de streaming no se reducirán, lo que significa que el rendimiento observado podría ser mayor que el garantizado, en función de la disponibilidad de los recursos de cálculo.

**Clave de partición**

Al escalar la consulta de forma horizontal con **Partition by** , el campo por el que se crea la partición debe ser **PartitionId**. El particionamiento en otros campos definidos por el usuario se habilitará en una versión futura. Para obtener más información sobre la forma de escalar su trabajo, consulte [Escalación de trabajos de Análisis de transmisiones de Azure][stream.analytics.scale.jobs].

### Entradas



**Codificación de caracteres**

Para los orígenes de entrada CSV y JSON, UTF-8 es el único formato de codificación admitido.


### Complejidad de la consulta
El número máximo de funciones de agregado compatibles en una definición de consulta de trabajo de Análisis de transmisiones es siete.

### Número de consultas
El número máximo de consultas admitidas en un origen de entrada determinado es cinco.


### Administración del ciclo de vida

**Actualización de trabajos**

En este momento, Análisis de transmisiones no admite modificaciones dinámicas en la definición o la configuración de un trabajo en ejecución. Para cambiar la entrada, salida, la consulta, la escala o la configuración de un trabajo en ejecución, primero debe detener el trabajo.

**Reinicio y detención de trabajos**

Al detener un trabajo no se conserva ningún estado sobre el progreso del trabajo, lo que significa que actualmente no hay ninguna manera de configurar un trabajo reiniciado para reanudar desde donde se detuvo la última vez. Ésta es una limitación que se solucionará en una versión futura. Para conocer las prácticas recomendadas sobre cómo iniciar y detener trabajos, consulte [Guía para desarrolladores de Análisis de transmisiones de Azure][stream.analytics.developer.guide].

### Supervisión
Algunas métricas relacionadas con el uso y el rendimiento del trabajo, como la latencia, no están disponibles en la versión preliminar. La versión preliminar también expone la capacidad de procesamiento únicamente en términos del número de eventos, no del tamaño.

## Notas de la versión y problemas conocidos

Esta sección contiene una lista de problemas conocidos de Análisis de transmisiones de Azure. Su contenido cambiará con el tiempo a medida que retiremos elementos de la lista, detectemos nuevos problemas o sepamos más sobre los problemas existentes.


### Permisos elevados necesarios para el Centro de eventos
En este momento, Análisis de transmisiones requiere una directiva de acceso compartido con permisos para administrar en los orígenes de entrada y salida del Centro de eventos.

### Retraso en la configuración de la cuenta de almacenamiento de Azure
Al crear un trabajo de Análisis de transmisiones en una región por primera vez, se le pedirá que cree una nueva cuenta de almacenamiento o que especifique una cuenta existente para la supervisión de trabajos de Análisis de transmisiones en esa región. Debido a la latencia en la configuración de la supervisión, si en los 30 minutos siguientes crea otro trabajo de Análisis de transmisiones en la misma región, se le solicitará que especifique una segunda cuenta de almacenamiento en lugar de mostrar la recién configurada en la lista desplegable **Cuenta de almacenamiento de supervisión**. Para evitar la creación de una cuenta de almacenamiento innecesaria, espere 30 minutos después de crear un trabajo en una región por primera vez antes de aprovisionar trabajos adicionales en dicha región.

### Los trabajos deben usar cada uno su propio grupo de consumidores del Centro de eventos
Cada entrada de trabajo de Análisis de transmisiones debe configurarse para tener su propio grupo de consumidores del centro de eventos. Cuando un trabajo contiene autocombinación o varias salidas, es posible que más un lector lea alguna entrada, lo que hace que el número total de lectores en un solo grupo de consumidores supere el límite del centro de eventos de 5 lectores por grupo de consumidores. En este caso, la consulta deberá dividirse en varias consultas y resultados intermedios que se enrutan a través de centros de eventos adicionales. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por centro de eventos. Para obtener más información, consulte la [Guía para desarrolladores del Centro de eventos][azure.event.hubs.developer.guide].

### Incorporación de una entrada y una salida: Centro de eventos 
La tercera página de los diálogos **Agregar entrada** y **Agregar salida** de orígenes del Centro de eventos tienen una lista desplegable llamada **Centro de eventos** que contiene una lista de espacios de nombres de Bus de servicio en la suscripción actual y una opción para conectarse a un Centro de eventos en otra suscripción. Si desea conectarse a un Centro de eventos en la misma suscripción, seleccione aquí su espacio de nombres de Bus de servicio. Si desea conectarse a un Centro de eventos fuera de la suscripción, seleccione **Usar Centro de eventos de otra suscripción**.


### No puede hacer referencia al mismo paso de consulta más de una vez
En esta versión preliminar, no se puede hacer referencia más de una vez a un determinado paso de subconsulta definido con la palabra clave **WITH**. Un escenario común al que esto puede afectar es una autocombinación con alias del mismo paso. Para solucionar este comportamiento, cree dos pasos independientes con la misma subconsulta y nombres diferentes.

### Las conversiones de tipo no admitido dan lugar a valores NULL
Cualquier valor de evento con conversiones de tipo no admitido en la sección "Tipos de datos" de [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure][stream.analytics.query.language.reference] dará lugar a un valor NULL. En esta versión preliminar no existe ningún registro de errores para estas excepciones de conversión.

### Problema de memoria agotada
Los trabajos de Análisis de transmisiones con una gran tolerancia para eventos sin orden o consultas complejas que mantienen una gran cantidad de estados pueden hacer que el trabajo se quede sin memoria, lo que da lugar a que este se reinicie. Las operaciones de inicio y detención serán visibles en los registros de operaciones del trabajo. Para evitar este comportamiento, escale horizontalmente el resultado de la consulta entre varias particiones. En una versión futura se abordará esta limitación de forma que provoque la degradación del rendimiento de los trabajos y no que se reinicien.

### Una partición de base de datos vacía del Centro de eventos en consultas particionadas no da lugar a ninguna salida
Cuando se ejecuta una consulta particionada con una subconsulta no particionada como segundo paso, si una de las particiones del Centro de eventos en la entrada está completamente vacía, la consulta no generará resultados. Un error relacionado con esto se reflejará en los registros de operaciones del trabajo. Para evitar ese problema, asegúrese de que todas las particiones del Centro de eventos tengan eventos entrantes en todo momento.

### Limitación del volumen de eventos de Base de datos SQL
Cuando se utiliza la Base de datos SQL como destino de la salida, unos volúmenes de datos de salida muy elevados pueden hacer que se agote el tiempo de espera del trabajo de Análisis de transmisiones. Para resolver este problema, reduzca el volumen de salida con agregados u operadores de unión, o bien, elija el almacenamiento de blobs de Azure o los Centros de eventos como destino de la salida.

### Las entradas de blobs grandes no se admiten
El consumo de archivos grandes en el almacenamiento de blobs puede provocar que los trabajos de Análisis de transmisiones se bloqueen. Para evitar este problema, mantenga el tamaño de todos los blobs por debajo de 10 MB.

### Espacio en blanco en encabezados de columna causa entradas de salida NULL
Análisis de transmisiones no recorta el espacio en blanco en los encabezados de columna. La inclusión de espacios en blanco al principio o al final de un nombre de columna se traducirá en entradas NULL en la salida del trabajo.


## Obtención de soporte técnico
Si necesita más ayuda, consulte el [foro de Análisis de transmisiones de Azure](stream-analytics-forum.md).


## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](stream-analytics-query-language-reference.md)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](stream-analytics-rest-api-reference.md) 

<!--Anchors-->
[Limitations]: #Limitations
[Release notes and known issues]: #Release-notes-and-known-issues
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.limitations]: stream-analytics-limitations.md


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->