---
title: "Instalación de una réplica de controlador de dominio de Active Directory en Azure | Microsoft Docs"
description: "Tutorial que explica cómo instalar un controlador de dominio de un bosque de Active Directory local en una máquina virtual de Azure."
services: virtual-network
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: virtual-network
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.openlocfilehash: f36a78fb8f8712ae8bb0ed6b5b8b081867198687
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure
En este tema se muestra cómo instalar controladores de dominio adicionales (también conocidos como controladores de dominio de réplica) para un dominio de Active Directory local en máquinas virtuales (VM) en una red virtual de Azure.

> [!IMPORTANT]
> Microsoft recomienda administrar Azure AD con el [Centro de administración de Azure AD](https://aad.portal.azure.com) en Azure Portal en lugar de usar el portal de Azure clásico al que se hace referencia en este artículo.

Es posible que también le interesen los siguientes temas relacionados:

* Opcionalmente, puede instalar un nuevo bosque de Active Directory en una red virtual. Para realizar esos pasos, consulte [Instalación de un bosque de Active Directory nuevo en una red virtual de Azure](active-directory-new-forest-virtual-machine.md).
* Para obtener una orientación en cuanto a conceptos sobre la instalación de los Servicios de dominio de Active Directory (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama del escenario
En este escenario, los usuarios externos necesitan tener acceso a las aplicaciones que se ejecutan en servidores unidos a un dominio. Las máquinas virtuales que ejecutan los servidores de aplicaciones y los controladores de dominio de réplica se instalan en una red virtual de Azure. La red virtual puede estar conectada a la red local mediante una conexión [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), tal y como se muestra en el diagrama siguiente, o bien puede usar [ExpressRoute](../expressroute/expressroute-locations-providers.md) para conseguir una conexión más rápida.

Los servidores de aplicaciones y los controladores de dominio se implementan en servicios en la nube independientes para distribuir el procesamiento informático y en [conjuntos de disponibilidad](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para una mejor tolerancia a errores.
Los controladores de dominio se replican entre sí y con controladores de dominio locales mediante la replicación de Active Directory. No se necesitan herramientas de sincronización.

![Diagrama de un controlador de dominio de Active Directory de réplica en una red virtual de Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Crear un sitio de Active Directory para la red virtual de Azure
Es buena idea crear un sitio de Active Directory que represente la región de red correspondiente a la red virtual. Eso ayuda a optimizar la autenticación, la replicación y otras operaciones de ubicación del controlador de dominio. Los pasos siguientes explican cómo crear un sitio y, para obtener más información, consulte [Agregar un sitio nuevo](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra Sitios y servicios de Active Directory: **Administrador de servidores** > **Herramientas** > **Sitios y servicios de Active Directory**.
2. Cree un sitio que represente la región donde creó una red virtual de Azure: haga clic en **Sitios** > **Acción** > **Nuevo sitio** escriba el nombre del nuevo sitio (por ejemplo, Azure, oeste de EE. UU.), seleccione un vínculo de sitio **Aceptar**.
3. Cree una subred y asóciela al nuevo sitio: haga doble clic en **Sitios** > haga clic con el botón derecho en **Subredes** > **Nueva subred** > escriba el intervalo de direcciones IP de la red virtual (por ejemplo, 10.1.0.0/16 en el diagrama del escenario) > seleccione el nuevo sitio de Azure > **Aceptar**.

## <a name="create-an-azure-virtual-network"></a>Creación de una red virtual de Azure
1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Nuevo** > **Network Services** > **Virtual Network** > **Creación personalizada** y use los siguientes valores para completar el asistente.

   | En esta página del asistente… | Especifique estos valores |
   | --- | --- |
   |  **Detalles de red virtual** |<p>Nombre: escriba un nombre para la red virtual, como WestUSVNet.</p><p>Región: elija la región más cercana.</p> |
   |  **Conectividad DNS y VPN** |<p>Servidores DNS: especifique el nombre y la dirección IP de uno o más servidores DNS locales.</p><p>Conectividad: seleccione **Configurar una VPN de sitio a sitio**.</p><p>Red local: especifique una nueva red local.</p><p>Si va a usar ExpressRoute en lugar de una VPN, consulte [Configuración de una conexión ExpressRoute mediante un proveedor de Exchange](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Conectividad de sitio a sitio** |<p>Nombre: escriba un nombre para la red local.</p><p>Dirección IP del dispositivo VPN: especifique la dirección IP pública del dispositivo que se conectará a la red virtual. El dispositivo VPN no se encuentra detrás de un NAT.</p><p>Dirección: especifique los intervalos de direcciones de la red local (por ejemplo, 192.168.0.0/16 en el diagrama del escenario).</p> |
   |  **Espacios de direcciones de la red virtual** |<p>Espacio de direcciones: especifique el intervalo de direcciones IP para las máquinas virtuales que desea ejecutar en la red virtual de Azure (por ejemplo, 10.1.0.0/16 en el diagrama del escenario). Este intervalo de direcciones no puede solaparse con los intervalos de direcciones de la red local.</p><p>Subredes: especifique un nombre y una dirección de una subred para los servidores de aplicaciones (como front-end, 10.1.1.0/24) y para los controladores de dominio (como back-end, 10.1.2.0/24).</p><p>Haga clic en **Agregar subred de puerta de enlace**.</p> |
2. A continuación, configurará la puerta de enlace de red virtual para crear una conexión VPN de sitio a sitio segura. Consulte [Configurar una puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obtener instrucciones.
3. Cree la conexión VPN de sitio a sitio entre la nueva red virtual y un dispositivo VPN local. Consulte [Configurar una puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obtener instrucciones.

## <a name="create-azure-vms-for-the-dc-roles"></a>Crear máquinas virtuales de Azure para los roles de controlador de dominio
Repita los pasos siguientes para crear máquinas virtuales para hospedar el rol de controlador de dominio según sea necesario. Debe implementar al menos dos controladores de dominio virtuales para proporcionar redundancia y tolerancia a errores. Si la red virtual de Azure incluye al menos dos controladores de dominio que están configurados de manera similar (es decir, son ambos catálogos globales, servidor DNS de ejecución y no contienen ningún rol FSMO, etc.), a continuación, coloque las máquinas virtuales que ejecutan los controladores de dominio en un conjunto de disponibilidad para aumentar la tolerancia a errores.
Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), haga clic en **Nuevo** > **Compute** > **Máquina virtual** > **De la galería**. Utilice los valores siguientes para completar el asistente. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.

   | En esta página del asistente… | Especifique estos valores |
   | --- | --- |
   |  **Elija una imagen** |Windows Server 2012 R2 Datacenter |
   |  **Configuración de la máquina virtual** |<p>Nombre de máquina virtual: escriba un nombre de etiqueta única (por ejemplo, AzureDC1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/Confirmar: Escriba una contraseña</p> |
   |  **Configuración de la máquina virtual** |<p>Servicio en la nube: elija <b>Crear un nuevo servicio en la nube</b> para la primera máquina virtual y seleccione ese mismo nombre de servicio en la nube al crear más máquinas virtuales que hospedan el rol de controlador de dominio.</p><p>Nombre de DNS del servicio en la nube: Especifique un nombre único global</p><p>Región/grupo de afinidad/red virtual: Especifique el nombre de red virtual (por ejemplo, WestUSVNet).</p><p>Cuenta de almacenamiento: elija <b>Use una cuenta de almacenamiento generada de forma automática</b> para la primera máquina virtual y, a continuación, seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que vayan a hospedar el rol de controlador de dominio.</p><p>Conjunto de disponibilidad: elija <b>Crear un conjunto de disponibilidad</b>.</p><p>Nombre del conjunto de disponibilidad: Escriba un nombre para el conjunto de disponibilidad al crear la primera máquina virtual y, a continuación, seleccione ese mismo nombre al crear más máquinas virtuales.</p> |
   |  **Configuración de la máquina virtual** |<p>Seleccione <b>Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p> |
2. Conecte un disco a cada máquina virtual que ejecutará el rol de servidor de controlador de dominio. Se necesita el disco adicional para almacenar la base de datos, los registros y SYSVOL de AD. Especifique un tamaño para el disco (por ejemplo, 10 GB) y deje la **Preferencia de caché de host** establecida en **Ninguno**. Para ver los pasos, consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Una vez que inicie sesión por primera vez en la máquina virtual, abra **Administrador del servidor** > **Servicios de archivos y almacenamiento** para crear un volumen en este disco con NTFS.
4. Reserve una dirección IP estática para las máquinas virtuales que ejecutarán el rol de controlador de dominio. Para reservar una dirección IP estática, descargue el instalador de plataforma web de Microsoft, [instale Azure PowerShell](/powershell/azure/overview) y ejecute el cmdlet Set-AzureStaticVNetIP. Por ejemplo:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Para obtener más información acerca de cómo establecer una dirección IP estática, consulte [Configuración de una dirección IP interna estática para una máquina virtual](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instalar AD DS en máquinas virtuales de Azure
Inicie sesión en una máquina virtual y compruebe que tiene conectividad a través de la conexión de ExpressRoute o de VPN de sitio a sitio a los recursos de la red local. Después, instale AD DS en las máquinas virtuales de Azure. Puede usar el mismo proceso que se utiliza para instalar un controlador de dominio adicional en la red local (interfaz de usuario, Windows PowerShell o un archivo de respuesta). Cuando instale AD DS, asegúrese de que especifica el nuevo volumen para la ubicación de la base de datos, los registros y SYSVOL de AD. Si necesita hacer un repaso sobre la instalación de AD DS, consulte [Instalar servicios de dominio de Active Directory (nivel 100)](https://technet.microsoft.com/library/hh472162.aspx) o [Instalar una réplica del controlador de dominio de Windows Server 2012 en un dominio existente  (nivel 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Volver a configurar el servidor DNS para la red virtual
1. En [Azure Portal](https://portal.azure.com), en el cuadro **Buscar recursos**, escriba *Redes virtuales* y, a continuación, haga clic en **Redes virtuales (clásico)** en los resultados de búsqueda. Haga clic en el nombre de la red virtual y, a continuación, [vuelva a configurar las direcciones IP del servidor DNS de la red virtual](../virtual-network/virtual-network-manage-network.md#dns-servers) para usar las direcciones IP estáticas que están asignadas a los controladores de dominio de réplica en lugar de las direcciones IP de los servidores DNS locales.
2. Para asegurarse de que todas las máquinas virtuales del controlador de dominio de réplica de la red virtual están configuradas para usar servidores DNS en la red virtual, haga clic en **Virtual Machines**, en la columna de estado para cada máquina virtual y, a continuación, en **Reiniciar**. Espere hasta que la máquina virtual muestre el estado **En ejecución** antes de intentar iniciar sesión en ella.

## <a name="create-vms-for-application-servers"></a>Crear máquinas virtuales para servidores de aplicaciones
1. Repita los pasos siguientes para crear VM que se ejecuten como servidores de aplicaciones. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.

   | En esta página del asistente… | Especifique estos valores |
   | --- | --- |
   |  **Elija una imagen** |Windows Server 2012 R2 Datacenter |
   |  **Configuración de la máquina virtual** |<p>Nombre de máquina virtual: escriba un nombre de etiqueta única (por ejemplo, AppServer1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/Confirmar: Escriba una contraseña</p> |
   |  **Configuración de la máquina virtual** |<p>Servicio en la nube: elija **Crear un nuevo servicio en la nube** para la primera máquina virtual y seleccione ese mismo nombre de servicio en la nube al crear más máquinas virtuales que hospedan la aplicación.</p><p>Nombre de DNS del servicio en la nube: Especifique un nombre único global</p><p>Región/grupo de afinidad/red virtual: Especifique el nombre de red virtual (por ejemplo, WestUSVNet).</p><p>Cuenta de almacenamiento: elija **Use una cuenta de almacenamiento generada de forma automática** para la primera máquina virtual y, a continuación, seleccione ese mismo nombre de cuenta de almacenamiento al crear más máquinas virtuales que vayan a hospedar la aplicación.</p><p>Conjunto de disponibilidad: elija **Crear un conjunto de disponibilidad**.</p><p>Nombre del conjunto de disponibilidad: Escriba un nombre para el conjunto de disponibilidad al crear la primera máquina virtual y, a continuación, seleccione ese mismo nombre al crear más máquinas virtuales.</p> |
   |  **Configuración de la máquina virtual** |<p>Seleccione <b>Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p> |
2. Una vez aprovisionada cada máquina virtual, inicie sesión y únalas al dominio. En **Administrador del servidor**, haga clic en **servidor Local** > **GRUPO DE TRABAJO** > **Cambiar...** y seleccione **Dominio** y escriba el nombre del dominio local. Proporcione las credenciales de un usuario de dominio y, a continuación, reinicie la máquina virtual para completar la unión al dominio.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Para más información acerca del uso de Windows PowerShell, consulte [Introducción a los cmdlets de Azure](/powershell/azure/overview) y [Azure Cmdlet Reference](/powershell/azure/get-started-azureps) (Referencia de cmdlets de Azure).

## <a name="additional-resources"></a>Recursos adicionales
* [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Cómo cargar los controladores de dominio de Hyper-V locales existentes a Azure con Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](active-directory-new-forest-virtual-machine.md)
* [Red virtual de Azure](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS para profesionales de TI: (01) Principios básicos sobre máquinas virtuales](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profesionales de TI: (05) Creación de redes virtuales y conectividad entre instalaciones](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlets de administración de Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
