---
title: "Azure Active Directory Domain Services: Habilitación de la delegación restringida de kerberos | Microsoft Docs"
description: "En este artículo se explica cómo habilitar la delegación restringida de kerberos en dominios administrados de Azure Active Directory Domain Services."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: f36f16a7bb00ace9fd5164eb38ba77f015f22f5c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configuración de la delegación restringida de kerberos (KCD) en un dominio administrado
Muchas aplicaciones necesitan tener acceso a recursos en el contexto del usuario. Active Directory admite un mecanismo denominado "delegación de kerberos", que posibilita este caso de uso. Además, puede restringir la delegación para que solo se puedan acceder a recursos específicos en el contexto del usuario. Los dominios administrados de Azure AD Domain Services son diferentes de los tradicionales de Active Directory, puesto que se bloquean de forma más segura.

En este artículo se muestra cómo configurar la delegación restringida de kerberos en un dominio administrado de Azure AD Domain Services.

## <a name="kerberos-constrained-delegation-kcd"></a>Delegación restringida de kerberos (KCD)
La delegación de kerberos permite a una cuenta suplantar a otra entidad de seguridad (por ejemplo, un usuario) para acceder a los recursos. Veamos el caso de una aplicación web que accede a una API web de back-end web en el contexto de un usuario. En este ejemplo, la aplicación web (que se ejecuta en el contexto de una cuenta de servicio o una cuenta de equipo o máquina) suplanta al usuario al acceder a los recursos (API web de back-end). La delegación de kerberos no es segura, ya que no limita los recursos a los que puede acceder la cuenta de suplantación en el contexto del usuario.

La delegación restringida de kerberos (KCD) restringe los servicios y recursos en los que el servidor especificado puede actuar en nombre de un usuario. La KCD tradicional requiere privilegios de administrador de dominio con el fin de configurar una cuenta de dominio para un servicio y restringe la cuenta en un solo dominio.

Asimismo, tiene algunos problemas inherentes. En versiones anteriores del sistema operativo en los que el administrador de dominio configuraba el servicio, el administrador de servicios no tenía ninguna manera útil saber qué servicios de front-end delegaban en los servicios de recurso que poseían. Además, cualquier servicio de front-end que podía delegar en un servicio de recursos representaba un punto de ataque potencial. Si un servidor que hospedaba un servicio de front-end estaba en peligro y configurado para delegar en servicios de recursos, estos también podrían estar en peligro.

> [!NOTE]
> En un dominio administrado de Azure AD Domain Services, no tiene privilegios de administrador de dominios. Por lo tanto, **la KCD tradicional no se puede configurar en un dominio administrado**. Use la KCD basada en recursos, como se describe en este artículo. Este mecanismo también es más seguro.
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>Delegación restringida de kerberos basada en recursos
En Windows Server 2012 R2 y Windows Server 2012, se ha transferido la funcionalidad de configurar la delegación restringida del servicio desde el administrador del dominio hasta el administrador de servicios. De esta manera, el administrador del servicio de back-end puede permitir o denegar servicios de front-end. Este modelo se conoce como **delegación restringida de kerberos basada en recursos**.

La KCD basada en recursos se configura mediante PowerShell. Use los cmdlets Set-ADComputer o Set-ADUser, dependiendo de si la cuenta de suplantación es una cuenta de equipo o una de servicio/usuario.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configuración de la KCD basada en recursos para una cuenta de equipo de un dominio administrado
Supongamos que tiene una aplicación web que se ejecuta en el equipo "contoso100-webapp.contoso100.com". Debe tener acceso al recurso (una API web que se ejecuta en "contoso100-api.contoso100.com") en el contexto de los usuarios del dominio. Este es el procedimiento para configurar la KCD basada en recursos para este escenario.

```
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configuración de la KCD basada en recursos para una cuenta de usuario de un dominio administrado
Supongamos que tiene una aplicación web que se ejecuta como una cuenta de servicio "appsvc" y debe acceder al recurso (una API web que se ejecuta como una cuenta de servicio "backendsvc") en el contexto de los usuarios del dominio. Este es el procedimiento para configurar la KCD basada en recursos para este escenario.

```
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Introducción a la delegación limitada de kerberos](https://technet.microsoft.com/library/jj553400.aspx)
