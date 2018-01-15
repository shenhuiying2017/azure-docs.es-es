---
title: "Cadena de conexión del almacén de imágenes de Azure Service Fabric | Microsoft Docs"
description: "Descripción de la cadena de conexión del almacén de imágenes"
services: service-fabric
documentationcenter: .net
author: alexwun
manager: timlt
editor: 
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: alexwun
ms.openlocfilehash: 4b64331a4f25ce0cc01b2ee9f32633ab035e3131
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Descripción del valor ImageStoreConnectionString

En alguna parte de nuestra documentación, se menciona brevemente la existencia de un parámetro "ImageStoreConnectionString" sin describir lo que realmente significa. Y después de pasar por un artículo como [Implementación y eliminación de aplicaciones con PowerShell][10], parece que todo lo que hace es copiar y pegar el valor tal y como aparece en el manifiesto de clúster del clúster de destino. Por tanto, el valor se debe configurar por clúster, pero cuando se crea un clúster a través de [Azure Portal][11], no hay ninguna opción para configurar este valor y siempre es "fabric:ImageStore". Entonces, ¿cuál es la finalidad de este valor?

![Manifiesto de clúster][img_cm]

Service Fabric comenzó como una plataforma para consumo interno de Microsoft por numerosos y diferentes equipos, por lo que algunos aspectos de la misma son muy personalizables (por ejemplo, el "almacén de imágenes"). En esencia, el almacén de imágenes es un repositorio conectable para almacenar paquetes de aplicaciones. Cuando la aplicación se implementa en un nodo del clúster, dicho nodo descarga el contenido del paquete de aplicación desde el almacén de imágenes. ImageStoreConnectionString es un valor que incluye toda la información necesaria para los clientes y nodos para buscar el almacén de imágenes correcto para un clúster determinado.

Actualmente existen tres posibles tipos de proveedores de almacenes de imágenes y sus correspondientes cadenas de conexión son las siguientes:

1. Servicio de almacén de imágenes: "fabric:ImageStore"

2. Sistema de archivos: "file:[ruta de acceso al sistema de archivos]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

El tipo de proveedor utilizado en producción es el servicio de almacén de imágenes, que es un servicio de sistema persistente con estado que puede ver en Service Fabric Explorer. 

![Servicio de almacén de imágenes][img_is]

El hospedaje del almacén de imágenes en un servicio de sistema dentro del propio clúster elimina dependencias externas para el repositorio de paquetes y nos proporciona mayor control sobre la localidad del almacenamiento. Es probable que las futuras mejoras para el almacén de imágenes se centren primero, si no exclusivamente, en el proveedor de dicho almacén. La cadena de conexión para el proveedor del servicio de almacén de imágenes no tiene ninguna información exclusiva puesto que el cliente ya está conectado al clúster de destino. El cliente solo debe saber que se deben usar protocolos cuyo destino sea el servicio del sistema.

El proveedor del sistema de archivos se utiliza en lugar del servicio de almacén de imágenes para los clústeres one-box locales durante el desarrollo para arrancar el clúster un poco más rápido. La diferencia es generalmente pequeña, pero es una optimización útil para la mayoría de la gente durante el desarrollo. Es posible implementar también un clúster one-box local con los otros tipos de proveedor de almacenamiento, pero normalmente no hay ninguna razón para hacerlo ya que el flujo de trabajo de desarrollo y pruebas sigue siendo el mismo independientemente del proveedor. Aparte de este uso, los proveedores de sistema de archivos y de Azure Storage solo existen para la compatibilidad heredada.

Por lo tanto, aunque ImageStoreConnectionString se puede configurar, generalmente solo se usa el valor predeterminado. Al publicar en Azure a través de Visual Studio, el parámetro se establece automáticamente en consecuencia. Para la implementación mediante programación en clústeres hospedados en Azure, la cadena de conexión siempre es "fabric:ImageStore". Sin embargo, en caso de duda, su valor siempre se puede comprobar recuperando el manifiesto de clúster mediante [PowerShell](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) o [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Tanto los clústeres de producción como de prueba locales siempre deben configurarse para usar también el proveedor de servicio de almacén de imágenes.

### <a name="next-steps"></a>pasos siguientes
[Implementación y eliminación de aplicaciones con PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md

