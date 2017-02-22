---
title: Uso del Conector para recursos de Azure en Logic Apps | Microsoft Docs
description: "Creación y configuración del Conector de recursos de Azure o la aplicación de API y su uso en una aplicación lógica en el Servicio de aplicaciones de Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: stepsic-microsoft-com
manager: erikre
editor: 
ms.assetid: a32e5a5c-34d7-4422-b0f7-c5cf7b8abffa
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 7ff7874538f23c8dd8c4aaa789018c77dfe2ab9a
ms.openlocfilehash: 7923735d9ed382ee8c554e7e0c6b1fb4a22208bb


---
# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Introducción al conector de recursos de Azure y su incorporación a la aplicación lógica
> [!NOTE]
> Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de aplicaciones lógicas.
> 
> 

Use el conector de recursos de Azure para administrar de manera fácil los recursos de Azure dentro de la aplicación lógica.

## <a name="create-the-azure-resource-connector"></a>Creación del Conector de recursos de Azure
Para usar la aplicación de API del Conector de recursos de Azure, deberá crear antes una instancia de ella. Esta tarea puede realizarse en línea mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del Conector del Administrador de recursos de Azure en Azure Marketplace.

Para configurarlo, tendrá que establecer una entidad de servicio con permisos para realizar lo que desee hacer en Azure. Todas las llamadas se realizan en nombre de esta entidad de servicio que configura. Esto le permitirá definir el ámbito del conector para utilizar solo exactamente lo que desea hacer y nada más.

David Ebbo ha escrito [una gran cantidad de entradas de blog](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sobre su configuración. Siga todas las instrucciones y obtenga el **id. del inquilino**, el **id. de cliente** y el **secreto**. Estos tres campos, más el de **Id. de suscripción**son los necesarios para configurar el Conector.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>Uso del Conector de recursos de Azure en el diseñador de aplicaciones lógicas
### <a name="trigger"></a>Desencadenador
Hay dos desencadenadores admitidos en el Conector:

| Nombre | Description |
| --- | --- |
| Se produce el evento |Se activa cuando se produce un evento en un recurso en la suscripción. |
| Métrica supera el umbral |Se activa cuando una métrica alcanza un umbral determinado. |

### <a name="action"></a>Acción
Del mismo modo, puede proporcionar un gran número de acciones dentro de la suscripción de Azure:

Para **Grupos de recursos** , puede:

| Nombre | Description |
| --- | --- |
| Enumeración de grupos de recursos |Enumere todos los grupos de recursos en la suscripción. |
| Obtención de un grupo de recursos |Obtenga un grupo de recursos por su identificador. |
| Creación de un grupo de recursos |Cree o actualice un grupo de recursos. |
| Eliminación de un grupo de recursos |Elimine un grupo de recursos. |

Para **Recursos** , puede:

| Nombre | Description |
| --- | --- |
| Enumeración de recursos |Enumere recursos en la suscripción por diferentes tipos de filtros. |
| Obtención de recursos |Obtenga un único recurso por su id. de recurso. |
| Creación o actualización de recursos |Cree un recurso o actualice uno existente. Debe proporcionar todas las propiedades para ese recurso. |
| Acción de recursos |Realice cualquier otra acción en un recurso. Necesitará saber el nombre de acción y la carga útil que precisa esta acción (si corresponde). |
| Eliminación de un recurso |Elimine un recurso. |

Para **Proveedores de recursos** , puede:

| Nombre | Description |
| --- | --- |
| Enumeración de proveedores de recursos |Enumere todos los proveedores de recursos disponibles en esta suscripción. |

Para **Implementaciones de grupo de recursos** , puede:

| Nombre | Description |
| --- | --- |
| Enumeración de implementaciones |Enumere todas las implementaciones en un grupo de recursos. |
| Obtención de implementación |Obtenga una implementación de plantilla por su id. |
| Creación de una implementación |Cree una nueva implementación de grupo de recursos proporcionando una plantilla. |

Para **Eventos** acerca de los recursos, puede:

| Nombre | Description |
| --- | --- |
| Obtención de eventos |Obtenga eventos en una suscripción o para un recurso. |

Para **Métricas** acerca de recursos, puede:

| Nombre | Description |
| --- | --- |
| Obtención de métricas |Obtenga una métrica para un id. de recurso. |

## <a name="do-more-with-your-connector"></a>Aplicaciones adicionales del conector
Después de crear el conector, puede agregarlo a un flujo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md)

> [!NOTE]
> Si desea empezar a trabajar con Azure Logic Apps antes de registrarse para obtener una cuenta de Azure, vaya a [Probar Logic Apps](https://azure.microsoft.com/try/app-service/logic/), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

Consulte la referencia de API de REST de Swagger en [Referencia de Aplicaciones de API y conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md



<!--HONumber=Jan17_HO3-->


