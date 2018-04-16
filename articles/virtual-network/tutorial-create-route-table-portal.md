---
title: 'Enrutamiento del tráfico de red (tutorial): Azure Portal | Microsoft Docs'
description: En este tutorial, aprenderá a enrutar el tráfico de red con una tabla de rutas mediante Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7254e9336fca14daee2021d5bde4c5538509fe35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutorial: Enrutamiento del tráfico de red con una tabla de rutas mediante Azure Portal

De forma predeterminada, Azure enruta automáticamente el tráfico entre todas las subredes de una red virtual. Sin embargo, puede crear sus propias rutas para invalidar las predeterminadas de Azure. La posibilidad de crear rutas personalizadas resulta de utilidad si, por ejemplo, quiere enrutar el tráfico entre subredes por medio de una aplicación virtual de red (NVA). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una tabla de rutas
> * Creación de una ruta
> * Creación de una red virtual con varias subredes
> * Asociación de una tabla de rutas a una subred
> * Creación de una aplicación virtual de red para enrutar el tráfico
> * Implementación de máquinas virtuales (VM) en subredes diferentes
> * Enrutamiento del tráfico desde una subred a otra a través de una aplicación virtual de red

Si lo prefiere, puede completar este tutorial con la [CLI de Azure](tutorial-create-route-table-cli.md) o [Azure PowerShell](tutorial-create-route-table-powershell.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure 

Inicie sesión en Azure Portal en http://portal.azure.com.

## <a name="create-a-route-table"></a>Creación de una tabla de rutas

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y, luego, **Tabla de rutas**.
3. Escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de las opciones y, a continuación, seleccione **Crear**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myRouteTablePublic|
    |La suscripción| Seleccione su suscripción.|
    |Grupos de recursos | Haga clic en **Crear nuevo** y escriba *myResourceGroup*.|
    |Ubicación|Este de EE. UU|
 
    ![Creación de una tabla de rutas](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Creación de una ruta

1. En el cuadro *Search resources, services, and docs* (Buscar recursos, servicios y documentos) en la parte superior del portal, comience a escribir *myRouteTablePublic*. Cuando aparezca **myRouteTablePublic** en los resultados de búsqueda, selecciónelo.
2. En **Configuración**, seleccione **Rutas** y, luego, seleccione **+Agregar**, como se muestra en la imagen siguiente:

    ![Adición de una ruta](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. En **Agregar ruta**, escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de las opciones y, a continuación, seleccione **Crear**:

    |Configuración|Valor|
    |---|---|
    |Nombre de ruta|ToPrivateSubnet|
    |Prefijo de dirección| 10.0.1.0/24|
    |Tipo de próximo salto | Seleccione **Aplicación virtual**.|
    |Siguiente dirección de salto| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Asociación de una tabla de rutas a una subred

Antes de poder asociar una tabla de rutas a una subred, debe crear una red virtual y una subred y, a continuación, puede asociar la tabla de rutas a una subred:

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. En **Crear red virtual**, escriba o seleccione la siguiente información, acepte los valores predeterminados para el resto de las opciones y, a continuación, seleccione **Crear**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVirtualNetwork|
    |Espacio de direcciones| 10.0.0.0/16|
    |La suscripción | Seleccione su suscripción.|
    |Grupos de recursos|Seleccione **Usar existente** y después seleccione **myResourceGroup**.|
    |Ubicación|Seleccione *Este de EE. UU*.|
    |Nombre de subred|Público|
    |Intervalo de direcciones|10.0.0.0/24|
    
4. En el cuadro **Search resources, services, and docs** (Buscar recursos, servicios y documentos) en la parte superior del portal, comience a escribir *myVirtualNetwork*. Cuando aparezca la opción **myVirtualNetwork** en los resultados de la búsqueda, selecciónela.
5. En **Configuración**, seleccione **Subredes** y, luego, **+ Subred**, como se muestra en la imagen siguiente:

    ![Subred agregada](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Seleccione o escriba la siguiente información y haga clic en **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|Privada|
    |Espacio de direcciones| 10.0.1.0/24|

7. Realice de nuevo los pasos 5 y 6 y proporcione la siguiente información:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|DMZ|
    |Espacio de direcciones| 10.0.2.0/24|

8. Después de completar el paso anterior, se muestra el cuadro **myVirtualNetwork - Subnets** (myVirtualNetwork: subredes). En **Configuración**, seleccione **Subredes** y, a continuación, haga clic en **Pública**.
9. Como se muestra en la siguiente imagen, seleccione **Tabla de rutas**, seleccione **MyRouteTablePublic** y, a continuación, seleccione **Guardar**:

    ![Asociación de una tabla de rutas](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Creación de una aplicación virtual de red

Una aplicación virtual de red es una máquina virtual que realiza una función de red, como el enrutamiento, el firewall o la optimización de la WAN.

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. Puede seleccionar otro sistema operativo, pero en los pasos restantes se supone que seleccionó **Windows Server 2016 Datacenter**. 
3. Seleccione o escriba la información siguiente para **Aspectos básicos** y luego seleccione **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |NOMBRE|myVmNva|
    |Nombre de usuario|Escriba un nombre de usuario de su elección.|
    |Password|Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupos de recursos| Seleccione **Usar existente** y después seleccione *myResourceGroup*.|
    |Ubicación|Seleccione **Este de EE. UU**.|
4. Seleccione un tamaño de máquina virtual en **Elegir un tamaño**.
5. Seleccione o escriba la información siguiente para **Configuración** y luego seleccione **Aceptar**:

    |Configuración|Valor|
    |---|---|
    |Red virtual|myVirtualNetwork: si no está seleccionada, seleccione **Red virtual** y, luego, seleccione **myVirtualNetwork** en **Elegir red virtual**.|
    |Subred|Seleccione **Subred** y, a continuación, seleccione **DMZ** en **Elegir subred**. |
    |Dirección IP pública| Seleccione **Dirección IP pública** y seleccione **Ninguna** en **Elegir dirección IP pública**. No se asigna ninguna dirección IP pública a esta máquina virtual, ya que no estará conectada a Internet.
6. En **Crear** de la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual.

    La máquina virtual tarda en crearse unos minutos. No continúe con el paso siguiente hasta que Azure termine de crear la máquina virtual y abra un cuadro con información sobre ella.

7. En el cuadro que se abre para la máquina virtual después de que se ha creado, en **Configuración**, seleccione **Redes** y, luego, seleccione **myvmnva158** (la interfaz de red que creó Azure para la máquina virtual tiene un número diferente después de **myvmnva**), como se muestra en la imagen siguiente:

    ![Redes de máquinas virtuales](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Para que una interfaz de red pueda reenviar el tráfico de red recibido, que no está destinado a su propia dirección IP, debe tener habilitado el reenvío IP. En **Configuración**, seleccione **Configuraciones de IP**, seleccione **Habilitado** en **Reenvío IP** y luego seleccione **Guardar**, como se muestra en la imagen siguiente:

    ![Habilitación del reenvío IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual para poder validar que el tráfico que procede de la subred *Public* se enruta a la subred *Private* mediante la aplicación virtual de red de un paso posterior. Realice los pasos 1 a 6 de [Creación de una aplicación virtual de red](#create-a-network-virtual-appliance). Use la misma configuración de los pasos 3 y 5, a excepción de los siguientes cambios:

|Nombre de la máquina virtual      |Subred      | Dirección IP pública     |
|--------- | -----------|---------              |
| myVmPublic  | Público     | Aceptar los valores predeterminados del portal |
| myVmPrivate | Privada    | Aceptar los valores predeterminados del portal |

Puede crear la máquina virtual *myVmPrivate* mientras Azure crea la máquina virtual *myVmPublic*. No continúe con los pasos siguientes hasta que Azure termine de crear ambas máquinas virtuales.

## <a name="route-traffic-through-an-nva"></a>Enrutamiento del tráfico a través de una aplicación virtual de red

1. En el cuadro *Búsqueda* que se encuentra en la parte superior del portal, escriba *myVmPrivate*. Cuando la máquina virtual **myVmPrivate** aparece en los resultados de búsqueda, selecciónela.
2. Cree una conexión a Escritorio remoto a la máquina virtual *myVmPrivate* seleccionando **Conectar**, tal como se muestra en la imagen siguiente:

    ![Conexión con una máquina virtual ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.
4. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual (puede que deba seleccionar **Más opciones** y luego **Usar una cuenta diferente**, para especificar las credenciales que escribió cuando creó la máquina virtual). A continuación, seleccione **Aceptar**.
5. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** para continuar con la conexión.
6. En el último paso, se usa el comando trace route para probar el enrutamiento. Trace route usa el Protocolo de mensajes de control de Internet (ICMP), al que no se permite atravesar el Firewall de Windows. Para habilitar ICMP mediante el Firewall de Windows, escriba el comando siguiente desde PowerShell en la máquina virtual *myVmPrivate*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Aunque en este tutorial se usa trace route para probar el enrutamiento, no se recomienda permitir que ICMP atraviese el Firewall de Windows en las implementaciones de producción.
7. El reenvío IP se habilitó dentro de Azure para la interfaz de red de la máquina virtual en [Habilitación del reenvío IP](#enable-ip-forwarding). Dentro de la máquina virtual, también el sistema operativo o una aplicación en ejecución deben poder reenviar el tráfico de red. Habilite el reenvío de IP en el sistema operativo de la máquina virtual *myVmNva*:

    Desde un símbolo del sistema en la máquina virtual *myVmPrivate* y el escritorio remoto en la máquina virtual *myVmNva*:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Para habilitar el reenvío IP dentro del sistema operativo, escriba el siguiente comando en PowerShell desde la máquina virtual *myVmNva*:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Reinicie la máquina virtual *myVmNva*, que también desconecta la sesión de Escritorio remoto.
8. Mientras sigue conectado a la máquina virtual *myVmPrivate*, cree una sesión de escritorio remoto en la máquina virtual *myVmPublic* después de que se reinicie la máquina virtual *myVmNva*:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Para habilitar que ICMP atraviese el Firewall de Windows, escriba el comando siguiente desde PowerShell en la máquina virtual *myVmPrivate*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmPrivate* desde la máquina virtual *myVmPublic*, escriba el siguiente comando de PowerShell en la máquina virtual *myVmPublic*:

    ```
    tracert myVmPrivate
    ```

    La respuesta es similar al siguiente ejemplo:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Puede ver que el primer salto es 10.0.2.4, que es la dirección IP privada de la aplicación virtual de red. El segundo salto es 10.0.1.4, la dirección IP privada de la máquina virtual *myVmPrivate*. La ruta agregada a la tabla de rutas *myRouteTablePublic* y asociada a la subred *Pública* hizo que Azure enrutara el tráfico mediante la NVA, en lugar de a la subred *Privada* directamente.
10.  Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPublic*, que aún le deja conectado a la máquina virtual *myVmPrivate*.
11. Para probar el enrutamiento del tráfico de red a la máquina virtual *myVmPublic* desde la máquina virtual *myVmPrivate*, escriba el siguiente comando desde un símbolo del sistema en la máquina virtual *myVmPrivate*:

    ```
    tracert myVmPublic
    ```

    La respuesta es similar al siguiente ejemplo:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Puede ver si el tráfico se enruta directamente de la máquina virtual *myVmPrivate* a la máquina virtual *myVmPublic*. De forma predeterminada, Azure enruta el tráfico directamente entre subredes.
12. Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene: 

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.
2. Seleccione **Eliminar grupo de recursos**.
3. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha creado una tabla de rutas y la ha asociado a una subred. Creó una aplicación virtual de red sencilla que enrutó el tráfico desde una subred pública hasta una subred privada. Puede implementar diversas aplicaciones virtuales de red preconfiguradas que realicen funciones de red, como son la optimización de la WAN y la de firewall, desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para más información acerca del enrutamiento, consulte [Introducción al enrutamiento](virtual-networks-udr-overview.md) y [Administración de una tabla de rutas](manage-route-table.md).


Aunque puede implementar muchos recursos de Azure en una red virtual, no es el caso de los recursos de algunos servicios de PaaS de Azure. Pero puede restringir el acceso a los recursos de algunos servicios de PaaS de Azure solo al tráfico que procede de una subred de una red virtual. Para aprender a restringir el acceso de red a los recursos de PaaS de Azure, pase al siguiente tutorial.

> [!div class="nextstepaction"]
> [Restringir el acceso de red a los recursos de PaaS](tutorial-restrict-network-access-to-resources.md)
