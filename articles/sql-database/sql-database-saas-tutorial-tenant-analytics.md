---
title: "Ejecución de consultas de análisis en varias bases de datos de Azure SQL | Microsoft Docs"
description: "Ejecución de consultas distribuidas entre varias bases de datos de Azure SQL"
keywords: tutorial de base de datos sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: a0742a004b618dda304618bca21ae715552c16e6
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="run-distributed-queries-across-multiple-azure-sql-databases"></a>Ejecución de consultas distribuidas entre varias bases de datos de Azure SQL

En este tutorial, se ejecutan consultas de análisis en cada inquilino del catálogo y se crea un trabajo elástico que ejecuta las consultas. El trabajo recupera los datos y los carga en una base de datos de análisis independiente creada en el servidor de catálogo. Es posible consultar esta base de datos para extraer la información que se encuentra oculta entre los datos de las operaciones diarias de todos los inquilinos. Como salida del trabajo, se crea una tabla a partir de las consultas que devuelven resultados dentro de la base de datos de análisis de inquilinos.


En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear la base de datos de análisis de inquilinos
> * Crear un trabajo programado para recuperar datos y rellenar la base de datos de análisis

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* La aplicación WTP está implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [Implementación y exploración de la aplicación SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* La versión más reciente de SQL Server Management Studio (SSMS) está instalada. [Descarga e instalación de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="tenant-operational-analytics-pattern"></a>Patrón de análisis operativos de inquilino

Una de las grandes oportunidades que ofrecen las aplicaciones SaaS es el uso de los datos enriquecidos de inquilino almacenados en la nube. Puede usar dichos datos para obtener información sobre el funcionamiento y el uso de la aplicación y sobre los inquilinos. Estos datos pueden guiarle a la hora de desarrollar características, mejorar la facilidad de uso y realizar otras inversiones en la aplicación y la plataforma. Es fácil obtener acceso a estos datos cuando se encuentran en una sola base de datos multiinquilino, pero no es tan fácil cuando se distribuyen a escala en miles de bases de datos. Un método para obtener acceso a estos datos consiste en usar trabajos elásticos, que permiten capturar en una base de datos y tabla de salida los resultados de consulta que devuelven resultados a partir de la ejecución del trabajo.

## <a name="get-the-wingtip-application-scripts"></a>Obtener scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Implementar una base de datos para resultados de análisis de inquilino

Para realizar este tutorial debe tener una base de datos implementada para capturar los resultados de la ejecución de trabajos de scripts, que contienen consultas que devuelven resultados. Vamos a crear una base de datos denominada tenantanalytics con este fin.

1. Abra …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* en *PowerShell ISE* y establezca el valor siguiente:
   * **$DemoScenario** = **2** *Deploy operational analytics database*
1. Presione **F5** para ejecutar el script de demostración (que llama al script *Deploy-TenantAnalyticsDB.ps1*) que crea la base de datos de análisis de inquilino.

## <a name="create-some-data-for-the-demo"></a>Crear algunos datos para la demostración

1. Abra …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* en *PowerShell ISE* y establezca el valor siguiente:
   * **$DemoScenario** = **1** *Purchase tickets for events at all venues*
1. Presione **F5** para ejecutar el script y crear un historial de compra de entradas.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Crear un trabajo programado para recuperar el análisis de inquilino sobre la compra de entradas

Este script crea un trabajo para recuperar información sobre la compra de entradas de todos los inquilinos. Una vez agregada en una misma tabla, puede obtener métricas detalladas enriquecidas sobre los patrones de compra de entradas en los inquilinos.

1. Abra SSMS y conéctese al servidor catalog-\<usuario\>.database.windows.net.
1. Abra ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*TicketPurchasesfromAllTenants.sql*.
1. Modifique \<WtpUser\> con el nombre de usuario que empleó al implementar la aplicación WTP en la parte superior del script, **sp\_add\_target\_group\_member** y **sp\_add\_jobstep**.
1. Haga clic con el botón derecho, seleccione **Conexión** y conéctese al servidor catalog-\<WtpUser\>.database.windows.net, en caso de que todavía no esté conectado.
1. Asegúrese de que está conectado a la base de datos **jobaccount** y presione **F5** para ejecutar el script.

* **sp\_add\_target\_group** crea el nombre del grupo de destino *TenantGroup*. Ahora hay que agregar miembros de destino.
* **sp\_add\_target\_group\_member** agrega un tipo de miembro de destino *servidor*, que considera que todas las bases de datos de ese servidor (tenga en cuenta que se trata del servidor customer1-&lt;WtpUser&gt; que contiene las bases de datos de inquilino) en el momento de la ejecución del trabajo deben incluirse en el trabajo.
* **sp\_add\_job** crea un trabajo programado semanal denominado "Ticket Purchases from all Tenants".
* **sp\_add\_jobstep** crea el paso del trabajo que contiene el texto del comando T-SQL que recupera toda la información sobre la compra de entradas de todos los inquilinos y copia el conjunto de resultados devuelto en una tabla denominada *AllTicketsPurchasesfromAllTenants*.
* En las demás vistas del script se muestran los objetos existentes y se supervisa la ejecución de los trabajos. Revise el valor de estado de la columna **Ciclo de vida** para supervisar el estado. El estado Correcto indica que el trabajo ha finalizado correctamente en todas las bases de datos de inquilino y las dos bases de datos adicionales que contienen la tabla de referencia.

La ejecución correcta del script debería producir unos resultados similares a los siguientes:

![results](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Crear un trabajo para recuperar un recuento de resumen de las compras de entradas de todos los inquilinos

Este script crea un trabajo para recuperar la suma de todas las compras de entradas de todos los inquilinos.

1. Abra SSMS y conéctese al servidor *catalog-&lt;Usuario&gt;.database.windows.net*.
1. Abra el archivo …\\Learning Modules\\Provision y Catalog\\Operational Analytics\\Tenant Analytics\\*Results-TicketPurchasesfromAllTenants.sql*.
1. Modifique &lt;WtpUser&gt; con el nombre de usuario que empleó al implementar la aplicación WTP en el script, en el procedimiento almacenado **sp\_add\_jobstep**.
1. Haga clic con el botón derecho, seleccione **Conexión** y conéctese al servidor catalog-\<WtpUser\>.database.windows.net, en caso de que todavía no esté conectado.
1. Asegúrese de que está conectado a la base de datos **tenantanalytics** y presione **F5** para ejecutar el script.

La ejecución correcta del script debería producir unos resultados similares a los siguientes:

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** crea un trabajo programado semanal denominado "ResultsTicketsOrders".

* **sp\_add\_jobstep** crea el paso del trabajo que contiene el texto del comando T-SQL que recupera toda la información sobre la compra de entradas de todos los inquilinos y copia el conjunto de resultados devuelto en una tabla denominada CountofTicketOrders.

* En las demás vistas del script se muestran los objetos existentes y se supervisa la ejecución de los trabajos. Revise el valor de estado de la columna **Ciclo de vida** para supervisar el estado. El estado Correcto indica que el trabajo ha finalizado correctamente en todas las bases de datos de inquilino y las dos bases de datos adicionales que contienen la tabla de referencia.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Implementar una base de datos de análisis de inquilinos
> * Crear un trabajo programado para recuperar datos analíticos en los inquilinos

¡Enhorabuena!

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación de la aplicación inicial Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Trabajos elásticos](sql-database-elastic-jobs-overview.md)
