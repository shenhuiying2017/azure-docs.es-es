<properties
   pageTitle="Requisitos previos técnicos para la creación de una imagen de máquina virtual para Azure Marketplace | Microsoft Azure"
   description="Entienda los requisitos para crear e implementar una imagen de máquina virtual en Azure Marketplace para que otros usuarios la compren."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace-publishing"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="10/09/2015"
  ms.author="hascipio; v-divte"/>

# Requisitos previos técnicos para la creación de una imagen de máquina virtual para Azure Marketplace
Lea el proceso minuciosamente antes de empezar y comprenda dónde y por qué se realiza cada paso. Tanto como sea posible, debe preparar la información de su compañía y otros datos, descargar las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta. Estos elementos deben estar claros tras la revisión de este artículo.

## Descargar herramientas y aplicaciones necesarias
Debe tener listos los elementos siguientes antes de comenzar el proceso:

- Dependiendo de su sistema operativo objetivo, instale los cmdlets de Azure PowerShell o la herramienta de la interfaz de línea de comandos de Linux desde la página de descargas de Azure.
- Instale el Explorador de almacenamiento de Azure desde CodePlex.
- Descargue e instale “Certification Test Tool for Azure Certified”:
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Necesitará un equipo basado en Windows para ejecutar la herramienta de certificación. Si no tiene un equipo basado en Windows disponible, puede ejecutar la herramienta con una VM basada en Windows en Azure.

## Plataformas compatibles
Puede desarrollar VM basadas en Azure en Windows o Linux. Algunos elementos del proceso de publicación, como la creación de un disco duro virtual compatible con Azure, usan diferentes herramientas y pasos según el sistema operativo que utiliza.

- Si usa Linux, consulte la sección "Creación de un VHD compatible con Azure (basado en Linux)" de [Virtual Machine Image Publishing Guide](marketplace-publishing-vm-image-creation.md) (Guía de publicación de imágenes de máquina virtual).
- Si usa Windows, consulte la sección "Creación de un VHD compatible con Azure (basado en Windows)" de [Virtual Machine Image Publishing Guide](marketplace-publishing-vm-image-creation.md) (Guía de publicación de imágenes de máquina virtual).

> [AZURE.NOTE]Necesitará tener acceso a un equipo de Windows para - Ejecutar la herramienta de validación de certificación - Crear la dirección URL de SAS del disco duro virtual para el envío de certificación del disco duro virtual

## Desarrollo de su disco duro virtual
Es posible desarrollar discos duros virtuales de Azure en la **nube** o de forma **local**.

- El desarrollo basado en la nube significa que todos los pasos de desarrollo se realizan de forma remota en un disco duro virtual residente en Azure.
- Para el desarrollo local se requiere la descarga de un disco duro virtual y su desarrollo mediante infraestructura local. Aunque esto es posible, no lo recomendamos. Tenga en cuenta que para el desarrollo para Windows o SQL de forma local se requiere que tenga las claves de licencia local pertinentes. No puede incluir ni instalar SQL Server después de crear una VM y debe basar su oferta en una imagen de SQL aprobada en el Portal de Azure. Si decide desarrollar de forma local, debe realizar algunos pasos de forma diferente a si desarrollara en la nube. Encontrará información pertinente en [Creating a VM image on-premise](marketplace-publishing-vm-image-creation-on-premise.md) (Creación de una imagen de VM de forma local).

## Pasos siguientes
Ahora que ha revisado los requisitos previos y completado la tarea necesaria, puede continuar con la creación de su oferta de imagen de máquina virtual como se detalla en [Virtual Machine Image Publishing Guide](marketplace-publishing-vm-image-creation.md) (Guía de publicación de imágenes de máquina virtual)

## Otras referencias
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Creación de una máquina virtual que ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-tutorial/)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=Oct15_HO3-->