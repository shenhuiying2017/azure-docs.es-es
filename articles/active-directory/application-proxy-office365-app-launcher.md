---
title: "Establecimiento de una página principal personalizada para aplicaciones publicadas mediante el Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se explican los conceptos básicos acerca de los conectores del Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 31e8e39580ed83f13fd3ffb9981221765063a0b7
ms.contentlocale: es-es
ms.lasthandoff: 04/21/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Establecimiento de una página principal personalizada para aplicaciones publicadas mediante el Proxy de aplicación de Azure AD

En este artículo se describe cómo puede configurar las aplicaciones para que dirija a los usuarios a una página principal personalizada cuando acceden a la aplicación desde el panel de acceso de Azure Active Directory (Azure AD) y el iniciador de aplicaciones de Office 365.

Cuando los usuarios inician sus aplicaciones, se les dirige de manera predeterminada a la dirección URL raíz del dominio raíz de la aplicación publicada. La página de aterrizaje normalmente se establece como la dirección URL de la página principal. Por ejemplo, para la aplicación de back-end http://ExpenseApp, la dirección URL se publica como *https://expenseApp-contoso.msappproxy.net*. De forma predeterminada, la dirección URL de la página principal se establece en *https://expenseApp-contoso.msappproxy.net*.

Con el módulo de Azure AD PowerShell, puede definir direcciones URL de página principal personalizadas para instancias cuando quiera que los usuarios lleguen a una página concreta dentro de la aplicación (por ejemplo, *https://expenseApp-contoso.msappproxy.net/login/login.aspx*).

>[!NOTE]
>Al proporcionar a los usuarios acceso a las aplicaciones publicadas, estas aparecen en el [panel de acceso de Azure AD](active-directory-saas-access-panel-introduction.md) y el [iniciador de aplicaciones de Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Antes de comenzar

### <a name="determine-the-home-page-url"></a>Determinación de la dirección URL de la página principal

Antes de establecer la dirección URL de la página principal tenga en cuenta lo siguiente:

* Asegúrese de que la ruta de acceso especificada sea una ruta de acceso de subdominio de la dirección URL raíz del dominio.

  Si la dirección URL raíz del dominio es, por ejemplo, https://apps.contoso.com/app1/, la dirección URL de la página principal que configure debe empezar por https://apps.contoso.com/app1/.

* Si realiza un cambio en la aplicación publicada, este podría restablecer el valor de la dirección URL de la página principal. Si actualiza la aplicación en el futuro, debe volver a comprobar y, si es necesario, actualizar la dirección URL de la página principal.

### <a name="install-the-azure-ad-powershell-module"></a>Instalación del módulo de PowerShell de Azure AD

Para poder definir una dirección URL de página principal personalizada con PowerShell, instale un paquete no estándar del módulo de Azure AD PowerShell. Puede descargar este paquete desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), que utiliza el punto de conexión de API Graph. 

Para instalar el paquete, siga estos pasos:

1. Abra una ventana de PowerShell estándar y luego ejecute el siguiente comando:

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    Si está ejecutando el comando como no administrador, use la opción `-scope currentuser`.
2. Durante la instalación, seleccione **Y** para instalar dos paquetes de Nuget.org. Se requieren ambos paquetes. 

## <a name="step-1-find-the-objectid-of-the-app"></a>Paso 1: busque el ObjectID de la aplicación

Obtenga el ObjectID de la aplicación y, a continuación, busque la aplicación por su página principal.

1. Abra PowerShell e importe el módulo de Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Inicie sesión como administrador de inquilinos en el módulo de Azure AD.

    ```
    Connect-AzureAD
    ```
3. Busque la aplicación en función de su dirección URL de página principal. Puede ver la dirección URL en el portal si va a **Azure Active Directory** > **Aplicaciones empresariales** > **Todas las aplicaciones**. Este ejemplo usa *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Debe obtener un resultado similar al que se muestra aquí. Copie el GUID de ObjectID para usarlo en la siguiente sección.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

## <a name="step-2-update-the-home-page-url"></a>Paso 2: actualice la dirección URL de la página principal

En el mismo módulo de PowerShell que ha usado para el paso 1, haga lo siguiente:

1. Confirme que tiene la aplicación correcta y reemplace *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* por el GUID (ObjectID) de la aplicación que copió en el paso anterior.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Ahora que ha confirmado la aplicación, está preparado para actualizar la página principal como se indica a continuación.

2. Cree un objeto de aplicación en blanco para que contenga los cambios que desea realizar.  

 >[!NOTE]
 >Se trata solo de una variable que contiene los valores que desea actualizar; por tanto, realmente no se ha creado nada.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Establezca la dirección URL de la página principal en el valor que quiera. El valor debe ser una ruta de acceso de subdominio de la aplicación publicada. Por ejemplo, si cambia la dirección URL de la página principal de *https://sharepoint-iddemo.msappproxy.net/* a *https://sharepoint-iddemo.msappproxy.net/hybrid/*, los usuarios de la aplicación irán directamente a la página principal personalizada.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Realice la actualización mediante el GUID (ObjectID) que copió en el "Paso 1: busque el ObjectID de la aplicación".

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Para confirmar que el cambio fue correcto, reinicie la aplicación.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Los cambios realizados en la aplicación pueden restablecer la dirección URL de la página principal. Si esto ocurre, repita el paso 2.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitar el acceso remoto a SharePoint con el Proxy de aplicación de Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Habilitación del proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md)

