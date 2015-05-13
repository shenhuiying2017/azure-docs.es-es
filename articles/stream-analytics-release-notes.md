<properties 
	pageTitle="Notas de la versión de Análisis de transmisiones | Azure" 
	description="Notas de la versión de Análisis de transmisiones GA" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Retraso en la configuración de la cuenta de almacenamiento de Azure

Al crear un trabajo de Análisis de transmisiones en una región por primera vez, se le pedirá que cree una nueva cuenta de almacenamiento o que especifique una cuenta existente para la supervisión de trabajos de Análisis de transmisiones en esa región. Debido a la latencia en la configuración de la supervisión, al crear otro trabajo de Análisis de transmisiones en la misma región al cabo de 30 minutos se le solicitará que especifique una segunda cuenta de almacenamiento en lugar de mostrar la configurada recientemente en la lista desplegable de supervisión de cuentas de almacenamiento. Para evitar la creación de una cuenta de almacenamiento innecesaria, espere 30 minutos después de crear un trabajo en una región por primera vez antes de aprovisionar trabajos adicionales en dicha región.

##Actualización del trabajo

En este momento, Análisis de transmisiones no admite modificaciones dinámicas en la definición o la configuración de un trabajo en ejecución. Para cambiar la entrada, salida, la consulta, la escala o la configuración de un trabajo en ejecución, primero debe detener el trabajo.

##Tipos de datos deducidos del origen de entrada

Si no se utiliza una instrucción **CREATE TABLE**, el tipo de entrada se deriva del formato de entrada; por ejemplo, todos los campos de CSV son cadenas. Los campos deben convertirse explícitamente al tipo adecuado mediante la función CAST para evitar errores de coincidencia de tipos.

##Los campos que faltan generan valores null

Hacer referencia a un campo que no está presente en el origen de entrada dará como resultado valores null en el evento de salida.

##Las instrucciones WITH deben preceder a las instrucciones SELECT

En una consulta, las instrucciones SELECT deben seguir a subconsultas definidas en instrucciones WITH.

##Problema de memoria agotada

Los trabajos de Análisis de transmisiones con una gran tolerancia para eventos sin orden o consultas complejas que mantienen una gran cantidad de estados pueden hacer que el trabajo se quede sin memoria, lo que da lugar a que este se reinicie. Las operaciones de inicio y detención serán visibles en los registros de operaciones del trabajo. Para evitar este comportamiento, escale horizontalmente el resultado de la consulta entre varias particiones. En una versión futura se abordará esta limitación de forma que provoque la degradación del rendimiento de los trabajos y no que se reinicien.

##Las entradas de blobs grandes sin marca de tiempo de carga pueden causar un problema de falta de memoria

El consumo de grandes archivos desde el almacenamiento de blobs puede hacer que los trabajos de Análisis de transmisiones se bloquee si no se especifica un campo de marca de tiempo a través de marca de tiempo. Para evitar este problema, mantenga el tamaño de todos los blobs por debajo de 10 MB.

##Limitación del volumen de eventos de Base de datos SQL

Cuando se utiliza la Base de datos SQL como destino de la salida, unos volúmenes de datos de salida muy elevados pueden hacer que se agote el tiempo de espera del trabajo de Análisis de transmisiones. Para resolver este problema, reduzca el volumen de salida con agregados u operadores de filtro o elija el almacenamiento de blobs de Azure o los centros de eventos como destino de la salida.

##Los conjuntos de datos PowerBI solo pueden contener una tabla

PowerBI no admite más de una tabla en un conjunto de datos determinado.

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream.analytics.get.started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=52-->
