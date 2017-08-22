---
title: "Autenticación y autorización de Azure Service Bus | Microsoft Docs"
description: "Autentique aplicaciones en Service Bus con la autenticación de firma de acceso compartido (SAS)."
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
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: es-es
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>Autenticación y autorización de Service Bus

Las aplicaciones pueden autenticarse en Azure Service Bus mediante la autenticación con Firma de acceso compartido (SAS). La autenticación de Firma de acceso compartido permite a las aplicaciones autenticarse en Service Bus mediante una clave de acceso configurada en el espacio de nombres o la entidad al que se asocian derechos específicos. A continuación, puede usar esta clave para generar un token de Firma de acceso compartido que los clientes pueden usar para autenticarse en Service Bus.

> [!IMPORTANT]
> Debe usar SAS en lugar de Azure Active Directory Access Control (también conocido como Access Control Service o ACS), ya que ACS está en desuso. SAS proporciona un esquema de autenticación sencillo, flexible y fácil de usar para Service Bus. Las aplicaciones pueden usar SAS en escenarios en los que no necesitan administrar la noción de “usuario” autorizado. Para más información, vea [esta publicación del blog](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).

## <a name="shared-access-signature-authentication"></a>Autenticación con Firma de acceso compartido

[autenticación de SAS](service-bus-sas.md) le permite conceder acceso a un usuario a los recursos de Service Bus con derechos específicos. La autenticación SAS en Service Bus implica la configuración de una clave criptográfica con derechos asociados en un recurso de Service Bus. Los clientes pueden obtener acceso a ese recurso presentando un token SAS, que consta del URI del recurso al que se tiene acceso y una fecha de expiración firmada con la clave configurada.

Puede configurar claves para SAS en un espacio de nombres de Service Bus. La clave se aplica a todas las entidades de mensajes de ese espacio de nombres. También puede configurar claves en temas y colas de Service Bus. SAS también es compatible con [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Para usar SAS, puede configurar un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) en un espacio de nombres, cola o tema. Esta regla está formada por los siguientes elementos:

* *KeyName* que identifica la regla.
* *PrimaryKey* es una clave criptográfica usada para firmar o validar tokens SAS.
* *SecondaryKey* es una clave criptográfica usada para firmar o validar tokens SAS.
* *Rights* representa la recopilación de derechos de escucha, envío o administración concedidos.

Las reglas de autorización configuradas en el nivel de espacio de nombres pueden conceder acceso a todas las entidades de un espacio de nombres a los clientes con tokens firmados con la clave correspondiente. Se puede configurar un máximo de 12 reglas de autorización en un espacio de nombres, cola o tema de Service Bus. De forma predeterminada, se configura un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) con todos los derechos para cada espacio de nombres cuando se aprovisiona por primera vez.

Para obtener acceso a una entidad, el cliente requiere un token SAS generado con un objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)determinado. El token SAS se genera mediante el HMAC-SHA256 de una cadena de recurso que consta del URI del recurso al que se notifica el acceso y una fecha de expiración con una clave criptográfica asociada a la regla de autorización.

La compatibilidad de la autenticación de SAS con Service Bus se incluye en el SDK .NET de Azure 2.0 y versiones posteriores. SAS incluye compatibilidad con un objeto [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Todas las API que aceptan una cadena de conexión como parámetro incluyen compatibilidad con cadenas de conexión SAS.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre SAS, siga consultando [Autenticación en Service Bus con Firmas de acceso compartido](service-bus-sas.md).
- [Cambios en los espacios de nombres habilitados para ACS.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Para obtener la información correspondiente a la autenticación y autorización de Azure Relay, consulte [Azure Relay authentication and authorization](../service-bus-relay/relay-authentication-and-authorization.md) (Autenticación y autorización de Azure Relay). 


