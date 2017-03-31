---
title: "Conexión a una máquina virtual de SQL Server en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo conectarse a SQL Server en ejecución en una máquina virtual en Azure. En este tema se usa el modelo de implementación clásica. Los escenarios varían según la configuración de red y la ubicación del cliente."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/28/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 37b0abfa38282d7e8789ba5687f0937a60262464
ms.lasthandoff: 03/25/2017


---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Conexión a una máquina virtual de SQL Server en Azure (Administrador de recursos)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-connect.md)
> * [Clásico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Información general
En este tema se muestra cómo conectarse a su instancia de SQL Server que se ejecuta en una máquina virtual de Azure. En él se describen algunos [escenarios de conectividad generales](#connection-scenarios) y se proporcionan los [pasos detallados para configurar la conectividad de SQL Server en una máquina virtual de Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver la versión clásica de este artículo, consulte [Conexión a una máquina virtual de SQL Server en Azure (implementación clásica)](../classic/sql-connect.md).

Para prefiere consultar un tutorial completo sobre aprovisionamiento y conectividad, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Escenarios de conexión
La forma en que un cliente se conecta a SQL Server que se ejecuta en una máquina virtual varía según la ubicación del cliente y la configuración del equipo y la red. Entre los escenarios se incluyen los siguientes:

* [Conexión a SQL Server a través de Internet](#connect-to-sql-server-over-the-internet)
* [Conexión a SQL Server en la misma red virtual](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Conexión a SQL Server a través de Internet
Si desea conectarse a su motor de base de datos de SQL Server desde Internet, debe completar varios pasos, como configurar el firewall, habilitar la Autenticación de SQL y configurar el grupo de seguridad de red; además, debe tener una regla de grupo de seguridad de red para permitir el tráfico TCP en el puerto 1433.

Si utiliza el portal para aprovisionar una imagen de máquina virtual de SQL Server con Resource Manager, estos pasos se realizan de forma automática cuando selecciona **Pública** como la opción de conectividad de SQL:

![Opción de conectividad SQL pública durante el aprovisionamiento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Si esto no ocurrió durante el aprovisionamiento, puede configurar manualmente SQL Server y las máquinas virtuales; para ello, siga los [pasos que aparecen en este artículo para configurar la conectividad de forma manual](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

> [!NOTE]
> La imagen de máquina virtual para SQL Server Express Edition no habilita automáticamente el protocolo TCP/IP. Para Express Edition, debe usar el Administrador de configuración de SQL Server para [habilitar manualmente el protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) después de crear la máquina virtual.
> 
> 

Una vez hecho esto, cualquier cliente con acceso a Internet podrá conectarse a la instancia de SQL Server si especifica la dirección IP pública de la máquina virtual o la etiqueta DNS asignada a esa dirección IP. Si el puerto de SQL Server es 1433, no es necesario que lo especifique en la cadena de conexión.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Aunque esto permite a los clientes conectarse a través de Internet, esto no implica que cualquier usuario pueda conectarse a SQL Server. Los clientes externos necesitan el nombre de usuario y la contraseña correctos. Para obtener más seguridad, puede evitar utilizar el conocido puerto 1433. Por ejemplo, si configuró SQL Server para escuchar en el puerto 1500 y estableció reglas adecuadas de firewall y de grupo de seguridad de red, podría conectarse si anexa el número de puerto al nombre del servidor, tal como se indica en el ejemplo siguiente:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

> [!NOTE]
> Es importante tener en cuenta que cuando se recurre a esta técnica para comunicarse con SQL Server, todos los datos salientes desde el centro de datos de Azure están sujetos a [precios de transferencia de datos salientes](https://azure.microsoft.com/pricing/details/data-transfers/)normales.
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conexión a SQL Server en la misma red virtual
[La red virtual](../../../virtual-network/virtual-networks-overview.md) permite otros escenarios. Puede conectar las máquinas virtuales en la misma red virtual, incluso si esas máquinas virtuales existen en distintos grupos de recursos. Asimismo, con una [VPN de sitio a sitio](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), puede crear una arquitectura híbrida que conecta las máquinas virtuales con redes y máquinas locales.

Las redes virtuales también permiten unir las máquinas virtuales de Azure a un dominio. Esta es la única forma de usar la autenticación de Windows para SQL Server. Los demás escenarios de conexión requieren la autenticación de SQL con nombres de usuario y contraseñas.

Si utiliza el portal para aprovisionar una imagen de máquina virtual con Resource Manager, las reglas adecuadas de firewall para la comunicación en la red virtual se configuran cuando selecciona **Privada** como la opción de conectividad de SQL. Si esto no ocurrió durante el aprovisionamiento, puede configurar manualmente SQL Server y las máquinas virtuales; para ello, siga los [pasos que aparecen en este artículo para configurar la conectividad de forma manual](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Sin embargo, si planea configurar un entorno de dominio y la autenticación de Windows, no es necesario seguir los pasos de este artículo para configurar los inicios de sesión y la autenticación de SQL. Además, tampoco es necesario configurar reglas de grupo de seguridad de red para el acceso a través de Internet.

> [!NOTE]
> La imagen de máquina virtual para SQL Server Express Edition no habilita automáticamente el protocolo TCP/IP. Para Express Edition, debe usar el Administrador de configuración de SQL Server para [habilitar manualmente el protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) después de crear la máquina virtual.
> 
> 

Si configuró DNS en la red virtual, puede conectarse a la instancia de SQL Server si especifica el nombre de equipo de la máquina virtual de SQL Server en la cadena de conexión. En el siguiente ejemplo, se presume que también se configuró la autenticación de Windows y que se concedió al usuario acceso a la instancia de SQL Server.

    "Server=mysqlvm;Integrated Security=true"

Tenga en cuenta que, en este escenario, también puede especificar la dirección IP de la máquina virtual.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Pasos para configurar manualmente la conectividad de SQL Server en una máquina virtual de Azure
Los pasos siguientes muestran cómo configurar manualmente la conectividad con la instancia de SQL Server y, luego, conectarse de forma opcional a través de Internet mediante SQL Server Management Studio (SSMS). Observe que muchos de estos pasos se realizan de forma automática cuando selecciona las opciones de conectividad de SQL Server adecuadas en el portal.

Antes de que pueda conectarse a la instancia de SQL Server desde otra máquina virtual o Internet, debe completar las siguientes tareas descritas en las secciones que aparecen a continuación:

* [Apertura de puertos TCP en el firewall de Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configuración de SQL Server para escuchar en el protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configuración de SQL Server para autenticación de modo mixto](#configure-sql-server-for-mixed-mode-authentication)
* [Creación de inicios de sesión para la autenticación de SQL Server](#create-sql-server-authentication-logins)
* [Configuración de una regla de entrada de grupo de seguridad de red](#configure-a-network-security-group-inbound-rule-for-the-vm)
* [Configuración de una etiqueta DNS para la dirección IP pública](#configure-a-dns-label-for-the-public-ip-address)
* [Conexión al motor de base de datos desde otro equipo](#connect-to-the-database-engine-from-another-computer)

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Pasos siguientes
Para ver las instrucciones de aprovisionamiento además de estos pasos de conectividad, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explore la ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para SQL Server en máquinas virtuales de Azure.

Para ver otros temas sobre la ejecución de SQL Server en las máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).


