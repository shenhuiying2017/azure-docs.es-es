---
title: "Solución de problemas de implementación de la máquina virtual Linux en Azure | Microsoft Docs"
description: "Solución de problemas de implementación de la máquina virtual Linux en el modelo de implementación de Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 3d0e9a43d9a3c9c770c4ab6159279db1d93f5134
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Solución de problemas de implementación de la máquina virtual Linux en Azure

Para solucionar problemas de implementación de la máquina virtual (VM) de Azure, revise la sección [Principales problemas](#top-issues) para conocer errores comunes y sus soluciones.

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.

## <a name="top-issues"></a>Principales problemas
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>El clúster no admite el tamaño de máquina virtual solicitado
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Vuelva a intentar la solicitud con un tamaño de máquina virtual menor.
- Si no se puede cambiar el tamaño de la máquina virtual solicitada:
    - Detenga todas las máquinas virtuales en el conjunto de disponibilidad. Haga clic en **Grupos de recursos** > su grupo de recursos > **Recursos** > su conjunto de disponibilidad > **Máquinas virtuales** > su máquina virtual > **Detener**.
    - Después de detener todas las máquinas virtuales, cree la máquina virtual con el tamaño deseado.
    - Inicie la nueva máquina virtual en primer lugar y luego seleccione cada una de las máquinas virtuales detenidas y haga clic en Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>El clúster no tiene recursos disponibles
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Vuelva a intentar realizar la solicitud más tarde.
- Si la nueva máquina virtual puede formar parte de un conjunto de disponibilidad diferente
    - Cree una máquina virtual en un conjunto de disponibilidad diferente (en la misma región).
    - Agregue la nueva máquina virtual a la misma red virtual.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Cómo se activa mi crédito mensual para Visual Studio Enterprise (BizSpark)

Para activar el crédito mensual, consulte este [artículo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>¿Por qué no puedo instalar el controlador de GPU para una máquina virtual NV de Ubuntu?

Actualmente, la compatibilidad con la GPU de Linux solo está disponible en las máquinas virtuales de Azure NC que ejecutan Ubuntu Server 16.04 LTS. Para más información consulte [Configuración de controladores de GPU para máquinas virtuales de la serie N con Linux](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Faltan los controladores para mi máquina virtual de la serie N de Linux

Los controladores para las máquinas virtuales basadas en Linux se encuentran [aquí](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>No puedo encontrar una instancia de la GPU en mi máquina virtual de la serie N

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan Windows Server 2016 o Windows Server 2012 R2, debe instalar los controladores de gráficos de NVIDIA en cada máquina virtual después de la implementación. La información de instalación del controlador está disponible para las [máquinas virtuales Windows](../windows/n-series-driver-setup.md) y las [máquinas virtuales Linux](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>¿Hay máquinas virtuales de la serie N en mi región?

Puede comprobar la disponibilidad en la [tabla de productos disponibles por región](https://azure.microsoft.com/regions/services) y el precio [aquí](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Al cambiar el tamaño de la máquina virtual, no puedo ver la familia de tamaño de máquina virtual que deseo.

Cuando una máquina virtual se está ejecutando, se implementa en un servidor físico. Los servidores físicos en regiones de Azure se agrupan en clústeres de hardware físico común. El modo de cambiar el tamaño de una máquina virtual que requiere que esta se mueva a clústeres de hardware diferentes difiere en función del modelo de implementación que se usara para implementarla.

- En el caso de las máquinas virtuales implementadas en el modelo de implementación clásico, la implementación del servicio de nube debe quitarse y volver a implementarse para cambiar su tamaño por uno de otra familia de tamaño.

- En el caso de las máquinas virtuales implementadas en el modelo de implementación Resource Manager, debe detener todas las máquinas virtuales dentro del conjunto de disponibilidad antes de cambiar el tamaño de cualquiera de estas.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>El tamaño de máquina virtual enumerado no se admite mientras se implementa en el conjunto de disponibilidad.

Elija un tamaño que se admita en el clúster del conjunto de disponibilidad. Al crear un conjunto de disponibilidad, se recomienda elegir el mayor tamaño de máquina virtual que piense que va a necesitar y tiene que ser la primera implementación para el conjunto de disponibilidad.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>¿Qué distribuciones o versiones de Linux son compatibles con Azure?

Puede encontrar la lista de Linux en [Linux en distribuciones aprobadas por Azure](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>¿Puedo agregar una máquina virtual clásica a un conjunto de disponibilidad?

Sí. Puede agregar una máquina virtual clásica existente a un conjunto de disponibilidad nuevo o existente. Para más información, consulte [Incorporación de una máquina virtual existente a un conjunto de disponibilidad](../windows/classic/configure-availability-classic.md#addmachine).


## <a name="next-steps"></a>pasos siguientes
Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/).

Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.
