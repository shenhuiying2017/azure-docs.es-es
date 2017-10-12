---
title: Publicar un elemento de Marketplace personalizado en Azure Stack (operador de nube) | Microsoft Docs
description: "Como operador de Azure Stack, aprenda cómo publicar un elemento de Marketplace personalizado en Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7b5f976eb2d51eb86761a2bd0be6adb45ca87681
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="the-azure-stack-marketplace-overview"></a>Información general de Azure Stack Marketplace

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Marketplace es un conjunto de servicios, aplicaciones y recursos personalizados para Azure Stack, como redes, máquinas virtuales, almacenamiento, etc. Es el lugar donde los usuarios vienen para crear nuevos recursos e implementar nuevas aplicaciones. Piense que es como un catálogo de compras donde los usuarios pueden examinar y elegir los elementos que se quieren usar. Los usuarios, para utilizar un elemento de Marketplace, deben suscribirse a una oferta que les concede acceso al elemento.

Como operador Azure Stack, usted decide qué elementos quiere agregar (publicar) en Marketplace. Puede publicar elementos, como bases de datos, App Services, etc. Esto hace que sean visibles para todos los usuarios. Puede publicar elementos personalizados creados por usted. También puede publicar elementos de una creciente [lista de elementos de Azure Marketplace](azure-stack-marketplace-azure-items.md). Al publicar un elemento en Marketplace, los usuarios pueden verlo a los cinco minutos.

Para abrir Marketplace, haga clic en **Nuevo**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementos de Marketplace
Un elemento de Azure Stack Marketplace es un servicio, una aplicación o un recurso que los usuarios pueden descargar y usar. Todos los elementos de Azure Stack Marketplace son visibles para todos los usuarios.

Cada elemento de Marketplace tiene:

* Una plantilla del Administrador de recursos de Azure para el aprovisionamiento de recursos
* Metadatos, como cadenas, iconos y otra documentación y material adjunto de marketing
* Información de formato para mostrar el elemento en el portal

Cada elemento publicado en Marketplace utiliza un formato denominado paquete de galería de Azure (azpkg). Agregue recursos de implementación o de entorno en tiempo de ejecución (por ejemplo, código, archivos comprimidos con software o imágenes de máquinas virtuales) a Azure Stack por separado, no como parte del elemento de Marketplace. 

## <a name="next-steps"></a>Pasos siguientes
[Crear y publicar un elemento en Marketplace](azure-stack-create-and-publish-marketplace-item.md)

