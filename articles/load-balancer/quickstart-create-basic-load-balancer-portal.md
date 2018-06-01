---
title: 'Guía de inicio rápido: Creación de una instancia pública de Load Balancer Básico mediante Azure Portal | Microsoft Docs'
description: Esta guía de inicio rápido muestra cómo crear una instancia pública de Load Balancer Básico mediante Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 49fa4cf9b24c432b0956f930a1429e1cdf827f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304885"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Guía de inicio rápido: Creación de una instancia pública de Load Balancer Básico mediante Azure Portal

El equilibrio de carga proporciona un mayor nivel de disponibilidad y escala, ya que distribuye las solicitudes entrantes entre varias máquinas virtuales. Puede usar Azure Portal para crear un equilibrador de carga para las máquinas virtuales. Esta guía de inicio rápido muestra cómo crear recursos de red, servidores de back-end y una instancia de Load Balancer del plan de tarifa Básico.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Para todas las tareas de esta guía de inicio rápido, inicie sesión en [Azure Portal](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Creación de una instancia de Load Balancer Básico

En esta sección, se crea una instancia pública de Load Balancer Básico mediante el portal. La dirección IP pública se configura automáticamente como front-end de la instancia de Load Balancer cuando se crean la dirección IP pública y el recurso del equilibrador de carga mediante el portal. El nombre del front-end es **LoadBalancerFrontend**.

1. En el lado superior izquierdo del portal, seleccione **Crear un recurso** > **Redes** > **Load Balancer**.
2. En el panel **Crear equilibrador de carga**, escriba estos valores:
   - **myLoadBalancer** como nombre del equilibrador de carga
   - **Público**: como tipo de equilibrador de carga 
   - **myPublicIP** como la dirección IP pública que debe crear, con **SKU** establecido en **Básica** y **Asignación** establecida en **Dinámica**
   - **myResourceGroupLB** como nombre del nuevo grupo de recursos
3. Seleccione **Crear**.
   
![Crear un equilibrador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Creación de los servidores de back-end

En esta sección, se crean una red virtual y dos máquinas virtuales para el grupo de back-end del equilibrador de carga Básico. A continuación, se instala Internet Information Services (IIS) en las máquinas virtuales para ayudar a probar el equilibrador de carga.

### <a name="create-a-virtual-network"></a>Crear una red virtual
1. En el lado superior izquierdo del portal, seleccione **Nuevo** > **Redes** > **Red virtual**.
2. En el panel **Crear red virtual**, escriba estos valores y seleccione **Crear**:
   - **myVNet** como nombre de la red virtual
   - **myResourceGroupLB** como nombre del grupo de recursos existente
   - **myBackendSubnet** como nombre de la subred

   ![Crear una red virtual](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

1. En el lado superior izquierdo del portal, seleccione **Nuevo** > **Compute** > **Windows Server 2016 Datacenter**. 
2. Especifique estos valores para la máquina virtual y, a continuación, seleccione **Aceptar**:
   - **myVM1** como nombre de la máquina virtual.        
   - **azureuser** como nombre del usuario administrador.    
   - **myResourceGroupLB** como grupo de recursos. (En **Grupo de recursos**, seleccione **Usar existente** y, a continuación, seleccione **myResourceGroupLB**).   
3. Seleccione **DS1_V2** como tamaño de la máquina virtual y haga clic en **Seleccionar**.
4. Especifique estos valores para la configuración de la máquina virtual:
   - **myAvailabilitySet** como nombre del nuevo conjunto de disponibilidad que crea.
   - **myVNet** como nombre de la red virtual. (Asegúrese de que está seleccionada).
   - **myBackendSubnet** como nombre de la subred. (Asegúrese de que está seleccionada).
   - **myVM1-ip** como dirección IP pública.
   - **myNetworkSecurityGroup** como nombre del nuevo grupo de seguridad de red (NSG, un tipo de firewall) que debe crear.
5. Seleccione **Deshabilitado** para deshabilitar los diagnósticos de arranque.
6. Seleccione **Aceptar**, revise la configuración en la página de resumen y seleccione **Crear**.
7. Mediante los pasos del 1 al 6, cree una segunda máquina virtual llamada **VM2**, con:
   - **myAvailabilityset** como el conjunto de disponibilidad.
   - **myVnet** como la red virtual.
   - **myBackendSubnet** como la subred.
   - **myNetworkSecurityGroup** como el grupo de seguridad de red. 

### <a name="create-nsg-rules"></a>Creación de reglas de NSG

En esta sección, se crean reglas de NSG para permitir conexiones entrantes que usen HTTP y RDP.

1. Seleccione **Todos los recursos** en el menú izquierdo. En la lista de recursos, seleccione **myNetworkSecurityGroup** en el grupo de recursos **myResourceGroupLB**.
2. En **Configuración**, seleccione **Reglas de seguridad de entrada** y, a continuación, seleccione **Agregar**.
3. Especifique los siguientes valores para la regla de seguridad de entrada llamada **myHTTPRule** para permitir que las conexiones HTTP entrantes usen el puerto 80. Después seleccione **Aceptar**.
   - **Etiqueta de servicio** en **Origen**
   - **Internet** en **Etiqueta de servicio de origen**
   - **80** en **Intervalos de puerto de destino**
   - **TCP** en **Protocolo**
   - **Permitir** en **Acción**
   - **100** en **Prioridad**
   - **myHTTPRule** en **Nombre**
   - **Permitir HTTP** en **Descripción**
 
   ![Creación de una regla de NSG](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Repita los pasos 2 y 3 para crear otra regla llamada **myRDPRule** que permita una conexión RDP entrante en el puerto 3389. Use los valores siguientes:
   - **Etiqueta de servicio** en **Origen**
   - **Internet** en **Etiqueta de servicio de origen**
   - **3389** en **Intervalos de puerto de destino**
   - **TCP** en **Protocolo**
   - **Permitir** en **Acción**
   - **200** en **Prioridad**
   - **myRDPRule** en **Nombre**
   - **Permitir RDP** en **Descripción**

   

### <a name="install-iis"></a>Instalación de IIS

1. Seleccione **Todos los recursos** en el menú izquierdo. En la lista de recursos, seleccione **myVM1** en el grupo de recursos **myResourceGroupLB**.
2. En la página **Información general**, seleccione **Conectar** para conectar mediante RDP con la máquina virtual.
3. Inicie sesión en la máquina virtual con el nombre de usuario **azureuser** y la contraseña **Azure123456!**.
4. En el escritorio del servidor, vaya a **Herramientas administrativas de Windows** > **Administrador del servidor** .
5. En el administrador del servidor, seleccione **Administrar** y, a continuación, seleccione **Agregar roles y características**.
   ![Adición de rol desde Administrador del servidor](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. En el Asistente para agregar roles y características, use los siguientes valores:
   - En la página **Seleccionar tipo de instalación**, seleccione **Instalación basada en características o en roles**.
   - En la página **Seleccionar servidor de destino **, seleccione** myVM1**.
   - En la página **Seleccionar rol de servidor**, seleccione **Servidor web (IIS)**.
   - Siga las instrucciones para completar el resto del asistente. 
7. Repita los pasos 1 a 6 para la máquina virtual **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Creación de recursos de Load Balancer Básico

En esta sección se configuran los valores del equilibrador de carga para un grupo de direcciones de back-end y un sondeo de mantenimiento. También se especifica el equilibrador de carga y las reglas NAT.


### <a name="create-a-back-end-address-pool"></a>Creación del grupo de direcciones de back-end

Para distribuir el tráfico a las máquinas virtuales, un grupo de direcciones de back-end contiene las direcciones IP de las tarjetas de interfaz de red (NIC) virtuales conectadas al equilibrador de carga. Cree el grupo de direcciones de back-end **myBackendPool** para incluir **VM1** y **VM2**.

1. Seleccione **Todos los recursos** en el menú de la izquierda y, a continuación, seleccione **myLoadBalancer** en la lista de recursos.
2. En **Configuración**, seleccione **Grupos de back-end** y, a continuación, seleccione **Agregar**.
3. En la página **Agregar un grupo back-end**, haga lo siguiente y, a continuación, seleccione **Aceptar**:
   - En **Nombre**, escriba **myBackEndPool**.
   - En **Asociado a**, en el menú desplegable, seleccione **Conjunto de disponibilidad**.
   - En **Conjunto de disponibilidad**, seleccione **myAvailabilitySet**.
   - Seleccione **Agregar una configuración IP de red de destino** para agregar cada máquina virtual (**myVM1** y **myVM2**) que ha creado al grupo de back-end.

   ![Incorporación al grupo de direcciones de back-end](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Asegúrese de que la configuración de grupo de back-end del equilibrador de carga muestra las dos máquinas virtuales, **VM1** y **VM2**.

### <a name="create-a-health-probe"></a>Creación de un sondeo de estado

Para permitir que la instancia de Load Balancer Básico supervise el estado de la aplicación se usa un sondeo de mantenimiento. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. Cree un sondeo de mantenimiento llamado **myHealthProbe** para supervisar el mantenimiento de las máquinas virtuales.

1. Seleccione **Todos los recursos** en el menú de la izquierda y, a continuación, seleccione **myLoadBalancer** en la lista de recursos.
2. En **Configuración**, seleccione **Sondeos de mantenimiento** y, a continuación, seleccione **Agregar**.
3. Use estos valores y, a continuación, seleccione **Aceptar**:
   - **myHealthProbe** como nombre del sondeo de mantenimiento
   - **HTTP** en tipo de protocolo
   - **80** en número de puerto
   - **15** en **Intervalo**, el número de segundos entre los intentos de sondeo
   - **2** en **Umbral incorrecto**, el número de errores de sondeo consecutivos que deben producirse para que se considere que una máquina virtual no funciona de manera correcta.

   ![Incorporación de un sondeo](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Creación de una regla de equilibrador de carga

Las reglas de Load Balancer se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales. Defina la configuración de la IP de front-end para el tráfico entrante y el grupo de IP de back-end para el tráfico entrante, junto con los puertos de origen y destino requeridos. 

Cree una regla del equilibrador de carga llamada **myLoadBalancerRuleWeb** para escuchar en el puerto 80 en el front-end **LoadBalancerFrontEnd**. La regla es también para el envío de tráfico de red con equilibrio de carga al grupo de direcciones de back-end **myBackEndPool**, también a través del puerto 80. 

1. Seleccione **Todos los recursos** en el menú de la izquierda y, a continuación, seleccione **myLoadBalancer** en la lista de recursos.
2. En **Configuración**, seleccione **Reglas de equilibrio de carga** y, a continuación, seleccione **Agregar**.
3. Use estos valores y, a continuación, seleccione **Aceptar**:
   - **myHTTPRule** en nombre de la regla del equilibrador de carga
   - **TCP** en tipo de protocolo
   - **80** en número de puerto
   - **80** en puerto de back-end
   - **myBackendPool** en nombre del grupo back-end
   - **myHealthProbe** como nombre del sondeo de mantenimiento
    
   ![Adición de una regla de Load Balancer](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Prueba del equilibrador de carga
1. Busque la dirección IP pública de Load Balancer en la pantalla **Información general**. Seleccione **Todos los recursos** y, después, seleccione **myPublicIP**.

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. La página predeterminada del servidor web IIS se muestra en el explorador.

   ![Servidor web IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, puede eliminar el grupo de recursos, el equilibrador de carga y todos los recursos relacionados. Seleccione el grupo de recursos que contiene el equilibrador de carga y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido se han creado un grupo de recursos, recursos de red y servidores de back-end. Después, se han usado dichos recursos para crear un equilibrador de carga básico. Para más información acerca de Azure Load Balancer, diríjase a los tutoriales correspondientes.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
