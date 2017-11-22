---
title: Nombre del emisor y clave del emisor en BizTalk Services | Microsoft Azure
description: "Obtenga información acerca de cómo recuperar el Nombre del emisor y la Clave de emisor para Service Bus o Access Control (ACS) en BizTalk Services. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 18eac72d75680ab12c4a0bea9dfc5ac8a5fce566
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: nombre del emisor y clave del emisor

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services usan el nombre y la clave de emisor de Service Bus, además del nombre y la clave de emisor del servicio Access Control. Concretamente:

| Tarea | Nombre de emisor y clave de emisor |
| --- | --- |
| Implementación de la aplicación desde Visual Studio |Nombre y clave de emisor del servicio de control de acceso |
| Configuración del Portal de Azure BizTalk Services |Nombre y clave de emisor del servicio de control de acceso |
| Creación de relés de LOB con los servicios de adaptador de BizTalk en Visual Studio |Nombre de emisor y clave de emisor de Service Bus |

Este tema incluye los pasos necesarios para recuperar el nombre y la clave de emisor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nombre y clave de emisor del servicio de control de acceso
Los siguientes elementos usan el nombre y la clave de emisor del servicio de control de acceso:

* Su aplicación del servicio de BizTalk de Azure creada en Visual Studio: para implementar correctamente la aplicación del servicio de BizTalk de Visual Studio en Azure, debe escribir el nombre y la clave de emisor del servicio de control de acceso. 
* El Portal de Azure BizTalk Services: cuando se crea una instancia de BizTalk Services y se abre el Portal de BizTalk Services, tanto el nombre como la clave del emisor de Access Control se registran automáticamente para las implementaciones con los mismos valores de Access Control.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Obtención del nombre y la clave de emisor de Access Control

Para usar ACS para la autenticación y obtener los valores de Nombre de emisor y Clave de emisor, los pasos generales son:

1. Instale los [cmdlets de Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Agregue su cuenta de Azure: `Add-AzureAccount`
3. Devuelva el nombre de su suscripción: `get-azuresubscription`
4. Seleccione su suscripción: `select-azuresubscription <name of your subscription>` 
5. Creación de un espacio de nombres nuevo: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Ejemplo: `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Cuando se crea el nuevo espacio de nombres de ACS (que puede tardar varios minutos), los valores de Nombre de emisor y Clave de emisor se enumeran en la cadena de conexión: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Resumiendo:  
Nombre de emisor = SharedSecretIssuer  
Clave de emisor = SharedSecretKey

Más información sobre el cmdlet [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx). 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nombre de emisor y clave de emisor de Service Bus
Los servicios de adaptador de BizTalk usan el nombre y la clave de emisor de Service Bus. En su proyecto de BizTalk Services en Visual Studio, se usan los servicios de adaptador de BizTalk para conectarse a un sistema local de línea de negocio (LOB). Para conectarse, debe crear el relé de LOB y especificar los detalles de su sistema de LOB. Para ello, debe especificar el nombre y la clave de emisor de Service Bus.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Recuperación del nombre y la clave de emisor de Service Bus
1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2. Busque **Service Bus**y seleccione el espacio de nombres. 
3. Abra las propiedades de **Directivas de acceso compartido**, seleccione la directiva y vea la **Cadena de conexión** para el nombre y los valores de clave.  

## <a name="next"></a>Pasos siguientes
Otros temas acerca de Azure BizTalk Services:

* [Instalación del SDK de Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriales: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [¿Cómo puedo comenzar a utilizar el SDK de Azure BizTalk Services?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Otras referencias
* [Uso del servicio de administración de ACS para configurar identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [BizTalk Services: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: copias de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

