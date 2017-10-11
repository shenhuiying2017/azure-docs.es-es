---
title: "Guía de solución de problemas de Azure Active Directory Domain Services | Microsoft Docs"
description: "Guía de solución de problemas Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: d6695b0c40f56093e8701dfe6394143268114453
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Guía de solución de problemas de Azure AD Domain Services
En este artículo se ofrecen sugerencias de solución de problemas para los problemas que puede encontrar al configurar o administrar Servicios de dominio de Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>No puede habilitar Azure AD Domain Services para su directorio de Azure AD
Esta sección ofrece ayuda para solucionar errores cuando intente habilitar Azure AD Domain Services para su directorio y se produzca un error o se vuelva a cambiar a Deshabilitado.

Elija los pasos de solución de problemas que correspondan al mensaje de error que encuentre.

| **Mensaje de error** | **Resolución** |
| --- |:--- |
| *El nombre contoso100.com ya se está usando en esta red. Especifique un nombre que no esté en uso.* |[Conflicto de nombres de dominio en la red virtual](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *No se pudo habilitar Domain Services en este inquilino de Azure AD. El servicio no tiene los permisos adecuados para la aplicación llamada 'Azure AD Domain Services Sync'. Elimine la aplicación llamada "Azure AD Domain Services Sync" e intente habilitar Domain Services para el inquilino de Azure AD.)* |[Domain Services carece de permisos suficientes para la aplicación Azure AD Domain Services Sync](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *No se pudo habilitar Domain Services en este inquilino de Azure AD. La aplicación de Domain Services en el inquilino de Azure AD carece de los permisos necesarios para habilitar Domain Services. Elimine la aplicación con el identificador d87dcbc6-a371-462e-88e3-28ad15ec4e64 e intente habilitar Domain Services para el inquilino de Azure AD.* |[La aplicación de Domain Services no está configurada correctamente en el inquilino](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *No se pudo habilitar Domain Services en este inquilino de Azure AD. La aplicación Microsoft Azure AD está deshabilitada en el inquilino de Azure AD. Habilite la aplicación con el identificador 00000002-0000-0000-c000-000000000000 e intente habilitar Domain Services para el inquilino de Azure AD.* |[La aplicación Microsoft Graph está deshabilitada en el inquilino de Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflicto de nombres de dominio
**Mensaje de error:**

*El nombre contoso100.com ya se está usando en esta red. Especifique un nombre que no esté en uso.*

**Corrección:**

Asegúrese de que no tenga un dominio existente con el mismo nombre de dominio disponible en esa red virtual. Por ejemplo, supongamos que tiene un dominio llamado 'contoso.com' ya disponible en la red virtual seleccionada. Posteriormente, intenta habilitar un dominio administrado de Azure AD Domain Services con el mismo nombre de dominio (es decir, contoso.com) en esa red virtual. Al intentar habilitar Azure AD Domain Services, aparece un error,

que se debe a los conflictos de nombre en el nombre de dominio de la red virtual. En esta situación, debe utilizar un nombre diferente para configurar el dominio administrado de los Servicios de dominio de Azure AD. Como alternativa, puede aprovisionar el dominio existente y luego proceder a habilitar los Servicios de dominio de Azure AD.

### <a name="inadequate-permissions"></a>Permisos insuficientes
**Mensaje de error:**

*No se pudo habilitar Domain Services en este inquilino de Azure AD. El servicio no tiene los permisos adecuados para la aplicación llamada 'Azure AD Domain Services Sync'. Elimine la aplicación llamada "Azure AD Domain Services Sync" e intente habilitar Domain Services para el inquilino de Azure AD.)*

**Corrección:**

Compruebe si hay una aplicación con el nombre "Azure AD Domain Services Sync" en el directorio de Azure AD. Si existe, elimínela y vuelva a habilitar Azure AD Domain Services.

Realice los pasos siguientes para comprobar la presencia de la aplicación y eliminarla, en caso de que exista:

1. Navegue hasta el **Portal de Azure clásico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. En el panel izquierdo, seleccione **Active Directory** .
3. Seleccione al inquilino (directorio) de Azure AD para el que quiere habilitar los Servicios de dominio de Azure AD.
4. Vaya a la pestaña **Aplicaciones** .
5. Seleccione la opción **Aplicaciones que tiene mi compañía** en la lista desplegable.
6. Busque una aplicación llamada **Sincronización de Azure AD Domain Services**. Si la aplicación existe, proceda a eliminarla.
7. Cuando haya eliminado la aplicación, intente volver a habilitar los Servicios de dominio de Azure AD una vez más.

### <a name="invalid-configuration"></a>Configuración no válida
**Mensaje de error:**

*No se pudo habilitar Domain Services en este inquilino de Azure AD. La aplicación de Domain Services en el inquilino de Azure AD carece de los permisos necesarios para habilitar Domain Services. Elimine la aplicación con el identificador d87dcbc6-a371-462e-88e3-28ad15ec4e64 e intente habilitar Domain Services para el inquilino de Azure AD.*

**Corrección:**

Compruebe si hay una aplicación con el nombre "AzureActiveDirectoryDomainControllerServices" (con el identificador d87dcbc6-a371-462e-88e3-28ad15ec4e64) en el directorio de Azure AD. Si existe, deberá eliminarla y luego volver a habilitar Azure AD Domain Services.

Use el siguiente script de PowerShell para encontrarla y eliminarla.

> [!NOTE]
> Este script usa cmdlets de **Azure AD PowerShell versión 2**. Para ver una lista completa de todos los cmdlets disponibles y descargar el módulo, lea la [documentación de referencia de Azure AD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph deshabilitado
**Mensaje de error:**

No se pudo habilitar Domain Services en este inquilino de Azure AD. La aplicación Microsoft Azure AD está deshabilitada en el inquilino de Azure AD. Habilite la aplicación con el identificador 00000002-0000-0000-c000-000000000000 e intente habilitar Domain Services para el inquilino de Azure AD.

**Corrección:**

Compruebe si ha deshabilitado una aplicación con el identificador 00000002-0000-0000-c000-000000000000. Se trata de la aplicación de Microsoft Azure AD, que proporciona acceso al inquilino de Azure AD a la API Graph. Azure AD Domain Services necesita que esta aplicación esté habilitada para sincronizar el inquilino de Azure AD con el dominio administrado.

Para resolver este error, habilite esta aplicación e intente habilitar Domain Services para su inquilino de Azure AD.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Los usuarios no pueden iniciar sesión en el dominio administrado de los Servicios de dominio de Azure AD
Si uno o más usuarios de su inquilino de Azure AD no pueden iniciar sesión en el dominio administrado creado recientemente, lleve a cabo los siguientes pasos de solución de problemas:

* **Inicio de sesión con formato UPN:** trate de iniciar sesión con el formato UPN; por ejemplo, "joeuser@contoso.com" en lugar del formato de SAMAccountName (CONTOSO\joeuser). El atributo SAMAccountName puede generarse automáticamente para los usuarios cuyo prefijo UPN sea demasiado largo o coincida con el de otro usuario del dominio administrado. El formato UPN garantiza que sea único dentro de un inquilino de Azure AD.

> [!NOTE]
> Recomendamos usar el formato UPN para iniciar sesión en el dominio administrado de Azure AD Domain Services.
>
>

* Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.
* **Cuentas externas** Asegúrese de que la cuenta de usuario afectada no es una cuenta externa en el inquilino de Azure AD. Entre los ejemplos de las cuentas externas se incluyen las cuentas de Microsoft; por ejemplo, "joe@live.com" o las cuentas de usuario de un directorio de Azure AD externo. Puesto que los Servicios de dominio de Azure AD no tienen las credenciales de dichas cuentas de usuario, estos usuarios no pueden iniciar sesión el dominio administrado.
* **Cuentas sincronizadas** : si las cuentas de usuario afectadas se sincronizan desde un directorio local, compruebe que ha hecho lo siguiente:

  * Ha implementado la [versión más reciente recomendada de Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594)o se ha actualización a dicha versión.
  * Ha configurado Azure AD Connect para [realizar una sincronización completa](active-directory-ds-getting-started-password-sync.md).
  * En función del tamaño de su directorio, se puede tardar algo en que las cuentas de usuario y los hash de credenciales estén disponibles en Servicios de dominio de Azure AD. Asegúrese de esperar el tiempo suficiente antes de volver a intentar la autenticación (depende del tamaño de su directorio, desde unas horas a un día o dos, para directorios grandes).
  * Si el problema persiste después de comprobar los pasos anteriores, pruebe a reiniciar el servicio de sincronización de Microsoft Azure AD. En el equipo de sincronización, inicie un símbolo del sistema y ejecute los comandos siguientes:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Cuentas de solo en la nube**: si la cuenta de usuario afectada es una cuenta de usuario de solo en la nube, asegúrese de que el usuario ha cambiado su contraseña después de habilitar Servicios de dominio de Azure AD. Este paso hace que se generen los hash de credenciales necesarios para los Servicios de dominio de Azure AD

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Los usuarios quitados del inquilino de Azure AD no se quitan de su dominio administrado
Azure AD protege contra la eliminación accidental de objetos de usuario. Cuando elimina una cuenta de usuario del inquilino de Azure AD, se mueve el objeto de usuario correspondiente a la Papelera de reciclaje. Cuando esta operación de eliminación se sincroniza con el dominio administrado, hace que la cuenta de usuario correspondiente se marque como deshabilitada. Esta característica ayuda a recuperar o restaurar la cuenta de usuario más adelante.

La cuenta de usuario permanece en estado deshabilitado en el dominio administrado, incluso si vuelve a crear una cuenta de usuario con la misma UPN en su directorio de Azure AD. Para quitar la cuenta de usuario de su dominio administrado, tiene que forzar la eliminación del usuario del inquilino de Azure AD.

Para quitar por completo la cuenta de usuario de su dominio administrado, elimine el usuario de forma permanente del inquilino de Azure AD. Use el cmdlet de PowerShell Remove-MsolUser con la opción "-RemoveFromRecycleBin", tal como se describe en este [artículo de MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).

## <a name="contact-us"></a>Ponerse en contacto con nosotros
Póngase en contacto con el equipo de productos de Active Directory Domain Services para [compartir comentarios u obtener asistencia](active-directory-ds-contact-us.md).
