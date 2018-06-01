---
title: 'Tutorial: VM de Load Balancer en una zona: Azure Portal | Microsoft Docs'
description: Este tutorial demuestra cómo crear una instancia de Load Balancer estándar con un front-end de zona para equilibrar la carga de las VM en una zona de disponibilidad mediante Azure Portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 52d0aeabab173caf4460827ca0d5984070688f0e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304732"
---
# <a name="tutorialload-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Tutorial: Equilibrio de carga de VM en una zona de disponibilidad con Load Balancer estándar mediante Azure Portal

Este tutorial le ayudará a crear una instancia pública de [Load Balancer estándar](https://aka.ms/azureloadbalancerstandard) con un front-end de zona mediante una dirección IP pública estándar y Azure Portal. En este escenario, puede especificar una zona determinada para sus instancias de servidor front-end y back-end para alinear la ruta de acceso a los datos y los recursos con una zona específica. Aprenderá a:

> [!div class="checklist"]
> * Crear un Load Balancer estándar de Azure con un front-end de zona
> * Crear grupos de seguridad de red para definir las reglas de tráfico de entrada
> * Crear VM de zona y conectarlas a un equilibrador de carga
> * Crear un sondeo de estado de un equilibrador de carga
> * Crear reglas de tráfico del equilibrador de carga
> * Crear un sitio de IIS básico
> * Ver un equilibrador de carga en acción

Para más información sobre cómo usar las zonas de disponibilidad con Load Balancer Estándar, consulte [Load Balancer Estándar y zonas de disponibilidad](load-balancer-standard-availability-zones.md).

Si lo prefiere, puede realizar los pasos de este artículo con la [CLI de Azure](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Creación de una instancia de Load Balancer Estándar pública

La versión Estándar de Load Balancer solo admite direcciones IP públicas estándar. Cuando se crea una dirección IP pública nueva al crear el equilibrador de carga, se configura automáticamente como una versión de la SKU Estándar y también tiene automáticamente redundancia de zona.

1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso** > **Redes** > **Equilibrador de carga**.
2. En la página **Crear equilibrador de carga** especifique estos valores para el equilibrador de carga:
    - *myLoadBalancer*: como nombre del equilibrador de carga.
    - **Pública**: como tipo de equilibrador de carga.
     - *myPublicIPZonal*: para la dirección IP pública nueva que crea. Para ello, haga clic en **Elegir una dirección IP pública** y, a continuación, en **Crear nueva**. En nombre, escriba *myPublicIP*, la SKU es Estándar de forma predeterminada y seleccione **Zona 1** en **Zona de disponibilidad**.
    - *myResourceGroupZLB*: como nombre del nuevo grupo de recursos que crea.
    - **Oeste de Europa**: como ubicación.
3. Haga clic en **Crear** para crear el equilibrador de carga.
   
    ![creación de una instancia de Load Balancer Estándar de zona con Azure Portal](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Creación de servidores back-end

En esta sección, se crea una red virtual y dos máquinas virtuales en la misma zona (por ejemplo, Zona 1) de la región para agregar al grupo de back-end del equilibrador de carga y, finalmente, se instala IIS en las máquinas virtuales para ayudar a probar el equilibrador de carga con redundancia de zona. Por lo tanto, si se produce un error en una máquina virtual, el sondeo de estado de máquinas virtuales de la misma zona produce un error y el tráfico lo siguen sirviendo las demás máquinas virtuales de la zona.

### <a name="create-a-virtual-network"></a>Crear una red virtual
1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso** > **Redes** > **Red virtual** y especifique estos valores para la red virtual:
    - *myVNet*: como nombre de la red virtual.
    - *myResourceGroupZLB*: como nombre del grupo de recursos existente.
    - *myAGSubnet*: como nombre de la subred.
2. Haga clic en **Crear** para crear una red virtual.

    ![Crear una red virtual](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso**, en el cuadro de búsqueda, escriba *Grupo de seguridad de red* y, en la página del grupo de seguridad de red, haga clic en **Crear**.
2. En la página Crear grupo de seguridad de red, escriba estos valores:
    - *myNetworkSecurityGroup*: como nombre del grupo de seguridad de red.
    - *myResourceGroupLBAZ*: como nombre del grupo de recursos existente.
   
    ![Crear una red virtual](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Creación de reglas de NSG

En esta sección, se crean reglas de NSG para permitir conexiones entrantes que usen HTTP y RDP mediante Azure Portal.

1. En Azure Portal, haga clic en **Todos los recursos** en el menú de la izquierda, y busque y haga clic en **myNetworkSecurityGroup**, que se encuentra en el grupo de recursos **myResourceGroupZLB**.
2. En **Configuración**, haga clic en **Reglas de seguridad de entrada** y, después, en **Agregar**.
3. Especifique estos valores para la regla de seguridad de entrada llamada *myHTTPRule* para permitir que las conexiones HTTP entrantes usen el puerto 80:
    - *Etiqueta de servicio*: en **Origen**.
    - *Internet*: en **Etiqueta de servicio de origen**
    - *80*: en **Intervalos de puerto de destino**
    - *TCP*: en **Protocolo**
    - *Permitir*: en **Acción**
    - *100* en **Prioridad**
    - *myHTTPRule* en **Nombre**
    - *Permitir HTTP* como **Descripción**
4. Haga clic en **OK**.
 
 ![Crear una red virtual](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Repita los pasos 2 a 4 para crear otra regla llamada *myRDPRule* que permita una conexión RDP entrante con el puerto 3389 con los valores siguientes:
    - *Etiqueta de servicio*: en **Origen**.
    - *Internet*: en **Etiqueta de servicio de origen**
    - *3389*: en **Intervalos de puerto de destino**
    - *TCP*: en **Protocolo**
    - *Permitir*: en **Acción**
    - *200* en **Prioridad**
    - *myRDPRule* como nombre
    - *Permitir RDP*: como descripción

    ![Creación de la regla de RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

1. En la parte superior izquierda de la pantalla, haga clic en **Crear un recurso** > **Proceso** > **Windows Server 2016 Datacenter** y especifique estos valores para la máquina virtual:
    - *myVM1*: para el nombre de la máquina virtual.        
    - *azureuser*: como nombre del usuario administrador.    
    - *myResourceGroupZLB*: como **Grupo de recursos**. Seleccione **Usar existente** y *myResourceGroupZLB*.
2. Haga clic en **OK**.
3. Seleccione **DS1_V2** como tamaño de la máquina virtual y haga clic en **Seleccionar**.
4. Especifique estos valores para la configuración de la máquina virtual:
    - *Zona 1*: como Zona de disponibilidad donde va a situar la máquina virtual.
    -  *myVNet*: asegúrese de que se selecciona como red virtual.
    - *myVM1PIP*: como la dirección IP pública nueva que crea. Haga clic en *Crear nuevo* y, como nombre, escriba *myVM1PIP*; como Zona, seleccione **1**. El SKU de la dirección IP es estándar de manera predeterminada.
    - *myBackendSubnet*: -asegúrese de que se selecciona como subred.
    - *myNetworkSecurityGroup*: como nombre del grupo de seguridad de red (firewall) existente.
5. Haga clic en **Deshabilitado** para deshabilitar los diagnósticos de arranque.
6. Haga clic en **Aceptar**, revise la configuración en la página de resumen y haga clic en **Crear**.
7. Cree una segunda máquina virtual llamada *myVM2* en la Zona 1 con *myVnet* como red virtual, *myVM2PIP* como dirección IP pública estándar, *myBackendSubnet* como subred y **myNetworkSecurityGroup* como grupo de seguridad de red (mediante los pasos del 1 al 6).

    ![Creación de la regla de RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalación de IIS en las máquinas virtuales

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, en la lista de recursos, haga clic en **myVM1**, que se encuentra en el grupo de recursos *myResourceGroupZLB*.
2. En la página **Información general**, haga clic en **Conectar** a RDP en la máquina virtual.
3. Inicie sesión en la máquina virtual con el nombre de usuario y la contraseña que especificó al crearla (puede que deba seleccionar **Más opciones** y **Usar una cuenta diferente** para especificar las credenciales que escribió al crear la máquina virtual). A continuación, seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión.
4. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows**>**Windows PowerShell**.
6. En la ventana de PowerShell, ejecute los comandos siguientes para instalar el servidor IIS, eliminar el archivo iisstart.htm predeterminado y agregar uno nuevo que muestre el nombre de la máquina virtual:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Cierre la sesión de RDP con *myVM1*
8. Repita los pasos del 1 al 7 para instalar IIS en *myVM2*.

## <a name="create-load-balancer-resources"></a>Creación de recursos del equilibrador de carga

En esta sección se configuran los valores del equilibrador de carga para un grupo de direcciones de back-end y un sondeo de mantenimiento, y especifique el equilibrador de carga y las reglas NAT.


### <a name="create-a-backend-address-pool"></a>Crear un grupo de direcciones de back-end

Para distribuir el tráfico a las máquinas virtuales, un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red (NIC) virtual conectadas al equilibrador de carga. Cree el grupo de direcciones de back-end *myBackendPool* para incluir *VM1* y *VM2*.

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, en **myLoadBalancer* en la lista de recursos.
2. Haga clic en **Configuración**, **Grupos de back-end** y luego en **Agregar**.
3. En la página **Agregar grupo back-end**, realice lo siguiente:
    - En el espacio para el nombre, escriba *myBackEndPool* como nombre del grupo de servidores back-end.
    - Para **Red virtual**, en el menú desplegable, haga clic en *myVNet*
    - Para **Máquina Virtual** y **Dirección IP**, agregue *myVM1* y *myVM2* y sus direcciones IP públicas correspondientes.
4. Haga clic en **Agregar**.
5. Asegúrese de que la configuración de grupo back-end del equilibrador de carga muestra las dos máquinas virtuales, **myVM1** y **myVM2**.
 
    ![Creación de un grupo de back-end](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado

Para permitir que el equilibrador de carga supervise el estado de la aplicación, utilice un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. Crear un sondeo de estado, *myHealthProbe*, para supervisar el estado de las máquinas virtuales.

1. Haga clic en **Todos los recursos** en el menú de la izquierda y, después, haga clic en **myLoadBalancer** en la lista de recursos.
2. En **Configuración**, haga clic en **Sondeos de estado** y luego en **Agregar**.
3. Use estos valores para crear el sondeo de estado:
    - *myHealthProbe*: como nombre del sondeo de estado.
    - **HTTP**: en tipo de protocolo.
    - *80*: en número de puerto.
    - *15*: como número de **Intervalo**, en segundos, entre los intentos de sondeo.
    - *2*: como número de **Umbral incorrecto** o errores de sondeo consecutivos que deben producirse para que una máquina virtual se considere que no funciona de manera incorrecta.
4. Haga clic en **OK**.

   ![Incorporación de un sondeo](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de equilibrador de carga

Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Se define la configuración de IP front-end para el tráfico entrante y el grupo IP de back-end para recibir el tráfico, junto con el puerto de origen y destino requeridos. Cree una regla de Load Balancer *myLoadBalancerRuleWeb* para escuchar el puerto 80 en el front-end *FrontendLoadBalancer* y enviar tráfico de red con equilibrio de carga al conjunto de direcciones back-end, *myBackEndPool*, que también usan el puerto 80. 

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
    
    ![Incorporación de una regla de equilibrio de carga](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga
1. Busque la dirección IP pública de Load Balancer en la pantalla **Información general**. Haga clic en **Todos los recursos** y, después, en **myPublicIP**. 

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. La página predeterminada que incluye el nombre de la página del servidor web se muestra en el explorador.

      ![Servidor web de IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Para ver el equilibrador de carga en acción, puede forzar la detención de la máquina virtual que se muestra y actualizar el explorador para ver el nombre del otro servidor en el explorador.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. Para ello, seleccione el grupo de recursos que contiene el equilibrador de carga y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de [Load Balancer Estándar](load-balancer-standard-overview.md).
- [Equilibrio de carga de máquinas virtuales en distintas zonas de disponibilidad](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
