---
title: 'Tutorial: crear una instancia pública de Load Balancer Básico: Azure Portal | Microsoft Docs'
description: En este tutorial se muestra cómo crear una instancia interna de Load Balancer Básico mediante Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a16e9ad5b72d87614f5d3630e24e6aa36def8c51
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>Tutorial: equilibrar la carga de tráfico interno de las máquinas virtuales con Load Balancer Básico mediante Azure Portal

El equilibrio de carga proporciona un mayor nivel de disponibilidad y escala, ya que distribuye las solicitudes entrantes entre varias máquinas virtuales. Puede usar Azure Portal para equilibrar la carga del tráfico interno de las máquinas virtuales con Load Balancer Básico. En este tutorial se muestra cómo crear recursos de red, servidores de back-end y una instancia interna de Load Balancer Básico.

Si lo prefiere, puede completar este tutorial con la [CLI de Azure](load-balancer-get-started-ilb-arm-cli.md) o [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Crear una red virtual
1. En la parte superior izquierda de la pantalla, haga clic en **Nuevo** > **Redes** > **Red virtual** y especifique estos valores para la red virtual:
    - *myVNet*: como nombre de la red virtual.
    - *myResourceGroupILB*: como nombre del grupo de recursos existente.
    - *myAGSubnet*: como nombre de la subred.
2. Haga clic en **Crear** para crear una red virtual.

## <a name="create-a-basic-load-balancer"></a>Creación de una instancia pública de Load Balancer Básico
Creación de Load Balancer Básico con el portal.

1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso** > **Redes** > **Equilibrador de carga**.
2. En la página **Crear equilibrador de carga** especifique estos valores para el equilibrador de carga:
    - *myLoadBalancer*: como nombre del equilibrador de carga.
    - **Interno**: como tipo de equilibrador de carga.
    - **Básica**: para la versión de SKU.
    - **10.1.0.7**: dirección IP privada estática.
    - *myVNet*: para la red virtual que haya escogido de la lista de redes existentes.
    - *mySubnet*: para la subred que haya escogido de la lista de subredes existentes.
    - *myResourceGroupILB*: como nombre del nuevo grupo de recursos que creó.
3. Haga clic en **Crear** para crear el equilibrador de carga.
   
    ![Crear un equilibrador de carga](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)


## <a name="create-backend-servers"></a>Creación de servidores back-end

En esta sección, se crean dos máquinas virtuales para el grupo de back-end de la instancia de Load Balancer Básico, y se instala IIS en las máquinas virtuales para ayudar a probar el equilibrador de carga.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso** > **Proceso** > **Windows Server 2016 Datacenter** y especifique estos valores para la máquina virtual:
    - *myVM1*: para el nombre de la máquina virtual.        
    - *azureuser*: como nombre del usuario administrador.   
    - *myResourceGroupILB*: en **Grupo de recursos**, seleccione **Usar existente** y *myResourceGroupILB*.
2. Haga clic en **OK**.
3. Seleccione **DS1_V2** como tamaño de la máquina virtual y haga clic en **Seleccionar**.
4. Especifique estos valores para la configuración de la máquina virtual:
    - *myAvailabilitySet*: como nombre del nuevo conjunto de disponibilidad que crea.
    -  *myVNet*: asegúrese de que se selecciona como red virtual.
    - *myBackendSubnet*: -asegúrese de que se selecciona como subred.
    - *myNetworkSecurityGroup*: como nombre del nuevo grupo de seguridad de red (firewall) que debe crear.
5. Haga clic en **Deshabilitado** para deshabilitar los diagnósticos de arranque.
6. Haga clic en **Aceptar**, revise la configuración en la página de resumen y haga clic en **Crear**.
7. Siga los pasos 1 a 6 para crear una segunda máquina virtual llamada *VM2* y elija *myAvailabilityset* como conjunto de disponibilidad, *myVnet* como red virtual,  *myBackendSubnet* como subred y *myNetworkSecurityGroup* como su grupo de seguridad de red. 

### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar IIS y personalizar la página web predeterminada

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, en la lista de recursos, haga clic en **myVM1**, que se encuentra en el grupo de recursos *myResourceGroupILB*.
2. En la página **Información general**, haga clic en **Conectar** a RDP en la máquina virtual.
3. Inicie sesión en la máquina virtual.
4. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows**>**Administrador del servidor** .
5. Inicie Windows PowerShell en VM1 y use los siguientes comandos para instalar el servidor IIS y actualizar el archivo htm predeterminado.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. Cierre la conexión de RDP con *myVM1*.
6. Repita los pasos del 1 al 5 con *myVM2* para instalar IIS y personalizar la página web predeterminada.

## <a name="create-nsg-rules"></a>Creación de reglas de NSG

En esta sección, se crean reglas de NSG para permitir conexiones entrantes que usen HTTP y RDP.

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, en la lista de recursos haga clic en **myNetworkSecurityGroup**, que se encuentra en el grupo de recursos **myResourceGroupLB**.
2. En **Configuración**, haga clic en **Reglas de seguridad de entrada** y, después, en **Agregar**.
3. Especifique estos valores para la regla de seguridad de entrada llamada *myHTTPRule* para permitir que las conexiones HTTP entrantes usen el puerto 80:
    - *Etiqueta de servicio*: en **Origen**.
    - *Internet*: en **Etiqueta de servicio de origen**
    - *80*: en **Intervalos de puerto de destino**
    - *TCP*: en **Protocolo**
    - *Permitir*: en **Acción**
    - *100* en **Prioridad**
    - *myHTTPRule* como nombre
    - *Permitir HTTP*: como descripción
4. Haga clic en **OK**.
 
5. Repita los pasos 2 a 4 para crear otra regla llamada *myRDPRule* que permita una conexión RDP entrante con el puerto 3389 con los valores siguientes:
    - *Etiqueta de servicio*: en **Origen**.
    - *Internet*: en **Etiqueta de servicio de origen**
    - *3389*: en **Intervalos de puerto de destino**
    - *TCP*: en **Protocolo**
    - *Permitir*: en **Acción**
    - *200* en **Prioridad**
    - *myRDPRule* como nombre
    - *Permitir RDP*: como descripción

## <a name="create-basic-load-balancer-resources"></a>Creación de recursos de Load Balancer Básico

En esta sección se configuran los valores del equilibrador de carga para un grupo de direcciones de back-end y un sondeo de mantenimiento, y especifique el equilibrador de carga y las reglas NAT.


### <a name="create-a-backend-address-pool"></a>Crear un grupo de direcciones de back-end

Para distribuir el tráfico a las máquinas virtuales, un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red (NIC) virtual conectadas al equilibrador de carga. Cree el grupo de direcciones de back-end *myBackendPool* para incluir *VM1* y *VM2*.

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, haga clic en **myLoadBalancer** en la lista de recursos.
2. Haga clic en **Configuración**, **Grupos de back-end** y luego en **Agregar**.
3. En la página **Agregar grupo back-end**, realice lo siguiente:
    - En el espacio para el nombre, escriba * myBackEndPool como nombre del grupo back-end.
    - En **Associated to** (Asociado a), en el menú desplegable, haga clic en **Conjunto de disponibilidad**.
    - En **Conjunto de disponibilidad**, haga clic en **myAvailabilitySet**.
    - Haga clic en **Agregar una configuración IP de red de destino** para agregar cada máquina virtual (*myVM1* & *myVM2*) que ha creado al grupo back-end.
    - Haga clic en **OK**.

        ![Incorporación al grupo de direcciones de back-end ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. Asegúrese de que la configuración de grupo back-end del equilibrador de carga muestra las dos máquinas virtuales, **VM1** y **VM2**.

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado

Para permitir que la instancia de Load Balancer Básico supervise el estado de la aplicación se usa un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. Crear un sondeo de estado, *myHealthProbe*, para supervisar el estado de las máquinas virtuales.

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, haga clic en **myLoadBalancer** en la lista de recursos.
2. En **Configuración**, haga clic en **Sondeos de estado** y luego en **Agregar**.
3. Use estos valores para crear el sondeo de estado:
    - *myHealthProbe*: como nombre del sondeo de estado.
    - **HTTP**: en tipo de protocolo.
    - *80*: en número de puerto.
    - *15*: como número de **Intervalo**, en segundos, entre los intentos de sondeo.
    - *2*: como número de **Umbral incorrecto** o errores de sondeo consecutivos que deben producirse para que una máquina virtual se considere que no funciona de manera incorrecta.
4. Haga clic en **OK**.

   ![Incorporación de un sondeo](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de Load Balancer

Las reglas de Load Balancer se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Se define la configuración de IP front-end para el tráfico entrante y el grupo IP de back-end para recibir el tráfico, junto con el puerto de origen y destino requeridos. Cree una regla de Load Balancer *myLoadBalancerRuleWeb* para escuchar el puerto 80 en el front-end *LoadBalancerFrontEnd* y enviar tráfico de red con equilibrio de carga al conjunto de direcciones back-end, *myBackEndPool*, que también usan el puerto 80. 

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, haga clic en **myLoadBalancer** en la lista de recursos.
2. En **Configuración**, haga clic en **Reglas de equilibrio de carga** y luego en **Agregar**.
3. Use estos valores para configurar la regla de equilibrio de carga:
    - *myHTTPRule*: como nombre de la regla de equilibrio de carga.
    - **TCP**: en tipo de protocolo.
    - *80*: en número de puerto.
    - *80*: como puerto de back-end.
    - *myBackendPool*: como nombre del grupo back-end.
    - *myHealthProbe*: como nombre del sondeo de estado.
4. Haga clic en **OK**.
    
    ![Incorporación de una regla de equilibrio de carga](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>Crear una máquina virtual para probar el equilibrador de carga
Para probar el equilibrador de carga interno, debe crear una máquina virtual que se encuentre en la misma red virtual que las máquinas virtuales del servidor de back-end.
1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso** > **Proceso** > **Windows Server 2016 Datacenter** y especifique estos valores para la máquina virtual:
    - *myVMTest*: como nombre de la máquina virtual.        
    - *myResourceGroupILB*: en **Grupo de recursos**, seleccione **Usar existente** y *myResourceGroupILB*.
2. Haga clic en **OK**.
3. Seleccione **DS1_V2** como tamaño de la máquina virtual y haga clic en **Seleccionar**.
4. Especifique estos valores para la configuración de la máquina virtual:
    -  *myVNet*: asegúrese de que se selecciona como red virtual.
    - *myBackendSubnet*: -asegúrese de que se selecciona como subred.
5. Haga clic en **Deshabilitado** para deshabilitar los diagnósticos de arranque.
6. Haga clic en **Aceptar**, revise la configuración en la página de resumen y haga clic en **Crear**.

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga
1. En Azure Portal, busque la dirección IP privada de Load Balancer en la pantalla **Información general**. Para ello: a. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, haga clic en **myLoadBalancer** en la lista de recursos.
    b. En la página de detalles **Introducción**, copie la dirección IP privada (en este ejemplo, es 10.1.0.7).

2. Cree una conexión remota a *myVMTest* tal como se indica a continuación: a. Haga clic en **Todos los recursos** en el menú de la izquierda y, en la lista de recursos, haga clic en **myVMTest**, que se encuentra en el grupo de recursos *myResourceGroupILB*.
2. En la página **Introducción**, haga clic en **Conectar** para iniciar una sesión remota con la máquina virtual.
3. Inicie sesión en *myVMTest*.
3. Pegue la dirección IP privada en la barra de direcciones del explorador en *myVMTest*. La página predeterminada del servidor web de IIS se muestra en el explorador.

      ![Servidor web de IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

Para ver cómo el equilibrador de carga distribuye el tráfico entre ambas máquinas virtuales que ejecutan la aplicación, puede realizar una actualización forzada del explorador web.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. Para ello, seleccione el grupo de recursos que contiene el equilibrador de carga y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial creó un grupo de recursos, recursos de red y servidores back-end. A continuación, use esos recursos para crear un equilibrador de carga interno para equilibrar la carga del tráfico interno a las máquinas virtuales. A continuación, aprenda a [equilibrar la carga de las máquinas virtuales entre las zonas disponibles](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
