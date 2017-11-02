---
title: "Guía para crear una plantilla de solución en Marketplace | Microsoft Docs"
description: "Se ofrecen instrucciones detalladas sobre cómo crear, certificar e implementar una plantilla de solución de imagen de varias máquinas virtuales para la venta en Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 675316b97e821a81ca4946d57a76d7bce978361b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guía para crear una plantilla de solución en Azure Marketplace
Después de completar el paso 1, [Creación y registro de cuentas][link-acct-creation], le guiamos en la creación de una plantilla de solución compatible con Azure en [Requisitos previos técnicos para crear una plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md). Ahora le guiaremos a través de los pasos para crear una plantilla de solución de varias máquinas virtuales en el [Portal de publicación][link-pubportal] de Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Creación de la oferta de plantilla de solución en el Portal de publicación
Vaya a [https://publish.windowsazure.com](http://publish.windowsazure.com). Cuando inicie sesión por primera vez en el [Portal de publicación](https://publish.windowsazure.com/), use la misma cuenta con la que se registró el perfil de vendedor de la compañía. Posteriormente, puede agregar a cualquier empleado de la empresa como coadministrador en el Portal de publicación.

### <a name="1-select-solution-templates"></a>1. Selección de "Plantillas de solución"
  ![dibujo][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Creación de una plantilla de solución
  ![dibujo][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Comienzo con topologías
Una plantilla de solución es una "matriz" para todas sus topologías. Puede definir varias topologías en una oferta o plantilla de solución. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus topologías. Siga estos pasos para definir la oferta:     

* Creación de una topología: el "identificador de la topología" suele ser el nombre de la topología en la plantilla de solución. El identificador de la topología se usará en la dirección URL, tal como se muestra a continuación:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure Portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Adición de una nueva versión

### <a name="4-get-your-topology-versions-certified"></a>4. Certificación de las versiones de topología
Cargue un archivo zip que contiene todos los archivos necesarios para realizar el aprovisionamiento de esa versión específica de la topología. Este archivo zip debe contener lo siguiente:

* Los archivos *mainTemplate.json* y *createUiDefinition.json* en su directorio raíz.
* Las plantillas vinculadas y todos los scripts requeridos.

  > [!TIP]
  > Mientras los desarrolladores trabajan en la creación de las topologías de la plantilla de solución y en conseguir que se certifiquen, el departamento comercial, de marketing o legal de su empresa puede trabajar en el contenido de marketing y legal.
  >
  >

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado la plantilla de solución y cargado el archivo .zip, siga las instrucciones de la [Guía de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md) antes de enviar la oferta al entorno de ensayo. Para ver el conjunto completo de artículos de la publicación de Marketplace, consulte [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md).

Es posible que también le interesen los siguientes artículos relacionados:

* Imágenes de máquinas virtuales: [Acerca de las imágenes de máquina virtual en Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Extensiones de VM: [Información general del agente de máquina virtual y las extensiones de VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) y [Características y extensiones de máquina virtual de Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) y [Ejemplos sencillos de plantillas](https://github.com/rjmax/ArmExamples)
* Limitaciones de cuentas de almacenamiento: [Supervisión de limitaciones de cuentas de almacenamiento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) y [Premium Storage](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
