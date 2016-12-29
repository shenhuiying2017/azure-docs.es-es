---
title: "Autenticación con un registro de contenedor de Azure | Microsoft Docs"
description: "Inicio de sesión en un registro de contenedor de Azure mediante una entidad de servicio de Azure Active Directory o una cuenta de administrador"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 1a5af0b498cfdf1946f5c405d9557b0c2d2c8e63
ms.openlocfilehash: 1e9e54ee935b4c27eb93f72eb99c3ce52cc6c7e2

---
# <a name="authenticate-with-a-container-registry"></a>Autenticación con un registro de contenedor
Para trabajar con imágenes de contenedor en un registro de contenedor de Azure, inicie sesión con el comando `docker login`. Puede iniciar una sesión utilizando una **[entidad de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md)** o una **cuenta de administrador** específica de registro. Este artículo proporciona más detalles sobre estas identidades. 


> [!NOTE]
> Container Registry está actualmente en vista previa. En la vista previa, las identidades individuales de Azure Active Directory (que permiten acceso y control por usuario) no se admiten para autenticar con un registro de contenedor. 
> 





## <a name="service-principal"></a>Entidad de servicio

Puede [asignar una entidad de servicio](container-registry-get-started-azure-cli.md#assign-a-service-principal) en el registro y usarla para la autenticación básica de Docker. Se recomienda el uso de una entidad de servicio para la mayoría de los escenarios. Proporcione el id. de la aplicación y la contraseña de la entidad de servicio al comando `docker login`, tal y como se muestra en el ejemplo siguiente:

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Una vez iniciada la sesión, Docker almacena en caché las credenciales, por lo que no es necesario recordar el id. de la aplicación.

> [!TIP]
> Si lo desea, puede volver a generar la contraseña de una entidad de servicio mediante el comando `az ad sp reset-credentials`.
> 


Las entidades de servicio permiten el [acceso basado en roles](../active-directory/role-based-access-control-configure.md) a un registro. Los roles disponibles son Lector (acceso de solo de extracción), Colaborador (extracción e inserción) y Propietario (extracción, inserción y asignación de roles a otros usuarios). Puede asignar varias entidades de servicio a un registro, lo que le permite definir el acceso a distintos usuarios o aplicaciones. Las entidades de servicio también habilitan la conectividad "desatendida" a un registro en escenarios de desarrollador o DevOps como los siguientes:

  * Las implementaciones de contenedor desde un registro a sistemas de orquestación incluyen DC/OS, Docker Swarm y Kubernetes. También puede extraer registros de contenedor para los servicios de Azure relacionados, como [Container Service](../container-service/index.md), [App Service](../app-service/index.md), [Batch](../batch/index.md) y [Service Fabric](../service-fabric/index.md).
  
  * Soluciones de integración e implementación continua (por ejemplo, Visual Studio Team Services o Jenkins) que crean imágenes de contenedor y las insertan en un registro.
  
  



## <a name="admin-account"></a>Cuenta de administrador
Con cada registro que se crea, se crea una cuenta de administrador automáticamente. De forma predeterminada, la cuenta está deshabilitada, pero puede habilitarla y administrar las credenciales, por ejemplo, a través del [portal](container-registry-get-started-portal.md#manage-registry-settings) o mediante los [comandos de versión preliminar de la CLI de Azure 2.0](container-registry-get-started-azure-cli.md#manage-admin-credentials). Si la cuenta está habilitada, puede pasar el nombre de usuario y la contraseña al comando `docker login` para la autenticación básica en el registro. Por ejemplo:

```
docker login myregistry-contoso.azurecr.io -u myAdminName -p myPassword
```

> [!IMPORTANT]
> La cuenta de administrador está diseñada para que un solo usuario acceda al registro, principalmente con fines de prueba. No se recomienda compartir las credenciales de cuenta de administrador entre otros usuarios. Todos los usuarios aparecen como un único usuario en el registro. Al cambiar o deshabilitar esta cuenta se deshabilita el acceso de registro para todos los usuarios que utilizan las credenciales. 
> 


### <a name="next-steps"></a>Pasos siguientes
* [Inserte la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md).
* Para más información sobre la autenticación en la vista previa del registro de contenedor, consulte el [entrada de blog](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/). 





<!--HONumber=Nov16_HO4-->


