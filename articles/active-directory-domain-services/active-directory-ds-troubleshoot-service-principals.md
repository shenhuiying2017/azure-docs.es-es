---
title: 'Azure Active Directory Domain Services: solución de problemas de configuración de la entidad de servicio | Microsoft Docs'
description: Solución de problemas de configuración de la entidad de servicio para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: d1a605ae5c0ea598ba507de0b21a841333df79ef
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Solución de problemas de configuración de entidad de servicio no válida para el dominio administrado

Este artículo le ayuda a solucionar problemas y resolver errores de configuración relacionados con la entidad de servicio que generan el mensaje de alerta siguiente:

## <a name="alert-aadds102-service-principal-not-found"></a>Alerta AADDS102: no se encuentra la entidad de servicio

**Mensaje de alerta:** *Una entidad de servicio necesaria para que Azure AD Domain Services funcione correctamente se ha eliminado del directorio de Azure AD. Esta configuración afecta a la capacidad de Microsoft para supervisar, administrar, revisar y sincronizar el dominio administrado.*

Las [entidades de servicio](../active-directory/develop/active-directory-application-objects.md) son aplicaciones que Microsoft usa para administrar, actualizar y mantener el dominio administrado. Si se eliminan, Microsoft deja de mantener el dominio.


## <a name="check-for-missing-service-principals"></a>Comprobación de las entidades de servicio que faltan
Utilice los pasos siguientes para determinar qué entidades de servicio deben volver a crearse:

1. Vaya a la página [Aplicaciones empresariales - Todas las aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) de Azure Portal.
2. En la lista desplegable **Mostrar**, seleccione **Todas las aplicaciones** y haga clic en **Aplicar**.
3. En la tabla siguiente, busque los identificadores de aplicación; para ello, pegue los identificadores uno a uno en el cuadro de búsqueda y presione Entrar. Si los resultados de búsqueda están vacíos, debe volver a crear la entidad de servicio mediante los pasos descritos en la columna "Resolución".

| Identificador de aplicación | Resolución |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Cómo volver a crear una entidad de servicio con PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Cómo volver a realizar el registro en el espacio de nombres de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Cómo volver a realizar el registro en el espacio de nombres de Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Entidades de servicio que se corrigen solas](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Cómo volver a crear una entidad de servicio con PowerShell
Siga estos pasos si una entidad de servicio con el identificador ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` no está en el directorio de Azure AD.

**Solución:** necesita Azure AD PowerShell para completar estos pasos. Para información sobre la instalación de Azure AD PowerShell, consulte [este artículo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para solucionar este problema, escriba los siguientes comandos en una ventana de PowerShell:
1. Instale el módulo de PowerShell de Azure AD e impórtelo.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Compruebe si la entidad de servicio necesaria para Azure AD Domain Services falta en el directorio; para ello, ejecute el siguiente comando de PowerShell:

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. Escriba el siguiente comando de PowerShell para crear la entidad de servicio:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. Después de crear la entidad de servicio que faltaba, espere dos horas y compruebe el estado del dominio administrado.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Cómo volver a realizar el registro en el espacio de nombres de Microsoft.AAD mediante Azure Portal
Siga estos pasos si una entidad de servicio con el identificador ```443155a6-77f3-45e3-882b-22b3a8d431fb``` o ```abba844e-bc0e-44b0-947a-dc74e5d09022``` no está en el directorio de Azure AD.

**Solución:** siga estos pasos para restaurar Domain Services en el directorio:

1. Vaya a la página [Suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) de Azure Portal.
2. Seleccione la suscripción de la tabla asociada con el dominio administrado.
3. En el panel de navegación izquierdo, elija **Proveedores de recursos**.
4. Busque "Microsoft.AAD" en la tabla y haga clic en **Volver a registrar**.
5. Para asegurarse de que la alerta se resuelve, consulte la página de estado del dominio administrado en dos horas.


## <a name="service-principals-that-self-correct"></a>Entidades de servicio que se corrigen solas
Siga estos pasos si una entidad de servicio con el identificador ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` no está en el directorio de Azure AD.

**Solución:** Azure AD Domain Services puede detectar el momento en el que esta entidad de servicio específica falta, está mal configurada o se ha eliminado. El servicio volverá a crear automáticamente esta entidad de servicio. Sin embargo, deberá eliminar la aplicación y el objeto que funcionaba con la aplicación eliminada, ya que cuando la certificación se sustituya, la nueva entidad de servicio no podrá modificar la aplicación y el objeto. Esto dará lugar a un nuevo error en el dominio. Para evitar este problema, siga los pasos descritos en la [sección de AADDS105](#alert-aadds105-password-synchronization-application-is-out-of-date). A continuación, compruebe el mantenimiento del dominio administrado transcurridas dos horas para asegurarse de que la entidad de servicio se ha vuelto a crear.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerta AADDS105: La aplicación de sincronización de contraseña no está actualizada

**Mensaje de alerta:** Se eliminó la entidad de servicio con el identificador de aplicación "d87dcbc6-a371-462e-88e3-28ad15ec4e64" y, a continuación, se volvió a crear. Esta entidad de servicio administra otra entidad de servicio y una aplicación que se usan para la sincronización de contraseña. La entidad de servicio administrada o la aplicación no tenían autorización en la entidad de servicio recién creada, por lo que nuestro servicio no puede administrarlas. Esto significa que la entidad de servicio recién creada no podrá actualizar las aplicaciones administradas antiguas y la sincronización de contraseñas resultará afectada.


**Solución:** necesita Azure AD PowerShell para completar estos pasos. Para información sobre la instalación de Azure AD PowerShell, consulte [este artículo](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Para solucionar este problema, escriba los siguientes comandos en una ventana de PowerShell:
1. Instale el módulo de PowerShell de Azure AD e impórtelo.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Elimine la aplicación y el objeto antiguos mediante los siguientes comandos de PowerShell.

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Después de haber eliminado ambos, el sistema se corrige autónomamente y se vuelven a crear las aplicaciones necesarias para la sincronización de contraseña. Para asegurarse de que la alerta se ha solucionado, espere dos horas y compruebe el mantenimiento del dominio.


## <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
