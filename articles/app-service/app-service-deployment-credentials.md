---
title: "Credenciales de implementación de Azure App Service | Microsoft Docs"
description: "Aprenda a usar las credenciales de implementación de Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: d66b5aa4eb2ad90596dfe9e26bbc18996c967295
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configuración de credenciales de implementación para Azure App Service
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) admite dos tipos de credenciales para la [implementación de GIT local](app-service-deploy-local-git.md) y la [implementación FTP/S](app-service-deploy-ftp.md). Estas credenciales no son las mismas que las de Azure Active Directory.

* **Credenciales de nivel de usuario**: un conjunto de credenciales para toda la cuenta de Azure. Se puede utilizar para implementar App Service en cualquier aplicación o suscripción para la que la cuenta de Azure tenga permiso de acceso. Este es el conjunto de credenciales predeterminado que se configura en **App Services** > **&lt;nombre_aplicación>** > **Credenciales de implementación**. Este es también el conjunto predeterminado que aparece en la interfaz gráfica de usuario del portal (como la **información general** y las **propiedades** de la [página de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de la aplicación).

    > [!NOTE]
    > Al delegar el acceso a recursos de Azure a través del control de acceso basado en rol (RBAC) o de permisos de coadministrador, cada usuario de Azure que recibe acceso a una aplicación puede usar sus credenciales de nivel de usuario personales hasta que se revoca el acceso. Estas credenciales de implementación no deben compartirse con otros usuarios de Azure.
    >
    >

* **Credenciales de nivel de aplicación**: un conjunto de credenciales para cada aplicación. Se puede utilizar para implementar únicamente en esa aplicación. Las credenciales para cada aplicación se generan automáticamente al crear la aplicación y se encuentran en el perfil de publicación de la aplicación. Las credenciales no se pueden configurar manualmente, pero se pueden restablecer para una aplicación en cualquier momento.

    > [!NOTE]
    > Para dar a alguien acceso a estas credenciales a través de Control de acceso basado en roles (RBAC), es preciso convertirlo en colaborador, o cualquier rol superior, en la aplicación web. A los lectores no se les permite publicar, por lo que no pueden acceder a dichas credenciales.
    >
    >

## <a name="userscope"></a>Establecimiento y restablecimiento de credenciales de nivel de usuario

Puede configurar las credenciales de nivel de usuario en cualquier [página de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de la aplicación. Independientemente de en qué aplicación configure estas credenciales, son válidas para todas las aplicaciones y para todas las suscripciones de su cuenta de Azure. 

Para configurar las credenciales de nivel de usuario:

1. En [Azure Portal](https://portal.azure.com), haga clic en App Service > **&lt;cualquier_aplicación >** > **Credenciales de implementación**.

    > [!NOTE]
    > En el portal, debe tener al menos una aplicación para acceder a la página de credenciales de implementación. Sin embargo, con la [CLI de Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set), puede configurar las credenciales de nivel de usuario sin tener ninguna aplicación.

2. Configure el nombre de usuario y la contraseña y, a continuación, haga clic en **Guardar**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Una vez que haya configurado las credenciales de implementación, puede encontrar el nombre de usuario de implementación de *Git* en la **Información general** de la aplicación

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

y el nombre de usuario de implementación de *FTP* en las **propiedades** de la aplicación.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure no muestra la contraseña de la implementación de nivel de usuario. Si olvida la contraseña, no podrá recuperarla. Sin embargo, podrá restablecer las credenciales siguiendo los pasos descritos en esta sección.
>
>  

## <a name="appscope"></a>Obtención y restablecimiento de las credenciales de nivel de aplicación
Para cada aplicación de App Service, sus credenciales de nivel de aplicación se almacenan en el perfil de publicación XML.

Para obtener las credenciales de nivel de aplicación:

1. En [Azure Portal](https://portal.azure.com), haga clic en App Service > **&lt;cualquier_aplicación >** > **Información general**.

2. Haga clic en **... Más** > **Obtener perfil de publicación** para que comience la descarga de un archivo .PublishSettings.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Abra el archivo .PublishSettings y busque la etiqueta `<publishProfile>` con el atributo `publishMethod="FTP"`. A continuación, obtenga sus atributos `userName` y `password`.
Estas son las credenciales de nivel de aplicación.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    De manera similar a las credenciales de nivel de usuario, el nombre de usuario de implementación FTP se encuentra en el formato de `<app_name>\<username>`, y el nombre de usuario de implementación de GIT es simplemente `<username>` sin la parte `<app_name>\` del principio.

Para restablecer las credenciales de nivel de aplicación:

1. En [Azure Portal](https://portal.azure.com), haga clic en App Service > **&lt;cualquier_aplicación >** > **Información general**.

2. Haga clic en **... Más** > **Restablecer perfil de publicación**. Haga clic en **SÍ** para confirmar el restablecimiento.

    La acción de restablecimiento invalida cualquier archivo .PublishSettings anteriormente descargado.

## <a name="next-steps"></a>pasos siguientes

Obtenga información sobre cómo usar estas credenciales para implementar la aplicación desde [GIT local](app-service-deploy-local-git.md) o con [FTP/S](app-service-deploy-ftp.md).
