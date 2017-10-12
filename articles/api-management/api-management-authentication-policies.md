---
title: "Directivas de autenticación de Azure API Management | Microsoft Docs"
description: "Aprenda sobre las directivas de autenticación disponibles para su uso en Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 2907c1e4e39f975b4dc4e9382d7258c5d56dbbdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-authentication-policies"></a>Directivas de autenticación de Azure API Management
En este tema se proporciona una referencia para las siguientes directivas de API Management. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AuthenticationPolicies"></a> Directivas de autenticación  
  
-   [Autenticar con opción básica](api-management-authentication-policies.md#Basic) : autenticar con un servicio de back-end mediante la autenticación básica.  
  
-   [Autenticar con certificado de cliente](api-management-authentication-policies.md#ClientCertificate) : autenticar con un servicio de back-end mediante certificados de cliente.  
  
##  <a name="Basic"></a> Authenticate with Basic  
 Use la directiva `authentication-basic` para realizar la autenticación con upolicy to authenticate with a n servicio de back-end mediante autenticación Básica. Esta directiva establece eficazmente el encabezado de autorización HTTP en el valor correspondiente a las credenciales proporcionadas en la directiva.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Ejemplo  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|authentication-basic|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|nombre de usuario|Especifica el nombre de usuario de la credencial básica.|Sí|N/D|  
|contraseña|Especifica la contraseña de usuario de la credencial básica.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound  
  
-   **Ámbitos de la directiva:** API  
  
##  <a name="ClientCertificate"></a> Autenticación Básica  
 Use la directiva `authentication-certificate` para realizar la autenticación con un servicio de back-end mediante un certificado de cliente. El certificado se debe [instalar primero en API Management](http://go.microsoft.com/fwlink/?LinkID=511599) y se identifica mediante su huella digital.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Ejemplo  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|thumbprint|La huella digital del certificado de cliente.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound  
  
-   **Ámbitos de la directiva:** API  
  

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con directivas, consulte a [Directivas de API Management](api-management-howto-policies.md).  