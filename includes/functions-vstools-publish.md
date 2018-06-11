---
title: archivo de inclusión
description: archivo de inclusión
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6cbf24c56458ab8b3b6c7b44bedbd8b48d4677b3
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34702428"
---
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

2. Seleccione **Azure Function App**, elija **Crear nuevo**y, después, seleccione **Publicar**.

    ![Elegir un destino de publicación](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

2. Si todavía no conectó Visual Studio a su cuenta de Azure, seleccione **Agregar una cuenta...**.

3. En el cuadro de diálogo **Crear servicio de aplicaciones**, utilice la configuración de **Hospedaje** que se especifica en la tabla que hay debajo de la imagen:

    ![Cuadro de diálogo Crear servicio de aplicaciones](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Configuración      | Valor sugerido  | DESCRIPCIÓN                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nombre de aplicación** | Nombre único globalmente | Nombre que identifica de forma única la nueva aplicación de función. |
    | **Suscripción** | Elija una suscripción | La suscripción de Azure que se va a usar. |
    | **[Grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nombre del grupo de recursos en el que se va a crear la aplicación de función. Elija **Nuevo** para crear un grupo de recursos nuevo.|
    | **[Plan de App Service](../articles/azure-functions/functions-scale.md)** | Plan de consumo | Asegúrese de que elige **Consumo** en **Tamaño** después de hacer clic en **Nuevo** para crear un plan. Además, elija una **ubicación** en una [región](https://azure.microsoft.com/regions/) cercana a usted o a otros servicios a los que acceden las funciones.  |
    | **[Cuenta de almacenamiento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** | Cuenta de almacenamiento de uso general | El entorno de ejecución de Functions necesita una cuenta de Azure Storage. Haga clic en **Nuevo** para crear una cuenta de almacenamiento de uso general, o bien use uno existente.  |

4. Haga clic en **Crear** para crear una aplicación de función y los recursos relacionados en Azure con esta configuración e implemente el código del proyecto de función. 

5. Una vez finalizada la implementación, anote el valor de la **dirección URL del sitio**, que es la dirección de la aplicación de función en Azure.

    ![Publicar mensaje de operación correcta](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
