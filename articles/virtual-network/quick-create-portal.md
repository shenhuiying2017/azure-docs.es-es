---
title: Crear una red virtual en Azure - Portal | Microsoft Docs
description: "Aprenda a crear una red virtual rápidamente mediante Azure Portal. Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen entre ellos de forma privada."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b1dbe96b9f522474cd2eeb2b63f3429f9ea4d8ed
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Creación de una red virtual mediante el Portal de Azure

En este artículo aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en esa red para probar la comunicación de red privada entre ellas.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-virtual-network"></a>Crear una red virtual

1. Seleccione el botón **+ Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Redes** y **Red virtual**.

3. Tal como se muestra en la siguiente imagen, escriba *myVirtualNetwork* en el campo **Nombre**, *myResourceGroup* en **Grupo de recursos**, seleccione un valor en **Ubicación** y **Suscripción**, acepte los valores predeterminados restantes y luego seleccione **Crear**. 

    ![Especificar información básica acerca de la red virtual](./media/quick-create-portal/virtual-network.png)

    El **Espacio de direcciones** se especifica en la notación CIDR. Una red virtual contiene cero o más subredes. La subred predeterminada **Intervalo de direcciones** de 10.0.0.0/24 usa todo el intervalo de direcciones de la red virtual, por lo que no se puede crear otra subred dentro de la red virtual mediante el espacio y el intervalo de direcciones predeterminados. El intervalo de direcciones especificado incluye las direcciones IP 10.0.0.0-10.0.0.254. Solo está disponible el intervalo 10.0.0.4-10.0.0.254, ya que Azure reserva las cuatro primeras direcciones (0-3) y la última dirección de cada subred. Las direcciones IP disponibles se asignan a recursos implementados en una red virtual.

## <a name="test-network-communication"></a>Prueba de la comunicación de red

Una red virtual permite que una gran cantidad de tipos de recursos de Azure se comuniquen entre ellos de forma privada. Un tipo de recurso que se puede implementar en una red virtual es una máquina virtual. Cree dos máquinas virtuales en la red virtual para poder validar la comunicación privada entre ellas posteriormente.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

1. Seleccione el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**.

3. Escriba la información de la máquina virtual que se muestra en la siguiente imagen. El **Nombre de usuario** y la **Contraseña** que especifique aquí se usarán en un paso posterior para iniciar sesión en la máquina virtual. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Seleccione su **Suscripción**, escoja el grupo de recursos *myResourceGroup* y asegúrese de que la **Ubicación** seleccionada es la misma que usó para crear la red virtual. Cuando haya terminado, seleccione **Aceptar**.

    ![Escribir información esencial acerca de una máquina virtual](./media/quick-create-portal/virtual-machine-basics.png)

4. Seleccione un tamaño para la máquina virtual y luego **Seleccionar**. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). Los tamaños que aparecen pueden ser diferentes a los que se muestran en el ejemplo siguiente: 

    ![Seleccionar un tamaño para la máquina virtual](./media/quick-create-portal/virtual-machine-size.png)

5. En **Configuración**, la opción *myVirtualNetwork* ya debería estar seleccionada en la **Red virtual**, pero si no es así, seleccione **Red virtual** y luego *myVirtualNetwork*. Deje *default* seleccionado para la **Subred** y luego seleccione **Aceptar**.

    ![Seleccionar una red virtual](./media/quick-create-portal/virtual-machine-network-settings.png)

6. En la página **Resumen**, seleccione **Crear** para iniciar la implementación de la máquina virtual. 

