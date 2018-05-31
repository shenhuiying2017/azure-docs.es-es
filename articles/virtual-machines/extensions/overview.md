---
title: Características y extensiones de las máquinas virtuales de Azure | Microsoft Docs
description: Obtenga información sobre las extensiones de máquina virtual de Azure y sobre su uso con máquinas virtuales de Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 01178995dbf9203082a6250ef256522bc1101e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944707"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Características y extensiones de las máquinas virtuales de Azure
Las extensiones de máquina virtual (VM) de Azure son pequeñas aplicaciones que proporcionan tareas de configuración y automatización tras la implementación en máquinas virtuales de Azure. Puede usar imágenes existentes y, después, personalizarlas como parte de las implementaciones, lo que le ahorra el trabajo de compilar imágenes personalizadas.

La plataforma de Azure hospeda numerosas extensiones, que abarcan aplicaciones de configuración, supervisión, seguridad y utilidad de máquinas virtuales. Los publicadores toman una aplicación, la encapsulan en una extensión y simplifican la instalación, de modo que lo único que debe hacer es proporcionar los parámetros obligatorios. 

 Existe una amplia variedad de extensiones propias y de terceros. Si la aplicación del repositorio de extensiones no existe, puede usar la extensión de script personalizado y configurar la máquina virtual con sus propios comandos y scripts.

Ejemplos de escenarios clave para los que se usan extensiones:
* Configuración de máquinas virtuales. Puede usar extensiones de DSC (Desired State Configuration) de Powershell, Chef, Puppet y script personalizado para instalar agentes de configuración de máquina virtual y configurar la máquina virtual. 
* Productos de antivirus, como Symantec y ESET.
* Herramientas de vulnerabilidad de máquina virtual, como Qualys, Rapid7 y HPE.
* Herramientas de supervisión de máquinas virtuales y aplicaciones, como DynaTrace, Azure Network Watcher, Site24x7 y Stackify.

Las extensiones se pueden incluir con una nueva implementación de máquina virtual. Por ejemplo, puede formar parte de una implementación más amplia que configure aplicaciones en el aprovisionamiento de máquinas virtuales o se ejecute en cualquier sistema operado por extensiones admitido tras la implementación.

## <a name="how-can-i-find-what-extensions-are-available"></a>¿Cómo puedo encontrar qué extensiones están disponibles?
Puede ver las extensiones disponibles en la hoja Máquina virtual del portal, en Extensiones. Aquí solo se presentan algunas de ellas; para ver la lista completa, use las herramientas de la CLI. Vea [Discovering VM Extensions for Linux](features-linux.md) (Detección de extensiones de máquina virtual para Linux) y [Discovering VM Extensions for Windows](features-windows.md) (Detección de extensiones de máquina virtual para Windows).

## <a name="how-can-i-install-an-extension"></a>¿Cómo puedo instalar una extensión?
Las extensiones de máquina virtual de Azure se pueden administrar con la CLI 2.0 de Azure, Azure PowerShell, plantillas de Azure Resource Manager y Azure Portal. Para probar una extensión, vaya a Azure Portal, seleccione la extensión de script personalizado, pase un comando o script y ejecute las extensiones.

Si quiere la misma extensión que ha agregado en el portal mediante la CLI o la plantilla de Resource Manager, debe consultar documentación diferente sobre extensiones, como [Windows Custom Script Extension](custom-script-windows.md) (Extensión de script personalizado de Windows) y [Linux Custom Script Extension](custom-script-linux.md) (Extensión de script personalizado de Linux).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>¿Cómo administro el ciclo de vida de las aplicaciones de extensión?
No es necesario conectarse directamente a una máquina virtual para instalar o eliminar la extensión. Como el ciclo de vida de las aplicaciones de extensión de Azure se administra fuera de la máquina virtual y se integra en la plataforma de Azure, también obtiene el estado integrado de la extensión.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>¿Debo tener en cuenta algo más sobre las extensiones?
Las extensiones instalan las aplicaciones. Como en todas las aplicaciones, existen algunos requisitos. En el caso de las extensiones, hay una lista de sistemas operativos de Windows y Linux admitidos. Además, debe tener instalados los agentes de máquina virtual de Azure. Algunas aplicaciones de extensión de máquina virtual individuales pueden tener sus propios requisitos previos de entorno, como el acceso a un punto de conexión.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo funcionan las extensiones y el agente de Linux, vea [Azure VM extensions and features for Linux](features-linux.md) (Características y extensiones de máquina virtual de Azure para Linux).
* Para más información sobre cómo funcionan las extensiones de Windows y Windows Guest Agent, vea [Azure VM extensions and features for Windows](features-windows.md) (Características y extensiones de máquina virtual de Azure para Windows).  
* Para instalar Windows Guest Agent, vea [Azure Windows Virtual Machine Agent Overview ](agent-windows.md) (Introducción al agente de máquina virtual de Windows de Azure).  
* Para instalar el agente de Linux, vea [Azure Linux Virtual Machine Agent Overview ](agent-linux.md) (Introducción al agente de máquina virtual de Linux de Azure).  

