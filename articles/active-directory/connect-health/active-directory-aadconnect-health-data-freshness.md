---
title: "Azure AD Connect Health: Alerta sobre que los datos del servicio de mantenimiento no están actualizados | Documentos de Microsoft"
description: "Este documento describe la causa de la alerta \"Los datos del servicio de mantenimiento no están actualizados\" y cómo solucionar el problema."
services: active-directory
documentationcenter: 
author: zhiweiw
manager: maheshu
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta sobre que los datos del servicio de mantenimiento no están actualizados

## <a name="overview"></a>Información general
<li>Azure AD Connect Health genera una alerta de datos actualizada si no recibe todos los puntos de datos del servidor durante dos horas. El título de la alerta es **Los datos del servicio de mantenimiento no están actualizados**. </li>
<li>La alerta con el estado **Advertencia** se desencadena si Connect Health no recibe elementos de datos parciales enviados desde el servidor durante dos horas. La alerta con el estado de advertencia no desencadena notificaciones de correo electrónico para el administrador de inquilinos. </li>
<li>La alerta con el estado **Error** se desencadena si Connect Health no recibe ningún elemento de datos enviado desde el servidor durante dos horas. La alerta con el estado de error desencadena notificaciones de correo electrónico para el administrador de inquilinos. </li>

>[!IMPORTANT] 
> Esta alerta sigue la [directiva de retención de datos](active-directory-aadconnect-health-gdpr.md#data-retention-policy) de Connect Health

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas 
* Asegúrese de repasar y cumplir la [sección de los requisitos](active-directory-aadconnect-health-agent-install.md#requirements).
* Utilice la [herramienta de pruebas de conectividad](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para detectar problemas de conectividad.


## <a name="next-steps"></a>pasos siguientes
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
