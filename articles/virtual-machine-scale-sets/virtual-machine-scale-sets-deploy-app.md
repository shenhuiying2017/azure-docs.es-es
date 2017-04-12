---
title: "Implementación de una aplicación en conjuntos de escalado de máquinas virtuales| Microsoft Docs"
description: "Implementación de una aplicación en conjuntos de escalado de máquinas virtuales"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: f39840ab2fb31775c9703799393d8c386a8451ee
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Implementación de una aplicación en conjuntos de escalado de máquinas virtuales
Una aplicación que se ejecuta en un conjunto de escalado de máquina virtual normalmente se implementa en una de tres maneras:

* Instalación de software nuevo en una imagen de plataforma en el momento de la implementación
* Creación de una imagen de máquina virtual personalizada que incluya tanto el sistema operativo como la aplicación en un solo disco duro virtual
* Implementación de una plataforma o una imagen personalizada como un host de contenedor y la aplicación como uno o varios contenedores

## <a name="install-new-software-on-a-platform-image-at-deployment-time"></a>Instalación de software nuevo en una imagen de plataforma en el momento de la implementación
Una imagen de plataforma en este contexto es una imagen del sistema operativo de Azure Marketplace, como Ubuntu 16.04, Windows Server 2012 R2, etc.

Puede instalar un nuevo software en una imagen de plataforma utilizando una [extensión de máquina virtual](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Una extensión de máquina virtual es software que se ejecuta cuando se implementa una máquina virtual. Puede ejecutar cualquier código que desee en el momento de la implementación usando una extensión de script personalizada. [Esto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) es una plantilla de Azure Resource Manager de ejemplo que usa una [extensión de configuración de estado deseado (DSC) de Azure](virtual-machine-scale-sets-dsc.md) para instalar IIS y una aplicación integrada de .NET MVC con el escalado automático de Azure.

Una ventaja de este enfoque es que ofrece un nivel de separación entre el código de aplicación y el sistema operativo, y permite mantener la aplicación por separado. Por supuesto esto significa que también hay más piezas móviles, y el tiempo de implementación de la máquina virtual puede ser mayor si el script tiene mucho que descargar y configurar.

>[!NOTE]
>Si pasa información confidencial en el comando de extensión de script personalizado (por ejemplo, una contraseña), asegúrese de especificar `commandToExecute` en el atributo `protectedSettings` de la extensión de script personalizado en lugar del atributo `settings`.

## <a name="create-a-custom-vm-image-that-includes-both-the-os-and-the-application-in-a-single-vhd"></a>Creación de una imagen de máquina virtual personalizada que incluya tanto el sistema operativo como la aplicación en un solo disco duro virtual 
Aquí el conjunto de escalado consta de un conjunto de máquinas virtuales que se copian desde una imagen creada por el usuario, y que este tiene que mantener. Este enfoque no requiere ninguna configuración adicional en momento de la implementación de máquina virtual. De todas formas, en la versión `2016-03-30` de los conjuntos de escalado de máquina virtual (y versiones anteriores), los discos del sistema operativo para las máquinas virtuales en el conjunto de escalado se limitan a una única cuenta de almacenamiento. Por lo tanto, puede tener un máximo de 40 máquinas virtuales en un conjunto de escalado, en lugar del límite de 100 máquinas virtuales por escalado de las imágenes de plataforma. Consulte [Introducción al diseño de conjuntos de escalado](virtual-machine-scale-sets-design-overview.md) para obtener más información.

>[!NOTE]
>La versión `2016-04-30-preview` de la API de conjuntos de escalado de máquinas virtuales admite el uso de Azure Managed Disks para el disco del sistema operativo y los discos de datos adicionales. Para obtener más información, consulte [Managed Disks Overview](../storage/storage-managed-disks-overview.md) (Información general de Managed Disks) y [Use Attached Data Disks](virtual-machine-scale-sets-attached-disks.md) (Uso de datos de discos conectados). 

## <a name="deploy-a-platform-or-a-custom-image-as-a-container-host-and-your-app-as-one-or-more-containers"></a>Implementación de una plataforma o una imagen personalizada como un host de contenedor y la aplicación como uno o varios contenedores
Una plataforma o una imagen personalizada es básicamente un host de contenedor, por lo que puede instalar la aplicación como uno o varios contenedores.  Puede administrar los contenedores de la aplicación con un orquestador o una herramienta de administración de configuraciones. La ventaja de este enfoque es que se abstrae la infraestructura de nube de la capa de aplicación, y se pueden mantener por separado.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>¿Qué pasa cuando un conjunto de escalado de máquina virtual escala horizontalmente?
Cuando se agregan una o más máquinas virtuales a un conjunto de escalado aumentando la capacidad, ya sea manualmente o mediante el escalado automático, la aplicación se instala automáticamente. Por ejemplo, si el conjunto de escalado tiene extensiones definidas, estas se ejecutan en una máquina virtual nueva cada vez que aquel se crea. Si el conjunto de escalado se basa en una imagen personalizada, cualquier nueva máquina virtual es una copia de la imagen personalizada de origen. Si las máquinas virtuales de conjunto de escalado son hosts de contenedor, podría tener código de inicio para cargar los contenedores en una extensión de script personalizado, o una extensión puede instalar un agente que se registra con un orquestador de clúster (por ejemplo, Azure Container Service).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>¿Cómo administrar las actualizaciones de aplicación en conjuntos de escalado de máquina virtual?
Para las actualizaciones en los conjuntos de escalado de máquina virtual, hay tres enfoques principales que se derivan de los tres métodos de implementación de aplicación anteriores:

* Actualización con extensiones de máquina virtual. Cualquiera de las extensiones de máquina virtual que están definidas para un conjunto de escalado de máquina virtual se ejecuta cada vez que se implementa una nueva máquina virtual, se restablece la imagen inicial de una máquina virtual ya existente o se actualiza una extensión de máquina virtual. Si necesita actualizar la aplicación, una solución viable es actualizar una aplicación directamente a través de extensiones: simplemente actualice la definición de la extensión. Una forma sencilla de hacerlo es cambiar el fileUris para que apunte al nuevo software.

* El enfoque de la imagen personalizada inmutable. Al integrar la aplicación (o los componentes de la aplicación) en una imagen de máquina virtual, se puede centrar en crear una canalización confiable para automatizar la compilación, prueba e implementación de las imágenes. Puede diseñar la arquitectura para facilitar el intercambio rápido de un conjunto de escalado de ensayo a producción. Un buen ejemplo de este enfoque es el [trabajo de controlador Azure Spinnaker](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Packer y Terraform también son compatibles con Azure Resource Manager, por lo que también puede definir sus imágenes "como código" y compilarlas en Azure, luego usar el disco duro virtual en el conjunto de escalado. Sin embargo, hacerlo así sería problemático para las imágenes de Marketplace, donde los scripts de extensiones o los personalizados se vuelven más importantes ya que los bits de Marketplace no se manipulan directamente.

* Actualización de contenedores. Abstraer la administración del ciclo de vida de la aplicación a un nivel por encima de la infraestructura de nube, por ejemplo mediante la encapsulación de aplicaciones, y los componentes de aplicación en contenedores y administrar estos contenedores a través de orquestradores de contenedor y administradores de configuración como Chef o Puppet.

Las máquinas virtuales de conjunto de escalado, se convierten así en un sustrato estable para los contenedores y solo requieren seguridad ocasional así como actualizaciones relacionadas con el sistema operativo. Como se ha mencionado, Azure Container Service es un buen ejemplo de la adopción de este enfoque y la creación de un servicio a su alrededor.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>¿Cómo aplicar una actualización de sistema operativo a través de dominios de actualización?
Suponga que desea actualizar la imagen de sistema operativo al tiempo que mantiene el conjunto de escalado de máquina virtual en ejecución. Una manera de hacerlo es actualizar las imágenes de máquina virtual en las máquinas virtuales una por una. Puede hacerlo con PowerShell o la CLI de Azure. Hay comandos diferentes para actualizar el modelo de conjunto de escalado de máquinas virtuales (cómo se define su configuración) y para emitir llamadas de "actualización manual" en máquinas virtuales concretas. En el documento de Azure [Actualización de un conjunto de escalado de máquinas virtuales](./virtual-machine-scale-sets-upgrade-scale-set.md) también se proporciona más información sobre qué opciones hay disponibles para realizar actualizaciones del sistema operativo en un conjunto de escalado de máquinas virtuales.


