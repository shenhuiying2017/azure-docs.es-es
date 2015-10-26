<properties 
	pageTitle="Actualización a Base de datos SQL de Azure V12" 
	description="Explica cómo actualizar a Base de datos SQL de Azure V12 desde una versión anterior de Base de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="10/08/2015" 
	ms.author="sstein"/>


# Actualización a Base de datos SQL de Azure V12


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


Base de datos SQL V12 es la versión más reciente de la Base de datos SQL y tiene [muchas ventajas respecto a la versión V2 anterior](sql-database-v12-whats-new.md). En este artículo se muestra cómo actualizar los servidores de V2 a V12 mediante el Portal de vista previa de Azure.

Durante el proceso de actualización a Base de datos SQL V12 también debe [actualizar todas las bases de datos Web y Business a un nuevo nivel de servicio](sql-database-upgrade-new-service-tiers.md). Las instrucciones siguientes incluyen los pasos para actualizar las bases de datos Web y Business con recomendaciones de planes de tarifa basadas en el uso histórico de la base de datos.



1. En el [Portal de vista previa de Azure](http://portal.azure.com/) vaya al servidor que desea actualizar seleccionando **EXAMINAR TODO** > **Servidores SQL** y seleccione el servidor deseado.
2. Seleccione **Última actualización de base de datos SQL** y, a continuación, seleccione **Actualizar este servidor**.

      ![actualizar el servidor][1]

## Actualización de las bases de datos Web y Business

2. Actualice todas las bases de datos Web y Business. Si el servidor tiene alguna base de datos Web o Business, debe actualizarla. Para ayudarle con la actualización, el servicio Base de datos SQL recomienda un nivel de servicio apropiado y un nivel de rendimiento (plan de tarifa) para cada base de datos. Al analizar el uso histórico para de base de datos, el servicio recomienda el nivel que mejor se adapte para ejecutar las cargas de trabajo de la base de datos existente. 
    
    Seleccione cada base de datos para su revisión y seleccione el plan de tarifa recomendado al que desea actualizar. Siempre puede examinar los planes de tarifa disponibles y seleccionar el que mejor se adapte a su entorno.

     ![bases de datos][2]



7. Tras hacer clic en el nivel sugerido, aparecerá la hoja **Elija su nivel de precios**, donde puede seleccionar un nivel y luego hacer clic en el botón **Seleccionar** para cambiar a ese nivel. Selección de un nuevo nivel para cada base de datos Web o Business

    ![de películas][6]


Cuando todas las bases de datos del servidor sean aptas, estará listo para iniciar la actualización.

## Iniciar la actualización

3. Cuando todas las bases de datos del servidor sean aptas para su actualización, tendrá que **ESCRIBIR EL NOMBRE DEL SERVIDOR** para verificar que desea realizar la actualización y, a continuación, hacer clic en **Aceptar**. 

    ![comprobar la actualización][3]


4. La actualización se inicia y muestra la notificación del progreso en curso. Se inicia el proceso de actualización. Dependiendo de los detalles de las bases de datos específicas, la actualización a V12 puede tardar algún tiempo. Durante este tiempo, todas las bases de datos en este servidor permanecerán en línea, pero se limitarán las acciones de administración de base de datos y servidor.

    ![actualización en curso][4]

    En el momento de la transición real al nuevo nivel de rendimiento, puede producirse una caída temporal de las conexiones a la base de datos de muy poca duración (se suele medir en segundos). Si una aplicación tiene gestión de errores temporal (lógica de reintento) para terminaciones de conexiones, es suficiente protegerse frente a las conexiones interrumpidas al final de la actualización.

5. Una vez completada la operación de actualización, se habilitan las características del servidor de Base de datos SQL V12.

    ![V12 habilitado][5]


## Vínculos relacionados

- [Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Planeación y preparación para realizar la actualización a Base de datos SQL V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO3-->