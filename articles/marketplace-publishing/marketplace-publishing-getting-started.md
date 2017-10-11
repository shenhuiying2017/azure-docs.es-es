---
title: "Información general de creación e implementación de una oferta en Marketplace | Microsoft Docs"
description: "Conozca los pasos necesarios para convertirse en un desarrollador de Microsoft aprobado y crear e implementar una imagen de máquina virtual, una plantilla, un servicio de datos o un servicio de desarrolladores en Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: 8fbf201343f6710d2781a4b56ae54833ed4c06cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicación y administración de una oferta en Azure Marketplace
Este artículo sirve de ayuda a los desarrolladores para crear, implementar y administrar sus soluciones incluidas en Azure Marketplace para que otros asociados y clientes de Azure las compren y usen.

## <a name="marketplace-publishing"></a>Publicación de Marketplace
Como publicador de Azure, puede distribuir y vender su solución o servicio innovador a otros desarrolladores, ISV y profesionales de TI de Marketplace. A través de Marketplace, puede llegar a clientes que desean desarrollar rápidamente sus aplicaciones basadas en la nube y soluciones para dispositivos móviles. Si la solución está dirigida a usuarios empresariales, puede que le interese el Marketplace de [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Tipos de soluciones admitidas
Lo primero que le convendría hacer como publicador es definir el tipo de solución que ofrece su compañía. Marketplace admite los siguientes tipos de ofertas:

|Tipo de solución|Máquina virtual|Plantilla de solución|
|---|---|---|
|**Definición**|Imágenes preconfiguradas con un sistema operativo completamente instalado y una o varias aplicaciones. Una imagen de máquina virtual proporciona la información necesaria para crear e implementar máquinas virtuales en el servicio Máquinas virtuales de Azure.|Una estructura de datos que puede hacer referencia a uno o más servicios de Azure distintos, incluidos los servicios publicados por otros vendedores. Los suscriptores de Azure pueden usarla para implementar una o más ofertas de forma única y coordinada.|
|**Ejemplo**|Como publicador de Azure, ha creado y validado una máquina virtual con un servicio de base de datos innovador. Otros suscriptores de Azure desean obtener e implementar esta máquina virtual en sus entornos de servicios en la nube.|Como publicador de Azure, ha agrupado un conjunto de servicios de Azure que facilitan la implementación rápida de servicios en la nube con equilibrio de carga, seguridad mejorada y alta disponibilidad. Otros suscriptores de Azure pueden ahorrar tiempo si obtienen la plantilla de solución que cumpla su objetivo. No tienen que buscar, adquirir, implementar y configurar manualmente servicios de Azure iguales o similares.|

> [!NOTE]
> Algunos pasos se comparten entre los diferentes tipos de soluciones y otros son distintos del tipo de solución correspondiente. En este artículo se ofrece una corta introducción a los pasos que debe completar para cualquier tipo de solución.

## <a name="publish-a-solution"></a>Publicación de una solución
![Nominación, registro y publicación](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Designación de la solución para su aprobación previa
Para publicar una [solución](https://createopportunity.azurewebsites.net) de máquina virtual en Marketplace, rellene el **formulario de nominación de soluciones** de Microsoft Azure Certified.

>[!NOTE]
> Si va a trabajar con un administrador de cuentas asociado o un administrador asociado de DX, pídales que nominen su solución para el programa Azure Certified. También puede ir a la página web de [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) y rellenar el formulario de solicitud. En el cuadro de **contacto con el patrocinador de Microsoft**, escriba el correo electrónico del administrador de cuentas asociado o el administrador asociado de DX.

Si se cumplen los criterios de elegibilidad de las [directivas de participación de Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) y se aprueba u solicitud, comenzaremos a trabajar con usted para incorporar su solución a Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registrar de la cuenta como vendedor de Microsoft
Registre su cuenta Microsoft como una [cuenta de Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publicación de la solución
Para publicar una solución en Marketplace, siga estos pasos:
1. Complete los requisitos no técnicos.

    a. Complete los [requisitos previos no técnicos](marketplace-publishing-pre-requisites.md).

    b. Complete los [requisitos previos técnicos de la máquina virtual](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Complete los [requisitos previos técnicos de la plantilla de la solución](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Cree la oferta.

    a. Cree una oferta de [máquina virtual](marketplace-publishing-vm-image-creation.md).

    b. Cree una oferta de [plantilla de solución](marketplace-publishing-solution-template-creation.md).

3. Cree el [contenido de marketing](marketplace-publishing-push-to-staging.md) de su oferta.

4. Pruebe su oferta en el entorno de ensayo.

    a. Pruebe la oferta de máquina virtual en el [entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md).

    b. Pruebe la oferta de plantilla de solución en el [entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md).

5. Implemente su oferta en [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Creación y administración de una imagen de máquina virtual
Cree y administre una imagen de máquina virtual mediante estos recursos:
* Creación de una imagen de máquina virtual en el [entorno local](marketplace-publishing-vm-image-creation-on-premise.md).
* Creación de una máquina virtual [Windows en Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Creación de una máquina virtual [Linux en Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Solución de problemas comunes detectados durante la [creación del VHD](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Administración de la solución
Administre la solución con la ayuda de los recursos siguientes:
* [Guía de postproducción para ofertas de máquina virtual en Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Actualización de los detalles no técnicos de una oferta o una SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Actualización de los detalles técnicos de una SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Incorporación de una nueva SKU en una oferta activa](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Cambio del número de discos de datos para una SKU activa](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Eliminación de una oferta activa de Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Eliminación de una SKU activa de Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Eliminación de la versión actual de una SKU activa de Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Reversión del precio de lista a los valores de producción](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Reversión del modelo de facturación a los valores de producción](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Reversión de la configuración de visibilidad de una SKU activa al valor de producción](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Cambio del incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
* [Descripción de los informes de pago](marketplace-publishing-report-payout.md)
* [Obtención de soporte técnico como publicador](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Recursos adicionales
[Configuración de Azure PowerShell](marketplace-publishing-powershell-setup.md)
