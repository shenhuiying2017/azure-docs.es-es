---
title: Asignación de directivas de acceso a los puntos de conexión del servicio de Azure Service Fabric | Microsoft Docs
description: Obtenga información acerca de cómo asignar directivas de acceso de seguridad a los puntos de conexión HTTP o HTTPS del servicio de Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: f9de8d213d11a8ccb3ffff484a67560d9e2abe77
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Asignación de una directiva de acceso de seguridad a los puntos de conexión HTTP y HTTPS
Si aplica una directiva de RunAs y el manifiesto de servicio declara los recursos de punto de conexión HTTP, debe especificar **SecurityAccessPolicy**.  El elemento **SecurityAccessPolicy** garantiza que los puertos asignados a estos puntos de conexión se restrinjan correctamente a la cuenta de usuario que el servicio ejecuta. En caso contrario, **http.sys** no tendrá acceso al servicio y aparecerán errores en las llamadas del cliente. En el ejemplo siguiente se aplica la cuenta Customer1 a un punto de conexión denominado **EndpointName**, que le concede derechos de acceso total.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

En el caso de un punto de conexión HTTPS, también es preciso indicar el nombre del certificado que se va a devolver al cliente. Hace referencia al certificado mediante **EndpointBindingPolicy**.  El certificado se define en la sección **Certificados** del manifiesto de aplicación.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Para ver los próximos pasos, lea los siguientes artículos:
* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
