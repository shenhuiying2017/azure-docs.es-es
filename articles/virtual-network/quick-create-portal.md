---
title: 'Guía de inicio rápido: Creación de una red virtual mediante Azure Portal | Microsoft Docs'
description: En esta guía de inicio rápido, aprenderá a crear una red virtual mediante Azure Portal. Una red virtual permite que los recursos de Azure, como las máquinas virtuales, se comuniquen entre sí de forma privada y con Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7107dc72686004141d8bea0083089cba065a9f4c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841408"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Guía de inicio rápido: Creación de una red virtual mediante Azure Portal

Una red virtual permite que los recursos de Azure, como máquinas virtuales (VM), se comuniquen entre sí de forma privada y con Internet. En esta guía de inicio rápido aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en la red virtual. Luego, se conectará a una máquina virtual desde Internet y se comunicará de forma privada entre las dos máquinas virtuales.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crear una red virtual

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. Escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de la configuración y luego seleccione **Crear**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVirtualNetwork|
    |La suscripción| Seleccione su suscripción.|
    |Grupos de recursos| Haga clic en **Crear nuevo** y escriba *myResourceGroup*.|
    |Ubicación| Seleccione **Este de EE. UU**.|

    ![Especificar información básica acerca de la red virtual](./media/quick-create-portal/create-virtual-network.png)

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual:

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**.
3. Escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de la configuración y luego seleccione **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVm1|
    |Nombre de usuario| Escriba un nombre de usuario de su elección.|
    |Password| Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |La suscripción| Seleccione su suscripción.|
    |Grupos de recursos| Seleccione **Usar existente** y, a continuación, **myResourceGroup**.|
    |Ubicación| Seleccione **Este de EE. UU**.|

    ![Información básica de la máquina virtual](./media/quick-create-portal/virtual-machine-basics.png)

4. Seleccione un tamaño para la máquina virtual y luego **Seleccionar**.
5. En **Configuración**, acepte todos los valores predeterminados y luego seleccione **Aceptar**.

    ![Configuración de máquina virtual](./media/quick-create-portal/virtual-machine-settings.png)

6. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual. La maquina virtual tarda unos minutos en implementarse. 

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual

Complete de nuevo los pasos 1 a 6, pero recuerde que en el paso 3 debe nombrar la máquina virtual *myVm2*.

## <a name="connect-to-a-vm-from-the-internet"></a>Conexión a una máquina virtual desde Internet

1. Una vez creada *myVm1*, conéctela. En la parte superior de Azure Portal, escriba *myVm1*. Seleccione **MyVm1** cuando aparezca en los resultados de búsqueda. Seleccione el botón **Conectar**.

    ![Conexión a una máquina virtual](./media/quick-create-portal/connect-to-virtual-machine.png)

2. Cuando seleccione el botón **Conectar**, se creará un archivo de Protocolo de Escritorio remoto (.rdp) y se descargará en el equipo.  
3. Abra el archivo .rdp descargado. Cuando se le pida, seleccione **Conectar**. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual. Puede que deba seleccionar **More choices** (Más opciones) y, luego, **Use a different account** (Usar una cuenta diferente), para especificar las credenciales que escribió al crear la máquina virtual. 
4. Seleccione **Aceptar**.
5. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Si recibe la advertencia, seleccione **Sí** o **Continuar** para continuar con la conexión.

## <a name="communicate-between-vms"></a>Comunicarse entre máquinas virtuales

1. En PowerShell, escriba `ping myvm2`. Se produce un error de ping porque se usa el Protocolo de mensajes de control de Internet (ICMP) y, de forma predeterminada, este protocolo no puede atravesar el Firewall de Windows.
2. Para permitir que *myVm2* haga ping a *myVm1* en un paso posterior, escriba el siguiente comando desde PowerShell, que permite una conexión entrante de ICMP a través del Firewall de Windows:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

3. Cierre la conexión de Escritorio remoto a *myVm1*. 

4. Vuelva a realizar los pasos de [Conexión a una máquina virtual desde Internet](#connect-to-a-vm-from-the-internet), pero conéctese a *myVm2*. Desde un símbolo del sistema, escriba `ping myvm1`.

    Recibe respuestas de *myVm1*, porque permitió ICMP a través del Firewall de Windows en la máquina virtual *myVm1* en un paso anterior.

5. Cierre la conexión de Escritorio remoto a *myVm2*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una red virtual predeterminada y dos máquinas virtuales. Se ha conectado a una máquina virtual desde Internet y se ha comunicado de forma privada entre las dos máquinas virtuales. Para más información sobre la configuración de red virtual, consulte [Administración de una red virtual](manage-virtual-network.md).

De forma predeterminada, Azure permite la comunicación privada sin restricciones entre máquinas virtuales, pero solo permite conexiones de Escritorio remoto entrantes a máquinas virtuales Windows desde Internet. Para aprender a permitir o restringir los distintos tipos de comunicación de red tanto hacia las máquinas virtuales como desde estas, diríjase al tutorial [Filtro del tráfico de red](tutorial-filter-network-traffic.md).