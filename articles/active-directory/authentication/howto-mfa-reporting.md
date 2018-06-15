---
title: Informes de acceso y uso para Azure MFA | Microsoft Docs
description: Aquí se describe cómo utilizar la característica de informes de Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: a3e7390e0df707c4898ad9573baa96b567499de1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866615"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Informes en Azure Multi-Factor Authentication

Azure Multi-Factor Authentication proporciona varios tipos de informes que usted o su organización pueden usar a través de Azure Portal. En la tabla siguiente se muestran los informes disponibles:

| Informe | Ubicación | DESCRIPCIÓN |
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

   <center>![Nube](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Informes de PowerShell

Identifique los usuarios que se han registrado en MFA mediante el PowerShell siguiente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifique los usuarios que no se han registrado en MFA mediante el PowerShell siguiente.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Pasos siguientes

* [Para los usuarios](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)
* [Lugar de implementación](concept-mfa-whichversion.md)
