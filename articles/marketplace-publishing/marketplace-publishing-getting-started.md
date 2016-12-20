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
ms.date: 09/30/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: aed47a7e9aa06e48199a71f67ae6d82bcfe27c9a


---
# <a name="how-to-publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publicación y administración de una oferta en Azure Marketplace
Este artículo sirve de ayuda para crear, implementar y administrar sus soluciones incluidas en Azure Marketplace para que otros asociados y clientes de Azure las compren y usen.

## <a name="what-is-the-azure-marketplace"></a>¿Qué es Azure Marketplace?
Azure Marketplace es el lugar en el que un suscriptor de Azure puede encontrar servicios para facilitar el desarrollo de soluciones y aplicaciones locales o basadas en la nube. Y usar los servicios de [Azure Certified](http://azure.com/certified) como bloques de creación para desarrollar rápidamente una aplicación o servicio innovadores para su línea de negocios y para otros suscriptores de Azure.

Como publicador de Azure, Azure Marketplace constituye la mejor forma de distribuir y vender soluciones o servicios innovadores a otros desarrolladores, ISV y profesionales de TI que desean desarrollar rápidamente sus aplicaciones basadas en la nube y sus soluciones para dispositivos móviles.

## <a name="supported-types-of-offers"></a>Tipos de ofertas admitidos
Lo primero que le convendría hacer como publicador es definir el tipo de solución que ofrece su compañía. Azure Marketplace admite tres tipos de ofertas:

* **Imágenes de máquina virtual** : son imágenes preconfiguradas con un sistema operativo completamente instalado y una o varias aplicaciones. Una imagen de máquina virtual proporciona la información necesaria para crear e implementar máquinas virtuales en el servicio Máquinas virtuales de Azure.

  > [!NOTE]
  > **Por ejemplo,** como publicador de Azure, ha creado y validado una máquina virtual con un servicio de base de datos innovador que es bastante atractiva, por lo que otros suscriptores de Azure estarían dispuestos a adquirir e implementar esta máquina virtual en sus entornos de servicios en la nube.
  >
  >
* **Servicios para desarrolladores** : son servicios totalmente administrados para usar en el desarrollo de aplicaciones o en la administración del sistema. Proporcionan una funcionalidad que permite el desarrollo rápido de aplicaciones de escala en la nube en Azure.

  > [!NOTE]
  > **Por ejemplo,** como publicador de Azure, desarrolló un servicio accesible de API (hospedado en Azure o en cualquier otro sitio) que proporciona predicciones basadas en datos históricos. Y este es un servicio que otros suscriptores de Azure que están creando soluciones desean utilizar. Puede implementar este servicio en Azure Marketplace para que otros lo encuentren, adquieran y lo usen en sus respectivos servicios.
  >
  >
* **Plantilla de solución** : es una estructura de datos que puede hacer referencia a uno o varios servicios distintivos de Azure, incluidos los publicados por otros vendedores, para permitir que los suscriptores de Azure implementen una o varias ofertas de forma sencilla y coordinada.

  > [!NOTE]
  > **Por ejemplo,** como publicador de Azure, ha agrupado un conjunto de servicios de Azure que facilitan la rápida implementación de un servicio en la nube seguro y de alta disponibilidad con equilibrio de carga en unos pocos clics. Otros suscriptores de Azure podrían valorar el ahorro de tiempo que se obtiene mediante la adquisición de esta plantilla de solución en lugar de tener que identificar y configurar manualmente servicios de Azure iguales o similares.
  >
  >

Algunos pasos se comparten entre los distintos tipos de soluciones. En este artículo se ofrece una breve introducción a los pasos que debe completar para cualquier tipo de solución.

## <a name="1-pre-requisites"></a>1. Requisitos previos
> [!NOTE]
> Antes de empezar cualquier trabajo en Azure Marketplace, debe obtener la [aprobación previa](http://azure.com/certified).
>
>

1. [Solicitar la aprobación previa de Microsoft Azure Certified](marketplace-publishing-azure-certification.md)
2. [Crear y registrar una cuenta de desarrollador de Microsoft](marketplace-publishing-accounts-creation-registration.md)
3. [Cumplir los requisitos previos no técnicos](marketplace-publishing-pre-requisites.md)

## <a name="2-publishing-your-offer"></a>2. Publicación de la oferta
### <a name="21-complete-offer-specific-technical-pre-requisites"></a>2.1 Realización de los requisitos previos técnicos específicos de la oferta
* [Requisitos previos técnicos de la máquina virtual](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Requisitos previos técnicos de la plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md)

### <a name="22-create-your-offer"></a>2.2 Creación de la oferta
1. Cree su oferta mediante las siguientes directrices específicas de la oferta.
   * [Crear su oferta de máquina virtual](marketplace-publishing-vm-image-creation.md)
   * [Crear su oferta de plantilla de solución](marketplace-publishing-solution-template-creation.md)
2. [Crear la oferta de contenido de marketing](marketplace-publishing-push-to-staging.md)

### <a name="23-test-your-offer-in-staging"></a>2.3 Prueba de la oferta de máquina virtual en el entorno de ensayo
* [Prueba de la oferta de máquina virtual en el entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md)
* [Prueba de la oferta de plantilla de solución en el entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md)

### <a name="24-deploy-your-offer-to-the-marketplace"></a>2.4 Implementación de la oferta en Marketplace
* [Implementación de su oferta en Azure Marketplace](marketplace-publishing-push-to-production.md)
* [Solución de problemas comunes de publicación en Marketplace](marketplace-publishing-support-common-issues.md)
* Para más información sobre los portales que se usan, vea [Portales que necesitará](marketplace-publishing-portals.md).

### <a name="virtual-machine-image-specific"></a>Pasos específicos de la imagen de máquina virtual
* [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Creación de una máquina virtual que ejecuta Windows en Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creación de una máquina virtual Linux en Azure Portal](../virtual-machines/virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Troubleshooting common issues encountered during VHD creation](marketplace-publishing-vm-image-creation-troubleshooting.md) (Solución de problemas comunes que se encuentran durante la creación de un disco duro virtual [VHD])

## <a name="3-post-publishing-management-of-your-offer"></a>3. Administración posterior a la publicación de la oferta
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



<!--HONumber=Nov16_HO3-->


