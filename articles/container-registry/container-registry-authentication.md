---
title: "Autenticación con un registro de contenedor de Azure"
description: "Opciones de autenticación para un Azure Container Registry, incluidas las entidades de servicio de Azure Active Directory directas y el inicio de sesión del registro."
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70758f938718aef160670bc023aff5fc0c9fb92a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticación con un registro de contenedor privado de Docker

Hay varias maneras de autenticar con un Azure Container Registry que se pueden aplicar a uno o más escenarios de uso de registros.

Puede iniciar sesión en un registro directamente a través del [inicio de sesión individual](#individual-login-with-azure-ad) y los organizadores de contenedores y aplicaciones pueden realizar una autenticación desatendida, mediante la [entidad de servicio](#service-principal) de Azure Active Directory (Azure AD).

Azure Container Registry no es compatible con las operaciones de Docker no autenticadas o con el acceso anónimo. Para imágenes públicas, puede usar [Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Inicio de sesión individual con Azure AD

Cuando trabaje con el registro directamente, como la extracción e inserción de imágenes en su estación de trabajo de desarrollo, autentique mediante el comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) en la [CLI de Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Cuando inicie sesión con `az acr login`, la CLI utiliza el token creado cuando ejecuta `az login` para autenticar sin problemas la sesión con su registro. Una vez que haya iniciado sesión de este modo, las credenciales se almacenan en caché y los comandos `docker` posteriores no requieren un nombre de usuario o una contraseña. Si el token expira, puede actualizarlo con el comando `az acr login` de nuevo para volver a autenticar. El uso de `az acr login` con identidades de Azure proporciona [acceso basado en roles](../active-directory/role-based-access-control-configure.md).

## <a name="service-principal"></a>Entidad de servicio

Puede asignar una [entidad de servicio](../active-directory/develop/active-directory-application-objects.md) en el registro, y la aplicación o servicio puede usarla para la autenticación desatendida. Las entidades de servicio permiten el [acceso basado en roles](../active-directory/role-based-access-control-configure.md) a un registro, y puede asignar varias entidades de seguridad de servicio a un registro. Las distintas entidades de servicio le permiten definir un acceso diferente para distintas aplicaciones.

Los roles disponibles son los siguientes:

  * **Lector**: extracción
  * **Colaborador**: extracción e inserción
  * **Propietario**: extracción, inserción y asignación de roles a otros usuarios

Las entidades de servicio permiten la conectividad desatendida en un registro en escenarios de extracción e inserción como los siguientes:

  * *Lector*: las implementaciones de contenedor desde un registro a sistemas de orquestación incluyen Kubernetes, DC/OS y Docker Swarm. También puede extraer registros de contenedor para los servicios de Azure relacionados, como [AKS](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), y otros.

  * *Colaborador*: soluciones de integración e implementación continua (por ejemplo, Visual Studio Team Services (VSTS) o Jenkins) que crean imágenes de contenedor y las insertan en un registro.

> [!TIP]
> Puede volver a generar la contraseña de una entidad de servicio mediante el comando [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials).
>

También puede iniciar sesión directamente con una entidad de servicio. Proporcione el id. de la aplicación y la contraseña de la entidad de servicio al comando `docker login`:

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Una vez iniciada la sesión, Docker almacena en caché las credenciales, por lo que no es necesario recordar el id. de la aplicación.

Según la versión de Docker que haya instalado, puede aparecer una advertencia de seguridad en la que se recomiende el uso del parámetro `--password-stdin`. Cuando su uso esté fuera del ámbito de este artículo, recomendamos seguir este procedimiento recomendado. Para más información, vea la referencia del comando [docker login](https://docs.docker.com/engine/reference/commandline/login/).

Para obtener más información sobre cómo se puede usar una entidad de servicio para la autenticación sin periféricos en ACR, consulte [Azure Container Registry authentication with service principals](container-registry-auth-service-principal.md) (Autenticación de Azure Container Registry con entidades de servicio).

## <a name="admin-account"></a>Cuenta de administrador

Cada registro de contenedor incluye una cuenta de usuario administrador, que está deshabilitada de forma predeterminada. Puede habilitar el usuario administrador y administrar sus credenciales en [Azure Portal](container-registry-get-started-portal.md#create-a-container-registry) o mediante la CLI de Azure.

> [!IMPORTANT]
> La cuenta de administrador está diseñada para que un solo usuario acceda al registro, principalmente con fines de prueba. No se recomienda compartir las credenciales de cuenta de administrador con varios usuarios. Todos los usuarios que se autentican con la cuenta de administrador aparecen como un único usuario con acceso de inserción y extracción en el registro. Al cambiar o deshabilitar esta cuenta se deshabilita el acceso de registro para todos los usuarios que utilizan sus credenciales. Se recomienda la identidad individual para usuarios y entidades de servicio para escenarios desatendidos.
>

A la cuenta de administrador se le proporcionan dos contraseñas, y las dos se pueden regenerar. Las dos contraseñas le permiten mantener la conexión con el registro mediante una contraseña mientras se regenera la otra. Si la cuenta de administrador está habilitada, puede pasar el nombre de usuario y cualquier contraseña al comando `docker login` para la autenticación básica en el registro. Por ejemplo: 

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Una vez más, Docker recomienda el uso del parámetro `--password-stdin` en lugar de proporcionarlo en la línea de comandos para aumentar la seguridad. También puede especificar solo el nombre de usuario, sin `-p` y escribir la contraseña cuando se le solicite.

Para habilitar el usuario administrador para un registro existente, puede usar el parámetro `--admin-enabled` del comando [az acr update](/cli/azure/acr?view=azure-cli-latest#az_acr_update) en la CLI de Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Puede habilitar el usuario administrador en Azure Portal dirigiéndose al registro, seleccionando **Claves de acceso** en **Configuración** y **Habilitar** en **Usuario administrador**.

![Activación de la interfaz de usuario administrador en Azure Portal][auth-portal-01]

## <a name="next-steps"></a>pasos siguientes

* [Push your first image using the Azure CLI](container-registry-get-started-azure-cli.md) (Inserción de la primera imagen mediante la CLI de Azure)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
