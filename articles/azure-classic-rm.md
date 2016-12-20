---
title: "Modos de implementación de Resource Manager y administración de servicios (clásico) | Microsoft Docs"
description: "Comprenda las diferencias entre los modelos de implementación Administrador de recursos y Clásico."
services: virtual-network
documentationcenter: 
author: telmosampaio
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 18a235d8-38ac-4886-9e56-b3855c73ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: telmos
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: b08630777e9cb66e3baae24ad9befc93a47e65db


---
# <a name="azure-deployment-models"></a>Modelos de implementación de Azure
La plataforma Azure está en transición.  Si está familiarizado con Azure o lo ha usado durante años, es importante que comprenda algunos de los principales cambios que hacemos en la plataforma durante esta transición.

Todos los recursos de Azure admiten uno o los dos modelos de implementación siguientes:

* **Administrador de recursos:** se trata del modelo de implementación más reciente para recursos de Azure. La mayoría de los recursos más recientes ya son compatibles con este modelo de implementación y, a la larga, todos los recursos lo serán.   
* **Clásico:** este modelo es compatible con la mayoría de los recursos de Azure existentes hoy en día. Los nuevos recursos que se agreguen a Azure no serán compatibles con este modelo.

La documentación sobre cada recurso de Azure describe detalladamente los modelos de servicio con los que se puede crear.

## <a name="why-does-this-matter"></a>¿Por qué es importante?
Es importante por los motivos siguientes:

* Las características de la plataforma Azure que se usan son diferentes en estos dos modelos.  Por ejemplo, los recursos creados con el modelo de implementación de Resource Manager (o simplemente, Resource Manager) pueden generarse a partir de [plantillas de Azure Resource Manager](azure-resource-manager/resource-group-overview.md#template-deployment), mientras que los recursos creados con el modelo de implementación clásica, no.
* Las características o los comportamientos de cada uno de los recursos de Azure pueden ser diferentes en los dos modelos o solo existir en uno de los modelos.  Por ejemplo, el tráfico de equilibrio de carga entre máquinas virtuales creadas con el modelo de implementación Clásico está *implícito* porque las máquinas virtuales son miembros de un servicio en la nube de Azure y la carga se equilibra automáticamente entre máquinas virtuales dentro de un servicio en la nube. Las máquinas virtuales creadas con el Administrador de recursos no son miembros de un servicio en la nube, por lo que debe crearse *explícitamente* un recurso de equilibrador de carga de Azure aparte para el tráfico de equilibrio de carga entre varias máquinas virtuales.  
* La forma de crear, configurar y administrar los recursos de Azure es diferente entre estos dos modelos.
* Los recursos creados con un modelo de implementación no necesariamente pueden interoperar con los recursos creados mediante otro modelo de implementación. Por ejemplo, las máquinas virtuales de Azure creadas con un modelo de implementación solo pueden conectarse a redes virtuales de Azure creadas con el mismo modelo de implementación.    

En cada uno de los modelos de implementación existe una interfaz de programación de aplicaciones (API) subyacente para cada recurso.  Existe [API de Resource Manager](https://msdn.microsoft.com/library/azure/dn948464.aspx) para el modelo de implementación Resource Manager y [Service Management API](https://msdn.microsoft.com/library/azure/ee460799.aspx) para el modelo de implementación clásica. Los desarrolladores pueden escribir código para interactuar con estas API *directamente*.  

Los profesionales de TI, sin embargo, suele interactuar con estas API *indirectamente* por medio de un portal gráfico en un explorador web, usando cmdlets de Azure PowerShell en un equipo Windows o usando la interfaz de línea de comandos (CLI) de Azure en un equipo Windows, OS X o Linux. Estos tres métodos indirectos que usa el profesional de TI interactúan directamente con las API. Esto significa que cuando se implementa una nueva funcionalidad en la plataforma o los recursos de Azure, siempre está disponible directamente a través de la API en primer lugar, mientras que los métodos indirectos admiten las características y los recursos nuevos cuando la API pasa a estar disponible.  

En las secciones siguientes se explica cómo se configuran los recursos de Azure con los diferentes modelos de implementación a través de los tres métodos indirectos.

## <a name="portals"></a>Portales
Azure tiene dos portales:

* **[Azure Portal](https://manage.windowsazure.com):** Si lleva tiempo usando Azure, ya utiliza este portal. Sirve para crear y configurar recursos de Azure anteriores que admiten el modelo de implementación Clásico. No se puede usar para crear o configurar recursos que solo admiten el Administrador de recursos. 
* **[Portal de vista previa de Azure](https://azure.microsoft.com/overview/preview-portal/):** Si usa un recurso de Azure más reciente, es probable que ya utilice este portal. Sirve para crear y configurar algunos recursos de Azure. Con el tiempo, podrá crear y configurar todos los recursos de Azure con él. En el caso de algunos recursos que admitan ambos modelos de implementación, puede usarse este portal para crear y configurar un recurso con cualquier modelo de implementación. 

Algunos recursos y características solo se pueden crear y configurar en un portal o en el otro. Algunos recursos o características (todavía) no se pueden crear ni configurar en ninguno de los portales, solo pueden configurarse con PowerShell, la CLI o ambos. La documentación sobre cada recurso de Azure describe detalladamente el método con el que se puede crear. 

## <a name="powershell"></a>PowerShell
Con [PowerShell](powershell-install-configure.md) puede usar una línea de comandos o crear scripts para crear y configurar recursos de Azure desde un equipo Windows.  Cada uno de los recursos de Azure tiene [cmdlets de Resource Manager](https://msdn.microsoft.com/library/azure/mt125356.aspx), [cmdlets de Service Management](https://msdn.microsoft.com/library/azure/dn708504.aspx) o ambos.  Algunos recursos y características solo se pueden crear y configurar con PowerShell o la CLI. Según el recurso, al usar cmdlets de PowerShell del Administrador de recursos puede tener dos opciones para crear y configurar recursos de Azure:

* **Solo cmdlets de PowerShell:** puede crear y configurar cada recurso de Azure individualmente con los cmdlets para cada recurso. Puede hacerlo desde una línea de comandos o mediante la inclusión de varios comandos en un script de PowerShell que puede almacenar y modificar para crear versiones.
* **Cmdlets de PowerShell con una plantilla del Administrador de recursos de Azure:** puede usar PowerShell para crear recursos de Azure con una plantilla del Administrador de recursos de Azure. Las plantillas se pueden guardar y modificar para crear versiones. Lea el artículo [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md) para obtener más información. Existen varias [plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) para soluciones comunes que también se pueden descargar y modificar.

## <a name="cli"></a>CLI
Puede crear y configurar recursos de Azure desde equipos Windows, OS X o Linux mediante la CLI.  Lea el artículo [Instalación de la CLI de Azure](xplat-cli-install.md) para instalar la CLI en el sistema operativo que prefiera. Como en el caso de PowerShell, existen distintos comandos que deben usarse en función de que se estén creando recursos con los modelos de implementación [Resource Manager](xplat-cli-azure-resource-manager.md) o [Clásica (Service Management)](virtual-machines/virtual-machines-linux-classic-manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre el [Resource Manager](azure-resource-manager/resource-group-overview.md).
* Aprenda a [diseñar plantillas](best-practices-resource-manager-design-templates.md).




<!--HONumber=Nov16_HO3-->


