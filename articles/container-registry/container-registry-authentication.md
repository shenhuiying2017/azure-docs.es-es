---
title: "Autenticación con un registro de contenedor de Azure | Microsoft Docs"
description: "Inicio de sesión en un registro de contenedor de Azure mediante una entidad de servicio de Azure Active Directory o una cuenta de administrador"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d7d2ae0e9b1f7850332d151d78a4a5fdb013777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticación con un registro de contenedor privado de Docker
Para trabajar con imágenes de contenedor en un registro de contenedor de Azure, inicie sesión con el comando `docker login`. Puede iniciar una sesión utilizando una **[entidad de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md)** o una **cuenta de administrador** específica de registro. Este artículo proporciona más detalles sobre estas identidades.

## <a name="service-principal"></a>Entidad de servicio

Puede asignar una entidad de servicio en el registro y usarla para la autenticación básica de Docker. Se recomienda el uso de una entidad de servicio para la mayoría de los escenarios. Proporcione el id. de la aplicación y la contraseña de la entidad de servicio al comando `docker login`, tal y como se muestra en el ejemplo siguiente:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Una vez iniciada la sesión, Docker almacena en caché las credenciales, por lo que no es necesario recordar el id. de la aplicación.

> [!TIP]
> Si lo desea, puede volver a generar la contraseña de una entidad de servicio mediante el comando `az ad sp reset-credentials`.
>

Las entidades de servicio permiten el [acceso basado en roles](../active-directory/role-based-access-control-configure.md) a un registro. Los roles disponibles son:
  * Lector (acceso de solo extracción).
  * Colaborador (extracción e inserción).
  * Propietario (extracción, inserción y asignación de roles a otros usuarios).

El acceso anónimo no está disponible en los registros de contenedor de Azure. Para imágenes públicas puede usar [Docker Hub](https://docs.docker.com/docker-hub/).

Puede asignar varias entidades de servicio a un registro, lo que le permite definir el acceso a distintos usuarios o aplicaciones. Las entidades de servicio también habilitan la conectividad "desatendida" a un registro en escenarios de desarrollador o DevOps como los siguientes ejemplos:

  * Las implementaciones de contenedor desde un registro a sistemas de orquestación incluyen DC/OS, Docker Swarm y Kubernetes. También puede extraer registros de contenedor para los servicios de Azure relacionados, como [Container Service](../container-service/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/) y otros.

  * Soluciones de integración e implementación continua (por ejemplo, Visual Studio Team Services o Jenkins) que crean imágenes de contenedor y las insertan en un registro.


## <a name="admin-account"></a>Cuenta de administrador
Con cada registro que se crea, se crea una cuenta de administrador automáticamente. De forma predeterminada, la cuenta está deshabilitada, pero puede habilitarla y administrar las credenciales, por ejemplo, a través del [portal](container-registry-get-started-portal.md#create-a-container-registry) o mediante los [comandos de la CLI de Azure 2.0](container-registry-get-started-azure-cli.md#create-a-container-registry). A cada cuenta de administrador se le proporcionan dos contraseñas, y las dos se pueden regenerar. Las dos contraseñas permiten mantener las conexiones con el registro mediante una contraseña mientras se regenera la otra. Si la cuenta está habilitada, puede pasar el nombre de usuario y cualquier contraseña al comando `docker login` para la autenticación básica en el registro. Por ejemplo:

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> La cuenta de administrador está diseñada para que un solo usuario acceda al registro, principalmente con fines de prueba. No se recomienda compartir las credenciales de cuenta de administrador entre otros usuarios. Todos los usuarios aparecen como un único usuario en el registro. Al cambiar o deshabilitar esta cuenta se deshabilita el acceso de registro para todos los usuarios que utilizan las credenciales.
>

### <a name="next-steps"></a>Pasos siguientes
* [Inserte la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md).
* Para más información sobre la autenticación en la vista previa del registro de contenedor, consulte el [entrada de blog](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).
