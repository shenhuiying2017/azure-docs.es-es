---
title: "Inicio rápido: Pausar y reanudar un proceso en Azure SQL Data Warehouse (Azure Portal) | Microsoft Docs"
description: "Tareas de Azure Portal que pausan un proceso para que una instancia de Azure SQL Data Warehouse ahorre costos. Reanude el proceso cuando esté listo para usar el almacenamiento de datos."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/23/2018
ms.author: barbkess
ms.openlocfilehash: f8b4e29595c6a71696cf2c4939ad4c6c096a28b5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Inicio rápido: Pausar y reanudar un proceso en una instancia de Azure SQL Data Warehouse en Azure Portal
Puede pausar un proceso para que una instancia de Azure SQL Data Warehouse ahorre costos. Reanude el proceso cuando esté listo para usar el almacenamiento de datos.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de empezar

Use [Crear y conectar: Portal](create-data-warehouse-portal.md) para crear un almacenamiento de datos denominado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausa del proceso
Para ahorrar costos, puede pausar y reanudar recursos de proceso a petición. Por ejemplo, si no va a usar la base de datos durante la noche y los fines de semana, puede pausarla durante esas horas y reanudarla durante el día. No se le cobrará por recursos de proceso mientras la base de datos se encuentre en pausa. Sin embargo, se le seguirá cobrando por el almacenamiento. 

Para pausar una instancia de SQL Data Warehouse, siga estos pasos.

1. En la página izquierda de Azure Portal, haga clic en **Bases de datos SQL**.
2. Seleccione **mySampleDataWarehouse** en la página **Bases de datos SQL**. Se abrirá el almacenamiento de datos. 
3. En la página **mySampleDataWarehouse**, observe que el **Estado** sea **En línea**.

    ![Proceso en línea](media/pause-and-resume-compute-portal/compute-online.png)

4. Para pausar el almacenamiento de datos, haga clic en el botón **Pausar**. 
5. Aparece una pregunta de confirmación en la que tiene que indicar si quiera continuar. Haga clic en **Sí**.
6. Espere un momento y, a continuación, observe que el **Estado** sea **Pausando**.

    ![Pausando](media/pause-and-resume-compute-portal/pausing.png)

7. Una vez completada la operación de pausa, el estado es **En pausa** y el botón de opción es **Iniciar**.
8. Ahora, los recursos de proceso para el almacenamiento de datos no tienen conexión. No se le cobrará el proceso hasta que reanude el servicio.

    ![Proceso sin conexión](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Reanudación del proceso
Para reanudar una instancia de SQL Data Warehouse, siga estos pasos.

1. En la página izquierda de Azure Portal, haga clic en **Bases de datos SQL**.
2. Seleccione **mySampleDataWarehouse** en la página **Bases de datos SQL**. Se abrirá el almacenamiento de datos. 
3. En la página **mySampleDataWarehouse**, observe que el **Estado** sea **En pausa**.

    ![Proceso sin conexión](media/pause-and-resume-compute-portal/compute-offline.png)

4. Para reanudar el almacenamiento de datos, haga clic en **Iniciar**. 
5. Aparece una pregunta de confirmación en la que tiene que indicar si lo quiere iniciar. Haga clic en **Sí**.
6. Observe que el **Estado** sea **Reanudando**.

    ![Reanudando](media/pause-and-resume-compute-portal/resuming.png)

7. Cuando el almacenamiento de datos vuelva a estar conectado, el estado será **En línea** y el botón de opción será **Pausar**.
8. Los recursos de proceso para el almacenamiento de datos ahora están en línea y se puede usar el servicio. Los cargos del proceso se han reanudado.

    ![Proceso en línea](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Se le está cobrando por unidades de almacenamiento de datos y por los datos almacenados en el almacenamiento de datos. Estos recursos de proceso y de almacenamiento se facturan por separado. 

- Si quiere conservar los datos de almacenamiento, pause el proceso.
- Si desea quitar cobros futuros, puede eliminar el almacenamiento de datos. 

Siga estos pasos para limpiar los recursos según estime oportuno.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en el almacenamiento de datos.

    ![Limpieza de recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Para pausar el proceso, haga clic en el botón **Pausar**. Cuando el almacenamiento de datos se haya puesto en pausa, verá un botón **Iniciar**.  Para reanudar el proceso, haga clic en **Iniciar**.

2. Para quitar el almacenamiento de datos para que no se le cobre por proceso o almacenamiento, haga clic en **Eliminar**.

3. Para quitar el servidor SQL que creó, haga clic en **mynewserver-20171113.database.windows.net** y, luego, haga clic en **Eliminar**.  Debe tener cuidado con este procedimiento, ya que la eliminación del servidor elimina también todas las bases de datos asignadas al servidor.

4. Para quitar el grupo de recursos, haga clic en **myResourceGroup** y luego haga clic en **Eliminar grupo de recursos**.


## <a name="next-steps"></a>pasos siguientes
Ya ha pausado y reanudado el proceso para el almacenamiento de datos. Para más información sobre Azure SQL Data Warehouse, siga el tutorial para cargar los datos.

> [!div class="nextstepaction"]
>[Carga de datos en una instancia de SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