7. La creación de la máquina virtual tarda algunos minutos. Después de la creación, la máquina virtual se ancla al panel de Azure Portal y el resumen de esta se abre automáticamente. Seleccionar **Redes**.

    ![Información de redes de la máquina virtual](./media/quick-create-portal/virtual-machine-networking.png)

    Podrá ver que la dirección **IP privada** es *10.0.0.4*. En el paso 5, en **Configuración**, seleccionó la red virtual *myVirtualNetwork* y aceptó la subred denominada *default* en el apartado **Subred**. Cuando [creó la red virtual](#create-a-virtual-network), aceptó el valor predeterminado de 10.0.0.0/24 de la subred **Intervalo de direcciones**. El servidor DHCP de Azure asigna la primera dirección disponible de la subred que seleccionó para la máquina virtual. Puesto que Azure reserva las cuatro primeras direcciones (0-3) de cada subred, 10.0.0.4 es la primera dirección IP disponible de la subred.

    La dirección **IP pública** asignada es diferente de la dirección asignada a la máquina virtual. De forma predeterminada, Azure asigna una dirección IP pública y enrutable mediante Internet a cada máquina virtual. La dirección IP pública se asigna a la máquina virtual desde un [grupo de direcciones asignadas a cada región de Azure](https://www.microsoft.com/download/details.aspx?id=41653). Aunque Azure sabe qué dirección IP pública tiene asignada una máquina virtual, el sistema operativo que se ejecuta en una máquina virtual no sabe si tiene asignada alguna dirección IP pública.

8. Complete de nuevo los pasos del 1 al 7, pero recuerde que en el paso 3 debe nombrar la máquina virtual *myVm2*. 

9. Después de crear la máquina virtual, seleccione **Redes**, tal y como hizo en el paso 7. A continuación podrá ver que la dirección **IP privada** es *10.0.0.5*. Como Azure ya asignó la primera dirección utilizable de la subred (*10.0.0.4*) a la máquina virtual *myVm1*, asignó *10.0.0.5* a la máquina virtual *myVm2*, ya que esta es la siguiente dirección disponible en la subred.

### <a name="connect-to-a-virtual-machine"></a>Conexión a una máquina virtual

1. Conéctese de forma remota a la máquina virtual *myVm1*. En la parte superior de Azure Portal, escriba *myVm1*. Seleccione **MyVm1** cuando aparezca en los resultados de búsqueda. Seleccione el botón **Conectar**.

    ![Información general de la máquina virtual](./media/quick-create-portal/virtual-machine-overview.png)

2. Cuando seleccione el botón **Conectar**, se creará un archivo de Protocolo de Escritorio remoto (.rdp) y se descargará en el equipo.  

3. Abra el archivo .rdp descargado. Cuando se le pida, seleccione **Conectar**. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual y luego seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Seleccione **Sí** o en **Continuar** para continuar con la conexión.

### <a name="validate-communication"></a>Validar la comunicación

No se puede hacer ping en una máquina virtual de Windows porque el Firewall de Windows no lo permite de forma predeterminada. Para permitir el ping en *myVm1*, escriba el siguiente comando desde el símbolo del sistema:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Para validar la comunicación con *myVm2*, escriba el siguiente comando desde el símbolo del sistema en la máquina virtual *myVm1*. Proporcione las credenciales que usó cuando creó la máquina virtual y, a continuación, complete la conexión:

```
mstsc /v:myVm2
```

La conexión al Escritorio remoto se completará correctamente porque ambas máquinas virtuales tienen direcciones IP privadas asignadas desde la subred *default* y porque el Escritorio remoto está abierto a través del Firewall de Windows de forma predeterminada. Puede conectarse a *myVm2* según el nombre de host porque Azure proporciona automáticamente la resolución de nombres DNS de todos los hosts de una red virtual. Desde el símbolo del sistema, haga ping en *myVm1* desde *myVm2*.

```
ping myvm1
```

El ping se realizará correctamente porque en un paso anterior permitió que atravesara el Firewall de Windows en la máquina virtual *myVm1*. Utilice el siguiente comando para confirmar la comunicación saliente a Internet:

```
ping bing.com
```

Recibirá cuatro respuestas de bing.com. De forma predeterminada, una máquina virtual que se encuentre en una red virtual puede realizar comunicaciones salientes a Internet. 

Salga de la sesión de escritorio remoto.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todo su contenido. En la parte superior de Azure Portal, escriba *myResourceGroup*. Seleccione **myResourceGroup** cuando aparezca en los resultados de búsqueda. Seleccione **Eliminar**.

## <a name="next-steps"></a>pasos siguientes

En este artículo implementó una red virtual predeterminada con una subred. Para saber cómo crear una red virtual personalizada que tenga varias subredes, continúe con el tutorial para crear una red virtual personalizada.

> [!div class="nextstepaction"]
> [Crear una red virtual personalizada](virtual-networks-create-vnet-arm-pportal.md#portal)
