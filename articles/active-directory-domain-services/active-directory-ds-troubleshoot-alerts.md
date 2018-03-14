---
title: "Azure Active Directory Domain Services: solución de problemas de alertas | Microsoft Docs"
description: "Solución de problemas de alertas para Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: 2f2ebb1dcc8bed86348389d6a5a7c274194efde0
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services: solución de problemas de alertas
En este artículo se brinda guías para la solución de problemas de cualquier alerta que pueda recibir en su dominio administrado.


Elija los pasos de solución de problemas que correspondan al mensaje o al identificador de alerta que encuentre.

| **Id. de alerta** | **Mensaje de alerta** | **Resolución** |
| --- | --- | :--- |
| AADDS001 | *LDAP seguro a través de Internet está habilitado para el dominio administrado. Sin embargo, el acceso al puerto 636 no está bloqueado mediante un grupo de seguridad de red. Esto puede exponer las cuentas de usuario del dominio administrado a los ataques de fuerza bruta de contraseñas.* | [Configuración incorrecta de LDAP seguro](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | *Es posible que se haya eliminado el directorio de Azure AD asociado con su dominio administrado. El dominio administrado ya no está en una configuración admitida. Microsoft no puede supervisar, administrar, revisar ni sincronizar el dominio administrado.* | [Falta un directorio](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services no se puede habilitar en un directorio de Azure AD B2C.* | [Azure AD B2C se ejecuta en este directorio](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Una entidad de servicio necesaria para que Azure AD Domain Services funcione correctamente se ha eliminado del directorio de Azure AD. Esta configuración afecta a la capacidad de Microsoft para supervisar, administrar, revisar y sincronizar el dominio administrado.* | [Falta una entidad de servicio](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | *El intervalo de direcciones IP de la red virtual en la que habilitó Azure AD Domain Services está en un intervalo IP público. Azure AD Domain Services debe estar habilitado en una red virtual con un intervalo de direcciones IP privado. Esta configuración afecta a la capacidad de Microsoft para supervisar, administrar, revisar y sincronizar el dominio administrado.* | [La dirección está en un intervalo IP público](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *Microsoft no puede tener acceso a los controladores de dominio de este dominio administrado. Esto puede ocurrir si un grupo de seguridad de red (NSG) configurado en la red virtual bloquea el acceso al dominio administrado. Otro motivo posible es que hay una ruta definida por el usuario que bloquea el tráfico entrante desde Internet.* | [Error de red](active-directory-ds-troubleshoot-nsg.md) |
| AADDS500 | *El dominio administrado se sincronizó por última vez con Azure AD el {0}. Los usuarios no pueden iniciar sesión en el dominio o puede que las pertenencias a grupos no estén sincronizadas con Azure AD.* | [Hace un tiempo que no se realiza una sincronización](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *Se hizo copia de seguridad del dominio administrado por última vez el XX.* | [Hace un tiempo que no se realiza una copia de seguridad](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *El certificado LDAP seguro del dominio administrado expirará el XX.* | [Expiración del certificado LDAP seguro](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *El dominio administrado se suspende debido a que la suscripción de Azure asociada al dominio no está activa.* | [Suspensión debida a una suscripción deshabilitada](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *El dominio administrado se suspende debido a una configuración no válida. El servicio lleva mucho tiempo sin poder administrar, actualizar o aplicar revisiones a los controladores de dominio en el dominio administrado.* | [Suspensión debida a una configuración no válida](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: falta un directorio
**Mensaje de alerta:**

*Es posible que se haya eliminado el directorio de Azure AD asociado con su dominio administrado. El dominio administrado ya no está en una configuración admitida. Microsoft no puede supervisar, administrar, revisar ni sincronizar el dominio administrado.*

**Corrección:**

Este error habitualmente se produce por la migración incorrecta de la suscripción de Azure a un directorio de Azure AD nuevo y la eliminación del directorio de Azure AD anterior que sigue asociado con Azure AD Domain Services.

Este error es irrecuperable. Para resolverlo, debe [eliminar el dominio administrado existente](active-directory-ds-disable-aadds.md) y volver a crearlo en el directorio nuevo. Si tiene problemas para eliminarlo, póngase en contacto con el equipo de productos de Azure Active Directory Domain Services [para obtener asistencia](active-directory-ds-contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C se ejecuta en este directorio
**Mensaje de alerta:**

*Azure AD Domain Services no se puede habilitar en un directorio de Azure AD B2C.*

**Corrección:**

>[!NOTE]
>Para seguir usando Azure AD Domain Services, debe volver a crear la instancia de Azure AD Domain Services en un directorio distinto de Azure AD B2C.

Para restaurar el servicio, siga estos pasos:

1. [Elimine el dominio administrado](active-directory-ds-disable-aadds.md) del directorio de Azure AD existente.
2. Cree un directorio que no sea un directorio de Azure AD B2C.
3. Siga la guía de [introducción](active-directory-ds-getting-started.md) para volver a crear un dominio administrado.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: la dirección está en un intervalo IP público

**Mensaje de alerta:**

*El intervalo de direcciones IP de la red virtual en la que habilitó Azure AD Domain Services está en un intervalo IP público. Azure AD Domain Services debe estar habilitado en una red virtual con un intervalo de direcciones IP privado. Esta configuración afecta a la capacidad de Microsoft para supervisar, administrar, revisar y sincronizar el dominio administrado.*

**Corrección:**

> [!NOTE]
> Para solucionar este problema, debe eliminar el dominio administrado existente y volver a crearlo en una red virtual con un intervalo de direcciones IP privado. Este proceso puede ser perjudicial.

Antes de comenzar, lea la sección sobre **el espacio de direcciones IPv4 privado** en [este artículo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro de la red virtual, las máquinas pueden realizar solicitudes a los recursos de Azure que se encuentran en el mismo intervalo de direcciones IP que los configurados para la subred. Sin embargo, puesto que la red virtual se configura para este intervalo, esas solicitudes se enrutarán dentro de la red virtual y no llegarán a los recursos web deseados. Esta configuración puede conducir a errores impredecibles con Azure AD Domain Services.

**Si es propietario del intervalo de direcciones IP que está configurado en la red virtual, puede omitir esta alerta. Sin embargo, no se puede confirmar Azure AD Domain Services para el [Acuerdo de nivel de servicio](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] con esta configuración, ya que puede conducir a errores impredecibles.**


1. [Elimine el dominio administrado](active-directory-ds-disable-aadds.md) del directorio.
2. Corrija el intervalo de direcciones IP de la subred.
  1. Navegue a la [página de redes virtuales en Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
  2. Seleccione la red virtual que planea usar para Azure AD Domain Services.
  3. En Configuración, haga clic en **Espacio de direcciones**.
  4. Para actualizar el intervalo de direcciones, haga clic en el existente y edítelo o agregue un intervalo de direcciones adicional. Asegúrese de que el intervalo de direcciones nuevo esté en un intervalo IP privado. Guarde los cambios.
  5. En el menú de navegación de la izquierda, haga clic en **Subredes**.
  6. Haga clic en la subred que desea editar en la tabla.
  7. Actualice el intervalo de direcciones y guarde los cambios.
3. Siga la [guía de introducción al uso de Azure AD Domain Services](active-directory-ds-getting-started.md) para volver a crear el dominio administrado. Asegúrese de elegir una red virtual con un intervalo de direcciones IP privado.
4. Para unir las máquinas virtuales al dominio nuevo, siga [esta guía](active-directory-ds-admin-guide-join-windows-vm-portal.md).
8. Para asegurarse de que la alerta se resuelve, compruebe el estado de su dominio transcurridas dos horas.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: Hace un tiempo que no se realiza una sincronización

**Mensaje de alerta:**

*El dominio administrado se sincronizó por última vez con Azure AD el {0}. Los usuarios no pueden iniciar sesión en el dominio o puede que las pertenencias a grupos no estén sincronizadas con Azure AD.*

**Corrección:**

[Compruebe el mantenimiento del dominio](active-directory-ds-check-health.md) para ver las alertas que podrían indicar problemas en la configuración del dominio administrado. En ocasiones, los problemas con la configuración pueden bloquear la capacidad de Microsoft para sincronizar el dominio administrado. Si puede resolver las alertas, espere dos horas y compruebe de nuevo para ver si se ha completado la sincronización.


## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Hace un tiempo que no se realiza una copia de seguridad

**Mensaje de alerta:**

*Se hizo copia de seguridad del dominio administrado por última vez el XX.*

**Corrección:**

[Compruebe el mantenimiento del dominio](active-directory-ds-check-health.md) para ver las alertas que podrían indicar problemas en la configuración del dominio administrado. En ocasiones, los problemas con la configuración pueden bloquear la capacidad de Microsoft para sincronizar el dominio administrado. Si puede resolver las alertas, espere dos horas y compruebe de nuevo para ver si se ha completado la sincronización.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensión debida a una suscripción deshabilitada

**Mensaje de alerta:**

*El dominio administrado se suspende debido a que la suscripción de Azure asociada al dominio no está activa.*

**Corrección:**

Para restaurar el servicio, [renueve su suscripción de Azure](https://docs.microsoft.com/en-us/azure/billing/billing-subscription-become-disable) asociada con el dominio administrado.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensión debida a una configuración no válida

**Mensaje de alerta:**

*El dominio administrado se suspende debido a una configuración no válida. El servicio lleva mucho tiempo sin poder administrar, actualizar o aplicar revisiones a los controladores de dominio en el dominio administrado.*

**Corrección:**

[Compruebe el mantenimiento del dominio](active-directory-ds-check-health.md) para ver las alertas que podrían indicar problemas en la configuración del dominio administrado. Si puede resolver alguna de estas alertas, hágalo. Después, póngase en contacto con el servicio de soporte técnico para volver a habilitar la suscripción.

## <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
