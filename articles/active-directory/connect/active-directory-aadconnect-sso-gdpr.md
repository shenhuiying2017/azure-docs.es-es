---
title: 'Azure AD Connect: inicio de sesión único de conexión directa: cumplimiento del RGPD | Microsoft Docs'
description: Este artículo trata sobre el SSO de conexión directa de Azure Active Directory (Azure AD) y el cumplimiento del RGPD.
services: active-directory
keywords: qué es Azure AD Connect, RGPD, componentes necesarios para Azure AD, SSO, inicio de sesión único
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
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Inicio de sesión único de conexión directa de Azure AD: cumplimiento del RGPD

## <a name="overview"></a>Información general

En mayo de 2018, entrará en vigor una ley de privacidad europea, el [Reglamento general de protección de datos (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). El RGPD impone nuevas reglas sobre las empresas, agencias gubernamentales, entidades sin ánimo de lucro y otras organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes en la UE. El GDPR se aplica con independencia de la ubicación. 

En la actualidad, hay productos y servicios de Microsoft para ayudarle a cumplir los requisitos de GDPR. Puede encontrar más información sobre la directiva de privacidad de Microsoft en [Trust Center](https://www.microsoft.com/trustcenter).

SSO de conexión directa de Azure AD crea el tipo de registro siguiente, que puede contener EUII:

- Archivos de registro de seguimiento de Azure AD Connect.

El cumplimiento del RGPD para el SSO de conexión directa se puede conseguir de dos maneras:

1.  Previa solicitud, extraer los datos de una persona y quitarlos de las instalaciones.
2.  Asegurarse de que ningún dato se conserva más de 48 horas.

Se recomienda encarecidamente la segunda opción, porque es más simple de implementar y mantener. A continuación, consulte las instrucciones para cada tipo de registro:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminación de archivos de registro de seguimiento de Azure AD Connect

Revise el contenido de la carpeta **%ProgramData%\AADConnect** y elimine el contenido de registro de seguimiento (archivos **trace-\*.log**) de esta carpeta en un plazo de 48 horas a contar de la instalación o la instalación de Azure AD Connect o de la modificación de la configuración del SSO de conexión directa, porque esta acción podría crear los datos que cubre el RGPD.

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

### <a name="note-about-domain-controller-logs"></a>Nota sobre los registros de controlador de dominio

Si el registro de auditoría está habilitado, este producto puede generar registros de seguridad para los controladores de dominio. Para más información sobre cómo configurar las directivas de auditoría, lea este [artículo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Pasos siguientes

- [**Solución de problemas**](active-directory-aadconnect-troubleshoot-sso.md): información para resolver problemas habituales de esta característica.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): para la tramitación de solicitudes de nuevas características.
