---
title: "Controles de página de Azure API Management | Microsoft Docs"
description: "Aprenda sobre los controles de página disponibles para su uso en las plantillas del portal para desarrolladores de Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: 4fd91ae079ff054932f4572874001dd69dd848e7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="azure-api-management-page-controls"></a>Controles de página de Azure API Management
Azure API Management proporciona los siguientes controles para su uso en las plantillas del portal para desarrolladores.  
  
Para usar un control, colóquelo en la ubicación deseada en la plantilla del portal para desarrolladores. Algunos controles, como el control [app-actions](#app-actions), tienen parámetros, como se muestra en el ejemplo siguiente:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Los valores de los parámetros se pasan como parte del modelo de datos de la plantilla. En la mayoría de los casos, puede pegarlos simplemente en el ejemplo proporcionado para cada control para que funcione correctamente. Para más información sobre los valores de parámetros, puede ver la sección del modelo de datos de cada plantilla en la que se puede usar un control.  
  
 Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Controles de página de las plantillas del portal para desarrolladores  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> app-actions  
 El control `app-actions` proporciona una interfaz de usuario para la interacción con aplicaciones en la página del perfil de usuario del portal para desarrolladores.  
  
 ![app&amp;#45;actions control](./media/api-management-page-controls/APIM-app-actions-control.png "Control app-actions de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>parameters  
  
|Parámetro|Descripción|  
|---------------|-----------------|  
|appId|Identificador de la aplicación.|  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `app-actions` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Applications](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> basic-signin  
 El control `basic-signin` ofrece un control para la recolección de información de inicio de sesión del usuario en la página de inicio de sesión del portal para desarrolladores.  
  
 ![basic&amp;#45;signin control](./media/api-management-page-controls/APIM-basic-signin-control.png "Control basic-signin de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>parameters  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `basic-signin` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Sign in](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> paging-control  
 El control `paging-control` proporciona funcionalidad de paginación en las páginas del portal para desarrolladores que muestran una lista de elementos.  
  
 ![paging control](./media/api-management-page-controls/APIM-paging-control.png "Paging control de APIM ")  
  
### <a name="usage"></a>Uso  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>parameters  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `paging-control` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [API list](api-management-api-templates.md#APIList)  
  
-   [Issue list](api-management-issue-templates.md#IssueList)  
  
-   [Product list](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> providers  
 El control `providers` proporciona un control para la selección de proveedores de autenticación en la página de inicio de sesión del portal para desarrolladores.  
  
 ![control providers](./media/api-management-page-controls/APIM-providers-control.png "Control providers de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>parameters  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `providers` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Sign in](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 El control `search-control` proporciona funcionalidad de búsqueda en las páginas del portal para desarrolladores que muestran una lista de elementos.  
  
 ![search control](./media/api-management-page-controls/APIM-search-control.png "Search control de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>parameters  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `search-control` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [API list](api-management-api-templates.md#APIList)  
  
-   [Product list](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> sign-up  
 El control `sign-up` proporciona un control para la recolección de información de perfil de usuario en la página de inicio de sesión del portal para desarrolladores.  
  
 ![sign&amp;#45;up control](./media/api-management-page-controls/APIM-sign-up-control.png "Sign-up control de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>parameters  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `sign-up` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Sign up](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscribe-button  
 El control `subscribe-button` proporciona un control para la suscripción de un usuario a un producto.  
  
 ![subscribe&amp;#45;button control](./media/api-management-page-controls/APIM-subscribe-button-control.png "Control subscribe-button de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>parameters  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `subscribe-button` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Producto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> subscription-cancel  
 El control `subscription-cancel` proporciona un control para la cancelación de una suscripción a un producto en la página de perfil de usuario del portal para desarrolladores.  
  
 ![subscription&amp;#45;cancel control](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Control subscription-cancel de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>parameters  
  
|Parámetro|Descripción|  
|---------------|-----------------|  
|subscriptionId|Identificador de la suscripción para cancelar.|  
|cancelUrl|Dirección URL de cancelación de la suscripción.|  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `subscription-cancel` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Producto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](api-management-developer-portal-templates.md).