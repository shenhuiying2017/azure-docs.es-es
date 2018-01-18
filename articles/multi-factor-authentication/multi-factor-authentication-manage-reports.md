---
title: Informes de acceso y uso para Azure MFA | Microsoft Docs
description: "Aquí se describe cómo utilizar la característica de informes de Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 696f4ae3cb479a208e73e53a9a9a437caeabd294
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Informes en Azure Multi-Factor Authentication

Azure Multi-Factor Authentication proporciona varios tipos de informes que usted o su organización pueden usar a través de Azure Portal. En la tabla siguiente se muestran los informes disponibles:

| Informe | La ubicación | DESCRIPCIÓN |
|:--- |:--- |:--- |
| Historial de usuarios bloqueados | Azure AD > Servidor MFA > Bloquear o desbloquear usuarios | Muestra el historial de solicitudes para bloquear o desbloquear usuarios. |
| Alertas de fraude y de uso | Azure AD > Inicios de sesión | Proporciona información sobre el uso general, el resumen del usuario, detalles del usuario; así como un historial de alertas de fraude enviadas durante el intervalo de fechas especificado. |
| Uso de componentes locales | Azure AD > Servidor MFA > Informe de actividad | Proporciona información sobre el uso general de MFA a través de la extensión NPS, AD FS y el servidor MFA. |
| Historial de usuarios omitidos | Azure AD > Servidor MFA > Omisión por única vez | Proporciona un historial de solicitudes para omitir Multi-Factor Authentication para un usuario. |
| Estado del servidor | Azure AD > Servidor MFA > Estado del servidor | Muestra el estado de los servidores de Multi-Factor Authentication asociados a su cuenta. |

## <a name="view-reports"></a>Ver informes 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. A la izquierda, seleccione **Azure Active Directory** > **MFA Server** (Servidor MFA).
3. Seleccione el informe que desee ver.

   <center>![Nube](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Informes de PowerShell

Identifique los usuarios que se han registrado en MFA mediante el PowerShell siguiente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifique los usuarios que no se han registrado en MFA mediante el PowerShell siguiente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>pasos siguientes

* [Para los usuarios](end-user/multi-factor-authentication-end-user.md)
* [Lugar de implementación](multi-factor-authentication-get-started.md)
