---
title: "Autenticación de Azure Container Registry con entidades de servicio"
description: "Aprenda a proporcionar acceso a las imágenes de su registro de contenedor privado mediante una entidad de servicio de Azure Active Directory."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 97036ecabceb12b87b76c6ecb7e521157cbef827
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticación de Azure Container Registry con entidades de servicio

Puede usar una entidad de servicio de Azure Active Directory (Azure AD) para proporcionar a las imágenes de contenedor `docker push` y `pull` acceso a su registro de contenedor. Al usar una entidad de servicio, podrá proporcionar acceso a aplicaciones y servicios de "equipos sin periféricos".

## <a name="what-is-a-service-principal"></a>¿Qué es una entidad de servicio?

Las *entidades de servicio* de Azure AD proporcionan acceso a los recursos de Azure de su suscripción. Las entidades de servicio son como identidades de usuario para un servicio, donde "servicio" equivale a cualquier aplicación, servicio o plataforma que necesita acceder a los recursos. Puede configurar una entidad de servicio con derechos de acceso limitados solo a aquellos recursos que especifique. A continuación, puede configurar su aplicación o servicio para usar las credenciales de la entidad de servicio y acceder a dichos recursos.

En el contexto de Azure Container Registry, puede crear una entidad de servicio de Azure AD con permisos de extracción, inserción y extracción o de propietario para su registro de Docker privado de Azure.

## <a name="why-use-a-service-principal"></a>Razones para usar una entidad de servicio

Al usar una entidad de servicio de Azure AD, podrá proporcionar acceso limitado a su registro de contenedor privado. Puede crear distintas entidades de servicio para cada uno de sus servicios o aplicaciones, cada una con derechos de acceso personalizados a su registro. Además, puesto que es posible evitar el uso compartido de credenciales entre servicios y aplicaciones, podrá alternar las credenciales o revocar el acceso solo a la entidad principal (y, por lo tanto, a la aplicación) que elija.

Por ejemplo, su aplicación web puede usar una entidad de servicio que solo proporcione acceso a la imagen `pull`, mientras el sistema de compilación puede usar una entidad de servicio que proporcione acceso a las imágenes `push` y `pull`. Si el desarrollo de su aplicación cambia de manos, puede alternar las credenciales de entidad de servicio sin que ello afecte al sistema de compilación.

## <a name="when-to-use-a-service-principal"></a>Cuándo usar una entidad de servicio

Debe usar una entidad de servicio para proporcionar acceso al registro en **escenarios de equipos sin periféricos**. Es decir, para cualquier aplicación, servicio o script que deba insertar o extraer imágenes de contenedor de forma automática o desatendida.

Para obtener acceso individual a un registro, como, por ejemplo, al extraer manualmente una imagen de contenedor en su estación de trabajo de desarrollo, debe usar en su lugar su propia [identidad de Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) para acceder al registro (por ejemplo, con [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="next-steps"></a>pasos siguientes

Cuando disponga de una entidad de servicio con acceso a su registro de contenedor, podrá usar sus credenciales en aplicaciones y servicios para interactuar con el registro.

Pese a que la configuración de aplicaciones individuales para usar las credenciales de la entidad de servicio está fuera del ámbito del presente artículo, podrá encontrar instrucciones para determinados servicios y plataformas aquí:

* [Autenticación con Azure Container Registry desde Azure Container Service (AKS)](container-registry-auth-aks.md)
* [Autenticación con Azure Container Registry desde Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login