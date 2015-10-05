<properties 
   pageTitle="Conexión con una máquina virtual de SQL Server | Microsoft Azure"
   description="En este tema se usan recursos creados con el modelo de implementación clásica y se describe cómo conectarse a SQL Server en una máquina virtual en Azure. Los escenarios varían según la configuración de red y la ubicación del cliente."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="jroth" />

# Conexión a una máquina virtual de SQL Server en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata el uso de un recurso con el modelo de implementación clásica.
 
## Información general

Configurar la conectividad con SQL Server que se ejecuta en una máquina virtual de Azure no es muy distinto de los pasos necesarios para una instancia local de SQL Server. Tendrá que trabajar con los pasos de configuración relacionados con el firewall, la autenticación y los inicios de sesión de base de datos.

Sin embargo, hay algunos aspectos de la conectividad de SQL Server que son específicos de las máquinas virtuales de Azure. En este artículo se describen algunos [escenarios de conectividad generales](#connection-scenarios) y, después, se proporcionan los [pasos detallados para configurar la conectividad de SQL Server en una máquina virtual de Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE]Este artículo se centra en la conectividad. Para obtener un tutorial completo sobre aprovisionamiento y conectividad, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

## Escenarios de conexión

La forma en que un cliente se conecta a SQL Server que se ejecuta en una máquina virtual varía según la ubicación del cliente y la configuración del equipo y la red. Entre los escenarios se incluyen los siguientes:

- [Conexión a SQL Server en el mismo servicio en la nube](#connect-to-sql-server-in-the-same-cloud-service)
- [Conexión a SQL Server a través de Internet](#connect-to-sql-server-over-the-internet)
- [Conexión a SQL Server en la misma red virtual](#connect-to-sql-server-in-the-same-virtual-network)

### Conexión a SQL Server en el mismo servicio en la nube

Se pueden crear varias máquinas virtuales en el mismo servicio en la nube. Para comprender el escenario de las máquinas virtuales, consulte [Cómo conectar máquinas virtuales con un servicio en la nube o red virtual](cloud-services-connect-virtual-machine.md).

En primer lugar, siga los [pasos de este artículo para configurar la conectividad](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Tenga en cuenta que no es necesario configurar un extremo público si va a conectar entre equipos del mismo servicio en la nube.

Puede usar la máquina virtual **hostname** en la cadena de conexión de cliente. El nombre de host es el nombre que asignó a la máquina virtual durante su creación. Por ejemplo, si su máquina virtual de SQL se llama **mysqlvm** con un nombre DNS de servicio en la nube de **mycloudservice.cloudapp.net**, una máquina virtual cliente en el mismo servicio en la nube podría usar la siguiente cadena de conexión para conectarse:

	"Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### Conexión a SQL Server a través de Internet

Si quiere conectarse a su motor de base de datos de SQL Server desde Internet, debe crear un extremo de máquina virtual para la comunicación TCP entrante. Este paso de la configuración de Azure dirige el tráfico del puerto TCP de entrada a un puerto TCP al que puede tener acceso la máquina virtual.

En primer lugar, siga los [pasos de este artículo para configurar la conectividad](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Cualquier cliente con acceso a Internet podrá conectarse a la instancia de SQL Server con el nombre DNS del servicio en la nube (como **mycloudservice.cloudapp.net**) y el extremo de la máquina virtual (como **57500**).

	"Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Aunque esto permite a los clientes conectarse a través de Internet, esto no implica que cualquier usuario pueda conectarse a SQL Server. Los clientes externos necesitan el nombre de usuario y la contraseña correctos. Para aumentar la seguridad, no use el conocido puerto 1433 para el extremo público de la máquina virtual. Y, si es posible, considere la posibilidad también de agregar una ACL en el extremo para restringir el tráfico solo a los clientes que permita. Para obtener instrucciones sobre el uso de ACL con extremos, consulte [Administración de la ACL en un extremo](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)

>[AZURE.NOTE]Es importante tener en cuenta que, cuando se usa esta técnica para comunicarse con SQL Server, todos los datos devueltos se consideran tráfico saliente desde el centro de datos. Está sujeto a los [precios normales de transferencias de datos salientes](http://azure.microsoft.com/pricing/details/data-transfers). Esto ocurre aunque use esta técnica desde otra máquina o servicio en la nube en el mismo centro de datos de Azure, ya que el tráfico sigue pasando por el equilibrador de carga pública de Azure.

### Conexión a SQL Server en la misma red virtual

[La red virtual](..\virtual-network\virtual-networks-overview.md) permite otros escenarios. Puede conectar las máquinas virtuales en la misma red virtual, aunque esas máquinas virtuales existan en servicios en la nube diferentes. Y con una [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), puede crear una arquitectura híbrida que conecta las máquinas virtuales con redes y máquinas locales.

Las redes virtuales también permiten unir las máquinas virtuales de Azure a un dominio. Esta es la única forma de usar la autenticación de Windows para SQL Server. Los demás escenarios de conexión requieren la autenticación de SQL con nombres de usuario y contraseñas.

En primer lugar, siga los [pasos de este artículo para configurar la conectividad](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm). Si va a configurar un entorno de dominio y la autenticación de Windows, no es necesario seguir los pasos de este artículo para configurar los inicios de sesión y la autenticación de SQL. Además, en este escenario no es necesario un extremo público.

Si configuró DNS, puede conectarse a la instancia de SQL Server especificando el nombre de host de la máquina virtual de SQL Server en la cadena de conexión. En el siguiente ejemplo se da por hecho también que se configuró la autenticación de Windows y que se concedió al usuario acceso a la instancia de SQL Server.

	"Server=mysqlvm;Integrated Security=true" 

Tenga en cuenta que, en este escenario, también puede especificar la dirección IP de la máquina virtual.

## Pasos para configurar la conectividad de SQL Server en una máquina virtual de Azure

[AZURE.INCLUDE [Conexión a SQL Server en una máquina virtual](../../includes/virtual-machines-sql-server-connection-steps.md)]

## Pasos siguientes

Para ver instrucciones de aprovisionamiento además de estos pasos de conectividad, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

Si planea usar también grupos de disponibilidad AlwaysOn para alta disponibilidad y recuperación ante desastres, considere la posibilidad de implementar un agente de escucha. Los clientes de la base de datos se conectan al agente de escucha en lugar de directamente a una de las instancias de SQL Server. El agente de escucha enruta los clientes a la réplica principal del grupo de disponibilidad. Para obtener más información, consulte [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

Es importante revisar todos los procedimientos recomendados de seguridad para SQL Server que se ejecuta en una máquina virtual de Azure. Para obtener más información, consulte [Consideraciones de seguridad para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-security-considerations.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Sept15_HO4-->