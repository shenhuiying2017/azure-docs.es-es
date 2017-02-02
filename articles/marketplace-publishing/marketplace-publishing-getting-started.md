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
ms.date: 12/07/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 98840e0bc1565eab0e5936cb65b68852e1423450
ms.openlocfilehash: fd71f55f636fa1b7918536d7e6b51fb6a2a05903


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicación y administración de una oferta en Azure Marketplace
Este artículo sirve de ayuda para crear, implementar y administrar sus soluciones incluidas en Azure Marketplace para que otros asociados y clientes de Azure las compren y usen.

## <a name="what-is-the-azure-marketplace"></a>¿Qué es Azure Marketplace?
Como publicador de Azure, Azure Marketplace constituye la mejor forma de distribuir y vender soluciones o servicios innovadores a otros desarrolladores, ISV y profesionales de TI que desean desarrollar rápidamente sus aplicaciones basadas en la nube y sus soluciones para dispositivos móviles. Si la solución está dirigida a usuarios empresariales, puede que le interese el catálogo de soluciones de [AppSource](http://appsource.microsoft.com).


## <a name="supported-types-of-solutions"></a>Tipos de soluciones admitidas
Lo primero que le convendría hacer como publicador es definir el tipo de solución que ofrece su compañía. Azure Marketplace admite los siguientes tipos de ofertas:

|Tipo de solución|Máquina virtual|Plantilla de solución|
|---|---|---|
|Definición|Imágenes preconfiguradas con un sistema operativo completamente instalado y una o varias aplicaciones. Una imagen de máquina virtual proporciona la información necesaria para crear e implementar máquinas virtuales en el servicio Máquinas virtuales de Azure.|Una estructura de datos que puede hacer referencia a uno o varios servicios distintivos de Azure, incluidos los publicados por otros vendedores, para permitir que los suscriptores de Azure implementen una o varias ofertas de forma sencilla y coordinada.|
|Ejemplo|**Por ejemplo,** como publicador de Azure, ha creado y validado una máquina virtual con un servicio de base de datos innovador que es bastante atractiva, por lo que otros suscriptores de Azure estarían dispuestos a adquirir e implementar esta máquina virtual en sus entornos de servicios en la nube.|**Por ejemplo,** como publicador de Azure, ha agrupado un conjunto de servicios de Azure que facilitan la rápida implementación de servicios en la nube seguros y de alta disponibilidad con equilibrio de carga en unos pocos clics. Otros suscriptores de Azure pueden ahorrar tiempo si adquieren la plantilla de solución que satisfaga sus objetivos en lugar de buscar, adquirir, implementar y configurar manualmente los mismos servicio de Azure o similares.|

> [!NOTE]
> Tenga en cuenta que algunos pasos se comparten entre los diferentes tipos de soluciones y que otros son distintos del tipo de solución correspondiente. En este artículo se ofrece una breve introducción a los pasos que debe completar para cualquier tipo de solución.

## <a name="how-to-publish-a-solution"></a>Cómo publicar una solución
![dibujo](media/marketplace-publishing-getting-started/img01.png)

### <a name="1-nominate-your-solution-for-pre-approval"></a>1. Designación de la solución para su aprobación previa
> [!IMPORTANT]
> Antes de empezar cualquier trabajo en Azure Marketplace, debe obtener la [aprobación previa](http://azure.com/certified).

- [Solicitar la aprobación previa de Microsoft Azure Certified](marketplace-publishing-azure-certification.md)

### <a name="2-register-your-account-as-a-microsoft-seller"></a>2. Registrar de la cuenta como vendedor de Microsoft
- [Registro de la cuenta Microsoft como una cuenta de Microsoft Developer](marketplace-publishing-accounts-creation-registration.md)

### <a name="3-publish-your-solution"></a>3. Publicación de la solución
1. Cumplimiento de los requisitos no técnicos
  - [Cumplir los requisitos previos no técnicos](marketplace-publishing-pre-requisites.md)
  - [Requisitos previos técnicos de la máquina virtual](marketplace-publishing-vm-image-creation-prerequisites.md)
  - [Requisitos previos técnicos de la plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md)
2. Creación de la oferta
  - [Máquina virtual](marketplace-publishing-vm-image-creation.md)
  - [Plantilla de solución](marketplace-publishing-solution-template-creation.md)
3. [Crear la oferta de contenido de marketing](marketplace-publishing-push-to-staging.md)
4. Prueba de la oferta en el entorno de ensayo
  - [Prueba de la oferta de máquina virtual en el entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md)
  - [Prueba de la oferta de plantilla de solución en el entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md)
5. [Implementación de su oferta en Azure Marketplace](marketplace-publishing-push-to-production.md)


### <a name="virtual-machine-image-specific"></a>Pasos específicos de la imagen de máquina virtual
* [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Creación de una máquina virtual que ejecuta Windows en Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creación de una máquina virtual Linux en Azure Portal](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Troubleshooting common issues encountered during VHD creation](marketplace-publishing-vm-image-creation-troubleshooting.md) (Solución de problemas comunes que se encuentran durante la creación de un disco duro virtual [VHD])

## <a name="how-to-manage-your-solution"></a>Cómo administrar la solución
* [Guía de pos-producción para ofertas de la máquina virtual](marketplace-publishing-vm-image-post-publishing.md)
* [Actualización de los detalles no técnicos de una oferta o una SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
* [Actualización de los detalles técnicos de una oferta o una SKU](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
* [Incorporación de una nueva SKU en una oferta activa](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
* [Cambio del número de discos de datos para una SKU activa](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
* [Eliminación de una oferta activa de Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Eliminación de una SKU activa de Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
* [Eliminación de la versión actual de una SKU activa de Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
* [Reversión del precio de lista a los valores de producción](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
* [Reversión del modelo de facturación a los valores de producción](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
* [Reversión de la configuración de visibilidad de una SKU activa al valor de producción](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
* [Cómo cambiar el incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
* [Descripción de los informes de información del vendedor](marketplace-publishing-report-seller-insights.md)
* [Descripción de los informes de pago](marketplace-publishing-report-payout.md)
* [Obtención de soporte técnico como publicador](marketplace-publishing-get-publisher-support.md)

## <a name="additional-resources"></a>Recursos adicionales
* [Configuración de Azure PowerShell](marketplace-publishing-powershell-setup.md)



<!--HONumber=Dec16_HO2-->


