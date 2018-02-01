---
title: "Azure Active Directory Domain Services: solución de problemas de configuración de la entidad de servicio | Microsoft Docs"
description: "Solución de problemas de configuración de la entidad de servicio para Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: ergreenl
ms.openlocfilehash: e6144a4018f7fbe7dbf7b4693e3f41884e4a80a2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-service-principal-configuration"></a>Azure AD Domain Services: solución de problemas de configuración de la entidad de servicio

Para el mantenimiento, la administración y la actualización del dominio, Microsoft se comunica con el directorio mediante distintas [entidades de servicio](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects). Si una está mal configurada o se ha eliminado, puede provocar una interrupción del servicio.

## <a name="aadds102-service-principal-not-found"></a>AADDS102: no se encuentra la entidad de seguridad de servicio

**Mensaje de alerta:**

*Una entidad de servicio necesaria para que Azure AD Domain Services funcione correctamente se ha eliminado del directorio de Azure AD. Esta configuración afecta a la capacidad de Microsoft para supervisar, administrar, revisar y sincronizar el dominio administrado.*

Las entidades de servicio son aplicaciones que Microsoft usa para administrar, actualizar y mantener el dominio administrado. Si se eliminan, Microsoft deja de mantener el dominio. Utilice los pasos siguientes para determinar qué entidades de servicio deben volver a crearse.

1. Vaya a la página [Aplicaciones empresariales - Todas las aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) de Azure Portal.
2. Con la lista desplegable Mostrar, seleccione **Todas las aplicaciones** y haga clic en **Aplicar**.
3. En la tabla siguiente, busque los identificadores de aplicación; para ello, pegue los identificadores uno a uno en el cuadro de búsqueda y presione Entrar. Si los resultados de búsqueda están vacíos, debe volver a crear la entidad de servicio mediante los pasos descritos en la columna "Resolución".

| Identificador de aplicación | Resolución |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Cómo volver a crear una entidad de servicio con PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Cómo volver a realizar el registro en el espacio de nombres de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Cómo volver a realizar el registro en el espacio de nombres de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Entidades de servicio que se corrigen solas](#service-principals-that-self-correct) |

### <a name="recreate-a-missing-service-principal-with-powershell"></a>Cómo volver a crear una entidad de servicio con PowerShell

*Siga estos pasos para los identificadores que falten: 2565bd9d-da50-47d4-8b85-4c97f669dc36*

**Corrección:**

Para completar estos pasos, necesita Azure AD PowerShell. Para información sobre la instalación de Azure AD PowerShell, consulte [este artículo](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para solucionar este problema, escriba los siguientes comandos en una ventana de PowerShell:
1. Install-Module AzureAD
2. Import-Module AzureAD
3. Compruebe si la entidad de servicio necesaria para Azure AD Domain Services falta en el directorio; para ello, ejecute el siguiente comando de PowerShell:
      ```PowerShell
      Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
      ```
4. Escriba el siguiente comando de PowerShell para crear la entidad de servicio:
     ```PowerShell
     New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
     ```
5. Después de haber creado a la entidad de servicio que faltaba, espere dos horas y compruebe el estado del dominio.


### <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Cómo volver a realizar el registro en el espacio de nombres de Microsoft.AAD mediante Azure Portal

*Siga estos pasos para los identificadores que falten: 443155a6-77f3-45e3-882b-22b3a8d431fb y abba844e-bc0e-44b0-947a-dc74e5d09022*

**Corrección:**

Siga estos pasos para restaurar Domain Services en el directorio:

1. Vaya a la página [Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) de Azure Portal.
2. Seleccione la suscripción de la tabla asociada con el dominio administrado.
3. En el panel de navegación izquierdo, elija **Proveedores de recursos**.
4. Busque "Microsoft.AAD" en la tabla y haga clic en **Volver a registrar**.
5. Para asegurarse de que la alerta se resuelve, consulte la página de alertas de estado en un plazo de dos horas.


### <a name="service-principals-that-self-correct"></a>Entidades de servicio que se corrigen solas

*Siga estos pasos para los identificadores que falten: d87dcbc6-a371-462e-88e3-28ad15ec4e64*

**Corrección:**

Microsoft puede identificar cuándo faltan entidades de seguridad de servicio específicas o cuándo están más configuradas o se han eliminado. Para solucionar el servicio rápidamente, Microsoft vuelve a crear por sí solo las entidades de servicio. Compruebe el estado del dominio a las dos horas para asegurarse de que la entidad de servicio se ha vuelto a crear.

## <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
