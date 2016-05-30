<properties
   pageTitle="Administración de tablas e índices en Almacenamiento de datos SQL de Azure | Microsoft Azure."
   description="Información general de las consideraciones, prácticas recomendadas y tareas para administrar tablas e índices en Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Administración de tablas e índices en Almacenamiento de datos SQL de Azure.

Información general de las consideraciones, prácticas recomendadas y tareas para administrar tablas e índices en Almacenamiento de datos SQL.



| Categoría | Tarea o consideración | Descripción |
| :-----------------------| :---------------------------------------------- | :----------- |
| Índices de almacén de columnas | Recompilar índices después de cargar o insertar datos | De forma predeterminada, Almacenamiento de datos SQL almacena cada tabla como índice de almacén de columnas agrupado. Esto mejora el rendimiento y la compresión de datos, especialmente para las tablas grandes. Según como introduzca los datos en el índice de almacén de columnas, es posible que no todos los datos se almacenen con compresión del índice de columnas. Cuando esto ocurra, puede que no obtenga el rendimiento previsto para los índices de almacén de columnas. <br/><br/>Para asegurarse de que los índices de almacén de columnas tienen un estado correcto, consulte [Administración de índices de almacén de columnas en Almacenamiento de datos SQL de Azure][]. |
| Tablas con distribución hash | Comprobar que los datos se distribuyen uniformemente en todos los nodos | Las tablas con distribución hash casi siempre son la mejor manera de optimizar las combinaciones y agregaciones en tablas grandes. Almacenamiento de datos SQL distribuyó las tablas según una columna de distribución especificada. Algunas columnas son buenas claves de distribución y otras, no. El propósito es que las filas se distribuyan uniformemente. Cierta irregularidad, o asimetría de datos, es aceptable pero si tiene demasiada asimetría de datos, perderá las ventajas del proceso paralelo masivo (MPP) que Almacenamiento de datos SQL está diseñado para proporcionar.<br/><br/>Para asegurarse de que las tablas con distribución hash no tengan demasiada asimetría de datos, consulte [Administrar la asimetría de datos de tablas distribuidas en el almacenamiento de datos SQL de Azure][]. |





## Pasos siguientes

Para obtener más sugerencias de administración, consulte la información general de [administración][].

<!--Image references-->

<!--Article references-->
[Administración de índices de almacén de columnas en Almacenamiento de datos SQL de Azure]: sql-data-warehouse-manage-columnstore-indexes.md
[Administrar la asimetría de datos de tablas distribuidas en el almacenamiento de datos SQL de Azure]: sql-data-warehouse-manage-distributed-data-skew.md
[administración]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->