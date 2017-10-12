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
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 811adc81424b8e53a740ec34f77a7610fc2a72a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Establecimiento de una página principal personalizada para aplicaciones publicadas mediante el Proxy de aplicación de Azure AD

En este artículo se explica cómo configurar aplicaciones para que dirijan a los usuarios a una página principal personalizada. Al publicar una aplicación con el proxy de la aplicación, se establece una dirección URL interna pero, a veces, esa no es la primera página que deben ver los usuarios. Establezca una página principal personalizada para que sus usuarios vayan a la página correcta cuando accedan a las aplicaciones. Sus usuarios podrán ver la página principal personalizada que ha establecido cuando accedan a las aplicaciones desde el Panel de acceso de Azure Active Directory o desde el iniciador de aplicaciones de Office 365.

Cuando los usuarios inician sus aplicaciones, se les dirige de manera predeterminada a la dirección URL raíz del dominio raíz de la aplicación publicada. La página de aterrizaje normalmente se establece como la dirección URL de la página principal. Use el módulo de Azure AD PowerShell para definir las direcciones URL de la página principal personalizada cuando desee que los usuarios de la aplicación lleguen a una página concreta de la aplicación. 

Este es un ejemplo de por qué una empresa establecería una página principal personalizada:
- En la red de su compañía, los usuarios van a *https://ExpenseApp/login/login.aspx* para iniciar sesión y acceder a su aplicación.
- Dado que tiene otros recursos, como imágenes, que el proxy de la aplicación necesita para acceder al nivel superior de la estructura de carpetas, publica la aplicación con *https://ExpenseApp* como dirección URL interna.
- La dirección URL externa predeterminada es *https://ExpenseApp-contoso.msappproxy.net*, que no lleva a los usuarios a la página de inicio de sesión.  
- Establezca *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* como dirección URL de la página principal. 

>[!NOTE]
>Al proporcionar a los usuarios acceso a las aplicaciones publicadas, estas aparecen en el [panel de acceso de Azure AD](active-directory-saas-access-panel-introduction.md) y el [iniciador de aplicaciones de Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Antes de comenzar

Antes de establecer la dirección URL de la página principal tenga en cuenta los siguientes requisitos:

* Asegúrese de que la ruta de acceso especificada sea una ruta de acceso de subdominio de la dirección URL raíz del dominio.

  Si la dirección URL raíz del dominio es, por ejemplo, https://apps.contoso.com/app1/, la dirección URL de la página principal que configure debe empezar por https://apps.contoso.com/app1/.

* Si realiza un cambio en la aplicación publicada, este podría restablecer el valor de la dirección URL de la página principal. Si actualiza la aplicación en el futuro, debe volver a comprobar y, si es necesario, actualizar la dirección URL de la página principal.

## <a name="change-the-home-page-in-the-azure-portal"></a>Vaya a la página principal de Azure Portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Vaya a **Azure Active Directory** > **Registros de aplicación** y elija la aplicación de la lista. 
3. Seleccione **Propiedades** en las opciones.
4. Actualice el campo **URL de página principal** con la nueva ruta de acceso. 

   ![Especifique la nueva dirección URL de la página principal](./media/application-proxy-office365-app-launcher/homepage.png)

5. Seleccione **Guardar**.

## <a name="change-the-home-page-with-powershell"></a>Cambio de la página principal con PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Instalación del módulo de PowerShell de Azure AD

Para poder definir la dirección URL de la página principal personalizada mediante PowerShell, instale el módulo de Azure AD PowerShell. Puede descargar este paquete desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), que utiliza el punto de conexión de API Graph. 

Para instalar el paquete, siga estos pasos:

1. Abra una ventana de PowerShell estándar y luego ejecute el siguiente comando:

    ```
     Install-Module -Name AzureAD
    ```
    Si está ejecutando el comando como no administrador, use la opción `-scope currentuser`.
2. Durante la instalación, seleccione **Y** para instalar dos paquetes de Nuget.org. Se requieren ambos paquetes. 

### <a name="find-the-objectid-of-the-app"></a>Busque el valor ObjectID de la aplicación

Obtenga el ObjectID de la aplicación y, a continuación, busque la aplicación por su página principal.

1. En la misma ventana de PowerShell, importe el módulo Azure AD.

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

### <a name="update-the-home-page-url"></a>Actualización de la dirección URL de la página principal

Cree la dirección URL de la página principal y actualice la aplicación con ese valor. Siga usando la misma ventana de PowerShell para ejecutar estos comandos. O, si está usando una nueva ventana de PowerShell, inicie sesión de nuevo en el módulo de Azure AD utilizando `Connect-AzureAD`. 

1. Confirme que tiene la aplicación correcta y reemplace *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* por el ObjectID que copió en la sección anterior.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Ahora que ha confirmado la aplicación, está preparado para actualizar la página principal como se indica a continuación.

2. Cree un objeto de aplicación en blanco para que contenga los cambios que desea realizar. Esta variable contiene los valores que desea actualizar. En este paso no se crea nada.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Establezca la dirección URL de la página principal en el valor que quiera. El valor debe ser una ruta de acceso de subdominio de la aplicación publicada. Por ejemplo, si cambia la dirección URL de la página principal de *https://sharepoint-iddemo.msappproxy.net/* a *https://sharepoint-iddemo.msappproxy.net/hybrid/*, los usuarios de la aplicación van directamente a la página principal personalizada.

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
>Los cambios realizados en la aplicación pueden restablecer la dirección URL de la página principal. Si la dirección URL de la página principal se restablece, repita los pasos de esta sección para volver a establecerlo de nuevo.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitar el acceso remoto a SharePoint con el Proxy de aplicación de Azure AD](application-proxy-enable-remote-access-sharepoint.md)
- [Habilitación del proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md)
