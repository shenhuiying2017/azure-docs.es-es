---
title: "Azure Active Directory Domain Services: solución de problemas de configuración del protocolo LDAP seguro | Microsoft Docs"
description: "Solución de problemas del protocolo LDAP seguro para Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: f80de47f8e9ba326fc224a8bc09f8029b5afd1ca
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services: solución de problemas de configuración del protocolo LDAP seguro

Este artículo proporciona soluciones para los problemas comunes de [configuración del protocolo LDAP seguro](active-directory-ds-admin-guide-configure-secure-ldap.md) para Azure AD Domain Services.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: configuración del grupo de seguridad de red de LDAP seguro

**Mensaje de alerta:**

*LDAP seguro a través de internet está habilitado para el dominio administrado. Sin embargo, el acceso al puerto 636 no está bloqueado mediante un grupo de seguridad de red. Esto puede exponer las cuentas de usuario del dominio administrado a los ataques de fuerza bruta de contraseña.*

### <a name="secure-ldap-port"></a>Puerto de LDAP seguro

Con LDAP seguro habilitado, se recomienda crear reglas adicionales para permitir el acceso de entrada LDAPS solo desde determinadas direcciones IP. Estas reglas protegen el dominio de los ataques de fuerza bruta que pueden suponer una amenaza de seguridad. El puerto 636 permite el acceso al dominio administrado. Aquí se muestra cómo actualizar el grupo de seguridad de red para permitir el acceso de LDAP seguro:

1. Vaya a la pestaña [Grupos de seguridad de red](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) de Azure Portal
2. Elija el grupo de seguridad de red asociado al dominio de la tabla.
3. Haga clic en **Reglas de seguridad de entrada**
4. Cree la regla de puerto 636
   1. Haga clic en **Agregar en la barra de navegación superior**.
   2. Elija **Direcciones IP** como origen.
   3. Especifique los intervalos de puerto de origen para esta regla.
   4. Escriba "636" como intervalos de puerto de destino.
   5. El protocolo es **TCP**.
   6. Asigne a la regla un nombre adecuado, la descripción y la prioridad. La prioridad de la regla debe ser mayor que la de "denegar todo", si tiene una.
   7. Haga clic en **OK**.
5. Verifique que se ha creado la regla.
6. Compruebe el estado del dominio en dos horas para asegurarse de que ha completado los pasos correctamente.

> [!TIP]
> El puerto 636 no es la única regla necesaria para que se ejecuten sin problemas Azure AD Domain Services. Para obtener más información, consulte los artículos sobre las [directrices de redes](active-directory-ds-networking.md) o sobre la [solución de problemas de configuración de grupo de seguridad de red](active-directory-ds-troubleshoot-nsg.md).
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Expiración del certificado LDAP seguro

**Mensaje de alerta:**

*El certificado LDAP seguro del dominio administrado expirará el XX.*

**Corrección:**

Cree un nuevo certificado LDAP seguro mediante los pasos descritos en el artículo [Configuración de LDAP seguro](active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
