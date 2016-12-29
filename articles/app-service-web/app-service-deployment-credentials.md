---
title: "Credenciales de implementación de Azure App Service | Microsoft Docs"
description: "Aprenda a usar las credenciales de implementación de Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: wpickett
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/21/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 73753bb96d2c9680ed5d5bdf5eb3780e371c08d3
ms.openlocfilehash: 53f971624c2c7a7f64eed257aeb0c402461cc7ba


---
# <a name="azure-app-service-deployment-credentials"></a>Credenciales de implementación de Azure App Service
La plataforma [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) admite dos tipos de credenciales para la implementación de contenido.
* Credenciales de implementación: credenciales con ámbito de usuario
* Perfil de publicación: credenciales con ámbito de aplicación 

## <a name="a-nameuserscopeauser-scoped-credentials"></a><a name="userscope"></a>Credenciales con ámbito de usuario
Las credenciales con ámbito de usuario las crea el usuario de Azure y se asignan directamente a una cuenta de Microsoft, en lugar de a cualquier aplicación de App Service específica. Las credenciales de implementación con ámbito de usuario se pueden establecer o restablecer desde [Azure Portal](https://portal.azure.com), donde cada aplicación de App Service tiene un punto de entrada de edición en **IMPLEMENTACIÓN DE LA APLICACIÓN > Credenciales de implementación**. Con independencia del punto de entrada, las modificaciones a estas credenciales con ámbito de usuario se aplican en toda la cuenta de Microsoft. Estas credenciales se utilizan con frecuencia para la implementación de FTP y Git.

![Credenciales de implementación de Azure App Service](./media/app-service-deployment-credentials/deployment_credentials.png)
 
Al delegar el acceso a los recursos de Azure a través del control de acceso basado en rol (RBAC) o de permisos de coadministrador, cada usuario de Azure que recibe acceso puede usar sus propias credenciales con ámbito de usuario hasta que se revoca el acceso. Estas credenciales de implementación no deben compartirse con otros usuarios de Azure.

## <a name="a-nameappscopeaapp-scoped-credentials"></a><a name="appscope"></a>Credenciales con ámbito de aplicación
Las credenciales con ámbito de aplicación se crean automáticamente por la plataforma de App Service. Se almacenan en el perfil de publicación de XML para cada aplicación de App Service. El perfil de publicación está disponible en [Azure Portal](https://portal.azure.com) a través de la acción **Obtener perfil de publicación** de la hoja **Introducción** de la aplicación. Estas credenciales se utilizan con frecuencia para la implementación basada en WebDeploy. También pueden usarse para la implementación de FTP o Git. Visual Studio, que es un punto de entrada para la implementación basada en WebDeploy, es capaz de analizar el perfil de publicación para la autenticación.

![Perfil de publicación de Azure App Service](./media/app-service-deployment-credentials/publish_profile.png)

Al delegar el acceso a los recursos de Azure a través del control de acceso basado en rol (RBAC) o de permisos de coadministrador, cada usuario de Azure que recibe acceso puede descargar el mismo perfil de publicación específico de la aplicación. Se puede restablecer el perfil de publicación en cualquier momento desde la hoja **Introducción** de aplicación de Azure Portal. El restablecimiento de las credenciales con ámbito de aplicación puede ser una buena idea después de revocar el acceso delegado.



<!--HONumber=Nov16_HO4-->


