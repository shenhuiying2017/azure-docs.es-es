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
	ms.date="2/10/2015" 
	ms.author="jgao"/>

#Problemas conocidos y limitaciones de Azure Stream Analytics en su versión preliminar

En este documento se describen las limitaciones y los problemas conocidos de [Azure Stream Analytics][stream.analytics.documentation] durante la versión preliminar.  En la mayoría de los casos estos límites existen con la intención de obtener sus comentarios anticipados o según las restricciones de capacidad actual. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##En este artículo
+ [Limitaciones](#limitations) 
+ [Notas de la versión y problemas conocidos](#knownissues)
+ [Pasos siguientes]

##<a name="limitations"></a> Limitaciones

###Disponibilidad regional
Los trabajos de Stream Analytics solo se pueden aprovisionar en las regiones de Europa occidental y Centro de EE. UU.

###Escala 
**Cuota de unidad de streaming**

Debido a las restricciones actuales de capacidad, se aplica una cuota de 12 unidades de streaming por región y por suscripción. Para obtener más información, consulte [Trabajos de escala de Azure Stream Analytics][stream.analytics.scale.jobs]. Si tiene la necesidad empresarial de flexibilizar este límite, llame al [soporte técnico de Microsoft][microsoft.support] y haremos lo posible por adaptarnos dentro de las restricciones de la oferta pública. 

**Utilización de unidades de streaming**

En esta versión preliminar, el número de unidades de streaming proporcionadas a un trabajo puede ser a veces mayor que la cantidad seleccionada o facturada.  Además, las unidades de streaming no se reducirán, lo que significa que el rendimiento observado podría ser mayor que el garantizado en función de la disponibilidad de los recursos de cálculo.

**Clave de partición**

Al escalar la consulta de forma horizontal con PARTITION BY, el campo por el que se particiona debe ser PartitionId.  El particionamiento en otros campos definidos por el usuario se habilitará en una versión futura.
Para obtener más información sobre la forma de escalar su trabajo, consulte [Escalación de trabajos de Azure Stream Analytics][stream.analytics.scale.jobs].

###Entradas



**Codificación de caracteres**

Para los orígenes de entrada CSV y JSON, UTF-8 es el único formato de codificación admitido.


###Complejidad de la consulta
El número máximo de funciones de agregado compatibles en una definición de consulta de trabajo de Stream Analytics es siete.

###Administración del ciclo de vida

**Actualización del trabajo**

En este momento, Stream Analytics no admite modificaciones dinámicas en la definición o la configuración de un trabajo en ejecución.  Para cambiar la entrada, salida, la consulta, la escala o la configuración de un trabajo en ejecución, primero debe detener el trabajo.

**Reinicio y detención de un trabajo**

Al detener un trabajo no se conserva ningún estado sobre el progreso del trabajo, lo que significa que actualmente no hay ninguna manera de configurar un trabajo reiniciado para reanudar desde donde se detuvo la última vez.  Ésta es una limitación que se solucionará en una versión futura.  Para conocer las prácticas recomendadas sobre cómo iniciar y detener los trabajos, consulte [Guía para desarrolladores de Azure Stream Analytics][stream.analytics.developer.guide]. 

###Supervisión
Algunas métricas relacionadas con el uso y el rendimiento del trabajo, como la latencia, no están disponibles en la versión preliminar.  También, la versión preliminar solo incluye la capacidad de procesamiento en términos del número de eventos, no del tamaño.

##<a name="knownissues"></a>Notas de la versión y problemas conocidos

Esta sección contiene una lista de problemas conocidos de Azure Stream Analytics. Su contenido cambiará con el tiempo a medida que retiremos elementos de la lista, detectemos nuevos problemas o sepamos más sobre los problemas existentes.


###Permisos elevados necesarios para el centro de eventos
En este momento, Stream Analytics requiere una directiva de acceso compartido con permisos para administrar para los orígenes de entrada y salida del centro de eventos.

###Retraso en la configuración de la cuenta de almacenamiento de Azure
Al crear un trabajo de Stream Analytics en una región por primera vez, se le pedirá que cree una nueva cuenta de almacenamiento o que especifique una cuenta existente para la supervisión de trabajos de Stream Analytics en esa región.  Debido a la latencia en la configuración de la supervisión, al crear otro trabajo de Stream Analytics en la misma región al cabo de 30 minutos se le solicitará que especifique una segunda cuenta de almacenamiento en lugar de mostrar la configurada recientemente en la lista desplegable de supervisión de cuentas de almacenamiento.  Para evitar la creación de una cuenta de almacenamiento innecesaria, espere 30 minutos después de crear un trabajo en una región por primera vez antes de aprovisionar trabajos adicionales en dicha región. 

###Los trabajos usan el grupo de consumidores predeterminado para el centro de eventos
Cuando se especifica un centro de eventos como entrada, los trabajos de Stream Analytics usarán el grupo de consumidores predeterminado para ingerir datos del centro de eventos.  Realizar esta acción sin configuración adicional significa que ningún otro receptor puede acceder al centro de eventos.  Para permitir que el centro de eventos tenga más de un receptor, se deben configurar grupos de consumidores adicionales.  Para obtener más información, consulte la [Guía para desarrolladores del centro de eventos][azure.event.hubs.developer.guide].

###Adición de una entrada y una salida: centro de eventos 
La tercera página de los cuadros de diálogo Agregar entrada y Agregar salida de los orígenes del centro de eventos tiene una lista desplegable llamada Centro de eventos que contiene una lista de espacios de nombres de bus de servicio en la suscripción actual y una opción para conectarse a un centro de eventos en una suscripción diferente.  Si desea conectarse a un centro de eventos en la misma suscripción, seleccione aquí su espacio de nombres de bus de servicio.  Si desea conectarse a un centro de eventos fuera de la suscripción, seleccione "Usar eventos concentrador de otra suscripción".  


###No puede hacer referencia al mismo paso de consulta más de una vez
En esta versión preliminar, no se puede hacer referencia a un paso de subconsulta predeterminado definido con la palabra clave WITH más de una vez.  Un escenario común al que esto puede afectar es una autocombinación con alias del mismo paso.  Para solucionar este comportamiento, cree dos pasos independientes con la misma subconsulta y nombres diferentes.

###Las conversiones de tipo no compatibles dan lugar a valores NULL
Cualquier valor de evento con conversiones de tipos no admitidas en la sección Tipos de datos de [Referencia del lenguaje de consulta de Azure Stream Analytics][stream.analytics.query.language.reference] dará lugar a un valor NULL.  En esta versión preliminar no existe ningún registro de errores para estas excepciones de conversión. 

###Problema de memoria agotada
Los trabajos de Streaming Analytics con ventanas grandes de tolerancia para eventos sin orden o consultas complejas que mantienen una gran cantidad de estados pueden hacer que el trabajo se quede sin memoria, lo que da lugar a que este se reinicie. Las operaciones de inicio y detención serán visibles en los registros de operaciones del trabajo.  Para evitar este comportamiento, escale horizontalmente el resultado de la consulta entre varias particiones.  En una versión futura se abordará esta limitación de forma que provoque la degradación del rendimiento de los trabajos y no que se reinicien.

###La partición vacía del centro de eventos en consultas particionadas da lugar a que no haya salidas
Cuando se ejecuta una consulta particionada con una subconsulta no particionada como segundo paso, si una de las particiones del centro de eventos en la entrada está completamente vacía, la consulta no generará resultados. Un error relacionado con esto se reflejará en los registros de operaciones para el trabajo.  Asegúrese de que todas las particiones del centro de eventos tengan eventos entrantes en todo momento para evitar este problema.

###Limitación del volumen de eventos de Base de datos SQL
Cuando se utiliza Base de datos SQL como origen de salida, volúmenes de datos de salida muy elevados pueden hacer que se agote el tiempo de espera del trabajo de Stream Analytics. Para resolver este problema, reduzca el volumen de salida con agregados u operadores de combinación o elija el almacenamiento de blobs o el centro de eventos como origen de salida.

###Las entradas de blobs grandes no se admiten
El consumo de archivos grandes del almacenamiento de blobs puede provocar que los trabajos de Stream Analytics se bloqueen.  Para evitar este problema, mantenga todos los blobs con un tamaño por debajo de 10 MB.

###LEFT OUTER JOIN no se admite
La operación LEFT OUTER JOIN está habilitada en el lenguaje de consulta de Stream Analytics, pero no admite.  La ejecución de una consulta con LEFT OUTER JOIN durante más de unos minutos, provocará problemas de consumo de memoria.  No se recomienda usar esta operación en ningún escenario que no sea la experimentación de consultas de corta duración.  Esta limitación se solucionará en una próxima versión.


##<a name="nextsteps"></a>Pasos siguientes

- [Introducción a Azure Stream Analytics][stream.analytics.introduction]
- [Introducción al uso de Azure Stream Analytics][stream.analytics.get.started]
- [Escalación de trabajos de Azure Stream Analytics][stream.analytics.scale.jobs]
- [Problemas conocidos y limitaciones de Azure Stream Analytics][stream.analytics.limitations]
- [Referencia del lenguaje de consultas de Azure Stream Analytics][stream.analytics.query.language.reference]
- [Referencia de la API de REST de administración de Azure Stream Analytics][stream.analytics.rest.api.reference] 

<!--Anchors-->
[Limitaciones]: #Limitations
[Notas de la versión y problemas conocidos]: #Release-notes-and-known-issues
[Pasos siguientes]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.limitations]: ../stream-analytics-limitations/


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Vínculo 1 a otro tema de documentación de azure.microsoft.com]: ../virtual-machines-windows-tutorial/
[Vínculo 2 a otro tema de documentación de azure.microsoft.com]: ../web-sites-custom-domain-name/
[Vínculo 3 a otro tema de documentación de azure.microsoft.com]: ../storage-whatis-account/

<!--HONumber=46--> 
