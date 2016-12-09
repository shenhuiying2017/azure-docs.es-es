---
title: "Configuración de DNS entre dos redes virtuales de Azure | Microsoft Docs"
description: "Aprenda a configurar las conexiones VPN y la resolución de nombres de dominio entre dos redes virtuales, y cómo configurar la replicación geográfica de HBase."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 881f4c60-0cac-481b-aedb-e7c0c9400df1
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dc2e5440d923fab0d029d72274c29c3ed9e38a5c


---
# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configuración de DNS entre dos redes virtuales de Azure
> [!div class="op_single_selector"]
> * [Configuración de la conectividad VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
> * [Configuración de DNS](hdinsight-hbase-geo-replication-configure-dns.md)
> * [Configuración de la replicación de HBase](hdinsight-hbase-geo-replication.md) 
> 
> 

Obtenga información acerca de cómo agregar y configurar los servidores DNS para redes virtuales de Azure para controlar la resolución de nombres dentro de las redes virtuales y entre ellas.

Este tutorial es la segunda parte de la [serie][hdinsight-hbase-geo-replication] sobre la creación de replicación geográfica de HBase:

* [Configuración de la conectividad VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet]
* Configuración de DNS para las redes virtuales (este tutorial)
* [Configuración de la replicación geográfica de HBase][hdinsight-hbase-geo-replication]

El diagrama siguiente ilustra las dos redes virtuales creadas en [Configuración de la conectividad VPN entre dos redes virtuales][hdinsight-hbase-geo-replication-vnet]:

![Diagrama de red virtual de replicación de HBase para HDInsight][img-vnet-diagram]

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Una estación de trabajo con Azure PowerShell**.
  
    Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:
  
        Add-AzureAccount
  
    Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:
  
        Select-AzureSubscription <AzureSubscriptionName>
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]
* **Dos redes virtuales de Azure con conectividad VPN**.  Para obtener instrucciones, consulte [Configuraración de la conectividad VPN entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-vnet].

> [!NOTE]
> Los nombres de servicio de Azure y los nombres de máquina virtual deben ser únicos. El nombre usado en este tutorial es Contoso-[Servicio de Azure/nombre de máquina virtual]-[EU/US]. Por ejemplo, Contoso-VNet-EU es la red virtual de Azure del centro de datos Norte de Europa; Contoso-DNS-US es la máquina virtual del servidor DNS del centro de datos Este de EE. UU. Debe proponer sus propios nombres.
> 
> 

## <a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Creación de máquinas virtuales de Azure que se van a usar como servidores DNS
**Para crear una máquina virtual en Contoso-VNet-EU, denominada Contoso-DNS-EU**

1. Haga clic en **NUEVO**, **PROCESO**, **MÁQUINA VIRTUAL** y **DE LA GALERÍA**.
2. Elija **Windows Server 2012 R2 Datacenter**.
3. Especifique:
   * **NOMBRE DE MÁQUINA VIRTUAL**: Contoso-DNS-EU
   * **NUEVO NOMBRE DE USUARIO:**: 
   * **CONTRASEÑA NUEVA**: 
4. Especifique:
   
   * **SERVICIO EN LA NUBE**: creación de un nuevo servicio en la nube
   * **REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL**: (seleccione Contoso-VNet-EU)
   * **SUBREDES DE LA RED VIRTUAL**: Subnet-1
   * **CUENTA DE ALMACENAMIENTO**: use una cuenta de almacenamiento generada automáticamente
     
     El nombre del servicio en la nube será el mismo que el nombre de la máquina virtual. En este caso, es Contoso-DNS-EU. Para las máquinas virtuales posteriores, puedo elegir usar el mismo servicio en la nube.  Todas las máquinas virtuales que se encuentran en el mismo servicio en la nube comparten la misma red virtual y sufijo de dominio.
     
     La cuenta de almacenamiento se usa para almacenar el archivo de imagen de la máquina virtual. 
   * **EXTREMOS**: (desplácese hacia abajo y seleccione **DNS**) 

Después de crear la máquina virtual, descubra la IP interna y externa.

