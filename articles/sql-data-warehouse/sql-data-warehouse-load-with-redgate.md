---
title: Uso de Redgate para cargar datos en Azure Data Warehouse | Microsoft Docs
description: "Aprenda cómo usar Redgate's Data Platform Studio para escenarios de almacenamiento de datos."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: a38b237d5bfc0450c1ca79b53a5784dbb9bf8602
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="load-data-with-redgate-data-platform-studio"></a>Carga de datos con Redgate's Data Platform Studio
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Factoría de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Este tutorial muestra cómo utilizar [Data Platform Studio de Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) para mover datos desde una instancia de SQL Server local a Azure SQL Data Warehouse. Data Platform Studio aplica las correcciones y optimizaciones de compatibilidad más adecuadas, por lo que es la forma más rápida de empezar a trabajar con SQL Data Warehouse.

> [!NOTE]
> [Redgate](http://www.red-gate.com) es un experimentado asociado de Microsoft que ofrece diversas herramientas de SQL Server. Esta característica de Data Platform Studio está disponible gratuitamente para uso comercial y no comercial.
> 
> 

## <a name="before-you-begin"></a>Antes de empezar
### <a name="create-or-identify-resources"></a>Creación o identificación de recursos
Antes de comenzar este tutorial, debe tener:

* **Base de datos de SQL Server local**: los datos que desea importar a SQL Data Warehouse deben proceder de una instancia local de SQL Server (versión 2008R2 o superior). Data Platform Studio no puede importar datos directamente desde Azure SQL Database ni desde archivos de texto.
* **Cuenta de Azure Storage**: Data Platform Studio realiza una copia intermedia de los datos en Azure Blob Storage antes de cargarlos en SQL Data Warehouse. La cuenta de almacenamiento debe usar el modelo de implementación de "Resource Manager" (predeterminado) en lugar del modelo de implementación "Clásico". Si no dispone de una cuenta de almacenamiento, infórmese sobre cómo crear una cuenta de almacenamiento. 
* **SQL Data Warehouse**: este tutorial traslada los datos desde la instancia local de SQL Server a SQL Data Warehouse, por lo que debe tener un almacén de datos en línea. Si no dispone de un almacén de datos, aprenda a cómo crear una instancia de Azure SQL Data Warehouse.

> [!NOTE]
> El rendimiento mejora si la cuenta de almacenamiento y el almacenamiento de datos se crean en la misma región.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Paso 1: Inicio de sesión en Data Platform Studio con su cuenta de Azure
Abra el explorador web y navegue hasta el sitio web de [Data Platform Studio](https://www.dataplatformstudio.com/). Inicie sesión con la misma cuenta de Azure que usó para crear el almacén de datos y la cuenta de almacenamiento. Si su dirección de correo electrónico está asociada con una cuenta profesional o educativa y una cuenta de Microsoft, asegúrese de elegir la cuenta que tiene acceso a los recursos.

> [!NOTE]
> Si esta es la primera vez que usa Data Platform Studio, deberá conceder el permiso de aplicación para administrar los recursos de Azure.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Paso 2: Inicio del asistente de importación
En la pantalla principal de DPS, seleccione el vínculo Importar en Azure SQL Data Warehouse para que se inicie el asistente de importación.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Paso 3: Instalación de la puerta de enlace de Data Platform Studio
Para conectarse a la base de datos local de SQL Server, debe instalar la puerta de enlace de DPS. La puerta de enlace es un agente de cliente que proporciona acceso a su entorno local, extrae los datos y los carga en su cuenta de almacenamiento. Los datos nunca pasan a través de los servidores de Redgate. Instalación de la puerta de enlace:

1. Haga clic en el vínculo **Crear puerta de enlace**
2. Descargue e instale la puerta de enlace con el instalador proporcionado

![][2]

> [!NOTE]
> La puerta de enlace puede instalarse en cualquier máquina con acceso de red a la base de datos de origen de SQL Server. Esta accede a la base de datos de SQL Server mediante la autenticación de Windows con las credenciales del usuario actual.
> 
> 

Una vez instalado, el estado de la puerta de enlace cambia a Conectado y puede seleccionar Siguiente.

## <a name="step-4-identify-the-source-database"></a>Paso 4: Identificación de la base de datos de origen
En el cuadro de texto *Escriba un nombre de servidor*, escriba el nombre del servidor que hospeda la base de datos y seleccione **Siguiente**. A continuación, en el menú desplegable, seleccione la base de datos desde la que desea importar los datos.

![][3]

DPS inspecciona la base de datos seleccionada para buscar las tablas que se van a importar. De forma predeterminada, DPS importa todas las tablas de la base de datos. Puede seleccionar o anular la selección de las tablas expandiendo el vínculo Todas las tablas. Seleccione el botón Siguiente para desplazarse hacia delante.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Paso 5: Elección de una cuenta de almacenamiento para realizar una copia intermedia de los datos
DPS le solicitará una ubicación para realizar una copia intermedia de los datos. Elija una cuenta de almacenamiento existente de la suscripción y seleccione **Siguiente**.

> [!NOTE]
> DPS creará un nuevo contenedor de blobs en la cuenta de almacenamiento elegida y utilizará una carpeta distinta para cada importación.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Paso 6: Selección del almacenamiento de datos
A continuación, seleccione una base de datos en línea de [Azure SQL Data Warehouse](http://aka.ms/sqldw) para importar los datos en la base de datos. Una vez que haya seleccionado la base de datos, tiene que introducir las credenciales para conectarse a la base de datos y seleccionar **Siguiente**.

![][5]

> [!NOTE]
> DPS combina las tablas de datos de origen en el almacén de datos. DPS le advierte si el nombre de la tabla requiere sobrescribir las tablas existentes en el almacén de datos. Opcionalmente, puede eliminar los objetos existentes en el almacén de datos haciendo clic en eliminar todos los objetos existentes antes de la importación.
> 
> 

## <a name="step-7-import-the-data"></a>Paso 7: Importación de los datos
DPS confirma que desea importar los datos. Simplemente haga clic en el botón Iniciar importación para empezar la importación de datos.

![][6]

DPS ofrece una visualización que muestra el progreso de la extracción y carga de los datos de SQL Server local y el progreso de la importación en SQL Data Warehouse.

![][7]

Una vez completada la importación, DPS muestra un resumen de la importación de datos y un informe de cambios de las correcciones de compatibilidad que se han realizado.

![][8]

## <a name="next-steps"></a>Pasos siguientes
Para explorar los datos en SQL Data Warehouse, empiece por:

* [Consultas en Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Visualización de datos con Power BI][Visualize data with Power BI]

Para más información acerca de Redgate’s Data Platform Studio:

* [Visite la página principal de DPS](http://www.dataplatformstudio.com/)
* [Vea una demostración de DPS en Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Para obtener información general acerca de otras formas de migrar y cargar los datos en SQL Data Warehouse, consulte:

* [Migración de una solución a SQL Data Warehouse][Migrate your solution to SQL Data Warehouse]
* [Carga de datos en Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md)

Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
