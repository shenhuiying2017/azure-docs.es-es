---
title: Actualización de Azure App Service en Azure Stack | Microsoft Docs
description: Guía detallada para actualizar Azure App Service en Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 151456bbb7f9331730e640e4bece3872c3c92f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="update-azure-app-service-on-azure-stack"></a>Actualización de Azure App Service en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!IMPORTANT]
> Aplique la actualización 1802 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service.
>
>

Siguiendo las instrucciones de este artículo, puede actualizar el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) implementado en un entorno de Azure Stack que esté conectado a Internet.

> [!IMPORTANT]
> Antes de ejecutar la actualización, asegúrese de que ya ha completado la [implementación de Azure App Service en el proveedor de recursos de Azure Stack](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

Durante este proceso, la actualización hará lo siguiente:

* Detectar la implementación anterior de App Service
* Preparar todos los paquetes de actualización y las nuevas versiones de todas las bibliotecas de OSS que se van a implementar
* Cargar en el almacenamiento
* Actualizar todos los roles de App Service (roles de controlador, administración, front-end, publicador y trabajo)
* Actualizar las definiciones de conjuntos de escalado de App Service
* Actualizar el manifiesto del proveedor de recursos de App Service

> [!IMPORTANT]
> El instalador de App Service se debe ejecutar en un equipo que pueda alcanzar el punto de conexión del administrador de Azure Resource Manager de Azure Stack.
>
>

Para actualizar la implementación de App Service en Azure Stack, siga estos pasos:

1. Descargue el [instalador de App Service](https://aka.ms/appsvcupdate1installer)

2. Ejecute appservice.exe como administrador

    ![Instalador de App Service][1]

3. Haga clic en **Implementar App Service o actualizar a la última versión.**

4. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Asegúrese de que la información del punto de conexión de Azure Resource Manager de Azure Stack y el inquilino de Active Directory son correctos. Si usó la configuración predeterminada durante la implementación del Kit de desarrollo de Azure Stack, puede aceptar los valores predeterminados aquí. Sin embargo, si personalizó las opciones cuando implementó Azure Stack, debe editar los valores de esta ventana para reflejarlo. Por ejemplo, si se usa el sufijo de dominio *mycloud.com*, el punto de conexión de Azure Resource Manager de Azure Stack debe cambiarse a *management.region.mycloud.com*. Después de confirmar su información, haga clic en **Siguiente**.

    ![Información de la nube de Azure Stack][2]

7. En la página siguiente:

   1. Haga clic en el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack).
        * Si se usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack. Haga clic en **Iniciar sesión**.
        * Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, *cloudadmin@azurestack.local*. Escriba la contraseña y haga clic en **Iniciar sesión**.
   2. En el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack), seleccione su suscripción.
   3. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a implementar con el Kit de desarrollo de Azure Stack.
   4. Si se detecta una implementación existente de App Service, la cuenta de almacenamiento y el grupo de recursos se rellenan y quedan atenuados.
   5. Haga clic en **Siguiente** para revisar el resumen de la actualización.

    ![Instalación de App Service detectada][3]

8. En la página de resumen:
   1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
   2. Si las configuraciones son correctas, active la casilla.
   3. Para iniciar la actualización, haga clic en **Siguiente**.

       ![Resumen de la actualización de App Service][4]

9. Página de progreso de la actualización:
    1. Realice un seguimiento del progreso de la actualización. La duración de la actualización de App Service en Azure Stack varía según el número de instancias de rol implementadas.
    2. Después de que la actualización finalice correctamente, haga clic en **Salir**.

        ![Progreso de la actualización de App Service][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [servicios de Plataforma como servicio (PaaS)](azure-stack-tools-paas-services.md).

* [Proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)
