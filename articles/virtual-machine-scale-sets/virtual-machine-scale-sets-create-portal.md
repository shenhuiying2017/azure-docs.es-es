---
title: "Creación de un conjunto de escalado de máquinas virtuales en Azure Portal | Microsoft Docs"
description: "Aprenda a crear rápidamente un conjunto de escalado de máquinas virtuales en Azure Portal"
keywords: "conjuntos de escalado de máquinas virtuales"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a501a852a317ec7d087904c3a675ebefce1bece0
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Creación de un conjunto de escalado de máquinas virtuales en Azure Portal
El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Puede escalar el número de máquinas virtuales del conjunto de escalado manualmente o definir reglas de escalado automático basado en el uso de recursos tales como la CPU, la demanda de memoria o el tráfico de red. En este artículo introductorio, debe crear un conjunto de escalado de máquinas virtuales en Azure Portal. También puede crear un conjunto de escalado con la [CLI de Azure 2.0](virtual-machine-scale-sets-create-cli.md) o [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Inicie sesión en Azure Portal: http://portal.azure.com/.


## <a name="create-virtual-machine-scale-set"></a>Creación de un conjunto de escalado de máquinas virtuales
Puede implementar un conjunto de escalado con una imagen de Windows Server o Linux como RHEL, CentOS, Ubuntu o SLES.

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Busque el *conjunto de escalado* elija **Conjunto de escalado de máquinas virtuales** y seleccione **Crear**.
3. Escriba un nombre para el conjunto de escalado, como *myScaleSet*.
4. Seleccione el tipo de sistema operativo deseado, como *Windows Server 2016 Datacenter*.
5. Escriba el nombre del grupo de recursos deseado, como *myResourceGroup*, y la ubicación, como *Este de EE. UU*.
6. Escriba el nombre de usuario que quiera y seleccione el tipo de autenticación que desee.
    - La **contraseña** debe tener 12 caracteres como mínimo y reunir, al menos, tres de los cuatro requisitos de complejidad siguientes: contener al menos una minúscula, una mayúscula, un número y un carácter especial. Para más información, consulte la sección acerca de los [requisitos de nombre de usuario y contraseña](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
    - Si selecciona una imagen de disco del sistema operativo Linux, puede elegir su **clave pública SSH** en su lugar. Proporcione solo su clave pública, como *~/.ssh/id_rsa.pub*. Puede usar Azure Cloud Shell del portal para [crear y utilizar claves SSH](../virtual-machines/linux/mac-create-ssh-keys.md).

7. Escriba un **nombre de dirección IP pública**, como *myPublicIP*.
8. Escriba una **etiqueta de nombre de dominio** única, como *myuniquedns*. Esta etiqueta del sistema de nombres de dominio es la base del FQDN del equilibrador de carga delante del conjunto de escalado.
9. Para confirmar las opciones del conjunto de escalado, seleccione **Crear**.

    ![Creación de un conjunto de escalado de máquinas virtuales en Azure Portal](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>Conexión a una máquina virtual en el conjunto de escalado
Al crear un conjunto de escalado en el portal se crea un equilibrador de carga. Las reglas de traducción de direcciones de red (NAT) se usan para distribuir el tráfico a las instancias del conjunto de escalado para la conectividad remota como RDP o SSH.

Para ver estas reglas NAT e información de conexión para las instancias de conjunto de escalado:

1. Seleccione el grupo de recursos que creó en el paso anterior, por ejemplo, *myResourceGroup*.
2. En la lista de recursos, seleccione el **equilibrador de carga**, como *myScaleSetLab*.
3. Elija **Reglas NAT de entrada** en el menú de la izquierda de la ventana.

    ![Las reglas NAT de entrada le permiten conectarse a las instancias de conjunto de escalado de máquinas virtuales](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

Puede conectarse a las máquinas virtuales del conjunto de escalado con estas reglas NAT. Cada instancia de máquina virtual muestra una dirección IP de destino y el valor del puerto TCP. Por ejemplo, si la dirección IP de destino es *104.42.1.19* y el puerto TCP es *50001*, se conecta a la instancia de máquina virtual como se indica a continuación:

- Para un conjunto de escalado de Windows, conéctese a la instancia de máquina virtual con RDP en `104.42.1.19:50001`
- Para un conjunto de escalado de Linux, conéctese a la instancia de máquina virtual con SSH en `ssh azureuser@104.42.1.19 -p 50001`

Cuando se le solicite, escriba las credenciales que especificó en el paso anterior al crear el conjunto de escalado. Las instancias de conjunto de escalado son máquinas virtuales normales que pueden interactuar con normalidad. Para más información sobre cómo implementar y ejecutar aplicaciones en las instancias de conjunto de escalado, consulte [Implementación de la aplicación en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no los necesite, elimine el grupo de recursos, el conjunto de escalado y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la máquina virtual y haga clic en **Eliminar**.


## <a name="next-steps"></a>pasos siguientes
En este artículo introductorio, ha creado un conjunto de escalado básico en Azure Portal. Para obtener mayor escalabilidad y automatización, expanda el conjunto de escalado con los siguientes artículos de procedimientos:

- [Implementación de la aplicación en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md)
- Escalado automático con [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), la [CLI de Azure](virtual-machine-scale-sets-autoscale-cli.md), o [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Uso de las actualizaciones automáticas del sistema operativo para las instancias de VM del conjunto de escalado](virtual-machine-scale-sets-automatic-upgrade.md)