1. Haga clic en el nombre de la máquina virtual, **Contoso-DNS-EU**.
2. Haga clic en **Panel**.
3. Escriba:
   * DIRECCIÓN IP VIRTUAL PÚBLICA
   * DIRECCIÓN IP INTERNA

**Para crear una máquina virtual en Contoso-VNet-US, denominada Contoso-DNS-US** 

* Repita el mismo procedimiento para crear una máquina virtual con los siguientes valores:
  * NOMBRE DE MÁQUINA VIRTUAL: Contoso-DNS-US
  * REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL: seleccione Contoso-VNet-EU
  * SUBREDES DE LA RED VIRTUAL: Subnet-1
  * CUENTA DE ALMACENAMIENTO: use una cuenta de almacenamiento generada automáticamente
  * EXTREMOS: (seleccione DNS)

## <a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Configuración de direcciones IP estáticas para las dos máquinas virtuales
Los servidores DNS requieren direcciones IP estáticas.  Este paso no puede realizarse desde el Portal de Azure clásico. Utilizará PowerShell de Azure.

**Para configurar la dirección IP estática para las dos máquinas virtuales**

1. Abra Windows PowerShell ISE.
2. Ejecute los siguientes cmdlets.  
   
        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
   
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 
   
    ServiceName es el nombre de servicio en la nube. Dado que el servidor DNS es la primera máquina virtual del servicio en la nube, el nombre del servicio en la nube es el mismo que el nombre de la máquina virtual.
   
    Es posible que tenga que actualizar ServiceName y Nombre para que coincidan con los nombres que tenga.

## <a name="add-the-dns-server-role-the-two-virtual-machines"></a>Adición de las dos máquinas virtuales al rol de servidor DNS 
**Para agregar el rol de servidor DNS para Contoso-DNS-EU**

1. En el Portal de Azure clásico, haga clic en **Máquinas virtuales** en la parte izquierda. 
2. Haga clic en **Contoso-DNS-EU**.
3. Haga clic en **PANEL** en la parte superior.
4. Haga clic en **CONECTAR** en la parte inferior y siga las instrucciones para conectarse a la máquina virtual a través de RDP.
5. Dentro de la sesión de RDP, haga clic en el botón de Windows en la esquina inferior izquierda para abrir la pantalla de inicio.
6. Haga clic en el mosaico **Administrador del servidor** .
7. Haga clic en **Agregar roles y características**.
8. Haga clic en **Siguiente**
9. Seleccione **Instalación basada en características o en roles** y haga clic en **Siguiente**.
10. Seleccione la máquina virtual DNS (ya debe estar marcada) y, a continuación, haga clic en **Siguiente**.
11. Elija **Servidor DNS**.
12. Haga clic en **Agregar características** y, a continuación, en **Continuar**.
13. Haga clic en **Siguiente** tres veces y, a continuación, haga clic en **Instalar**. 

**Para agregar el rol de servidor DNS para  Contoso-DNS-US**

* Repita los pasos para agregar el rol de DNS para **Contoso-DNS-US**.

## <a name="assign-dns-servers-to-the-virtual-networks"></a>Asignación de servidores DNS a las redes virtuales
**Para registrar los dos servidores DNS**

1. En el Portal de Azure clásico, haga clic en **NUEVO**, **NETWORK SERVICES**, **VIRTUAL NETWORK** y **REGISTRAR SERVIDOR DNS**.
2. Especifique:
   
   * **Nombre**: Contoso-DNS-EU
   * **DIRECCIÓN IP DEL SERVIDOR DNS**: 10.1.0.4: la dirección IP debe coincidir con la dirección IP de la máquina virtual del servidor DNS.
3. Repita el proceso para registrar Contoso-DNS-US con la siguiente configuración:
   
   * **NOMBRE**: Contoso-DNS-US
   * **DIRECCIÓN IP DEL SERVIDOR DNS**: 10.2.0.4

**Para asignar a los dos servidores DNS a las dos redes virtuales**

