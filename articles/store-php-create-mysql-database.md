---
title: "Creación y conexión de una base de datos MySQL en Azure"
description: "Obtenga información sobre cómo usar el Portal de Azure para crear una base de datos MySQL y, después, conectarse a ella desde una aplicación web PHP en Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Creación y conexión de una base de datos MySQL en Azure
En este tutorial se le enseñará a crear una base de datos MySQL en [Azure Portal](https://portal.azure.com) (el proveedor es [ClearDB](http://www.cleardb.com/)) y cómo conectarse a ella desde una aplicación web PHP que se ejecute en [Azure App Service](app-service/app-service-web-overview.md).

> [!NOTE]
> También puede crear una base de datos MySQL como parte de una <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">plantilla de aplicación de Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Creación una base de datos MySQL en el Portal de Azure
Para restaurar una base de datos MySQL en el Portal de Azure, haga lo siguiente:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú izquierdo, haga clic en **Nuevo** > **Datos y almacenamiento** > **Base de datos MySQL**.

    ![Creación una base de datos MySQL en Azure: inicio](./media/store-php-create-mysql-database/create-db-1-start.png)
3. En la [hoja](azure-portal-overview.md)Nueva base de datos MySQL, configure la nueva base de datos MySQL tal y como se muestra a continuación (*hoja*: una página del Portal que se abre horizontalmente):

   * **Nombre de la base de datos**: escriba un nombre identificable de forma única.
   * **Suscripción**: elija la suscripción que va a utilizar
   * **Tipo de base de datos**: seleccione **Compartido** para los niveles de bajo coste o gratuitos, o bien **Dedicado** si desea obtener recursos específicos.
   * **Grupo de recursos**: agregue la base de datos MySQL a un [grupo de recursos](azure-resource-manager/resource-group-overview.md) existente, o bien colóquela en uno nuevo. Los recursos del mismo grupo pueden administrarse juntos de manera sencilla.
   * **Ubicación**: elija una ubicación cercana a usted. Cuando se agrega la base de datos a un grupo de recursos existente, no podrá acceder a la ubicación de este.
   * **Plan de tarifa**: haga clic en **Plan de tarifa**; después, seleccione una opción de precios (el nivel **Mercurio** es gratuito) y, luego, haga clic en **Seleccionar**.
   * **Condiciones legales**: haga clic en **Condiciones legales**, revise los detalles de la compra y haga clic en **Comprar**.
   * **Anclar al panel**: seleccione si desea acceder directamente desde el panel. Esta opción le resultará especialmente útil si todavía se está familiarizando con la navegación por el Portal.

     La siguiente captura de pantalla es tan solo un ejemplo de cómo puede configurar la base de datos MySQL.  
     ![Creación una base de datos MySQL en Azure: configuración](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Cuando haya terminado, haga clic en **Crear**.

    ![Creación una base de datos MySQL en Azure: creación](./media/store-php-create-mysql-database/create-db-3-create.png)

    Verá una notificación emergente donde se le informará que se ha iniciado la implementación.

    ![Creación una base de datos MySQL en Azure: en curso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Se mostrará otra ventana emergente cuando se complete correctamente la implementación. El Portal también abrirá automáticamente la hoja de la base de datos MySQL.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Conexión a la base de datos MySQL
Para ver la información de conexión de la nueva base de datos MySQL, haga clic en **Propiedades** en la hoja de la aplicación web.

![Creación una base de datos MySQL en Azure: hoja Base de datos MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Ahora puede emplear esa información de conexión en cualquier aplicación web. [Aquí](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)puede encontrar un ejemplo que muestra cómo utilizar la información de conexión de una aplicación PHP simple.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).
