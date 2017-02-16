---
title: "Autenticación y autorización de Service Bus | Microsoft Docs"
description: "Información general de la autenticación de Firma de acceso compartido (SAS)."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2c1a1f3b73466526b13bcfeb4580335390506c23
ms.openlocfilehash: 14dfe58f6296a4ec516845bace456ffd59fa608a


---
# <a name="service-bus-authentication-and-authorization"></a>Autenticación y autorización de Bus de servicio
Las aplicaciones pueden autenticarse en Bus de servicio de Azure mediante autenticación de Firma de acceso compartido (SAS) o Servicio de control de acceso de Azure Active Directory (también conocido como Servicio de control de acceso o ACS). La autenticación de Firma de acceso compartido permite a las aplicaciones autenticarse en Bus de servicio mediante una clave de acceso configurada en el espacio de nombres o la entidad al que se asocian derechos específicos. A continuación, puede usar esta clave para generar un token de Firma de acceso compartido que los clientes pueden usar para autenticarse en Bus de servicio.

> [!IMPORTANT]
> Se recomienda usar SAS en lugar de ACS, ya que proporciona un esquema de autenticación sencillo, flexible y fácil de usar para Bus de servicio. Las aplicaciones pueden usar SAS en escenarios en los que no necesitan administrar la noción de “usuario” autorizado. 

## <a name="shared-access-signature-authentication"></a>Autenticación con Firma de acceso compartido
[autenticación de SAS](service-bus-sas-overview.md) le permite conceder acceso a un usuario a los recursos de Bus de servicio con derechos específicos. La autenticación SAS en el Bus de servicio implica la configuración de una clave criptográfica con derechos asociados en un recurso del Bus de servicio. Los clientes pueden obtener acceso a ese recurso presentando un token SAS que consta del URI del recurso al que se tiene acceso y una fecha de expiración firmada con la clave configurada.

Puede configurar claves para SAS en un espacio de nombres de Bus de servicio. La clave se aplica a todas las entidades de mensajes de ese espacio de nombres. También puede configurar claves en temas y colas de Bus de servicio. SAS es compatible igualmente con retransmisiones de Bus de servicio.

Para usar SAS, puede configurar un objeto [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) en un espacio de nombres, cola o tema que consta de lo siguiente:

* *KeyName* que identifica la regla.
* *PrimaryKey* es una clave criptográfica usada para firmar o validar tokens SAS.
* *SecondaryKey* es una clave criptográfica usada para firmar o validar tokens SAS.
* *Rights* representa la recopilación de derechos de escucha, envío o administración concedidos.

Las reglas de autorización configuradas en el nivel de espacio de nombres pueden conceder acceso a todas las entidades de un espacio de nombres a los clientes con tokens firmados con la clave correspondiente. Se puede configurar un máximo de 12 reglas de autorización en un espacio de nombres, cola o tema de Bus de servicio. De forma predeterminada, se configura un objeto [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con todos los derechos para cada espacio de nombres cuando se aprovisiona por primera vez.

Para obtener acceso a una entidad, el cliente requiere un token SAS generado con un objeto [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)determinado. El token SAS se genera mediante el HMAC-SHA256 de una cadena de recurso que consta del URI del recurso al que se notifica el acceso y una fecha de expiración con una clave criptográfica asociada a la regla de autorización.

La compatibilidad de la autenticación de SAS con Bus de servicio se incluye en el SDK .NET de Azure 2.0 y versiones posteriores. SAS incluye compatibilidad con un objeto [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas las API que aceptan una cadena de conexión como parámetro incluyen compatibilidad con cadenas de conexión SAS.

## <a name="acs-authentication"></a>Autenticación de ACS
La autenticación de Bus de servicio mediante ACS se administra a través de un espacio de nombres de ACS "-sb" complementario. Si desea crear un espacio de nombres de ACS complementario para un espacio de nombres de Service Bus, no puede crearlo mediante el Portal de Azure clásico. Debe crear dicho espacio mediante el cmdlet [New-AzureSBNamespace de PowerShell](https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/New-AzureSBNamespace). Por ejemplo:

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Para evitar la creación de un espacio de nombres de ACS, emita el comando siguiente:

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Por ejemplo, si crea un espacio de nombres de Service Bus denominado **contoso.servicebus.windows.net**, se aprovisiona automáticamente un espacio de nombres de ACS complementario denominado **contoso-sb.accesscontrol.windows.net**. Para todos los espacios de nombres que se crearon antes de agosto de 2014, se creó igualmente un espacio de nombres de ACS acompañante.

De forma predeterminada, se aprovisiona una identidad de servicio predeterminada “propietario” con todos los derechos en este espacio de nombres de ACS complementario. Puede obtener un control específico para cualquier entidad de Bus de servicio mediante ACS al configurar las relaciones de confianza apropiadas. Puede configurar identidades de servicio adicionales para administrar el acceso a las entidades de Bus de servicio.

Para obtener acceso a una entidad, el cliente solicita un token SWT de ACS con las notificaciones adecuadas mediante la presentación de sus credenciales. El token SWT debe, a continuación, enviarse como parte de la solicitud a Bus de servicio para habilitar la autorización del cliente a fin de tener acceso a la entidad.

La compatibilidad de la autenticación de ACS con Bus de servicio se incluye en el SDK .NET de Azure 2.0 y versiones posteriores. Esta autenticación incluye compatibilidad con un objeto [SharedSecretTokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Todas las API que aceptan una cadena de conexión como parámetro incluyen compatibilidad con cadenas de conexión ACS.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre SAS, siga consultando [Autenticación con firma de acceso compartido en Bus de servicio](service-bus-shared-access-signature-authentication.md) .

Para obtener una descripción general de SAS en el Bus de servicio, consulte [Firmas de acceso compartido](service-bus-sas-overview.md).



<!--HONumber=Jan17_HO3-->


