---
title: "Requisitos previos técnicos para la creación de una imagen de máquina virtual para Azure Marketplace | Microsoft Docs"
description: "Entienda los requisitos para crear e implementar una imagen de máquina virtual en Azure Marketplace para que otros usuarios la compren."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: af3e2ad623d8d7bfafe676411f9ae3fbee78aab8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Requisitos previos técnicos para la creación de una imagen de máquina virtual para Azure Marketplace
Lea el proceso minuciosamente antes de empezar y comprenda dónde y por qué se realiza cada paso. Tanto como sea posible, debe preparar la información de su compañía y otros datos, descargar las herramientas necesarias o crear componentes técnicos antes de comenzar el proceso de creación de la oferta. Estos elementos deben estar claros tras la revisión de este artículo.  

## <a name="download-needed-tools--applications"></a>Descargar herramientas y aplicaciones necesarias
Debe tener listos los elementos siguientes antes de comenzar el proceso:

* Dependiendo de su sistema operativo objetivo, instale los [cmdlets de Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) o la [herramienta de la interfaz de la línea de comandos de Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) desde la página de [descargas de Azure](https://azure.microsoft.com/downloads/).
* Instale el Explorador de almacenamiento de Azure desde CodePlex.
* Descargue e instale la herramienta Certification Test Tool for Azure Certified:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Necesita un equipo basado en Windows para ejecutar la herramienta de certificación. Si no tiene un equipo basado en Windows disponible, puede ejecutar la herramienta con una VM basada en Windows en Azure.

## <a name="platforms-supported"></a>Plataformas compatibles
Puede desarrollar VM basadas en Azure en Windows o Linux. Algunos elementos del proceso de publicación, como la creación de un disco duro virtual compatible con Azure, usan diferentes herramientas y pasos según el sistema operativo que usa:  

* Si usa Linux, consulte la sección "Creación de un VHD compatible con Azure (basado en Linux)" de la [Virtual machine image publishing guide](marketplace-publishing-vm-image-creation.md)(Guía de publicación de imágenes de máquina virtual).
* Si usa Windows, consulte la sección "Creación de un VHD compatible con Azure (basado en Windows)" de la [Virtual machine image publishing guide](marketplace-publishing-vm-image-creation.md)(Guía de publicación de imágenes de máquina virtual).

> [!NOTE]
> Se necesita acceso a una máquina basada en Windows:
> 
> * Ejecute la herramienta de validación de certificados.
> * Cree la dirección URL de firma de acceso compartido del VHD para la presentación de la certificación de dicho.
> 
> 

## <a name="develop-your-vhd"></a>Desarrollar el disco duro virtual
Puede desarrollar discos duros virtuales de Azure en la nube o de forma local:

* El desarrollo basado en la nube significa que todos los pasos de desarrollo se realizan de forma remota en un disco duro virtual residente en Azure.
* Para el desarrollo local se requiere la descarga de un disco duro virtual y su desarrollo mediante infraestructura local. Aunque esto es posible, no lo recomendamos. Tenga en cuenta que para el desarrollo para Windows o SQL de forma local se requiere que tenga las claves de licencia local pertinentes. No puede incluir ni instalar SQL Server tras crear una máquina virtual. También debe basar su oferta en una imagen SQL aprobada del portal de Azure. Si decide desarrollar de forma local, debe realizar algunos pasos de forma diferente a si desarrollara en la nube. Encontrará información pertinente en [Creación de una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
