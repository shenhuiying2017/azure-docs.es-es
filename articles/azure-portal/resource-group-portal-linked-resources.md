---
title: "Recursos relacionados y vinculados en la galería de iconos"
description: "Obtenga información acerca de los recursos vinculados y relacionados que se muestran en la galería de iconos del Portal de vista previa de Azure."
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: efa7bce26c2c4c153b083e0e34d689a11d27dd16
ms.lasthandoff: 03/06/2017


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Recursos relacionados y vinculados en la galería de iconos
La galería de iconos permite encontrar iconos para un recurso determinado y arrastrarlos a la hoja actual. Con la galería de iconos puede crear vistas de administración que abarcan los recursos. Para cualquier recurso especificado, los recursos relacionados incluyen todos los recursos del mismo grupo de recursos, además de los recursos que se vinculan a o desde el recurso.

## <a name="linked-resources-in-resource-manager"></a>Recursos vinculados en Resource Manager
La vinculación es una característica de Resource Manager.  permite declarar relaciones entre los recursos, incluso si no residen en el mismo grupo de recursos. La vinculación no influye en el tiempo de ejecución de los recursos ni tampoco en la facturación ni en el acceso basado en rol.  Es simplemente un mecanismo que puede usar para representar relaciones para que herramientas como la galería de iconos puedan proporcionar una experiencia de administración más rica.  Las herramientas pueden inspeccionar los vínculos mediante la API de vínculos y proporcionar también experiencias de administración de relaciones personalizadas. 

## <a name="how-do-i-link-my-resources"></a>¿Cómo puedo vincular mis recursos?
Al crear recursos a través del portal o mediante la implementación de una plantilla a través de Azure PowerShell o CLI de Azure, se crean automáticamente vínculos para algunos recursos dependientes. También puede vincular recursos mediante programación usando la [API de REST de recursos vinculados](/rest/api/resources/resourcelinks).

## <a name="next-steps"></a>Pasos siguientes
* Si necesita una introducción a la escritura de plantillas de Resource Manager, vea [Creación de plantillas](../azure-resource-manager/resource-group-authoring-templates.md).
* Para conocer más sobre cómo trabajar con grupos de recursos a través del Portal, vea [Uso de Azure Portal para administrar los recursos de Azure](../azure-resource-manager/resource-group-portal.md).