1. Haga clic en **Redes** en el panel izquierdo del Portal clásico.
2. Haga clic en **Contoso-VNet-EU**.
3. Haga clic en **CONFIGURAR**.
4. Seleccione **Contoso-DNS-EU** en la sección **Servidores DNS**.
5. Haga clic en **GUARDAR** en la parte inferior de la página y en **Sí** para confirmar.
6. Repita el proceso para asignar el servidor DNS **Contoso-DNS-US** para la red virtual de **Contoso-VNet-US**.

Todas las máquinas virtuales que se han implementado en las redes virtuales deben reiniciarse para actualizar la configuración del servidor DNS.

**Para reiniciar las máquinas virtuales**

1. En el Portal de Azure clásico, haga clic en **Máquinas virtuales** en la parte izquierda.
2. Haga clic en **Contoso-DNS-EU**.
3. Haga clic en **Panel** en la parte superior.
4. Haga clic en **REINICIAR** en la parte inferior.
5. Repita los mismos pasos para reiniciar **Contoso-DNS-US**.

## <a name="configure-dns-conditional-forwarders"></a>Configuración de reenviadores condicionales de DNS
El servidor DNS en cada red virtual solo puede resolver nombres DNS en la red virtual. Deberá configurar un reenviador condicional para que señale al servidor DNS del mismo nivel para la resolución de nombres en la red virtual del mismo nivel.

Para configurar el reenviador condicional, necesitará saber los sufijos de dominio de los dos servidores DNS. Los sufijos DNS pueden ser diferentes según la configuración de Servicios en la nube que utilizó al crear las máquinas virtuales. Para cada sufijo DNS que se utiliza en la red virtual, debe agregar un reenviador condicional. 

**Para buscar los sufijos de dominio de los dos servidores DNS**

1. RDP en **Contoso-DNS-EU**.
2. Abra una consola de Windows PowerShell o un símbolo del sistema.
3. Ejecute **ipconfig** y anote el **sufijo DNS específico de la conexión**.
4. No cierre la sesión de RDP, ya que la necesitará más adelante. 
5. Repita los mismos pasos para averiguar el **sufijo DNS específico de la conexión** de **Contoso-DNS-US**.

**Para configurar reenviadores DNS**

1. Desde la sesión de RDP para **Contoso-DNS-EU**, haga clic en la tecla Windows en la parte inferior izquierda.
2. Haga clic en **Herramientas administrativas**.
3. Haga clic en **DNS**.
4. En el panel izquierdo, expanda **DSN**, **Contoso-DNS-EU**.
5. Escriba la siguiente información:
   * **Dominio DNS**: escriba el sufijo DNS de Contoso-DNS-US. Por ejemplo: Contoso-DNS-US.b5.internal.cloudapp.net.
   * **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-US.
6. Presione **ENTRAR** y luego haga clic en **Aceptar**.  Ahora podrá resolver la dirección IP de Contoso-DNS-US de Contoso-DNS-EU.
7. Repita los pasos para agregar un reenviador de DNS al servicio DNS en la máquina virtual de Contoso-DNS-US con los siguientes valores:
   * **Dominio DNS**: escriba el sufijo DNS de Contoso-DNS-EU. 
   * **Direcciones IP de los servidores maestros**: escriba 10.2.0.4, que es la dirección IP de Contoso-DNS-EU.

## <a name="test-the-name-resolution-across-the-virtual-networks"></a>Prueba de la resolución de nombres a través de las redes virtuales
Ahora puede realizar la prueba de la resolución de nombres a través de las redes virtuales. El firewall bloquea el ping de forma predeterminada.  Puede usar nslookup para resolver las máquinas virtuales del servidor DNS (debe usar FQDN) en las redes del mismo nivel.  

## <a name="next-steps"></a>Pasos siguientes
En este tutorial ha aprendido cómo configurar una resolución de nombre en las redes virtuales con conexiones VPN. Los otros dos artículos de la serie tratan lo siguiente:

* [Configuración de una conexión VPN entre dos redes virtuales de Azure][hdinsight-hbase-geo-replication-vnet]
* [Configuración de la replicación geográfica de HBase][hdinsight-hbase-geo-replication]

[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 



<!--HONumber=Nov16_HO3-->


