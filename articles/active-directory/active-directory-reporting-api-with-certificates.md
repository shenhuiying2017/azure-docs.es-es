---
title: Obtención de datos mediante Reporting API de Azure AD con certificados | Microsoft Docs
description: Explica cómo usar Reporting API de Azure AD con credenciales de certificado para obtener datos de directorios sin intervención del usuario.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obtención de datos mediante Reporting API de Azure Active Directory con certificados

Las [Reporting API de Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas.

Si quiere acceder a Reporting API de Azure AD sin intervención del usuario, puede configurar el acceso para usar certificados.

Este artículo:

- Proporciona los pasos necesarios para acceder a Reporting API de Azure AD mediante certificados.
- Se supone que ha completado los [requisitos previos para acceder a Reporting API de Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 


Para acceder a Reporting API con certificados, debe realizar las acciones siguientes:

1. Instalación de los requisitos previos
2. Establecimiento del certificado en la aplicación 
3. Concesión de permisos
4. Obtención de un token de acceso




Para más información sobre el código fuente, consulte el [módulo Leverage Report API](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils) (Uso de Report API). 

## <a name="install-prerequisites"></a>Requisitos previos de instalación

Debe tener instalado Azure AD PowerShell V2 y el módulo AzureADUtils.

1. Descargue e instale Azure AD Powershell V2, según las instrucciones de [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Descargue el módulo Azure AD Utils de [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Este módulo proporciona varios cmdlets de utilidades incluidos los siguientes:
    - La versión más reciente de ADAL mediante Nuget
    - Los tokens de acceso de usuario, las claves de aplicación y los certificados mediante ADAL
    - Graph API con control de resultados paginados

**Para instalar el módulo Azure AD Utils:**

1. Cree un directorio para guardar el módulo de utilidades (por ejemplo, c:\azureAD) y descargar el módulo de GitHub.
2. Abra una sesión de PowerShell y vaya al directorio que acaba de crear. 
3. Importe el módulo e instálelo en la ruta de acceso del módulo de PowerShell mediante el cmdlet Install-AzureADUtilsModule. 

La sesión debe tener un aspecto similar a esta pantalla:

  ![Windows Powershell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Establecimiento del certificado en la aplicación

**Para establecer el certificado en la aplicación:**

1. [Obtenga el id. de objeto](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) de la aplicación de Azure Portal. 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Abra una sesión de PowerShell y conéctese a Azure AD mediante el cmdlet Connect-AzureAD.

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Use el cmdlet New-AzureADApplicationCertificateCredential de AzureADUtils para agregarle una credencial de certificado. 

>[!Note]
>Debe proporcionar el identificador de objeto de aplicación capturado anteriormente, así como el objeto de certificado (se obtiene mediante la unidad Cert:).
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Obtención de un token de acceso

Para obtener un token de acceso, use el cmdlet **Get-AzureADGraphAPIAccessTokenFromCert** de AzureADUtils. 

>[!NOTE]
>Debe utilizar el identificador de aplicación en lugar del identificador de objeto usado en la última sección.
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Uso del token de acceso para llamar a Graph API

Ya puede crear el script. A continuación se muestra un ejemplo de cómo utilizar el cmdlet **Invoke-AzureADGraphAPIQuery** de AzureADUtils. Este cmdlet controla los resultados de paginado múltiple y los envía luego a la canalización de PowerShell. 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

Ya puede exportar a CSV y guardar en un sistema SIEM. También puede encapsular el script en una tarea programada para obtener datos de Azure AD de su inquilino periódicamente sin tener que almacenar las claves de aplicación en el código fuente. 

## <a name="next-steps"></a>Pasos siguientes

- [Obtención de una primera impresión de Reporting API](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Creación de una solución propia](active-directory-reporting-api-getting-started-azure-portal.md#customize)




