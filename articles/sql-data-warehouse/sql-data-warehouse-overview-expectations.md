<properties
   pageTitle="Expectativas de la versión preliminar de Almacenamiento de datos SQL | Microsoft Azure"
   description="Resumen de las capacidades de la versión preliminar pública y los objetivos de disponibilidad general de Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Expectativas de la versión preliminar de Almacenamiento de datos SQL

En este artículo se describen las funcionalidades de la versión preliminar de Almacenamiento de datos SQL y nuestros objetivos de disponibilidad general del servicio. Actualizaremos esta información de manera continua mientras mejoramos las capacidades de la versión preliminar pública.

Nuestros objetivos para el Almacenamiento de datos SQL:

- Rendimiento predecible y escalabilidad lineal hasta petabytes de datos
- Alta confiabilidad para todas las operaciones de almacenamiento de datos
- Poco tiempo desde la carga de los datos hasta obtener información detallada sobre los datos en datos no relacionales y relacionales.

Seguiremos trabajando para lograr estos objetivos durante la versión preliminar de Almacenamiento de datos SQL.

## Rendimiento predecible y escalable

Almacenamiento de datos SQL de Azure presenta Unidades de almacenamiento de datos (DWU) como una forma de medir los recursos informáticos (CPU, memoria, E/S de almacenamiento) disponibles para el almacenamiento de datos. Aumentar el número de DWU aumenta los recursos. A medida que aumenta el número de DWU, el Almacenamiento de datos SQL ejecuta operaciones en paralelo (por ejemplo, la carga o la consulta de datos) a través de recursos más distribuidos. Esto reduce la latencia y mejora el rendimiento.

Cualquier almacén de datos tiene 2 métricas de rendimiento fundamentales:

- Velocidad de carga. El número de registros que se pueden cargar en el almacén de datos por segundo. Medimos específicamente el número de registros que se pueden importar a través de PolyBase, de Almacenamiento de blobs de Azure a una tabla con un índice de almacén-columna en clústeres.
- Velocidad de digitalización. El número de registros que se pueden recuperar de forma secuencial del almacén de datos por segundo. Medimos específicamente el número de registros devueltos por una consulta de un índice de almacén de columnas en clústeres.

Se están midiendo algunas mejoras importantes del rendimiento y pronto se compartirán las velocidades esperadas. Durante la versión preliminar, realizaremos mejoras continuas (por ejemplo, aumentar la compresión y el almacenamiento en caché) para aumentar estas métricas de rendimiento y asegurar que se escalan de forma predecible.

## Protección de datos

Almacenamiento de datos SQL almacena todos los datos en Almacenamiento de Azure mediante almacenamiento con redundancia local. Se mantienen varias copias sincrónicas de los datos en el centro de datos local para garantizar la protección transparente de los datos en caso de errores localizados.

## Copias de seguridad

Almacenamiento de datos SQL de Azure realiza una copia de seguridad de todos los datos cada 8 horas como mínimo mediante instantáneas de Almacenamiento de Azure. Estas instantáneas se mantienen durante 7 días. Esto permite restaurar los datos en un mínimo de 21 momentos anteriores en los últimos 7 días hasta la hora en que se tomó la última instantánea. Puede restaurar datos desde una instantánea mediante las API de PowerShell o REST.

## Confiabilidad de las consultas

Almacenamiento de datos SQL se basa en una arquitectura de procesamiento paralelo masivo (MPP). Almacenamiento de datos SQL detecta automáticamente los errores de nodo de proceso y control, realiza la migración. Sin embargo, se puede producir un error en una operación (como una carga de datos o una consulta) como resultado de un error en un nodo o la migración. En la versión preliminar, realizamos mejoras continuas para completar correctamente las operaciones a pesar de los errores de nodo.


## Actualizaciones y tiempo de inactividad

Almacenamiento de datos SQL se actualizará periódicamente con el fin de agregar nuevas características e instalar correcciones críticas. Estas actualizaciones pueden interrumpir y en este momento las actualizaciones no se realizan con una programación previsible. Si piensa que este proceso causa demasiadas interrupciones, es aconsejable que [cree una incidencia de soporte técnico][] para que podamos proporcionarle una alternativa.


## Pasos siguientes

[Introducción][] a la versión preliminar pública.

<!--Image references-->

<!--Article references-->
[cree una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Introducción]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->