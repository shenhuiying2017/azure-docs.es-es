---
title: "Establecimiento de una página principal personalizada para la aplicación publicada mediante el Proxy de aplicación de Azure AD | Microsoft Docs"
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
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d543849ccdc3cd93845756954b063ae169b066d5
ms.openlocfilehash: 9f642b03b6c10f6312b2c31bbc810cb6701352e3


---

# <a name="set-a-custom-home-page-for-your-published-application-using-azure-ad-app-proxy"></a>Establecimiento de una página principal personalizada para la aplicación publicada mediante el Proxy de aplicación de Azure AD

En este artículo se describe cómo puede configurar la aplicación para que dirija a los usuarios a una página de inicio personalizada cuando los usuarios tienen acceso a la aplicación desde el panel de acceso de Azure AD y el iniciador de aplicaciones de Office 365.

>[!NOTE]
>Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> 
 
Con el módulo de PowerShell de Azure AD, puede definir direcciones URL de página principal personalizada para esas instancias cuando desee que los usuarios lleguen a una página específica dentro de la aplicación; por ejemplo https://expenseApp-contoso.msappproxy.net/login/login.aspx.

>[!NOTE]
>Al proporcionar a los usuarios acceso a las aplicaciones publicadas, estas aparecen en el [panel de acceso de Azure AD](active-directory-saas-access-panel-introduction.md) y el [iniciador de aplicaciones de Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher). 
>

Cuando los usuarios inician sus aplicaciones, se le dirige de manera predeterminada a la dirección URL raíz del dominio raíz de la aplicación publicada. La página de aterrizaje normalmente se establece en la dirección URL de la página principal. Por ejemplo, para la aplicación de back-end http://ExpenseApp, se publica como https://expenseApp-contoso.msappproxy.net. De forma predeterminada, la dirección URL de la página principal se establece en https://expenseApp-contoso.msappproxy.net.

## <a name="determine-the-home-page-url"></a>Determinación de la dirección URL de la página principal

Hay varios aspectos que debe tener en cuenta antes de establecer la dirección URL de la página principal:

* Debe asegurarse de que la ruta de acceso especificada sea una ruta de acceso de subdominio de la dirección URL raíz del dominio.

 Por ejemplo, si se puede tener acceso a la aplicación publicada desde la dirección URL de página principal https://intranet-contoso.msappproxy.net/, la dirección URL de página principal que configure debe empezar por https://intranet-contoso.msappproxy.net/. 
 
* Si la dirección URL de la página principal es https://apps.contoso.com/app1/, la dirección URL de la página principal debe empezar por https://apps.contoso.com/app1/.

* Si realiza un cambio en la aplicación publicada, puede restablecer el valor de la dirección URL de la página principal. Por lo tanto, si decide actualizar la aplicación, debe volver a comprobar y posiblemente actualizar la dirección URL de la página principal.


En la siguiente sección se le guiará por el proceso de configuración de una dirección URL de página principal personalizada para las aplicaciones publicadas. 

## <a name="install-the-azure-ad-powershell-module"></a>Instalación del módulo de PowerShell de Azure AD

Para poder definir una dirección URL de página principal personalizada con Powershell, primero debe instalar un paquete no estándar del módulo de PowerShell de Azure AD.  Puede descargar este paquete desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0), que utiliza el punto de conexión de API Graph. 

**Para instalar el paquete con Powershell:**

1. Abra una instancia de PowerShell estándar.
2. Ejecute el siguiente comando:

```
 Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
 ```
 Si está ejecutando la aplicación con un rol diferente de administrador, debe usar la opción _-scope currentuser_.
3. Durante la instalación, seleccione "Y" para instalar dos paquetes de Nuget.org.  Ambos son necesarios para usar el paquete. 

##<a name="set-a-custom-home-page-url-using-the-azure-ad-powershell-module"></a>Establecimiento der una dirección URL de página principal personalizada con el módulo de PowerShell de Azure AD

Ahora que tiene instalado el módulo de PowerShell de Azure AD, está listo para establecer la dirección URL de página principal con dos pasos sencillos.

1. Busque la aplicación que desea actualizar.
2. Actualice la dirección URL de página principal de la aplicación.

###<a name="step-1--find-the-objectid-of-the-application"></a>Paso 1: busque el ObjectID de la aplicación

Primero debe obtener el ObjectID de la aplicación y, a continuación, buscar la aplicación mediante la página principal.

1. Abra PowerShell.
2. Importe el módulo de Azure AD.
  
 ```
 Import-Module AzureAD
 ```
3. Inicie sesión en el módulo de Azure AD.  Use el cmdlet siguiente y siga las instrucciones que aparecen en la pantalla. Asegúrese de iniciar sesión como el administrador del inquilino.
 
 ```
 Connect-AzureAD
 ```
4. El siguiente cmdlet busca las aplicaciones en función de la página principal que contiene _sharepoint-iddemo_. Se trata de la aplicación que desea editar. Debe reemplazar este valor por el valor que funcione para su aplicación.
  
 ```
 Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
 ```
5. Debe aparecer un resultado similar a la respuesta siguiente. El GUID (el valor de ObjectID que se muestra a continuación) es el elemento que se debe copiar.
 
 ```
 DisplayName : SharePoint
 Homepage    : https://sharepoint-iddemo.msappproxy.net/
 ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```
6. Copie el valor GUID (ObjectID). Lo necesitará para el siguiente paso.


###<a name="step-2--update-the-homepage-url"></a>Paso 2: actualice la dirección URL de la página principal

Use el mismo módulo de PowerShell para actualizar la dirección URL de la página principal como hizo para buscar el identificador de aplicación. Tras iniciar sesión en PowerShell, siga estos pasos:

1. Confirme que tiene la aplicación correcta y reemplace _8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4_ por el valor de ObjectID de la aplicación que ha copiado en el paso 1 anterior. 
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
 ```
 
 Ahora que ha confirmado la aplicación, está preparado para actualizar la página principal como se indica a continuación.
 
2. Cree un objeto de aplicación en blanco para que contenga los cambios que desea realizar. Se trata solo de una variable que contiene los valores que desea actualizar; por tanto, realmente no se ha creado nada.
  
 ```
 $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
 ```
3. Establezca la página principal con el valor que desee. Tenga en cuenta que debe ser una ruta de acceso de subdominio de la aplicación publicada. Por ejemplo, si cambia la página principal de _https://sharepoint-iddemo.msappproxy.net/_ a _https://sharepoint-iddemo.msappproxy.net/hybrid/_, los usuarios irán directamente a la página principal personalizada.
  
 ```
 $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
 ```
4. Por último, debe realizar la actualización. Recuerde que debe usar el GUID que ha copiado en el paso 1 anterior.
  
 ```
 Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
 ```
5. Ahora debe confirmar la página principal personalizada; para ello, inicie de nuevo la aplicación para comprobar que el cambio se realizó correctamente.
  
 ```
 Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
 ```

>[!NOTE]
>Tenga en cuenta que los cambios que haga en la aplicación pueden restablecer la dirección URL de la página principal. En ese caso, tendrá que repetir este proceso.

##<a name="next-steps"></a>Pasos siguientes

[Habilitación del acceso remoto a SharePoint con el Proxy de aplicación de Azure AD](application-proxy-enable-remote-access-sharepoint.md)<br>
[Habilitación del proxy de aplicación en Azure Portal](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


