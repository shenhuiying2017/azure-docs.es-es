---
title: "Redes aceleradas para una máquina virtual - Portal | Microsoft Docs"
description: "Aprenda a configurar Redes aceleradas para una máquina virtual de Azure con Azure Portal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: a2e8e0dc40a63c363f295149e35f9823c8e90fa8
ms.lasthandoff: 02/28/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>Redes aceleradas para una máquina virtual mediante Azure Portal
> [!div class="op_single_selector"]
> * [Portal de Azure](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

Accelerated Networking habilita Virtualización de E/S de raíz única (SR-IOV) en una máquina virtual (VM), lo que mejora significativamente el rendimiento de la red. Esta ruta de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para su uso con las cargas de trabajo de red más exigentes en tipos de máquina virtual compatibles. En este artículo se explica cómo usar Azure Portal para configurar Redes aceleradas en el modelo de implementación de Azure Resource Manager. También puede crear una máquina virtual con Redes aceleradas mediante Azure PowerShell. Para aprender a hacerlo, haga clic en la casilla PowerShell en la parte superior de este artículo.

En la siguiente imagen, se muestra la comunicación entre dos máquinas virtuales (VM) con y sin Accelerated Networking:

![De comparación](./media/virtual-network-accelerated-networking-portal/image1.png)

Sin Accelerated Networking, todo el tráfico de red dentro y fuera de la máquina virtual debe atravesar el host y el conmutador virtual. El conmutador virtual se encarga de toda la aplicación de directivas, como grupos de seguridad de red, listas de control de acceso, aislamiento y otros servicios virtualizados de red, al tráfico de red. Para más información, lea el artículo [Hyper-V Network Virtualization and Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) (Virtualización de red de Hyper-V y conmutador virtual).

Con Accelerated Networking, el tráfico de red llega a la tarjeta de red (NIC) y se reenvía después a la máquina virtual. Todas las directivas de red que el conmutador virtual aplica sin Accelerated Networking se descargan y aplican en el hardware. La aplicación de directivas en hardware permite que la NIC reenvíe el tráfico de red directamente a la máquina virtual, pasando por alto el host y el conmutador virtual, al mismo tiempo que se mantienen todas las directivas aplicadas en el host.

Las ventajas de Accelerated Networking solo se aplican a la máquina virtual donde esté habilitada. Para obtener resultados óptimos, lo ideal es habilitar esta característica en al menos dos máquinas virtuales conectadas a la misma red virtual. Al comunicarse entre redes virtuales o conectarse de forma local, esta característica tiene un efecto mínimo sobre la latencia total.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>Ventajas
* **Menor latencia/Más paquetes por segundo (pps):** al quitarse el conmutador virtual de la ruta de acceso de datos, se elimina el tiempo que los paquetes pasan en el host para el procesamiento de las directivas y se aumenta el número de paquetes que se pueden procesar dentro de la máquina virtual.
* **Inestabilidad reducida:** el procesamiento del conmutador virtual depende de la cantidad de directivas que deben aplicarse y la carga de trabajo de la CPU que se encarga del procesamiento. Al descargarse la aplicación de directivas en el hardware, se elimina esa variabilidad, ya que los paquetes se entregan directamente a la máquina virtual y se elimina el host de la comunicación de la máquina virtual, así como todas las interrupciones de software y los cambios de contexto.
* **Disminución de la utilización de la CPU:** el pasar por alto el conmutador virtual en el host conlleva una disminución de la utilización de la CPU para procesar el tráfico de red.

## <a name="limitations"></a>Limitaciones
Cuando se utiliza esta funcionalidad, existen las siguientes limitaciones:

* **Creación de interfaz de red:** Accelerated Networking solo se puede habilitar para una nueva interfaz de red.  No es posible para una interfaz de red existente.
* **Creación de máquina virtual:** una interfaz de red con Accelerated Networking habilitada solo se puede asociar a una máquina virtual cuando esta se crea. La interfaz de red no puede asociarse a una máquina virtual existente.
* **Regiones:** solo se ofrece en las regiones Centro occidental de EE. UU. y Europa Occidental de Azure. El conjunto de regiones se expandirá en el futuro.
* **Sistema operativo compatible:** Microsoft Windows Server 2012 R2 y Windows Server 2016 Technical Preview 5. Próximamente se agregará compatibilidad con Linux y Windows Server 2012.
* **Tamaño de máquina virtual:** Standard_D15_v2 y Standard_DS15_v2 son los únicos tamaños de instancia de máquina virtual admitidos. Para más información, consulte el artículo sobre los [tamaños de máquina virtual Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . El conjunto de tamaños de instancia de máquina virtual compatibles se ampliará en el futuro.

Los cambios en estas limitaciones se anunciarán a través de la página con [actualizaciones para redes virtuales de Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Creación de una máquina virtual Windows con Accelerated Networking
1. Para registrarse para la versión preliminar, envíe un correo electrónico a [Accelerated Networking Subscriptions](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con el identificador de suscripción y el uso previsto. No complete los pasos restantes hasta después de recibir una notificación por correo electrónico de que se le acepta en la versión preliminar.
2. Inicie sesión en Azure Portal en http://portal.azure.com.
3. Cree una máquina virtual según los pasos descritos en el artículo [Creación de una máquina virtual Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) seleccionando las opciones siguientes:
   
   * Seleccione un sistema operativo en la sección Limitaciones de este artículo.
   * Seleccione una ubicación (región) en la sección Limitaciones de este artículo.
   * Seleccione un tamaño de máquina virtual en la sección Limitaciones de este artículo. Si uno de los tamaños admitidos no aparece, haga clic en **Ver todo** en la hoja **Elegir un tamaño** para seleccionarlo en una lista ampliada.
   * En la hoja **Configuración**, haga clic en *Habilitado* para **Redes aceleradas**, como se muestra en la siguiente imagen:
     
       ![Configuración](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > La opción Redes aceleradas solo estará visible si:
     > 
     > * Se le ha aceptado en la versión preliminar.
     > * Ha seleccionado el sistema operativo, la ubicación y los tamaños de máquina virtual admitidos que se mencionan en la sección Limitaciones de este artículo.
     > 
     > 
4. Una vez creada la máquina virtual, descargue el [controlador de Redes aceleradas](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conéctese a la máquina virtual, inicie sesión en ella y ejecute el instalador del controlador dentro de la máquina virtual.
5. Haga clic con el botón derecho en el botón Windows y haga clic en **Administrador de dispositivos**. Compruebe que el **adaptador Ethernet de función virtual ConnectX-3 de Mellanox** aparezca bajo la opción **Red** expandida, como se muestra en la siguiente imagen:
   
    ![Administrador de dispositivos](./media/virtual-network-accelerated-networking-portal/image2.png)


