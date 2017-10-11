---
title: Nombre del emisor y clave del emisor en BizTalk Services | Microsoft Azure
description: "Obtenga información acerca de cómo recuperar el Nombre del emisor y la Clave de emisor para el Bus de servicio o Control de acceso (ACS) en Servicios de BizTalk. MABS, WABS"
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
ms.openlocfilehash: b9fd985c23558596408e78eadae00dd0f95c4214
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Servicios de BizTalk: nombre del emisor y clave del emisor

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Los servicios de BizTalk de Azure usan el nombre y la clave de emisor del bus de servicio, además del nombre y la clave de emisor del servicio de control de acceso. Concretamente:

| Tarea | Nombre de emisor y clave de emisor |
| --- | --- |
| Implementación de la aplicación desde Visual Studio |Nombre y clave de emisor del servicio de control de acceso |
| Configuración del Portal de servicios de BizTalk de Azure |Nombre y clave de emisor del servicio de control de acceso |
| Creación de relés de LOB con los servicios de adaptador de BizTalk en Visual Studio |Nombre de emisor y clave de emisor del bus de servicio |

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

    Ejemplo:`new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
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

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nombre de emisor y clave de emisor del bus de servicio
Los servicios de adaptador de BizTalk usan el nombre y la clave de emisor del bus de servicio. En su proyecto de los servicios de BizTalk en Visual Studio, se usan los servicios de adaptador de BizTalk para conectarse a un sistema local de línea de negocio (LOB). Para conectarse, debe crear el relé de LOB y especificar los detalles de su sistema de LOB. Para ello, debe especificar el nombre y la clave de emisor del bus de servicio.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Recuperación del nombre y la clave de emisor del bus de servicio
1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, haga clic en **Bus de servicio**.
3. Seleccione su espacio de nombres. Seleccione **Información de conexión**en la barra de tareas. De este modo se muestran el **emisor predeterminado** (nombre de emisor) y la **clave predeterminada** (clave de emisor). Sus valores se pueden copiar.  

Resumiendo:  
Nombre de emisor = Emisor predeterminado  
Clave de emisor = Clave predeterminada

## <a name="next"></a>Pasos siguientes
Otros temas acerca de los servicios de BizTalk de Azure:

* [Instalación del SDK de Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriales: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [¿Cómo puedo comenzar a utilizar el SDK de Servicios de BizTalk de Azure?](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Otras referencias
* [Uso del servicio de administración de ACS para configurar identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Servicios de BizTalk: gráfico de las ediciones Developer, Basic, Standard y Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Servicios de BizTalk: aprovisionamiento con el Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Servicios de BizTalk: gráfico del estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Servicios de BizTalk: pestañas Panel, Monitor y Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Servicios de BizTalk: copias de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Servicios de BizTalk: limitaciones](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

