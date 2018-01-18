---
title: Versiones de Azure MFA y planes de consumo | Microsoft Docs
description: "Información sobre el cliente de Multi-Factor Authentication y los distintos métodos y versiones disponibles. Detalles sobre cada plan de consumo"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: af86434e1205d67829fc7079d97a37f013c0f2d8
ms.sourcegitcommit: 7d4b3cf1fc9883c945a63270d3af1f86e3bfb22a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Cómo conseguir Azure Multi-Factor Authentication

En lo referente a la protección de las cuentas, la comprobación en dos pasos debe ser la norma en cualquier organización. Esta característica es especialmente importante en las cuentas administrativas que tienen acceso con privilegios a los recursos. Por esta razón, Microsoft ofrece a los administradores de Office 365 y Azure características básicas de verificación en dos pasos sin cargo adicional. Si quiere actualizar las características para los administradores o extender la verificación en dos pasos al resto de los usuarios, puede comprar Azure Multi-Factor Authentication. 

En este artículo se explica la diferencia entre las versiones que se ofrecen a los administradores y la versión completa de Azure MFA. Si está listo para implementar la oferta completa de Azure MFA, las secciones posteriores tratan las opciones de implementación y cómo Microsoft calcula el consumo.


>[!IMPORTANT]
>Este artículo pretende ser una guía para ayudarle a entender las diferentes maneras de comprar Azure Multi-Factor Authentication. Para información específica sobre precios y facturación, consulte siempre la [página de precios de Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versiones disponibles de Azure Multi-Factor Authentication

En la tabla siguiente se describe las diferencias entre las tres versiones de autenticación multifactor:

| Versión | DESCRIPCIÓN |
| --- | --- |
| Multi-Factor Authentication para Office 365 |Esta versión funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. Los administradores pueden [proteger los recursos de Office 365 con la comprobación en dos pasos](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Esta versión forma parte de una suscripción a Office 365. |
| Multi-Factor Authentication para administradores de Azure AD | Los usuarios asignados al rol de administrador global en inquilinos de Azure AD pueden habilitar la verificación en dos pasos para sus cuentas de administrador global de Azure AD sin costo adicional alguno.|
| Azure Multi-Factor Authentication | Conocida con frecuencia como la versión "completa", Azure Multi-Factor Authentication ofrece el conjunto más rico de funcionalidades. Proporciona opciones de configuración adicionales a través de [Azure Portal](https://portal.azure.com), funcionalidades de generación de informes avanzadas y compatibilidad con una amplia variedad de aplicaciones locales y en la nube. Azure Multi-Factor Authentication se incluye en los [planes de Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) y en los [planes de Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing), y se puede implementar en la nube o de forma local. |

## <a name="feature-comparison-of-versions"></a>Comparación de características de las versiones
En la tabla siguiente se proporciona una lista de las características que están disponibles en las distintas versiones de Azure Multi-Factor Authentication.

> [!NOTE]
> En esta tabla comparativa, se describen las características incluidas en cada versión de Multi-Factor Authentication. En caso de que tenga el servicio completo de Azure Multi-Factor Authentication, es posible que algunas características no estén disponibles en función de si usa [MFA en la nube o MFA en local](multi-factor-authentication-get-started.md).


| Característica | Multi-Factor Authentication para Office 365 | Multi-Factor Authentication para administradores de Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Protección de cuentas de administración de Azure AD con MFA |● |● (Solo cuentas de administrador global de Azure AD) |● |
| Aplicación móvil como segundo factor |● |● |● |
| Llamada de teléfono como segundo factor |● |● |● |
| SMS como segundo factor |● |● |● |
| Contraseñas de aplicación para clientes que no admiten MFA |● |● |● |
| Control administrativo sobre métodos de comprobación |● |● |● |
| Protección de cuentas no de administrador con MFA |● (Aplicaciones de Office 365) | |● |
| Modo de PIN | | |● |
| Alerta de fraude | | |● |
| Informes de MFA | | |● |
| Omisión por única vez | | |● |
| Saludos personalizados para las llamadas de teléfono | | |● |
| Identificador de llamada personalizado para llamadas telefónicas | | |● |
| IP de confianza | | |● |
| Recordar MFA para dispositivos de confianza |● |● |● |
| SDK de MFA | | |● (En desuso) | 
| MFA para aplicaciones locales | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Cómo conseguir Azure Multi-Factor Authentication
Si desea todas las funcionalidades que ofrece Azure Multi-Factor Authentication, hay varias opciones:

### <a name="option-1---mfa-licenses"></a>Opción 1: Licencias de MFA

Compre licencias de Azure Multi-Factor Authentication y asígnelas a los usuarios de Azure Active Directory. 

Si usa esta opción, cree un proveedor de Azure Multi-Factor Authentication solo si tiene que proporcionar verificación en dos pasos para algunos usuarios que no tienen licencia. Si no lo hace, le podrían facturar dos veces.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Opción 2: Paquete de licencias que incluyen MFA

Compre licencias que incluyan Azure Multi-Factor Authentication, como Azure Active Directory Premium o Enterprise Mobility + Security, y asígnelas a los usuarios de Azure Active Directory. 

Si usa esta opción, debe crear un proveedor de Azure Multi-Factor Authentication solo si también tiene que proporcionar comprobación en dos pasos para algunos usuarios que no tienen licencia. Si no lo hace, le podrían facturar dos veces. 

### <a name="option-3---mfa-consumption-based-model"></a>Opción 3: Modelo basado en el consumo de MFA

Crear un proveedor de Azure Multi-Factor Authentication dentro de una suscripción a Azure. Los proveedores de Azure MFA son recursos de Azure que se facturan según el contrato Enterprise, el compromiso monetario de Azure o con tarjeta de crédito, igual que todos los demás recursos de Azure. Estos proveedores solo pueden crearse en suscripciones completas de Azure, y no en suscripciones limitadas que tienen un límite de gasto de 0 $. Las suscripciones limitadas se crean cuando se activan licencias, como en las opciones 1 y 2. 

Si se utiliza un proveedor de Azure Multi-Factor Authentication, hay dos modelos de uso disponibles que se facturan a través de la suscripción a Azure:  

1. **Por usuario habilitado**: para empresas que quieren habilitar la comprobación en dos pasos para un número fijo de empleados que con frecuencia necesitan autenticación. La facturación por usuario se basa en el número de usuarios habilitados para MFA en el inquilino de Azure AD y en la instancia de Servidor Azure MFA. Si los usuarios están habilitados para MFA en Azure AD y en el Servidor Azure MFA y se habilita la sincronización de dominios (Azure AD Connect), contamos el conjunto más grande de usuarios. Si la sincronización de dominios no está habilitada, contamos la suma de todos los usuarios habilitados para MFA en Azure AD y el Servidor Azure MFA. La facturación se prorratea y se notifica al sistema de comercio diariamente. 

  > [!NOTE]
  > Ejemplo de facturación 1: hoy tiene 5000 usuarios habilitados para MFA. El sistema MFA divide ese número entre 31 y notifica 161,29 usuarios ese día. Mañana habilita 15 usuarios más, así que el sistema MFA notifica 161,77 usuarios ese día. Al final del ciclo de facturación, el número total de usuarios que se facturan con su suscripción de Azure asciende aproximadamente a 5000. 
  >
  > Ejemplo de facturación 2: tiene una mezcla de usuarios con licencia y sin licencia, por lo que tiene un proveedor de Azure MFA por usuario para compensar la diferencia. Hay 4500 licencias de Enterprise Mobility + Security en el inquilino, pero 5000 usuarios habilitados para MFA. En su suscripción de Azure se facturan 500 usuarios, y se prorratean y notifican diariamente como 16,13 usuarios. 

2. **Por autenticación**: para empresas que quieren habilitar la comprobación en dos pasos para un número mayor de usuarios que no suelen necesitar frecuentemente autenticación. La facturación se basa en el número de solicitudes de verificación en dos pasos recibidas, con independencia de si esas verificaciones se han aceptado o denegado. Esta facturación aparece en el extracto de uso de Azure en paquetes de diez autenticaciones y se notifica diariamente. 

  > [!NOTE]
  > Ejemplo de facturación 3: hoy, el servicio Azure MFA recibió 3105 comprobaciones en dos pasos. En su suscripción de Azure se facturan 310,5 paquetes autenticación. 

Es importante advertir que aunque tenga licencias de Azure MFA, se le puede seguir facturando según la configuración basada en el consumo. Si configura un proveedor de Azure MFA por autenticación, se le factura cada solicitud de comprobación en dos pasos, aunque las hayan realizado usuarios con licencia. Si ha configurado un proveedor de Azure MFA por usuario en un dominio que no está vinculado a su inquilino de Azure AD, se le facturará por usuario habilitado, incluso si los usuarios tienen licencias en Azure AD. 

## <a name="next-steps"></a>pasos siguientes

- Para más información sobre los precios, consulte [Precios de Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Elija si va a implementar Azure MFA [en la nube o de forma local](multi-factor-authentication-get-started.md).
