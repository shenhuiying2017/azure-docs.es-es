---
title: "Protección de API mediante la autenticación de certificados de cliente en API Management - Azure API Management | Microsoft Docs"
description: "Obtenga información acerca de cómo proteger el acceso a las API mediante certificados de cliente."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 841825923819bdb257e5b5983071d999cca805e9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Protección de API mediante la autenticación de certificados de cliente en API Management

API Management proporciona la capacidad de proteger el acceso a las API (es decir, de cliente a API Management) mediante certificados de cliente. Actualmente, puede comprobar la huella digital de un certificado de cliente en relación con un valor deseado. También puede comprobar la huella digital en relación con certificados existentes que se hayan cargado en API Management.  

Para obtener información acerca de cómo proteger el acceso al servicio de back-end de una API mediante certificados de cliente (por ejemplo, de API Management a back-end), consulte [Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Administración de API de Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>Comprobación de la fecha de expiración

Las directivas siguientes pueden configurarse para comprobar si el certificado ha caducado:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Comprobación del emisor y el asunto

Es posible configurar las directivas siguientes para comprobar el emisor y el firmante de un certificado de cliente:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Comprobación de la huella digital

Es posible configurar las directivas siguientes para comprobar la huella digital de un certificado de cliente:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Comprobación de una huella digital en relación con certificados cargados en API Management

En el ejemplo siguiente se muestra cómo comprobar la huella digital de un certificado de cliente en relación con los certificados cargados en API Management: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Paso siguiente

*  [Cómo asegurar servicios back-end con la autenticación de certificados de cliente](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [¿Cómo se cargan certificados?](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

