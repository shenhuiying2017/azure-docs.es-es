---
title: "Instalar controladores de dominio de réplica para el dominio de Active Directory local en máquinas virtuales de Azure | Microsoft Docs"
description: "Cómo instalar controladores de dominio de réplica para un dominio de Active Directory local en máquinas virtuales (VM) de Azure en una red virtual de Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7624d588e958985a73c5b40e8010e18e8879cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure
En este tema se muestra cómo instalar controladores de dominio adicionales para usarlos como controladores de dominio de réplica para un dominio de Active Directory local en máquinas virtuales (VM) de Azure en una red virtual de Azure. También [puede instalar un bosque de Windows Server Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md). Para obtener instrucciones sobre la instalación de Active Directory Domain Services (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama del escenario
En este escenario, los usuarios externos necesitan tener acceso a las aplicaciones que se ejecutan en servidores unidos a un dominio. Las máquinas virtuales que ejecutan los servidores de aplicaciones y los controladores de dominio de réplica se instalan en una red virtual de Azure. La red virtual puede estar conectada a la red local a través de [ExpressRoute](../expressroute/expressroute-locations-providers.md), o bien puede usar una conexión [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), tal como se muestra a continuación: 

![Diagrama de un controlador de dominio de Active Directory de réplica en una red virtual de Azure][1]

Los servidores de aplicaciones y los controladores de dominio se implementan en servicios en la nube independientes para distribuir el procesamiento informático y en conjuntos de disponibilidad para una mejor tolerancia a errores.
Los controladores de dominio se replican entre sí y con controladores de dominio locales mediante la replicación de Active Directory. No se necesitan herramientas de sincronización.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Crear un sitio de Active Directory local para la red virtual de Azure
Puede crear un sitio en Active Directory que represente la región de red correspondiente a la red virtual. Este sitio puede ayudar a optimizar la autenticación, la replicación y otras operaciones de ubicación del controlador de dominio. Los pasos siguientes explican cómo crear un sitio y, para obtener más información, consulte [Agregar un sitio nuevo](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra Sitios y servicios de Active Directory: **Administrador de servidores** > **Herramientas** > **Sitios y servicios de Active Directory**.
2. Cree un sitio que represente la región donde creó una red virtual de Azure: haga clic en **Sitios** > **Acción** > **Nuevo sitio** escriba el nombre del nuevo sitio (por ejemplo, Azure, oeste de EE. UU.), seleccione un vínculo de sitio **Aceptar**.
3. Cree una subred y asóciela al nuevo sitio: haga doble clic en **Sitios** > haga clic con el botón derecho en **Subredes** > **Nueva subred** > escriba el intervalo de direcciones IP de la red virtual (por ejemplo, 10.1.0.0/16 en el diagrama del escenario) > seleccione el nuevo sitio de Azure > **Aceptar**.

## <a name="create-an-azure-virtual-network"></a>Creación de una red virtual de Azure
Para crear una red virtual de Azure y configurar la conexión VPN de sitio a sitio, siga los pasos que se incluyen en el artículo [Creación de una conexión de sitio a sitio mediante Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

También puede configurar la puerta de enlace de red virtual para crear una conexión VPN de sitio a sitio segura. Cree la conexión VPN de sitio a sitio entre la nueva red virtual y un dispositivo VPN local. Para obtener instrucciones, consulte [Configurar una puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>Crear máquinas virtuales de Azure para los roles de controlador de dominio
Para crear máquinas virtuales para hospedar el rol de controlador de dominio, repita los pasos de [Cree una máquina virtual Windows en Azure Portal](../virtual-machines/windows/quick-create-portal.md) según sea necesario. Implemente al menos dos controladores de dominio virtuales para proporcionar redundancia y tolerancia a errores. Si la red virtual de Azure tiene al menos dos controladores de dominio que estén configurados de forma similar, puede colocar las máquinas virtuales que ejecutan esos controladores de dominio en un conjunto de disponibilidad para mejorar la tolerancia a errores.

Para crear las máquinas virtuales con Windows PowerShell en lugar Azure Portal, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Reserve una dirección IP estática para las máquinas virtuales que ejecutarán el rol de controlador de dominio. Para reservar una dirección IP estática, descargue el instalador de plataforma web de Microsoft, [instale Azure PowerShell](/powershell/azure/overview) y ejecute el cmdlet Set-AzureStaticVNetIP. Por ejemplo:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Para obtener más información acerca de cómo establecer una dirección IP estática, consulte [Configuración de una dirección IP interna estática para una máquina virtual](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instalar AD DS en máquinas virtuales de Azure
Inicie sesión en una máquina virtual y compruebe que tiene conectividad a través de la conexión de ExpressRoute o de VPN de sitio a sitio a los recursos de la red local. Después, instale AD DS en las máquinas virtuales de Azure. Puede usar el mismo proceso que se utiliza para instalar un controlador de dominio adicional en la red local (interfaz de usuario, Windows PowerShell o un archivo de respuesta). Cuando instale AD DS, asegúrese de que especifica el nuevo volumen para la ubicación de la base de datos, los registros y SYSVOL de AD. Si necesita hacer un repaso sobre la instalación de AD DS, consulte [Instalar servicios de dominio de Active Directory (nivel 100)](https://technet.microsoft.com/library/hh472162.aspx) o [Instalar una réplica del controlador de dominio de Windows Server 2012 en un dominio existente  (nivel 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Volver a configurar el servidor DNS para la red virtual
1. Para obtener una lista de nombres de red virtual, en [Azure Portal](https://portal.azure.com), busque *Redes virtuales* y, a continuación, seleccione **Redes virtuales** para ver la lista. 
2. Abra la red virtual que quiere administrar y, a continuación, [vuelva a configurar las direcciones IP del servidor DNS de la red virtual](../virtual-network/virtual-network-manage-network.md#dns-servers) para usar las direcciones IP estáticas que están asignadas a los controladores de dominio de réplica en lugar de las direcciones IP de los servidores DNS locales.
3. Para asegurarse de que todas las máquinas virtuales de controlador de dominio de réplica en la red virtual estén configuradas para usar servidores DNS en la red virtual:
  1. Seleccione **Máquinas virtuales**.
  2. Seleccione las máquinas virtuales y, a continuación, elija **Reiniciar**. 
  3. Espere hasta que la máquina virtual esté **En ejecución** de nuevo y, a continuación, inicie sesión en esta.

## <a name="create-vms-for-application-servers"></a>Crear máquinas virtuales para servidores de aplicaciones

Para crear máquinas virtuales para hospedar el rol Servidor de aplicaciones, repita los pasos de [Cree una máquina virtual Windows en Azure Portal](../virtual-machines/windows/quick-create-portal.md) según sea necesario. Para crear las máquinas virtuales con Microsoft PowerShell en lugar Azure Portal, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). La siguiente tabla contiene las configuraciones sugeridas.

| Configuración | Valores |
| --- | --- |
|  **Elija una imagen** | Windows Server 2012 R2 Datacenter |
|  **Configuración de la máquina virtual** |<p>Nombre de máquina virtual: escriba un nombre de etiqueta única (por ejemplo, AppServer1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/Confirmar: Escriba una contraseña</p> |
|  **Configuración de la máquina virtual** |<p>Servicio en la nube: elija **Crear un nuevo servicio en la nube** para la primera máquina virtual y seleccione ese mismo nombre de servicio en la nube al crear más máquinas virtuales que hospedan la aplicación.</p><p>Nombre de DNS del servicio en la nube: Especifique un nombre único global</p><p>Región/grupo de afinidad/red virtual: Especifique el nombre de red virtual (por ejemplo, WestUSVNet).</p><p>Cuenta de almacenamiento: elija **Use una cuenta de almacenamiento generada de forma automática** para la primera máquina virtual y, a continuación, seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que vayan a hospedar la aplicación.</p><p>Conjunto de disponibilidad: elija **Crear un conjunto de disponibilidad**.</p><p>Nombre del conjunto de disponibilidad: Escriba un nombre para el conjunto de disponibilidad al crear la primera máquina virtual y, a continuación, seleccione ese mismo nombre al crear más máquinas virtuales.</p> |
|  **Configuración de la máquina virtual** |<p>Seleccione <b>Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p> |
  
Una vez aprovisionada cada máquina virtual, inicie sesión y únalas al dominio. 
1. En **Administrador del servidor** &gt; **Servidor local** &gt; **GRUPO_TRABAJO** &gt; **Cambiar...**, seleccione **Dominio**.
2. Escriba el nombre del dominio local. 
3. Proporcione las credenciales del usuario del domino.
4. Reinicie la máquina virtual.

## <a name="additional-resources"></a>Recursos adicionales

* Para más información acerca del uso de Windows PowerShell, consulte [Introducción a los cmdlets de Azure](/powershell/azure/overview) y [Azure Cmdlet Reference](/powershell/azure/get-started-azureps) (Referencia de cmdlets de Azure).
* [Directrices para implementar Windows Server Active Directory en Microsoft Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Cómo cargar los controladores de dominio de Hyper-V locales existentes a Azure con Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS para profesionales de TI: (01) Principios básicos sobre máquinas virtuales](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profesionales de TI: (05) Creación de redes virtuales y conectividad entre instalaciones](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlets de administración de Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
