<properties
   pageTitle="Información general sobre rendimiento y escala | Microsoft Azure"
   description="Introducción a las características de rendimiento y escala de Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="barbkess;sonyama"/>

# Información general sobre rendimiento y escala
Al colocar el Almacenamiento de datos en la nube, ya no es necesario preocuparse por los problemas de hardware de bajo nivel. Lejos quedan los días en que había que investigar qué tipo de procesadores, qué cantidad de memoria o qué tipo de almacenamiento era necesario para obtener un rendimiento excelente en el almacenamiento de datos. En su lugar, Almacenamiento de datos SQL formula esta pregunta: ¿con qué rapidez se desean analizar los datos?

Almacenamiento de datos SQL es una plataforma de base de datos distribuida basada en la nube y diseñada para ofrecer un gran rendimiento de escala cuando se tiene el control total de los recursos usados para resolver las consultas. Simplemente ajustando el número de unidades de almacenamiento de datos asignada al almacenamiento de datos, puede escalar con flexibilidad el tamaño de los recursos de almacenamiento en cuestión de segundos. Como plataforma distribuida, de escala horizontal, Almacenamiento de datos SQL permite procesar grandes volúmenes de datos de manera eficiente y eficaz mientras permite mantener el control total del costo de la solución.

*La siguiente información se aplica a Almacenamiento de datos SQL de Azure en su versión preliminar. Esta información se actualizará continuamente durante la versión preliminar, ya que el servicio mejora según se avanza hacia la versión de disponibilidad general.*

Nuestros objetivos para Almacenamiento de datos SQL son:
-	Un rendimiento predecible y una escalabilidad lineal hasta petabytes de datos.
-	Alta confiabilidad para todas las operaciones de almacenamiento de datos, respaldadas por un Contrato de nivel de servicio (SLA).
-	Poco tiempo desde la carga de los datos hasta obtener información detallada sobre los datos en datos no relacionales y relacionales.

Trabajaremos sin pausa en estos objetivos durante la versión preliminar para cumplirlos antes de la disponibilidad general (GA).

>[AZURE.NOTE] En las secciones siguientes se describe el servicio Almacenamiento de datos SQL Azure en el lanzamiento de la versión preliminar pública. Esta información se actualizará continuamente durante la versión preliminar, ya que el servicio mejora según se avanza hacia la versión de disponibilidad general.

## Protección de datos
Almacenamiento de datos SQL almacena todos los datos en el Almacenamiento de Azure con blobs de redundancia geográfica. Se mantienen tres copias sincrónicas de los datos en la región de Azure local para garantizar la protección de datos transparente en caso de errores localizados (por ejemplo, errores en la unidad de almacenamiento). Además, se mantienen tres copias asincrónicas más en una región remota de Azure para garantizar la protección de los datos en caso de errores regionales (recuperación ante desastres). Las regiones locales y remotas están emparejadas para mantener latencias de sincronización aceptables (p. ej., Este de EE. UU y Oeste de EE. UU).

## Restauración de base de datos
Almacenamiento de datos SQL realiza una copia de seguridad de todos los datos cada 4 horas mediante instantáneas de Almacenamiento de Azure. Estas instantáneas se mantienen durante 7 días de forma gratuita. Esto permite restaurar los datos en un máximo de 42 momentos anteriores en los últimos 7 días hasta la hora en que se tomó la última instantánea. Durante la versión preliminar, se introducirá la capacidad de especificar valores de retención diferentes. Los datos pueden restaurarse a partir de una instantánea mediante PowerShell o las API de REST. Las instantáneas se copian de forma asincrónica en una región de Azure remota para aumentar la capacidad de recuperación en caso de errores regionales (recuperación ante desastres).

## Confiabilidad
Almacenamiento de datos SQL es un sistema distribuido con varios componentes, donde el número de componentes crece a medida que aumenta el número de unidades de almacenamiento de datos (DWU). Almacenamiento de datos SQL automáticamente detecta y soluciona errores de proceso; sin embargo, una operación (por ejemplo, la carga o la consulta de datos) puede producir un error debido a errores de proceso individuales. Durante la versión preliminar, haremos mejoras continuas en la confiabilidad de las consultas para que la mayoría de las operaciones se completen correctamente a pesar de los errores.

