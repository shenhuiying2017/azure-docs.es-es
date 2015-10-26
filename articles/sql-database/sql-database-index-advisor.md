<properties 
   pageTitle="Asesor de índices de Base de datos SQL de Azure" 
   description="El Asesor de índices de Base de datos SQL recomienda índices nuevos para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/22/2015"
   ms.author="sstein"/>

# Asesor de índices de Base de datos SQL

El Asesor de índices de Base de datos SQL recomienda índices nuevos para las Bases de datos SQL existentes que pueden mejorar el rendimiento de la consulta actual.

El servicio de Base de datos SQL evalúa el rendimiento del índice mediante el análisis del uso histórico de los recursos de una Base de datos SQL y se recomiendan los índices más apropiados para ejecutar la carga de trabajo típica de la base de datos.

Para facilitar la administración de los índices, el Asesor de índices proporciona recomendaciones sobre los índices que se deben crear. En el caso de los servidores V12, el Asesor de índices también puede crear y validar índices solo con unos clics en el [Portal de vista previa de Azure](https://portal.azure.com/). Una vez que se crea el índice, el servicio de Base de datos SQL analiza el rendimiento de la carga de trabajo de la base de datos y proporciona detalles del impacto que tiene el índice nuevo. Si el análisis determina que un índice recomendado tiene un impacto negativo en el rendimiento, el índice se revierte de manera automática.

El Asesor de índices permite dedicar menos tiempo a ajustar el rendimiento de la base de datos.


> [AZURE.NOTE]El Asesor de índices actualmente se encuentra en vista previa y solo está disponible en el [Portal de vista previa de Azure](https://portal.azure.com/).


## Consideraciones de la vista previa

El Asesor de índices actualmente se encuentra en vista previa y tiene las limitaciones siguientes:

- Las recomendaciones de índice solo se pueden crear y validar de manera automática para los servidores V12 (las recomendaciones y los scripts de creación de índice se proporcionan para los servidores V12).
- La administración y las recomendaciones solo se encuentran disponibles para los índices ni agrupados en clúster.

## Requisitos previos

Para ver y crear recomendaciones de índice, necesita los permisos de [control de acceso basado en rol](role-based-access-control-configure.md) correctos en Azure.

- Para ver las recomendaciones, se requieren los permisos de **Lector** y **Colaborador de Base de datos SQL**.
- Para ejecutar cualquier acción, crear o eliminar índices y cancelar la creación de índices, se requieren los permisos de **Propietario** y **Colaborador de Base de datos SQL**.


## Uso del Asesor de índice

El Asesor de índices es fácil de usar. Siga estas instrucciones para simplificar la administración de índices de la base de datos:

- Primero, revise la lista de recomendaciones de índices y decida los índices que va a crear o que se deben omitir. La lista de recomendaciones está ordenada y etiquetada según el impacto estimado que tienen en el rendimiento (los detalles aparecen a continuación). 
- Cree u omita los índices recomendados. Cree el índice de manera automática; para ello, haga clic en **Crear índice** en el portal, o bien cree manualmente el índice a través de la ejecución del script para la creación de índices.
- En el caso de los índices creados manualmente, debe supervisar el proceso de creación y medir el impacto en el rendimiento. En el caso de los índices creados automáticamente, el servicio Base de datos SQL de Azure realiza de manera automática la supervisión y el análisis del impacto en el rendimiento. 



## Revisión de los índices recomendados

El Asesor de índices proporciona una lista de recomendaciones de índices en la hoja de base de datos del [Portal de vista previa de Azure](https://portal.azure.com/). Las principales recomendaciones seleccionadas aparecen para cada tabla en la base de datos seleccionada en que la creación de un índice nuevo puede significar ganancias en el rendimiento.

### Para revisar las recomendaciones de índices actualmente disponibles:

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com/).
2. Haga clic en **EXAMINAR** en el menú de la izquierda.
3. Haga clic en **Bases de datos SQL** en la hoja **Examinar**.
4. En la hoja **Bases de datos SQL**, haga clic en la base de datos en la que desea revisar los índices recomendados.
5. Haga clic en **Asesor de índices** para abrir y ver las **recomendaciones de índices** disponibles para la base de datos seleccionada.

> [AZURE.NOTE]Para obtener recomendaciones de índices, una base de datos debe tener alrededor de una semana de uso y, dentro de esa semana, debe haber alguna actividad. También debe haber actividad coherente. El Asesor de índice puede optimizar los patrones de consultas coherentes con más facilidad que en el caso de ráfagas irregulares de actividad.

![Índices recomendados][3]

Las recomendaciones se ordenan en las 4 siguientes categorías, según su impacto potencial en el rendimiento:

| Impacto | Descripción |
| :--- | :--- |
| Alto | Las recomendaciones de alto impacto debe tener el impacto más importante en el rendimiento. |
| Significativo | Las recomendaciones de impacto significativo deben mejorar considerablemente el rendimiento. |
| Moderado | Las recomendaciones de impacto moderado deben mejorar el rendimiento, pero no de manera significativa. |
| Bajo | Las recomendaciones de bajo impacto deben proporcionar un mejor rendimiento que el que ocurriría sin el índice, pero es posible que las mejoras no sean significativas. 
Utilice la etiqueta de impacto para determinar los mejores candidatos para la creación de índices nuevos.

### Administración de la lista de índices recomendados

Si la lista de los índices recomendados contiene índices que no considera como beneficiosos, el Asesor de índices le permite descartar las recomendaciones de índices (puede volver a agregar los índices descartados a la lista de **Índices recomendados** más adelante, si corresponde).

#### Descarte de una recomendación de índices

1. Seleccione el índice en la lista de **Índices recomendados**.
1. Haga clic en **Descartar índice** en la hoja **Detalles del índice**.

#### Visualización de los índices descartados y su incorporación nuevamente en la lista principal

1. En la hoja **Recomendaciones de índices**, haga clic en **Ver recomendaciones de índices descartadas**.
1. Seleccione un índice descartado en la lista para ver los detalles.
1. Opcionalmente, haga clic en **Deshacer descartar** para agregar el índice nuevamente a la lista principal de las **Recomendaciones de índices**.



## Creación de índices nuevos

El Asesor de índices le permite controlar completamente la manera en que se crean los índices. Cada recomendación brinda un script para la creación de índices de T-SQL y puede consultar los detalles exactos sobre cómo se creará el índice antes de realizar cualquier acción en una base de datos.

Las recomendaciones de índices están disponibles para todos los servidores de Base de datos SQL de Azure, pero solo los servidores V12 proporcionan la creación automatizada de índices. Los servidores que no son V12 también se pueden beneficiar del Asesor de índices, pero se deben crear los índices de forma manual, tal como se describe a continuación.

Para la creación de índices, tanto automática como manual, simplemente seleccione un índice recomendado en la hoja **Recomendaciones de índices** y haga lo siguiente:

### Creación automática de índices (solo servidores V12)

Si la base de datos se encuentra en un servidor V12, puede crear fácilmente un índice recomendado; para ello, seleccione el índice deseado en el portal y luego haga clic en **Crear índice**.

La base de datos permanece en línea durante la creación del índice, debido a que usar el Asesor de índices para crear un índice no hace que la base de datos quede sin conexión.

Además, los índices que se crean con **Crear índice** no requieren otra supervisión del rendimiento. Si el índice tiene un impacto negativo en el rendimiento, se revierte de manera automática. Después de usar Crear índice, las métricas del impacto del índice nuevo se encuentran disponibles en el portal.


### Creación manual de índices (todos los servidores)

Seleccione cualquier índice recomendado en el portal y luego haga clic en **Ver script**. Ejecute este script en la base de datos para crear el índice recomendado. Los índices que se crean de manera manual no se supervisan ni validan para conocer el impacto real en el rendimiento, por lo que se recomienda supervisar estos índices después de su creación para comprobar que brindan ganancias en el rendimiento y ajustarlos o eliminarlos, en caso de que sea necesario. Si desea conocer detalles sobre la creación de índices, consulte [CREAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Cancelación de la creación de índices

Es posible cancelar los índices en estado **Pendiente**. No es posible cancelar los índices que se están creando (estado **En ejecución**).

1. Seleccione cualquier índice **Pendiente** en el área **Operaciones de índice** para abrir la hoja **Detalles del índice**.
1. Haga clic en **Cancelar** para anular el proceso de creación de índices.

## Supervisión de las operaciones de índice después de la creación de índices

La creación de un índice no es inmediata. El portal proporciona detalles sobre el estado de las operaciones de índice. Cuando se administran los índices, un índice puede tener los siguientes estados:

| Estado | Descripción |
| :--- | :--- |
| Pending | Se recibió el comando para la creación de índice y el índice está programado para su creación. |
| Executing | El comando para la creación de índice está en ejecución y el índice se está creando. |
| Correcto | El índice se creó correctamente. |
| Con error | El índice no se creó. Puede tratarse de un problema transitorio, o posiblemente se produjo un cambio de esquema en la tabla y el script ya no es válido. |
| En reversión | El proceso de creación de índice se canceló o se consideró sin rendimiento, por lo que se revierte de manera automática. |



![Índices recomendados][4]



## Eliminación de un índice
Puede quitar índices creados con el Asesor de índices.


1. Seleccione un índice creado correctamente en la lista de **Operaciones de índice**.
1. Haga clic en **Quitar índice** en la hoja **Detalles del índice**, o bien haga clic en **Ver script** en un script DROP INDEX.



## Resumen

Las recomendaciones de índices brindan una experiencia automatizada para administrar la creación de índices y el análisis de cada base de datos SQL y recomendar los mejores índices. Haga clic en el icono de **Asesor de índices** en la hoja de una base de datos para ver las recomendaciones de índices.



## Pasos siguientes

Supervise las recomendaciones de índices y continúe aplicándolas para refinar el rendimiento. Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. El Asesor de índices seguirá supervisando y recomendando índices que podrían mejorar el rendimiento de la base de datos.


<!--Image references-->
[1]: ./media/sql-database-index-advisor/index-recommendations.png
[2]: ./media/sql-database-index-advisor/index-details.png
[3]: ./media/sql-database-index-advisor/recommended-indexes.png
[4]: ./media/sql-database-index-advisor/index-operations.png

<!---HONumber=Oct15_HO3-->