---
title: "Eliminación de una oferta de Azure Marketplace | Microsoft Docs"
description: "En este artículo se proporcionan detalles sobre la eliminación de una oferta de Azure Marketplace"
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>Eliminación de una oferta/SKU de Azure Marketplace

Puede decidir eliminar su oferta de Marketplace por varios motivos.  La eliminación de ofertas garantiza que los clientes nuevos ya no puedan adquirir ni implementar la oferta, pero afecta a los clientes existentes. La finalización de la oferta es el proceso de terminación del servicio o contrato de licencia entre usted y los clientes existentes.  Las guías y las directivas relacionadas con la eliminación y la terminación de ofertas se rigen por el [contrato del anunciante de Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (consulte la sección 7) y las [directivas de participación](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (consulte la sección 6.2).   En este artículo se habla de los diferentes escenarios de eliminación admitidos y los pasos que puede seguir en ellos.

## <a name="delete-a-live-sku-from-azure-marketplace"></a>Eliminación de una SKU en directo de Azure Marketplace

Para eliminar una SKU en directo de Azure Marketplace siga estos pasos:

1.  Inicie sesión en [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Seleccione la oferta en la pestaña **Todas las ofertas**.

3.  En el panel izquierdo de la pantalla, seleccione la pestaña **SKU**.

4.  Seleccione la SKU que quiera eliminar y haga clic en el botón Eliminar de esa SKU.

5.  [Vuelva a publicar](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) la oferta en Azure Marketplace.

Cuando la oferta esté en directo en Azure Marketplace, la SKU se eliminará de Azure Marketplace y Azure Portal.

## <a name="rollback-to-a-previous-sku-version"></a>Reversión a una versión anterior de SKU

Para eliminar la versión actual de una SKU en directo de Azure Marketplace siga estos pasos. Cuando se complete el proceso, la SKU se revertirá a la versión anterior.

1.  Inicie sesión en [Cloud Partner Portal](https://cloudpartner.azure.com/).

2.  Seleccione la oferta en la pestaña **Todas las ofertas**.

3.  En el panel izquierdo de la pantalla, seleccione la pestaña **SKU**.

4.  Elimine la **versión de disco** más reciente de la lista de versiones de disco publicada.

5.  [Vuelva a publicar](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) la oferta en Azure Marketplace.

Cuando la oferta esté en directo en Azure Marketplace, la versión actual de la SKU indicada se eliminará de Azure Marketplace y Azure Portal. La SKU se revertirá a la versión anterior.

## <a name="delete-a-live-offer"></a>Eliminación de una oferta en directo

En el caso de una solicitud para quitar una oferta activa, son varios los aspectos que se deben tener en cuenta. Siga estos pasos si quiere recibir ayuda del equipo de soporte técnico para eliminar una oferta en directo de Azure Marketplace:

1.  Genere una incidencia de soporte técnico mediante este [vínculo](https://go.microsoft.com/fwlink/?linkid=844975)

2.  Seleccione **Managing offers** (Administrar ofertas) de la lista **Tipo de problema** y de la lista**Category** (Categoría), **Modificar una oferta o SKU ya en producción**.

3.  Envíe la solicitud.

El equipo de soporte técnico le guiará en el proceso de eliminación de la oferta.

Tenga en cuenta que la eliminación de una oferta o SKU no afectará a las compras ya realizadas de la SKU/oferta. Estas seguirán funcionando como antes. No estarán disponibles para las compras futuras.  
