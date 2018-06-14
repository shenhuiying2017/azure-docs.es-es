---
title: Usar datos de Azure Blockchain Workbench en Microsoft Excel
description: Obtenga información sobre cómo cargar y ver los datos de la base de datos de SQL de Azure Blockchain Workbench en Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: e8c20f4b8e39615e2a8c486130d7c8bec655a936
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076953"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Ver datos de Azure Blockchain Workbench en Microsoft Excel

Puede usar Microsoft Excel para ver los datos de la base de datos de SQL de Azure Blockchain Workbench. En este artículo se proporcionan los pasos que necesita realizar para lo siguiente:

* Conectarse a la base de datos de Blockchain Workbench desde Microsoft Excel
* Examinar las tablas y vistas de base de datos de Blockchain Workbench
* Cargar los datos de vista de Blockchain Workbench en Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Conectarse a la base de datos de Blockchain Workbench

Para conectarse a la base de datos de Blockchain Workbench:

1. Abra Microsoft Excel.
2. En la pestaña **Satos**, seleccione **Obtener datos**.
3. Elija **Desde Azure** y seleccione **Desde Azure SQL Database**.

   ![Conexión a una instancia de Azure SQL Database](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. En el cuadro de diálogo **Base de datos de SQL Server**:

    * En **Servidor**, escriba el nombre del servidor de Blockchain Workbench.
    * En **Base de datos (opcional)**, escriba el nombre de la base de datos.

   ![Proporcionar el servidor de bases de datos y la base de datos](media/blockchain-workbench-data-excel/provide-server-db.png)

5. En la barra de navegación del cuadro de diálogo **Base de datos de SQL Server**, seleccione **Base de datos**. Escriba su **nombre de usuario** y **contraseña**, y haga clic en **Conectar**.

    > [!NOTE]
    > Si usa las credenciales creadas durante el proceso de implementación de Azure Blockchain Workbench, el **nombre de usuario** es `dbadmin`. La **contraseña** es la que creó al implementar Blockchain Workbench.
    
   ![Proporcionar las credenciales de acceso a la base de datos](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Examinar las tablas y vistas de la base de datos

Después de conectarse a la base de datos, se abre el cuadro de diálogo del navegador de Excel. Puede usar el navegador para examinar las tablas y vistas en la base de datos. Las vistas están diseñadas para la creación de informes y sus nombres tienen el prefijo **vw**.

   ![Vista previa de navegador de Excel de una vista](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Cargar los datos de vista en un libro de Excel

En el ejemplo siguiente se muestra cómo puede cargar datos desde una vista en un libro de Excel.

1. En la barra de desplazamiento del **navegador**, seleccione la vista **vwContractAction**. En la vista previa de **vwContractAction** se muestran todas las acciones relacionadas con un contrato en la base de datos de Blockchain Workbench.
2. Seleccione **Cargar** para recuperar todos los datos de la vista y colocarlos en el libro de Excel.

   ![Datos cargados desde una vista](media/blockchain-workbench-data-excel/view-data.png)

Ahora que tiene los datos cargados, puede usar las características de Excel para crear sus propios informes con los datos de transacciones y metadatos de la base de datos de Azure Blockchain Workbench.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Vistas de base de datos de Azure Blockchain Workbench](blockchain-workbench-database-views.md)