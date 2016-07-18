<properties
   pageTitle="Información general de creación e implementación de una oferta en Marketplace | Microsoft Azure"
   description="Conozca los pasos necesarios para convertirse en un desarrollador de Microsoft aprobado y crear e implementar una imagen de máquina virtual, una plantilla, un servicio de datos o un servicio de desarrolladores en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/05/2016"
   ms.author="hascipio" />

# Publicación y administración de una oferta en Azure Marketplace
Este artículo sirve de ayuda para crear, implementar y administrar sus soluciones incluidas en Azure Marketplace para que otros asociados y clientes de Azure las compren y usen.

Lo primero que le convendría hacer como publicador es definir el tipo de solución que ofrece su compañía. Azure Marketplace admite varias soluciones y cada una de ellas requiere que se establezca un conjunto de trabajo ligeramente diferente para publicar correctamente en Marketplace.

## Tipos de ofertas
|Tipo de oferta| Definición |
|---|---|
|Imagen de máquina virtual | Imagen de la máquina virtual preconfigurada con un sistema operativo completamente instalado y una o varias aplicaciones. Las ofertas de imagen de máquina virtual pueden incluir una sola imagen o varias relacionadas mediante una plantilla de solución. Una imagen de máquina virtual ("Imagen") proporciona la información necesaria para crear e implementar máquinas virtuales en el servicio Máquinas virtuales de Azure. Una imagen está formada por una unidad de disco duro virtual del sistema operativo y cero o más unidades de disco duro virtuales del disco de datos. Los clientes pueden implementar cualquier cantidad de máquinas virtuales a partir de una única imagen.|
|Servicio de desarrolladores| Servicios totalmente administrados para trabajadores de la información, analistas de negocios, desarrolladores o profesionales de TI que se usan en el desarrollo de aplicaciones personalizadas o la administración de sistemas. Los servicios de desarrolladores aportan una funcionalidad que permite a los clientes desarrollar con rapidez aplicaciones para la nube en Azure. Para comprar servicios de desarrolladores, los clientes deben tener una suscripción de Azure. Los publicadores son responsables de medir el uso de los servicios de desarrolladores por parte de los clientes, así como de notificar la información de uso a Microsoft, tal y como se detalla en el Acuerdo del publicador de Microsoft Azure Marketplace.|
|Plantilla de solución|Una "Plantilla de solución de Azure Resource Manager (ARM)" es una estructura de datos que puede hacer referencia a una o varias ofertas distintivas, incluidas las publicadas por otros publicadores, para permitir que los clientes de Azure implementen una o varias ofertas de forma sencilla y coordinada.|

Algunos pasos se comparten entre los distintos tipos de soluciones. En este artículo se ofrece una breve introducción a los pasos que debe completar para cualquier tipo de solución.

## 1\. Requisitos previos

> [AZURE.NOTE] Antes de empezar cualquier trabajo en Azure Marketplace, debe obtener la probación previa. Esto no se aplica a los publicadores de servicios de datos.

1. [Solicitar la aprobación previa de Microsoft Azure Certified](marketplace-publishing-azure-certification.md)
2. [Crear y registrar una cuenta de desarrollador de Microsoft](marketplace-publishing-accounts-creation-registration.md)
3. [Cumplir los requisitos previos no técnicos](marketplace-publishing-pre-requisites.md)

## 2\. Publicación de la oferta
### 2\.1 Realización de los requisitos previos técnicos específicos de la oferta
- [Requisitos previos técnicos de la máquina virtual](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Requisitos previos técnicos de la plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 Creación de la oferta
1. Cree su oferta mediante las siguientes directrices específicas de la oferta.
    - [Crear su oferta de máquina virtual](marketplace-publishing-vm-image-creation.md)
    - [Crear su oferta de plantilla de solución](marketplace-publishing-solution-template-creation.md)
2. [Crear la oferta de contenido de marketing](marketplace-publishing-push-to-staging.md)

### 2\.3 Prueba de la oferta de máquina virtual en el entorno de ensayo
- [Prueba de la oferta de máquina virtual en el entorno de ensayo](marketplace-publishing-vm-image-test-in-staging.md)
- [Prueba de la oferta de plantilla de solución en el entorno de ensayo](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 Implementación de la oferta en Marketplace
- [Implementación de su oferta en Azure Marketplace](marketplace-publishing-push-to-production.md)

### Pasos específicos de la imagen de máquina virtual ###
- [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [Solución de problemas comunes de publicación en Marketplace](marketplace-publishing-support-common-issues.md)
- Para más información sobre los portales que se usan, vea [Portales que necesitará](marketplace-publishing-portals.md).


## 3\. Administración posterior a la publicación de la oferta
- [Guía de pos-producción para ofertas de la máquina virtual](marketplace-publishing-vm-image-post-publishing.md)
- [Actualización de los detalles no técnicos de una oferta o una SKU](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [Eliminación de una oferta o una SKU de Azure Marketplace](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [Cómo cambiar el incentivo de revendedores para proveedores de soluciones en la nube](marketplace-publishing-csp-incentive.md)
- [Descripción de los informes de información del vendedor](marketplace-publishing-report-seller-insights.md)
- [Descripción de los informes de pago](marketplace-publishing-report-payout.md)
- [Obtención de soporte técnico como publicador](marketplace-publishing-get-publisher-support.md)

## Recursos adicionales
- [Configuración de Azure PowerShell](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->