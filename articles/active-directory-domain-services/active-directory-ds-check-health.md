---
title: "Azure AD Domain Services: comprobación del estado del dominio administrado | Microsoft Docs"
description: "Compruebe el estado del dominio administrado mediante la página de estado en Azure Portal."
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: a9421ace7abf1f3d45b1f8cd810067d79faa92ec
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Comprobación del estado de un dominio administrado con Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Información general de la página de estado
Mediante la página de estado de Azure Portal, puede mantenerse actualizado sobre lo que está pasando en el dominio administrado. Este artículo le guía por los elementos de la página de estado.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Cómo ver el estado del dominio administrado
1. Vaya a la [página de Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) en Azure Portal.
2. Haga clic en el dominio cuyo estado quiera ver.
3. En el panel de navegación izquierdo, haga clic en **Estado**.

En la siguiente imagen se muestra un ejemplo de la página de estado: ![Ejemplo de la página de estado](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> El estado del dominio administrado se evalúa cada hora. Tras realizar cambios en el dominio administrado, espere hasta el siguiente ciclo de evaluación para ver el estado actualizado del dominio administrado. La marca de tiempo "Última evaluación" de la esquina superior derecha muestra cuándo se evaluó por última vez el estado del dominio administrado.
>

### <a name="status-of-your-managed-domain"></a>Estado del dominio administrado
El estado de la parte superior derecha de la página de mantenimiento indica el estado general del dominio administrado. El estado se tiene en cuenta en todas las alertas existentes en su dominio. También puede ver el estado del dominio en la página de información general de Azure AD Domain Services.

| Status | Icono | Explicación |
| --- | :----: | --- |
| En ejecución | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | El dominio administrado se ejecuta sin problemas y no tiene ninguna alerta crítica ni de advertencia. Este dominio puede tener alertas informativas. |
| Requiere atención (advertencia) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | No hay ninguna alerta crítica en el dominio administrado, pero hay una o varias alertas de advertencia que se deben abordar. |
| Requiere atención (crítico) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Hay una o varias alertas críticas en el dominio administrado. También puede tener alertas de advertencia o informativas. |
| Implementando | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | El dominio está en proceso de implementación. |

## <a name="monitors"></a>Monitores
Los monitores son aspectos del dominio administrado que Azure AD Domain Services supervisa de forma regular. La mejor manera de mantener los monitores en un estado correcto es resolver todas las alertas activas de su dominio administrado.

Actualmente, Azure AD Domain Services supervisa lo siguiente:
 - Backup
 - Sincronización con Azure AD

### <a name="the-backup-monitor"></a>Monitor de "copia de seguridad"
Este monitor supervisa si se realizan copias de seguridad periódicas del dominio administrado. En la siguiente tabla se explica lo que puede esperar en la columna de detalles del monitor de copia de seguridad:

| Valor del detalle | Explicación |
| --- | --- |
|"Nunca se le realizó una copia de seguridad" | Este estado es normal para un dominio administrado recién creado. Por lo general, la primera copia de seguridad se crea 24 horas después de aprovisionar el dominio administrado. Si el dominio administrado no se ha creado recientemente o ve este estado durante un período de tiempo poco habitual, [póngase en contacto con el soporte técnico](active-directory-ds-contact-us.md). |
| La última copia de seguridad se realizó hace entre 1 y 14 días | En general, este es el valor que se espera para el monitor de copia de seguridad. |
| La última copia de seguridad se realizó hace más de 14 días. | Cualquier período superior a dos semanas es un período demasiado largo desde la última copia de seguridad. Las alertas críticas activas pueden evitar que se hagan copias de seguridad del dominio administrado de forma regular. En primer lugar, resuelva todas las alertas activas para el dominio administrado y, a continuación, si el problema persiste, [póngase en contacto con el soporte técnico](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Monitor de "sincronización con Azure AD"
Microsoft supervisa la frecuencia con la que se sincroniza el dominio administrado con Azure Active Directory. El número de objetos (usuarios y grupos) y el número de cambios realizados en el directorio de Azure AD desde la última sincronización pueden afectar al tiempo que puede tardar un período de sincronización. Si el dominio administrado se sincronizó por última vez hace más de tres días, [póngase en contacto con el soporte técnico](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Alertas
Las alertas se generan para los problemas en el dominio administrado que deben solucionarse para que Azure AD Domain Services pueda ejecutarse. Cada alerta explica el problema y proporciona una URL de resolución que muestra los pasos específicos para solucionar el problema. Para ver todas las alertas y sus resoluciones, vaya al artículo [Solución de problemas de alertas](active-directory-ds-troubleshoot-alerts.md).

### <a name="alert-severity"></a>Gravedad de las alertas
Las alertas se categorizan en tres niveles distintos de gravedad: críticas, de advertencia e informativas.

 * Las **alertas críticas** hacen referencia a los problemas que afectan gravemente al dominio administrado. Estas alertas deben abordarse inmediatamente, ya que Microsoft no puede supervisar, administrar, revisar ni sincronizar el dominio administrado. 
 * Las **alertas de advertencia** le notifican problemas que podrían afectar al dominio administrado en el futuro. Estas alertas ofrecen recomendaciones para proteger el dominio administrado.
 * Las **alertas informativas** son notificaciones que no afectan negativamente al dominio. Las alertas informativas están diseñadas para mantenerlo informado sobre lo que sucede en el dominio y Azure AD Domain Services.

## <a name="next-steps"></a>pasos siguientes
- [Resuelva alertas en el dominio administrado](active-directory-ds-troubleshoot-alerts.md)
- [Más información sobre Azure AD Domain Services](active-directory-ds-overview.md)
- [Póngase en contacto con el equipo de productos](active-directory-ds-contact-us.md)
