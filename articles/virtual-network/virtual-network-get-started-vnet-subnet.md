---
title: "Creación de su primera red virtual de Azure | Microsoft Docs"
description: "Aprenda a crear una red virtual de Azure, a conectar dos máquinas virtuales a la red virtual y a conectarse a las máquinas virtuales."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
ms.openlocfilehash: e653764d7cb514d50b44fadd0cc5963dd404d99e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="create-your-first-virtual-network"></a>Creación de su primera red virtual

Aprenda a crear una red virtual con dos subredes, a crear dos máquinas virtuales y a conectar cada máquina virtual a una de las subredes, tal como se muestra en la siguiente imagen:

![Diagrama de red virtual](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Una red virtual de Azure (VNet) es una representación de su propia red en la nube. Puede controlar la configuración de red de Azure y definir bloques de direcciones DHCP, la configuración de DNS, las directivas de seguridad y el enrutamiento. Para aprender más sobre los conceptos de red virtual, lea el artículo con la [introducción a las redes virtuales](virtual-networks-overview.md). Complete los pasos siguientes para crear los recursos que aparecen en la imagen:

1. [Cree una red virtual con dos subredes](#create-vnet)
2. [Cree dos máquinas virtuales, cada una con una interfaz de red (NIC)](#create-vms)y asocie un grupo de seguridad de red (NSG) a cada NIC
3. [Conéctese a las máquinas virtuales y desde ellas](#connect-to-from-vms)
4. [Elimine todos los recursos](#delete-resources). Incurrirá cargos por algunos de los recursos creados en este ejercicio mientras estén aprovisionados. Para minimizarlos, después de terminar el ejercicio, asegúrese de completar los pasos descritos en esta sección para eliminar los recursos que cree.

Tendrá conocimientos básicos de cómo usar una red virtual después de completar los pasos de este artículo. Los pasos siguientes se proporcionan para que pueda aprender más detalles sobre cómo usar las redes virtuales.

## <a name="create-vnet"></a>Creación de una red virtual con dos subredes

Para crear una red virtual con dos subredes, complete los pasos siguientes. Se suelen usar subredes diferentes para controlar el flujo del tráfico entre subredes.

1. Inicie sesión en el [Portal de Azure](<https://portal.azure.com>). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free). 
2. En el panel **Favoritos** del portal, haga clic en **Nuevo**.
3. En la hoja **Nuevo**, haga clic en **Redes**. En la hoja **Redes**, Haga clic en **Red virtual**, como se muestra en la siguiente imagen:

    ![Diagrama de red virtual](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  En la hoja **Red virtual**, deje seleccionado *Resource Manager* como modelo de implementación y haga clic en **Crear**.
5.  En la **hoja Crear red virtual** que aparece, escriba los valores siguientes y haga clic en **Crear**:

    |**Configuración**|**Valor**|**Detalles**|
    |---|---|---|
    |**Name**|*MyVNet*|El nombre debe ser único dentro del grupo de recursos.|
    |**Espacio de direcciones**|*10.0.0.0/16*|Puede especificar cualquier espacio de direcciones que desee en notación CIDR.|
    |**Nombre de subred**|*Front-end*|El nombre de la subred debe ser único dentro de la red virtual.|
    |**Intervalo de direcciones de subred**|*10.0.0.0/24*| El intervalo que especifique debe pertenecer al espacio de direcciones definido para la red virtual.|
    |**Suscripción**|*[Su suscripción]*|Seleccione una suscripción en la que crear la red virtual. Una red virtual existe dentro de una sola suscripción.|
    |**Grupos de recursos**|**Crear nuevo:** *MyRG*|Cree un grupo de recursos. El nombre del grupo de recursos debe ser único dentro de la suscripción seleccionada. Para más información sobre los grupos de recursos, lea el artículo [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups).|
    |**Ubicación**|*Oeste de EE. UU.*| Normalmente se selecciona la ubicación más próxima a su ubicación física.|

    La red virtual tarda unos segundos en crearse. Una vez creada, se ve el panel de Azure Portal.

6. Con la red virtual creada, en el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**. Haga clic en la red virtual **MyVNet** en la hoja **Todos los recursos**. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir *MyVNet* en el cuadro **Filtrar por nombre** para acceder fácilmente a la red virtual.
7. Se abre la hoja **MyVNet**, que muestra información sobre la red virtual, como se ve en la siguiente imagen:

    ![Diagrama de red virtual](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Como se muestra en la imagen anterior, haga clic en **Subredes** para abrir una lista de las subredes en la red virtual. La única subred que existe es **Front-end**, la que ha creado en el paso 5.
9. En la hoja MyVNet: Subredes, haga clic en **+ Subred** para crear una subred con la información siguiente y en **Aceptar** para crearla:

    |**Configuración**|**Valor**|**Detalles**|
    |---|---|---|
    |**Name**|*Back-end*|El nombre debe ser único dentro de la red virtual.|
    |**Intervalo de direcciones**|*10.0.1.0/24*|El intervalo que especifique debe pertenecer al espacio de direcciones definido para la red virtual.|
    |**Grupo de seguridad de red** y **Tabla de rutas**|*Ninguno* (valor predeterminado)|Los grupos de seguridad de red se tratan más adelante en este artículo. Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).|

10. Una vez agregada la nueva subred a la red virtual, puede cerrar la hoja **MyVNet: Subredes** y después la hoja **Todos los recursos**.

## <a name="create-vms"></a>Creación de máquinas virtuales

Con la red virtual y las subredes creadas, puede crear las máquinas virtuales. Para este ejercicio, ambas máquinas virtuales se ejecutan en el sistema operativo Windows Server, aunque sirve cualquier sistema operativo compatible con Azure, incluidas distintas distribuciones de Linux.

### <a name="create-web-server-vm"></a>Creación de la máquina virtual del servidor web

Para crear la máquina virtual del servidor web, complete los siguientes pasos:

1. En el panel Favoritos de Azure Portal, haga clic en **Nuevo**, **Proceso** y en **Windows Server 2016 Datacenter**.
2. En la hoja **Windows Server 2016 Datacenter**, haga clic en **Crear**.
3. En la hoja **Datos básicos** que aparece, escriba o seleccione los valores siguientes y haga clic en **Aceptar**:

    |**Configuración**| **Valor**|**Detalles**|
    |---|---|---|
    |**Name**|*MyWebServer*|Esta máquina virtual actúa como servidor web al que se conectan los recursos de Internet.|
    |**Tipo de disco de máquina virtual**|*SSD*|
    |**Nombre de usuario**|*Su elección*|
    |**Contraseña y Confirmar contraseña**|*Su elección*|
    | **Suscripción**|*<Your subscription>*|La suscripción debe ser la misma que seleccionó en el paso 5 de la sección [Creación de una red virtual con dos subredes](#create-vnet) en este artículo. La red virtual a la que se conecta una máquina virtual debe encontrarse en la misma suscripción que la máquina virtual.|
    |**Grupos de recursos**|**Usar existente:** seleccione *MyRG*|Aunque se va a usar el mismo grupo de recursos que para la red virtual, no es necesario que los recursos se encuentren en el mismo grupo de recursos.|
    |**Ubicación**|*Oeste de EE. UU.*|La ubicación debe ser la misma que especificó en el paso 5 de la sección [Creación de una red virtual con dos subredes](#create-vnet) en este artículo. Las máquinas virtuales y las redes virtuales a las que se conectan deben encontrarse en la misma ubicación.|

4. En la hoja **Elegir un tamaño**, haga clic en *DS1_V2 Standard* y en **Seleccionar**. Lea el artículo [Tamaños de las máquinas virtuales Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ver una lista de todos los tamaños de máquina virtual Windows compatibles con Azure.
5. En la hoja **Configuración**, escriba o seleccione los valores siguientes y haga clic en **Aceptar**:

    |**Configuración**|**Valor**|**Detalles**|
    |---|---|---|
    |**Almacenamiento: Usar discos administrados**|*Sí*||
    |**Red virtual**| Seleccione *MyVNet*|Puede seleccionar cualquier red virtual que se encuentre en la misma ubicación que la máquina virtual que vaya a crear. Para aprender más sobre redes virtuales y subredes, lea el artículo [Redes virtuales](virtual-networks-overview.md).|
    |**Subred**|Seleccione *Front-end*.|Puede seleccionar cualquier subred que se encuentre en la red virtual.|
    |**Dirección IP pública**|Acepte el valor predeterminado.|Una dirección IP pública le permite conectarse a la máquina virtual desde Internet. Para más información sobre las direcciones IP públicas, lea el artículo sobre [direcciones IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).|
    |**Grupo de seguridad de red (firewall)**|Acepte el valor predeterminado.|Haga clic en el grupo de seguridad de red predeterminado **(nuevo) MyWebServer-nsg** creado en el portal para ver su configuración. En la hoja **Crear grupo de seguridad de red** que se abre, observe que tiene una regla de entrada que permite el tráfico TCP/3389 (RDP) procedente de cualquier dirección IP de origen.|
    |**Todos los demás valores**|Acepte los valores predeterminados.|Para aprender más sobre el resto de las configuraciones, lea el artículo [acerca de las máquinas virtuales](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|

    Los grupos de seguridad de red permiten crear reglas de entrada o salida para el tipo de tráfico de red que puede llegar a la máquina virtual y salir de ella. De forma predeterminada, se deniega todo el tráfico entrante a la máquina virtual. Puede agregar más reglas de entrada para TCP/80 (HTTP) y TCP/443 (HTTPS) para un servidor web de producción. No existe ninguna regla para el tráfico saliente porque, de forma predeterminada, se permite todo. Puede agregar o quitar reglas para controlar el tráfico de acuerdo con sus directivas. Consulte el artículo sobre [grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre estos grupos.

6.  En la hoja **Resumen**, revise la configuración y haga clic en **Aceptar** para crear la máquina virtual. Se muestra un icono de estado en el panel del portal mientras se crea la máquina virtual. Esto puede tardar unos minutos. No es necesario esperar a que finalice. Puede continuar con el paso siguiente mientras se crea la máquina virtual.

### <a name="create-database-server-vm"></a>Creación de la máquina virtual del servidor de bases de datos

Para crear la máquina virtual del servidor de bases de datos, complete los siguientes pasos:

1.  En el panel Favoritos, haga clic en **Nuevo**, **Proceso** y en **Windows Server 2016 Datacenter**.
2.  En la hoja **Windows Server 2016 Datacenter**, haga clic en **Crear**.
3.  En la **hoja Datos básicos**, escriba o seleccione los valores siguientes y haga clic en **Aceptar**:

    |**Configuración**|**Valor**|**Detalles**|
    |---|---|---|
    |**Name**|*MyDBServer*|Esta máquina virtual actúa como servidor de bases de datos al que se conecta el servidor web, pero al que Internet no se puede conectar.|
    |**Tipo de disco de máquina virtual**|*SSD*||
    |**Nombre de usuario**|Su elección||
    |**Contraseña y Confirmar contraseña**|Su elección||
    |**Suscripción**|<Your subscription>|La suscripción debe ser la misma que seleccionó en el paso 5 de la sección [Creación de una red virtual con dos subredes](#create-vnet) en este artículo.|
    |**Grupos de recursos**|**Usar existente:** seleccione *MyRG*|Aunque se va a usar el mismo grupo de recursos que para la red virtual, no es necesario que los recursos se encuentren en el mismo grupo de recursos.|
    |**Ubicación**|*Oeste de EE. UU.*|La ubicación debe ser la misma que especificó en el paso 5 de la sección [Creación de una red virtual con dos subredes](#create-vnet) en este artículo.|

4.  En la hoja **Elegir un tamaño**, haga clic en *DS1_V2 Standard* y en **Seleccionar**.
5.  En la hoja **Configuración**, escriba o seleccione los valores siguientes y haga clic en **Aceptar**:

    |**Configuración**|**Valor**|**Detalles**|
    |----|----|---|
    |**Almacenamiento: Usar discos administrados**|*Sí*||
    |**Red virtual**|Seleccione *MyVNet*|Puede seleccionar cualquier red virtual que se encuentre en la misma ubicación que la máquina virtual que vaya a crear.|
    |**Subred**|Para seleccionar *Back-end*, haga clic en el cuadro **Subred** y seleccione **Back-end** en la hoja **Elegir una subred**.|Puede seleccionar cualquier subred que se encuentre en la red virtual.|
    |**Dirección IP pública**|Ninguna: haga clic en la dirección predeterminada y en **Ninguna** en la hoja **Elegir dirección IP pública**.|Sin una dirección IP pública, solo puede conectarse a la máquina virtual desde otra máquina virtual conectada a la misma red virtual. No se puede conectar a ella directamente desde Internet.|
    |**Grupo de seguridad de red (firewall)**|Acepte el valor predeterminado.| Al igual que el grupo de seguridad de red predeterminado creado para la máquina virtual MyWebServer, este comparte la misma regla de entrada predeterminada. Podría agregar otra regla de entrada para TCP/1433 (MS SQL) para un servidor de bases de datos. No existe ninguna regla para el tráfico saliente porque, de forma predeterminada, se permite todo. Puede agregar o quitar reglas para controlar el tráfico de acuerdo con sus directivas.|
    |**Todos los demás valores**|Acepte los valores predeterminados.||

6.  En la hoja **Resumen**, revise la configuración y haga clic en **Aceptar** para crear la máquina virtual. Se muestra un icono de estado en el panel del portal mientras se crea la máquina virtual. Esto puede tardar unos minutos. No es necesario esperar a que finalice. Puede continuar con el paso siguiente mientras se crea la máquina virtual.

## <a name="review"></a>Revisión de los recursos

Aunque ha creado una red virtual y dos máquinas virtuales, Azure Portal crea automáticamente varios recursos más en el grupo de recursos MyRG. Revise el contenido del grupo de recursos MyRG completando los pasos siguientes:

1. En el panel **Favoritos**, haga clic en **Más servicios**.
2. En el panel **Más servicios**, escriba *Grupos de recursos* en el cuadro con la palabra *Filtro*. Haga clic en **Grupos de recursos** cuando lo vea en la lista filtrada.
3. En el panel **Grupos de recursos**, haga clic en el grupo de recursos *MyRG*. Si ya tiene muchos grupos de recursos en su suscripción, puede escribir *MyRG* en el cuadro que contiene el texto *Filtrar por nombre* para acceder rápidamente al grupo de recursos MyRG.
4.  En la hoja **MyRG**, se ve que el grupo de recursos contiene 12 recursos, como se muestra en la siguiente imagen:

    ![Contenido del grupo de recursos](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

Para aprender más sobre las máquinas virtuales, los discos y las cuentas de almacenamiento, lea los artículos introductorios sobre las [máquinas virtuales](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), los [discos](../virtual-machines/windows/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y la [cuenta de almacenamiento](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se ven los dos grupos de seguridad de red predeterminados creados en el portal automáticamente. También se ve que el portal ha creado dos recursos de interfaz de red (NIC). Una NIC permite que una máquina virtual se conecte a otros recursos a través de la red virtual. Leer el artículo sobre las [NIC](virtual-network-network-interface.md) para informarse más. El portal también creó un recurso de dirección IP pública. Las direcciones IP públicas son una configuración para un recurso de dirección IP pública. Para más información sobre las direcciones IP públicas, lea el artículo sobre [direcciones IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

## <a name="connect-to-from-vms"></a>Conexión a las máquinas virtuales

Con la red virtual y las dos máquinas virtuales que creó, ahora puede conectarse a las máquinas virtuales siguiendo los pasos descritos en las secciones a continuación:

### <a name="connect-from-internet"></a>Conexión a la máquina virtual del servidor web desde Internet

Para conectarse a la máquina virtual del servidor web desde Internet, complete estos pasos:

1. En el portal, abra el grupo de recursos MyRG siguiendo los pasos descritos en la sección [Revisión de los recursos](#review) en este artículo.
2. En la hoja **MyRG**, haga clic en la máquina virtual **MyWebServer**.
3. En la hoja **MyWebServer**, haga clic en **Conectar**, como se muestra en la siguiente imagen:

    ![Conexión a la máquina virtual del servidor web](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Permita que el explorador descargue el archivo *MyWebServer.rdp* y ábralo.
5. Si aparece un cuadro de diálogo que le informa de que no se puede comprobar el editor de la conexión remota, haga clic en **Conectar**.
6. Al escribir sus credenciales, asegúrese de iniciar sesión con el nombre de usuario y la contraseña que especificó en el paso 3 de la sección [Creación de la máquina virtual del servidor web](#create-web-server-vm) en este artículo. Si en el cuadro **Seguridad de Windows** que aparece no se muestran las credenciales correctas, puede que tenga que hacer clic en **Más opciones** y en **Usar otra cuenta** para especificar el nombre de usuario y la contraseña correctos. Haga clic en **Aceptar** para conectarse a la máquina virtual.
7. Si aparece un cuadro **Conexión a Escritorio remoto** que le informa de que no se puede comprobar la identidad del equipo remoto, haga clic en **Sí**.
8. Se ha conectado a la máquina virtual MyWebServer desde Internet. Deje la conexión a Escritorio remoto abierta para completar los pasos descritos de la sección siguiente.

La conexión remota se establece con la dirección IP pública asignada al recurso de dirección IP pública que el portal creó en el paso 5 de la sección [Creación de una red virtual con dos subredes](#create-vnet) en este artículo. Se permite la conexión porque la regla predeterminada creada en el grupo de seguridad de red **MyWebServer-nsg** permitía TCP/3389 (RDP) de entrada a la máquina virtual desde cualquier origen de dirección IP. Si intenta conectarse a la máquina virtual a través de cualquier otro puerto, la conexión produce un error, a menos que agregue reglas de entrada adicionales al grupo de seguridad de red para permitir otros puertos.

>[!NOTE]
>Si agrega más reglas de entrada al grupo de seguridad de red, asegúrese de que los mismos puertos estén abiertos en el firewall de Windows o se producirá un error en la conexión.
>

### <a name="connect-to-internet"></a>Conexión a Internet desde la máquina virtual del servidor web

Para establecer una conexión de salida a Internet desde la máquina virtual del servidor web, complete estos pasos:

1. Si ya no tiene abierta ninguna conexión remota con la máquina virtual MyWebServer, establézcala con la máquina virtual siguiendo los pasos descritos en la sección [Conexión a la máquina virtual del servidor web desde Internet](#connect-from-internet) en este artículo.
2. En el escritorio de Windows, abra Internet Explorer. En el cuadro de diálogo **Programa de instalación de Internet Explorer 11**, haga clic en **No usar la configuración recomendada** y en **Aceptar**. Se aconseja que acepte la configuración recomendada para un servidor de producción.
3. En la barra de direcciones de Internet Explorer, escriba [bing.com](http:www.bing.com). Si aparece un cuadro de diálogo de Internet Explorer, haga clic en **Agregar**, en **Agregar** en el cuadro de diálogo **Sitios de confianza**, y en **Cerrar**. Repita este proceso para cualquier otro cuadro de diálogo de Internet Explorer.
4. En la página de búsqueda de Bing, escriba *whatsmyipaddress* y haga clic en el botón de lupa. Bing devuelve la dirección IP pública asignada para el recurso de dirección IP pública que se creó en el portal al crear la máquina virtual. Si examina la configuración del recurso **MyWebServer-ip**, verá la misma dirección IP asignada al recurso de dirección IP pública, como se muestra en la imagen siguiente. Sin embargo, la dirección IP asignada a la máquina virtual es diferente.

    ![Conexión a la máquina virtual del servidor web](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Deje la conexión a Escritorio remoto abierta para completar los pasos descritos de la sección siguiente.

Puede conectarse a Internet desde la máquina virtual porque toda la conectividad de salida desde esta está permitida de forma predeterminada. Puede limitar la conectividad de salida si agrega reglas adicionales al grupo de seguridad de red que se aplica a la NIC, a la subred a la que está conectada la NIC o a ambas.

Si se pone la máquina virtual en estado detenido (desasignado) mediante el portal, puede cambiar la dirección IP pública. Si necesita que la dirección IP pública no cambie nunca, puede usar el método de asignación estático para ella, en lugar del dinámico (que es el valor predeterminado). Para aprender más sobre las diferencias entre los métodos de asignación, lea el artículo [Tipos de direcciones IP y métodos de asignación](virtual-network-ip-addresses-overview-arm.md).

### <a name="webserver-to-dbserver"></a>Conexión a la máquina virtual del servidor de bases de datos desde la máquina virtual del servidor web

Para conectarse a la máquina virtual del servidor de bases de datos desde la del servidor web, complete estos pasos:

1. Si ya no tiene abierta ninguna conexión remota con la máquina virtual MyWebServer, establézcala con la máquina virtual siguiendo los pasos descritos en la sección [Conexión a la máquina virtual del servidor web desde Internet](#connect-from-internet) en este artículo.
2. Haga clic en el botón Inicio en la esquina inferior izquierda del escritorio de Windows y empiece a escribir *Escritorio remoto*. Cuando se muestre en la lista del menú Inicio **Conexión a Escritorio remoto**, haga clic en ella.
3. En el cuadro de diálogo **Conexión a Escritorio remoto**, escriba *MyDBServer* como nombre del equipo y haga clic en **Conectar**.
4. Escriba el nombre de usuario y las contraseñas que especificó en el paso 3 de la sección [Creación de la máquina virtual del servidor de bases de datos](#create-database-server-vm) en este artículo y haga clic en **Aceptar**.
5. Si aparece un cuadro de diálogo que le informa de que no se puede comprobar la identidad del equipo remoto, haga clic en **Sí**.
6. Deje abierta la conexión a Escritorio remoto a ambos servidores para completar los pasos de la sección siguiente.

Es posible establecer la conexión a la máquina virtual del servidor de bases de datos desde la máquina virtual del servidor web por las razones siguientes:

- Las conexiones entrantes de TCP/3389 están habilitadas para cualquier dirección IP de origen en el grupo de seguridad de red predeterminado creado en el paso 5 de la sección [Creación de la máquina virtual del servidor de bases de datos](#create-database-server-vm) en este artículo.
- Inició la conexión desde la máquina virtual del servidor web, que está conectado a la misma red virtual que la máquina virtual del servidor de bases de datos. Para conectarse a una máquina virtual sin dirección IP pública asignada, debe conectarse desde otra máquina virtual conectada a la misma red virtual, incluso si la máquina virtual está conectada a una subred diferente.
- Aunque las máquinas virtuales están conectadas a distintas subredes, Azure crea rutas predeterminadas que hacen posible la conectividad entre subredes. No obstante, puede invalidar las rutas predeterminadas creando la suya propia. Lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md) para aprender más sobre las rutas en Azure.

Si intenta iniciar una conexión remota a la máquina virtual del servidor de bases de datos desde Internet, como hizo en la sección [Conexión a la máquina virtual del servidor web desde Internet](#connect-from-internet) en este artículo, verá que la opción **Conectar** aparece atenuada. Esta opción está atenuada porque no hay ninguna dirección IP pública asignada a la máquina virtual, por lo que no son posibles las conexiones entrantes a ella desde Internet.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Conexión a Internet desde la máquina virtual del servidor de bases de datos

Para establecer una conexión de salida a Internet desde la máquina virtual del servidor de bases de datos, complete estos pasos:

1. Si ya no tiene abierta ninguna conexión remota con la máquina virtual MyDBServer desde la máquina virtual MyWebServer, complete los pasos en la sección [Conexión a la máquina virtual del servidor de bases de datos desde la máquina virtual del servidor web](#webserver-to-dbserver) en este artículo.
2. En el escritorio de Windows de la máquina virtual MyDBServer, abra Internet Explorer y responda a los cuadros de diálogo como lo hizo en los pasos 2 y 3 de la sección [Conexión a la máquina virtual del servidor web desde Internet](#connect-to-internet) en este artículo.
3. En la barra de direcciones, escriba [bing.com](http:www.bing.com).
4. Haga clic en **Agregar** en el cuadro de diálogo de Internet Explorer que aparezca, en **Agregar** y en **Cerrar** en el cuadro de diálogo **Sitios de confianza**. Complete estos pasos en los cuadros de diálogo adicionales que aparezcan.
5. En la página de búsqueda de Bing, escriba *whatsmyipaddress* y haga clic en el botón de lupa. Bing devuelve la dirección IP pública asignada actualmente a la máquina virtual en la infraestructura de Azure. 6. Cierre el escritorio remoto a la máquina virtual MyDBServer desde la máquina virtual MyWebServer y después cierre la conexión remota a la máquina virtual MyWebServer.

Se permite la conexión saliente a Internet porque todo el tráfico saliente está permitido de forma predeterminada, aunque no se asigne ningún recurso de dirección IP pública a la máquina virtual MyDBServer. De forma predeterminada, todas las máquinas virtuales pueden establecer una conexión de salida a Internet, tanto si tienen asignado un recurso de dirección IP pública como si no. Sin embargo, no puede conectarse a la dirección IP pública desde Internet, como podía hacer para la máquina virtual MyWebServer que tiene asignado un recurso de dirección IP pública.

## <a name="delete-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, complete los pasos siguientes:

1. Para ver el grupo de recursos MyRG que creó en este artículo, complete los pasos del 1 al 3 en la sección [Revisión de los recursos](#review) en este artículo. Una vez más, revise los recursos del grupo de recursos. Si ha creado el grupo de recursos MyRG, según los pasos anteriores, verá los 12 recursos que se muestran en la imagen del paso 4.
2. En la hoja MyRG, haga clic en el botón **Eliminar**.
3. El portal requiere que escriba el nombre del grupo de recursos para confirmar que desea eliminarlo. Si ve recursos distintos de los que aparecen en el paso 4 de la sección [Revisión de los recursos](#review) en este artículo, haga clic en **Cancelar**. Si solo ve los 12 recursos creados como parte de este artículo, escriba *MyRG* como nombre de grupo de recursos y haga clic en **Eliminar**. Al eliminarse un grupo de recursos, se eliminan todos los recursos que contiene, por lo que siempre debe asegurarse de comprobar su contenido antes de eliminarlo. El portal elimina todos los recursos incluidos en el grupo de recursos y, después, el grupo de recursos en sí. Este proceso tarda varios minutos.

## <a name="next-steps"></a>Pasos siguientes

En este ejercicio, ha creado una red virtual y dos máquinas virtuales. Ha especificado configuraciones personalizadas durante la creación de máquinas virtuales y ha aceptado otras predeterminadas. Se recomienda que lea los siguientes artículo, antes de implementar redes virtuales y máquinas virtuales de producción, para asegurarse de que comprende todas las configuraciones disponibles:

- [Redes virtuales](virtual-networks-overview.md)
- [Direcciones IP públicas](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Interfaces de red](virtual-network-network-interface.md)
- [Grupos de seguridad de red](virtual-networks-nsg.md)
- [Máquinas virtuales](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
