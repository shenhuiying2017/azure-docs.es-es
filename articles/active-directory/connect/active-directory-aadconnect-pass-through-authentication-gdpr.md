---
title: 'Azure AD Connect: autenticación de paso a través: cumplimiento del RGPD | Microsoft Docs'
description: Este artículo trata sobre la autenticación de paso a través de Azure Active Directory (Azure AD) y el cumplimiento del RGPD.
services: active-directory
keywords: Autenticación de paso a través de Azure AD Connect, RGPD, componentes necesarios para Azure AD, SSO, inicio de sesión único
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 21874c961163e3efba45c2ee8557c03135987f95
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-pass-through-authentication-gdpr-compliance"></a>Autenticación de paso a través de Azure Active Directory: cumplimiento del RGPD

## <a name="overview"></a>Información general

En mayo de 2018, entrará en vigor una ley de privacidad europea, el [Reglamento general de protección de datos (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). El RGPD impone nuevas reglas sobre las empresas, agencias gubernamentales, entidades sin ánimo de lucro y otras organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes en la UE. El GDPR se aplica con independencia de la ubicación. 

En la actualidad, hay productos y servicios de Microsoft para ayudarle a cumplir los requisitos de GDPR. Puede encontrar más información sobre la directiva de privacidad de Microsoft en [Trust Center](https://www.microsoft.com/trustcenter).

La autenticación paso a través de Azure AD crea los tipos de registro siguientes, que pueden contener EUII:

- Archivos de registro de seguimiento de Azure AD Connect.
- Archivos de registro de seguimiento del agente de autenticación.
- Archivos de registro de eventos de Windows.

El cumplimiento del RGPD para la autenticación de paso a través se puede conseguir de dos maneras:

1.  Previa solicitud, extraer los datos de una persona y quitarlos de las instalaciones.
2.  Asegurarse de que ningún dato se conserva más de 48 horas.

Se recomienda encarecidamente la segunda opción, porque es más simple de implementar y mantener. A continuación, aparecen las instrucciones para cada tipo de registro:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminación de archivos de registro de seguimiento de Azure AD Connect

Revise el contenido de la carpeta **%ProgramData%\AADConnect** y elimine el contenido de registro de seguimiento (archivos **trace-\*.log**) de esta carpeta en un plazo de 48 horas a contar de la instalación o la instalación de Azure AD Connect o de la modificación de la configuración de la autenticación de paso a través, porque esta acción podría crear los datos que cubre el RGPD.

>[!IMPORTANT]
>No elimine el archivo **PersistedState.xml** de esta carpeta, porque este archivo se usa para mantener el estado de la instalación anterior de Azure AD Connect y se usa cuando se realiza una instalación de actualización. Este archivo nunca contendrá ningún dato sobre una persona y nunca deberá eliminarse.

Puede revisar y eliminar estos archivos de registro de seguimiento con el Explorador de Windows, o bien puede usar el script de PowerShell siguiente para realizar las acciones necesarias:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde el script en un archivo con la extensión ".PS1". Ejecute este script según sea necesario.

Para más información sobre los requisitos relacionados del RGPD de Azure AD Connect, consulte [este artículo](active-directory-aadconnect-gdpr.md).

### <a name="delete-authentication-agent-event-logs"></a>Eliminación de registros de eventos del agente de autenticación

Este producto también puede crear **registros de eventos de Windows**. Para más información, lea [este artículo](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Para ver los registros relacionados con el agente de autenticación de paso a través, abra la aplicación **Visor de eventos** en el servidor y consulte **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Eliminación de archivos de registro de seguimiento del agente de autenticación.

Debe comprobar con regularidad el contenido de **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\** y eliminar el contenido de esta carpeta cada 48 horas. 

>[!IMPORTANT]
>Si el servicio del agente de autenticación está en ejecución, no podrá eliminar el archivo de registro actual en la carpeta. Detenga el servicio antes de volver a intentarlo. Para evitar errores de inicio de sesión de usuario, ya debe haber configurado la autenticación de paso a través para una [alta disponibilidad](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

Estos archivos se pueden revisar y eliminar mediante el Explorador de Windows, o bien puede usar el script siguiente para realizar las acciones necesarias:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Para programar que este script se ejecute cada 48 horas, siga estos pasos:

1.  Guarde el script en un archivo con la extensión ".PS1".
2.  Abra el **panel de control** y haga clic en **Sistema y seguridad**.
3.  En el encabezado **Herramientas administrativas**, haga clic en "**Programar tareas**".
4.  En el **Programador de tareas**, haga clic con el botón derecho en "**Biblioteca del Programador de tareas**" y haga clic en "**Crear tarea básica…**".
5.  Escriba el nombre de la nueva tarea y haga clic en **Siguiente**.
6.  Seleccione "**Diariamente**" para el **desencadenador de tareas** y haga clic en **Siguiente**.
7.  Establezca la repetición en dos días y haga clic en **Siguiente**.
8.  Seleccione "**Iniciar un programa**" como la acción y haga clic en **Siguiente**.
9.  Escriba "**PowerShell**" en el cuadro Programa o script y, en el cuadro "**Agregar argumentos (opcional)**", escriba la ruta de acceso completa al script que creó anteriormente y, luego, haga clic en **Siguiente**.
10. En la siguiente pantalla se muestra un resumen de la tarea que se dispone a crear. Compruebe los valores y haga clic en **Finalizar** para crear la tarea:
 
### <a name="note-about-domain-controller-logs"></a>Nota sobre los registros de controlador de dominio

Si el registro de auditoría está habilitado, este producto puede generar registros de seguridad para los controladores de dominio. Para más información sobre cómo configurar las directivas de auditoría, lea este [artículo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Pasos siguientes
- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): información para resolver problemas habituales de esta característica.
