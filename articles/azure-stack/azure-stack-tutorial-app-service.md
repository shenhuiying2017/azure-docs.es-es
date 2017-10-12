---
title: "Aplicaciones web, móviles y de API disponibles para los usuarios de Azure Stack | Microsoft Docs"
description: "Tutorial para instalar el proveedor de recursos de App Service y crear ofertas que permitan a los usuarios de Azure Stack crear aplicaciones web, móviles y de API."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 2d011e933cb063eef88a372fccc49d2b9de19717
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="make-web-mobile-and-api-apps-available-to-your-azure-stack-users"></a>Aplicaciones web, móviles y de API disponibles para los usuarios de Azure Stack

Como administrador de la nube de Azure Stack, puede crear ofertas que permitan a los usuarios (inquilinos) crear aplicaciones web, móviles y de API, así como de Azure Functions. Si proporciona a los usuarios acceso a estas aplicaciones en la nube y a petición, puede ahorrarles tiempo y recursos. Para configurar esta opción, tendrá que:

> [!div class="checklist"]
> * Implementar el proveedor de recursos de App Service
> * Creación de una oferta
> * Probar la oferta

## <a name="deploy-the-app-service-resource-provider"></a>Implementar el proveedor de recursos de App Service

1. [Prepare el host de Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). Esto incluye implementar el proveedor de recursos de SQL Server, que se requiere para crear algunas aplicaciones.
2. [Descargue los scripts de aplicación auxiliar y del instalador](azure-stack-app-service-deploy.md).
3. [Ejecute el script de aplicación auxiliar para crear los certificados necesarios](azure-stack-app-service-deploy.md).
4. [Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md) (tardará un par de horas en instalarse y que todos los roles de trabajo aparezcan).
5. [Valide la instalación](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Creación de una oferta

Por ejemplo, puede crear una oferta que permita a los usuarios crear sistemas de administración de contenido web DNN. Requiere el servicio de SQL Server que ya habilitó al instalar al proveedor de recursos de SQL Server.

1.  [Establezca una cuota](azure-stack-setting-quotas.md) y asígnele el nombre *CuotaDeAppService*. Seleccione **Microsoft.Web** para el campo **Espacio de nombres**.
2.  [Cree un plan](azure-stack-create-plan.md). Asígnele el nombre *PlanDePruebaDeAppService*, seleccione el servicio **Microsoft.SQL** y la cuota **AppService Quota** (Cuota de AppService).

    > [!NOTE]
    > Para permitir que los usuarios creen otras aplicaciones, podrían ser necesarios otros servicios en el plan. Por ejemplo, Azure Functions requiere que el plan incluya el servicio **Microsoft.Storage**, mientras que Wordpress requiere **Microsoft.MySQL**.
    > 
    >

3.  [Cree una oferta](azure-stack-create-offer.md), asígnele el nombre **OfertaDePruebaDeAppService** y seleccione el plan **PlanDePruebaDeAppService**.

## <a name="test-the-offer"></a>Probar la oferta

Ahora que ha implementado el proveedor de recursos de App Service y ha creado una oferta, puede iniciar sesión como un usuario, suscribirse a la oferta y crear una aplicación. En este ejemplo, vamos a crear un sistema de administración de contenido de la plataforma DNN. Primero debe crear una base de datos SQL y, a continuación, la aplicación web DNN.

### <a name="subscribe-to-the-offer"></a>Suscripción a la oferta
1. Inicie sesión Azure Stack Portal (https://portal.local.azurestack.external/) como inquilino.
2. Haga clic en **Obtener una suscripción** > escriba **SuscripciónDePruebaDeAppService** en **Nombre para mostrar** > **Seleccionar una oferta**  >  **OfertaDePruebaDeAppService** > **Crear**.

### <a name="create-a-sql-database"></a>Creación de una base de datos SQL

1. Haga clic en **+** > **Datos y almacenamiento** > **SQL Database**.
2. Deje los valores predeterminados para los campos, excepto los siguientes:
    - **Nombre de la base de datos**: DNNdb
    - **Tamaño máximo en MB**: 100
    - **Suscripción**: OfertaDePruebaDeAppService
    - **Grupo de recursos**: DNN-RG
3. Haga clic en **Configuración de inicio de sesión**, escriba las credenciales de la base de datos y, a continuación, haga clic en **Aceptar**. Usará estas credenciales más adelante en estos pasos.
4. Haga clic en **SKU** > seleccione la SKU de SQL que ha creado para el servidor de hospedaje SQL > **Aceptar**.
5. Haga clic en **Crear**.

### <a name="create-a-dnn-app"></a>Creación de una aplicación DNN    

1. Haga clic en  **+**   >  **Ver todo** > **DNN Platform preview** (Versión preliminar de la plataforma DNN)  > **Crear**.
2. Escriba *AppDNN* en **Nombre de la aplicación** y seleccione **OfertaDePruebaDeAppService** en **Suscripción**.
3. Haga clic en **Configurar los valores obligatorios** > **Crear nuevo** > escriba un nombre de **Plan de App Service**.
4. Haga clic en **Nivel de precios** > **F1 gratuito** > **Seleccionar** > **Aceptar**.
5. Haga clic en **Base de datos** y escriba la información de la base de datos SQL que creó anteriormente.
6. Haga clic en **Crear**.

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Implementar el proveedor de recursos de App Service
> * Creación de una oferta
> * Probar la oferta

Prosiga con el siguiente tutorial para aprender a:

> [!div class="nextstepaction"]
> [Implementar aplicaciones en Azure y Azure Stack](user/azure-stack-solution-pipeline.md)
