---
title: Resolver errores de directorios que no coinciden en dominios administrados existentes de Azure AD Domain Services | Microsoft Docs
description: Entender y resolver errores de directorios que no coinciden en dominios administrados existentes de Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: maheshu
ms.openlocfilehash: 24e11769e9b403bc00157e3f60869effa6a9633f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Resolver errores de directorios que no coinciden en dominios administrados existentes de Azure AD Domain Services
Ya dispone de un dominio administrado con Azure AD Domain Services. Cuando navega a Azure Portal y ve el dominio administrado, aparece el siguiente mensaje de error:

![Error de directorio que no coincide](.\media\getting-started\mismatched-tenant-error.png)

Este dominio administrado no se puede administrar mientras no se resuelva el error.


## <a name="whats-causing-this-error"></a>¿Qué es lo que causa este error?
Este error se produce cuando el dominio administrado y la red virtual en la que está habilitado pertenecen a dos inquilinos de Azure AD diferentes. Por ejemplo, tiene un dominio administrado denominado "contoso.com" que se ha habilitado para el inquilino de Azure AD de Contoso, pero la red virtual de Azure en la que se ha habilitado el dominio administrado pertenece a Fabrikam, un inquilino de Azure AD diferente.

El nuevo Azure Portal (y, en concreto, la extensión de Azure AD Domain Services) se basa en Azure Resource Manager. En el entorno moderno de Azure Resource Manager, se aplican ciertas restricciones para proporcionar mayor seguridad y un control de acceso basado en rol (RBAC) a los recursos. La operación de habilitar Azure AD Domain Services para un inquilino de Azure AD es delicada, ya que hace que los valores hash de credenciales se sincronicen con el dominio administrado. Para realizar esta operación, debe ser un administrador de inquilinos del directorio. Además, debe tener privilegios administrativos en la red virtual en la que se habilita el dominio administrado. Para que las comprobaciones de RBAC funcionen de forma coherente, el dominio administrado y la red virtual deben pertenecer al mismo inquilino de Azure AD.

En resumen, no puede habilitar un dominio administrado para un inquilino de Azure AD de "contoso.com" en una red virtual que pertenezca a una suscripción de Azure propiedad de otro inquilino de Azure AD de "fabrikam.com". 

**Configuración válida**: en este escenario de implementación, el dominio administrado de Contoso está habilitado para el inquilino de Azure AD de Contoso. El dominio administrado se expone en una red virtual que pertenece a una suscripción de Azure propiedad del inquilino de Azure AD de Contoso. Por lo tanto, tanto el dominio administrado como la red virtual pertenecen al mismo inquilino de Azure AD. Esta configuración es válida y totalmente compatible.

![Configuración de inquilino válida](./media/getting-started/valid-tenant-config.png)

**Configuración de inquilino que no coincide**: en este escenario de implementación, el dominio administrado de Contoso está habilitado para el inquilino de Azure AD de Contoso, pero el dominio administrado se expone en una red virtual que pertenece a una suscripción de Azure propiedad del inquilino de Azure AD de Fabrikam. Por lo tanto, el dominio administrado y la red virtual pertenecen a dos inquilinos de Azure AD diferentes. Esta configuración es una configuración de inquilino que no coincide y no se admite. La red virtual debe moverse al mismo inquilino de Azure AD (es decir, Contoso) que el dominio administrado. Vea la sección [Resolución](#resolution) para obtener más información.

![Configuración de inquilino que no coincide](./media/getting-started/mismatched-tenant-config.png)

Por lo tanto, verá este error cuando el dominio administrado y la red virtual en la que está habilitado pertenecen a dos inquilinos de Azure AD diferentes.

Las reglas siguientes se aplican al entorno de Resource Manager:
- Un directorio de Azure AD puede tener varias suscripciones de Azure.
- Una suscripción de Azure puede tener varios recursos, como redes virtuales.
- Solo hay un dominio administrado de Azure AD Domain Services habilitado para un directorio de Azure AD.
- Un dominio administrado de Azure AD Domain Services puede habilitarse en una red virtual que pertenezca a cualquiera de las suscripciones de Azure incluidas dentro del mismo inquilino de Azure AD.


## <a name="resolution"></a>Resolución
Tiene dos opciones para resolver el error de directorio que no coincide. Puede hacer lo siguiente:

- Haga clic en el botón **Eliminar** para eliminar el dominio administrado existente. Vuelva a crearlo en [Azure Portal](https://portal.azure.com), de modo que el dominio administrado y la red virtual en la que está disponible pertenezcan al directorio de Azure AD. Una todas las máquinas que antes estaban unidas al dominio eliminado al dominio administrado recién creado.

- Mueva la suscripción de Azure que contiene la red virtual al directorio de Azure AD al que pertenece el dominio administrado. Siga los pasos del artículo [Transferencia de la propiedad de una suscripción de Azure a otra cuenta](../billing/billing-subscription-transfer.md).


## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Guía de solución de problemas de Azure AD Domain Services](active-directory-ds-troubleshooting.md)
