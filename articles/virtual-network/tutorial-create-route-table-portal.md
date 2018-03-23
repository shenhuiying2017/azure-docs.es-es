---
title: Enrutamiento del tráfico de red en Azure Portal | Microsoft Docs
description: Aprenda a enrutar el tráfico de red con una tabla de rutas mediante Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Enrutamiento del tráfico de red con una tabla de rutas mediante Azure Portal

De forma predeterminada, Azure enruta automáticamente el tráfico entre todas las subredes de una red virtual. Sin embargo, puede crear sus propias rutas para invalidar las predeterminadas de Azure. La posibilidad de crear rutas personalizadas resulta de utilidad si, por ejemplo, quiere enrutar el tráfico entre subredes por medio de un firewall. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de una tabla de rutas
> * Creación de una ruta
> * Asociar una tabla de rutas a una subred de red virtual
> * Probar el enrutamiento
> * Solucionar problemas de enrutamiento

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-route-table"></a>Creación de una tabla de rutas

De forma predeterminada, Azure enruta el tráfico entre todas las subredes de una red virtual. Para más información sobre las rutas predeterminadas de Azure, consulte el artículo sobre las [rutas del sistema](virtual-networks-udr-overview.md). Para invalidar el enrutamiento predeterminado de Azure, cree una tabla de rutas que contenga rutas y asóciela a una subred de la red virtual.

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y, luego, **Tabla de rutas**.
3. Seleccione su **suscripción** y seleccione o escriba la información siguiente y haga clic en **Crear**:
 
    ![Creación de una tabla de rutas](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Creación de una ruta

Una tabla de rutas puede contener varias rutas o ninguna. 

1. En el cuadro *Search resources, services, and docs* (Buscar recursos, servicios y documentos) en la parte superior del portal, comience a escribir *myRouteTablePublic*. Cuando aparezca **myRouteTablePublic** en los resultados de búsqueda, selecciónelo.
2. En **Configuración**, seleccione **Rutas** y, luego, seleccione **+Agregar**, como se muestra en la imagen siguiente:

    ![Adición de una ruta](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. En **Agregar ruta**, seleccione o escriba la información siguiente y, a continuación, haga clic en **Aceptar**:
    - **Nombre de ruta**: *ToPrivateSubnet*
    - **Prefijo de dirección**: 10.0.1.0/24
    - **Tipo de próximo salto**: seleccione **Aplicación virtual**.
    - **Dirección de próximo salto**: 10.0.2.4

    La ruta dirige todo el tráfico destinado al prefijo de dirección 10.0.1.0/24 por una aplicación virtual de red con la dirección IP 10.0.2.4. En pasos posteriores, se crean la aplicación virtual de red y la subred con el prefijo de dirección especificado. La ruta invalida el enrutamiento predeterminado de Azure, que enruta el tráfico directamente entre subredes. Cada ruta especifica un tipo de próximo salto. El tipo de próximo salto indica a Azure cómo enrutar el tráfico. En este ejemplo, el tipo de próximo salto es *VirtualAppliance*. Para más información sobre todos los tipos de próximo salto disponibles en Azure y cuándo usarlos, consulte [aquí](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Asociación de una tabla de rutas a una subred

Para poder asociar una tabla de rutas a una subred, debe crear una red virtual y una subred:

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. En **Crear red virtual**, seleccione o escriba la información siguiente y luego haga clic en **Crear**:
    
    - **Nombre**: *myVirtualNetwork*.
    - **Espacio de direcciones**: *10.0.0.0/16*
    - **Suscripción**: seleccione la suscripción.
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione **myResourceGroup**.
    - **Ubicación**: seleccione *Este de EE. UU.*
    - **Nombre de subred**: *Pública*.
    - **Intervalo de direcciones:** *10.0.0.0/24*.

4. En el cuadro **Search resources, services, and docs** (Buscar recursos, servicios y documentos) en la parte superior del portal, comience a escribir *myVirtualNetwork*. Cuando aparezca la opción **myVirtualNetwork** en los resultados de la búsqueda, selecciónela.
5. Agregue dos subredes adicionales a la red virtual. En **Configuración**, seleccione **Subredes** y, luego, **+ Subred**, como se muestra en la imagen siguiente:

    ![Subred agregada](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Seleccione o escriba la siguiente información y haga clic en **Aceptar**:
    - **Nombre**: Privada
    - **Espacio de direcciones:**: *10.0.1.0/24*

7. Realice de nuevo los pasos 5 y 6 y proporcione la siguiente información:
    - **Nombre**: DMZ
    - **Espacio de direcciones:**: *10.0.2.0/24*

Puede asociar una tabla de rutas a varias subredes o a ninguna. Una subred puede tener una tabla de ruta asociada a ella o ninguna. El tráfico saliente de una subred se enruta en función de las rutas predeterminadas de Azure y de cualquier ruta que haya agregado a una tabla de rutas que asocie a una subred. Asocie la tabla de rutas *myRouteTablePublic* a la subred *Pública*:

1. Después de completar el paso anterior, se muestra el cuadro **myVirtualNetwork - Subnets** (myVirtualNetwork: subredes). En **Configuración**, seleccione **Subredes** y, a continuación, haga clic en **Pública**.
2. Como se muestra en la siguiente imagen, seleccione **Tabla de rutas** y, luego, **MyRouteTablePublic**.

    ![Asociación de una tabla de rutas](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Seleccione **Guardar**.

## <a name="test-routing"></a>Probar el enrutamiento

Para probar el enrutamiento, creará una máquina virtual que actúe como aplicación virtual de red mediante la que se enruta la ruta que creó en un paso anterior. Después de crear la aplicación virtual de red, implementará una máquina virtual en las subredes *Pública* y *Privada*. A continuación, enrutará el tráfico de la subred *Pública* a la subred *Privada* mediante la aplicación virtual de red.

### <a name="create-a-network-virtual-appliance"></a>Creación de una aplicación virtual de red

En un paso anterior, creó una ruta que especificaba una aplicación virtual de red como el tipo de próximo salto. Una máquina virtual que se ejecuta en una aplicación de red se conoce a menudo como aplicación virtual de red. En entornos de producción, la aplicación virtual de red se suele implementar en una máquina virtual configurada previamente. Hay varias aplicaciones virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). En este artículo, se crea una máquina virtual básica.

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. Puede seleccionar otro sistema operativo, pero en los pasos restantes se supone que seleccionó **Windows Server 2016 Datacenter**. 
3. Seleccione o escriba la información siguiente para **Aspectos básicos** y luego seleccione **Aceptar**:
    - **Nombre**: *myVmNva*
    - **Grupo de recursos**: seleccione **Usar existente** y seleccione *myResourceGroup*.
    - **Ubicación**: seleccione *Este de EE. UU*.

    El **nombre de usuario** y la **contraseña** que escriba se usarán en un paso posterior. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). La **ubicación** y la **suscripción** que se seleccionen deben ser la misma ubicación y suscripción en que se encuentra la red virtual. No es necesario que seleccione el mismo grupo de recursos en que se creó la red virtual pero el mismo grupo de recursos se selecciona para este tutorial.
4. Seleccione un tamaño de máquina virtual en **Elegir un tamaño**.
5. Seleccione o escriba la información siguiente para **Configuración** y luego seleccione **Aceptar**:
    - **Red virtual**: asegúrese de que esté seleccionada la opción **myVirtualNetwork**. En caso contrario, seleccione **Red virtual** y, luego, seleccione **myVirtualNetwork** en **Elegir red virtual**.
    - **Subred**: seleccione **Subred** y, a continuación, seleccione **DMZ** en **Elegir subred**.
    - **Dirección IP pública**: seleccione **Dirección IP pública** y, luego, seleccione **Ninguna** en **Elegir dirección IP pública**. No se asigna ninguna dirección IP pública a esta máquina virtual, ya que no se realizará ninguna conexión a ella desde Internet.
6. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual.

La creación de la máquina virtual tarda algunos minutos. No continúe con el paso siguiente hasta que Azure termine de crear la máquina virtual y abra un cuadro con información sobre ella.

Cuando Azure crea la máquina virtual, también crea una [interfaz de red](virtual-network-network-interface.md) en la subred *DMZ* y asocia esta interfaz de red a la máquina virtual. En cada interfaz de red de Azure que reenvíe el tráfico destinado a cualquier dirección IP que no esté asignada a la interfaz de red, debe estar habilitado el reenvío de IP.

1. En el cuadro que se abre para la máquina virtual después de que se ha creado, en **Configuración**, seleccione **Redes** y, luego, seleccione **myvmnva158** (la interfaz de red que creó Azure para la máquina virtual tiene un número diferente después de **myvmnva**), como se muestra en la imagen siguiente:

    ![Redes de máquina virtual](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    Cuando creó la aplicación virtual de red en la subred *DMZ*, Azure creó automáticamente la interfaz de red, la asoció a la máquina virtual y le asignó la dirección IP privada *10.0.2.4*. 

2. En **Configuración**, seleccione **Configuraciones de IP**, seleccione **Habilitado** en **Reenvío IP** y luego seleccione **Guardar**, como se muestra en la imagen siguiente:

    ![Habilitación del reenvío IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual para que pueda validar que el tráfico que procede de la subred *Pública* se enruta a la subred *Privada* mediante la aplicación virtual de red de un paso posterior.

Complete los pasos del 1 al 6 de [Creación de una aplicación virtual de red](#create-a-network-virtual-appliance). Use la misma configuración de los pasos 3 y 5, a excepción de los siguientes cambios:

|Máquina virtual   |NOMBRE      |Subred      | Dirección IP pública     |
|---------         |--------- | -----------|---------              |
|Máquina virtual 1 | myVmWeb  | Público     | Aceptar los valores predeterminados del portal |
|Máquina virtual 2 | myVmMgmt | Privada    | Aceptar los valores predeterminados del portal |

Puede crear la máquina virtual *myVmMgmt* mientras Azure crea la máquina virtual *myVmWeb*. No continúe con los pasos siguientes hasta que Azure termine de crear ambas máquinas virtuales.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Redirigir el tráfico a través de una aplicación virtual de red

1. En el cuadro *Búsqueda* que se encuentra en la parte superior del portal, comience a escribir *myVmMgmt*. Seleccione **myVmMgmt** cuando aparezca en los resultados de la búsqueda.
2. Cree una conexión a Escritorio remoto a la máquina virtual *myVmMgmt* mediante la selección de **Conectar**, tal como se indica en la imagen siguiente:

    ![Conexión a la máquina virtual](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.
4. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **More choices** [Más opciones] y luego **Use a different account** [Usar una cuenta diferente], para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**.
5. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión.
6. En el último paso, se usa el comando tracert.exe para probar el enrutamiento. Tracert usa ICMP, que se deniega a través del Firewall de Windows. Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde un símbolo del sistema:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Aunque en este artículo se usa tracert para probar el enrutamiento, no se recomienda permitir ICMP mediante el Firewall de Windows para las implementaciones en producción.
7. El reenvío IP se habilitó dentro de Azure para la interfaz de red de la máquina virtual en [Habilitación del reenvío IP](#enable-ip-forwarding). Dentro de la máquina virtual, el sistema operativo o una aplicación que se ejecuta dentro de la máquina virtual, también debe poderse reenviar el tráfico de red. Al implementar una aplicación virtual de red en un entorno de producción, la aplicación normalmente filtra, registra o realiza alguna otra función antes de reenviar el tráfico. Sin embargo, en este artículo el sistema operativo simplemente reenvía todo el tráfico que recibe. Habilite el reenvío IP dentro del sistema operativo de la máquina virtual *myVmNva* siguiendo estos pasos en la máquina virtual *myVmMgmt*:

    Realice una conexión de Escritorio remoto a la máquina virtual *myVmNva* con el siguiente comando desde un símbolo del sistema:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Para habilitar el reenvío IP dentro del sistema operativo, escriba el siguiente comando en PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Reinicie la máquina virtual, que también desconectará la sesión de Escritorio remoto.
8. Mientras permanece conectado a la máquina virtual *myVmMgmt*, después de que se reinicia la máquina virtual *myVmNva*, cree una sesión de Escritorio remoto a la máquina virtual *myVmWeb* con el siguiente comando:

    ``` 
    mstsc /v:myVmWeb
    ```
    
    Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde un símbolo del sistema:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmMgmt* desde la máquina virtual *myVmWeb*, escriba el siguiente comando desde un símbolo del sistema:

    ```
    tracert myvmmgmt
    ```

    La respuesta es similar al siguiente ejemplo:
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Puede ver que el primer salto es 10.0.2.4, que es la dirección IP privada de la aplicación virtual de red. El segundo salto es 10.0.1.4, la dirección IP privada de la máquina virtual *myVmMgmt*. La ruta agregada a la tabla de rutas *myRouteTablePublic* y asociada a la subred *Pública* hizo que Azure enrutara el tráfico mediante la NVA, en lugar de a la subred *Privada* directamente.
10.  Cierre la sesión de Escritorio remoto a la máquina virtual *myVmWeb*, que aún le deja conectado a la máquina virtual *myVmMgmt*.
11. Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmWeb* desde la máquina virtual *myVmMgmt*, escriba el siguiente comando desde un símbolo del sistema:

    ```
    tracert myvmweb
    ```

    La respuesta es similar al siguiente ejemplo:

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Puede ver que el tráfico se enruta directamente desde la máquina virtual *myVmMgmt* hasta la máquina virtual *myVmWeb*. De forma predeterminada, Azure enruta el tráfico directamente entre subredes.
12. Cierre la sesión de Escritorio remoto a la máquina virtual *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Solucionar problemas de enrutamiento

Como ha aprendido en pasos anteriores, Azure aplica las rutas predeterminadas, que puede, opcionalmente, invalidar con las suyas propias. En ocasiones, puede que el tráfico no se enrute de la forma esperada. En este caso, puede usar la funcionalidad [Próximo salto](../network-watcher/network-watcher-check-next-hop-portal.md) de Azure Network Watcher para determinar cómo Azure enruta el tráfico entre dos máquinas virtuales. 

1. En el cuadro *Buscar* que se encuentra en la parte superior del portal, comience a escribir *Network Watcher*. Cuando aparezca la opción **Network Watcher** en los resultados de búsqueda, selecciónela.
2. En **Herramientas de diagnóstico de red**, seleccione **Próximo salto**.
3. Para probar el enrutamiento del tráfico desde la máquina virtual *myVmWeb* (10.0.0.4) hasta la máquina virtual *myVmMgmt* (10.0.1.4), seleccione su suscripción, escriba la información que se muestra en la siguiente imagen (el nombre de su **interfaz de red** es algo diferente) y luego seleccione **Próximo salto**:

    ![Próximo salto](./media/tutorial-create-route-table-portal/next-hop.png)  

    La salida **Resultado** le informa que la dirección IP del próximo salto para el tráfico que va desde *myVmWeb* hasta *myVmMgmt* es 10.0.2.4 (la máquina virtual *myVmNva*), que el tipo de próximo salto es *VirtualAppliance* y que la tabla de rutas que da lugar al enrutamiento es *myRouteTablePublic*.

Las rutas eficaces para cada interfaz de red son una combinación de las rutas predeterminadas de Azure y las rutas que defina. Para ver todas las rutas eficaces de una interfaz de red en una máquina virtual, realice los pasos siguientes:

1. En el cuadro *Buscar* que se encuentra en la parte superior del portal, comience a escribir *myVmWeb*. Cuando aparezca **myVmWeb** en los resultados de búsqueda, selecciónelo.
2. En **Configuración**, seleccione **Redes** y luego seleccione **myvmweb369** (la interfaz de red que creó Azure para su máquina virtual tiene un número diferente después de **myvmweb**).
3. En **SUPPORT + TROUBLESHOOTING** (Soporte técnico y solución de problemas), seleccione **Rutas eficaces**, como se muestra en la imagen siguiente:

    ![Rutas eficaces](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Puede ver las rutas predeterminadas de Azure y la ruta que ha agregado en la tabla de rutas *myRouteTablePublic*. Para más información sobre cómo Azure selecciona una ruta de la lista de rutas, consulte [Selección de rutas por parte de Azure](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene: 

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, creó una tabla de rutas y la asoció a una subred. Creó una aplicación virtual de red que enrutó el tráfico desde una subred pública hasta una subred privada. Aunque puede implementar muchos recursos de Azure en una red virtual, no es el caso de los recursos de algunos servicios de PaaS de Azure. Pero puede restringir el acceso a los recursos de algunos servicios de PaaS de Azure solo al tráfico que procede de una subred de una red virtual. Avance al siguiente tutorial para aprender a restringir el acceso de red a los recursos de PaaS de Azure.

> [!div class="nextstepaction"]
> [Restringir el acceso de red a los recursos de PaaS](virtual-network-service-endpoints-configure.md#azure-portal)