En función de los datos de telemetría recopilados, la confiabilidad de Almacenamiento de datos SQL se estima en el 98% para las cargas de trabajo de almacenamiento de datos más habituales. Esto significa que, como promedio, 2 de cada 100 consultas pueden dar error debido a errores del sistema. Este no es un SLA para la versión preliminar, más bien es un indicador de la confiabilidad esperada de las consultas que se ejecutan. Observe que la probabilidad de que una consulta dé error aumenta en función de su tiempo de ejecución (por ejemplo, una consulta que tarde más de 2 horas tiene una probabilidad mucho mayor de error que otra que tome menos de 10 minutos). Durante la versión preliminar se harán mejoras continuas para garantizar el mismo nivel de confiabilidad en las operaciones, con independencia de su tiempo de ejecución. Actualizaremos la confiabilidad esperada a medida que publicamos estas mejoras con el objetivo de ofrecer un SLA completo con el lanzamiento de disponibilidad general.

Durante la versión preliminar, Almacenamiento de datos SQL se actualizará periódicamente con el fin de agregar nuevas características y de instaslar correcciones críticas. Estas actualizaciones pueden interrumpir y en este momento las actualizaciones no se realizan con una programación previsible. Si piensa que este proceso provoca demasiadas interrupciones, le animamos a [crear una incidencia de soporte técnico][] para que podemos ayudarlo a solucionar este proceso.

## Rendimiento y escalabilidad
El servicio Almacenamiento de datos SQL presenta las unidades de almacenamiento de datos (DWU) como una abstracción de los recursos informáticos (UPC, memoria, E/S de almacenamiento) que se agregan en una serie de nodos. Aumentar el número de DWU aumenta los recursos informáticos agregados de una instancia de Almacenamiento de datos SQL. El servicio Almacenamiento de datos SQL distribuye las operaciones (por ejemplo, carga o consulta de datos) por toda la infraestructura de proceso de la instancia para aumentar o disminuir el rendimiento de las cargas y consultas a medida que el sistema se escala vertical u horizontalmente.

Cualquier almacén de datos tiene 2 métricas de rendimiento fundamentales:
- **Velocidad de carga**: número de registros que se pueden cargar en el almacén de datos por segundo. Medimos específicamente el número de registros que se pueden importar a través de PolyBase, de Almacenamiento de blobs de Azure a una tabla con un índice de almacén-columna en clústeres.
- **Velocidad de exploración**: número de registros que se pueden recuperar de forma secuencial del almacén de datos por segundo. Esta métrica mide específicamente el número de registros devueltos por una consulta de una tabla con un índice agrupado de almacén de columnas.

Durante la versión preliminar, realizaremos mejoras continuas para aumentar estas métricas de rendimiento y asegurar que se escalan de forma predecible.

## Principales conceptos sobre rendimiento

Consulte los artículos siguientes para comprender mejor algunos conceptos fundamentales adicionales sobre rendimiento y escala:

- [rendimiento y escala][]
- [modelo de simultaneidad][]
- [diseño de tablas][]
- [selección de una clave de distribución hash para una tabla][]
- [estadísticas para mejorar el rendimiento][]

## Pasos siguientes
Consulte el artículo [Introducción al desarrollo][] para obtener orientación sobre la creación de soluciones de Almacenamiento de datos SQL.

<!--Image references-->

<!--Article references-->

[rendimiento y escala]: sql-data-warehouse-performance-scale.md
[modelo de simultaneidad]: sql-data-warehouse-develop-concurrency.md
[diseño de tablas]: sql-data-warehouse-develop-table-design.md
[selección de una clave de distribución hash para una tabla]: sql-data-warehouse-develop-hash-distribution-key.md
[estadísticas para mejorar el rendimiento]: sql-data-warehouse-develop-statistics.md
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md
[crear una incidencia de soporte técnico]: sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0420_2016-->