---
title: "Solución de problemas de los grupos de seguridad de red - Portal | Microsoft Docs"
description: "Obtenga información acerca de cómo solucionar problemas de los grupos de seguridad de red en el modelo de implementación de Azure Resource Manager utilizando Azure Portal."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 92816a5b1a74be5b64d974ad9ade47374db5db10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Solución de problemas de los grupos de seguridad de red utilizando Azure Portal
> [!div class="op_single_selector"]
> * 
            [Azure Portal](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Si ha configurado grupos de seguridad de red (NSG) en la máquina virtual (VM) y está experimentando problemas de conectividad en la misma, este artículo proporciona información general sobre las funcionalidades de diagnóstico para los NSG que puede ayudarle solucionar el problema.

Los NSG le permiten controlar los tipos de tráfico de flujo de entrada y salida de las máquinas virtuales. Los NSG pueden aplicarse a subredes en Azure Virtual Network (VNet), interfaces de red (NIC) o a ambas. Las reglas vigentes aplicadas a una interfaz de red son una agregación de las reglas que existen en los NSG que se aplican a una NIC y a la subred a la que está conectada. Las reglas en estos NSG a veces pueden entrar en conflicto entre sí y afectar la conectividad de red de la máquina virtual.  

Puede ver todas las reglas de seguridad vigentes de sus NSG, tal y como se aplican a las NIC de la máquina virtual. Este artículo muestra cómo solucionar problemas de conectividad de máquina virtual utilizando estas reglas en el modelo de implementación de Azure Resource Manager. Si no está familiarizado con conceptos de VNet y NSG, lea los artículos de información general sobre [redes virtuales](virtual-networks-overview.md) y [grupos de seguridad de red](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Uso de las reglas de seguridad vigentes para solucionar problemas de flujo de tráfico de máquinas virtuales
El siguiente escenario es un ejemplo de un problema de conexión común:

Una máquina virtual denominada *VM1* forma parte de una subred *Subnet1* dentro de una red virtual denominada *WestUS VNet1*. Se produce un error al intentar conectar la máquina virtual mediante RDP a través del puerto TCP 3389. Los NSG se aplican en ambas la NIC *VM1 NIC1* y la subred *subred1*. Se permite el tráfico al puerto TCP 3389 en el NSG asociado con la interfaz de red *VM1-NIC1*; sin embargo, el ping TCP al puerto 3389 de VM1 produce un error.

Aunque este ejemplo utiliza el puerto TCP 3389, los pasos siguientes pueden utilizarse para determinar errores de conexión entrante y saliente a través de cualquier puerto.

### <a name="vm"></a>Visualización de las reglas de seguridad vigentes para una máquina virtual
Complete los pasos siguientes para solucionar problemas de los NSG para una máquina virtual:

Puede ver una lista completa de las reglas de seguridad vigentes en una NIC, desde la propia máquina virtual. También puede agregar, modificar y eliminar reglas de NIC y de subred de NSG en la hoja reglas vigentes, si dispone de permisos para realizar estas operaciones.

1. Inicie sesión en Azure Portal en https://portal.azure.com con una cuenta de Azure. La cuenta debe tener asignada la operación *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* para la interfaz de red. Para más información sobre cómo asignar operaciones a las cuentas, consulte [Creación de roles personalizados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Haga clic en **Más servicios** y, luego, haga clic en **Máquinas virtuales** en la lista que aparece.
3. En la lista seleccione una máquina virtual para solucionar problemas, después de hacerlo se abrirá una hoja de máquina virtual con opciones.
4. Haga clic en **Diagnosticar y solucionar problemas** y, a continuación, seleccione un problema común. En este ejemplo está seleccionado **No puedo conectar con mi VM Windows** . 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Bajo el problema aparecen una serie de pasos, tal como se muestra en la siguiente imagen: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Haga clic en *effective security group rules* (reglas de grupo de seguridad vigentes) en la lista de pasos recomendados.
6. Aparece la hoja **Get effective security rules** (Obtener reglas de seguridad vigentes), como se muestra en la siguiente imagen:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Tenga en cuenta las siguientes secciones que puede observar en la imagen:
   
   * **Ámbito:** establecido en *VM1*, la máquina virtual que seleccionó en el paso 3.
   * **Interfaz de red:***VM1- NIC1* está seleccionada. Una máquina virtual puede tener varias interfaces de red (NIC). Cada NIC puede tener sus propias reglas de seguridad vigentes. Para solucionar un problema, puede que necesite ver las reglas de seguridad vigentes para cada NIC.
   * **Associated NSGs** (NSG asociados): los NSG se pueden aplicar a la NIC y la subred a la que la NIC está conectada. En la imagen, se ha aplicado un NSG tanto a la NIC como a la subred a la que está conectada. Puede hacer clic en los nombres de los NSG para modificar directamente las reglas de los mismos.
   * **Pestaña VM1-nsg:** la lista de reglas que se muestran en la imagen es para el NSG aplicado a la NIC. Azure crea varias reglas predeterminadas cuando se crea un NSG. Las reglas predeterminadas no se pueden quitar, pero puede reemplazarlas con reglas de prioridad más alta. Para más información acerca de las reglas predeterminadas, consulte el artículo de [información general sobre los grupos de seguridad de red](virtual-networks-nsg.md#default-rules) .
   * **Columna Destino:** algunas de las reglas tienen texto en la columna, mientras que otras tienen prefijos de direcciones. El texto es el nombre de las etiquetas predeterminadas que se aplicaron a la regla de seguridad cuando se creó. Las etiquetas son identificadores proporcionados por el sistema que representan varios prefijos. Seleccionar una regla con una etiqueta, como *AllowInternetOutBound*, coloca los prefijos en la hoja **Address prefixes** (Prefijos de dirección).
   * **Descargar:** la lista de reglas puede ser larga. Puede descargar un archivo .csv con las reglas para analizarlo sin conexión haciendo clic en **Descargar** y guardando el archivo.
   * **AllowRDP** : esta regla permite las conexiones RDP con la máquina virtual.
7. Haga clic en la pestaña **Subnet1-NSG** para ver las reglas vigentes desde el NSG aplicado a la subred, tal como se muestra en la siguiente imagen: 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Observe la regla de *entrada* **denyRDP** . Las reglas de entrada aplicadas a la subred se evalúan antes que las reglas que se aplican a la interfaz de red. Debido a que la regla de denegación se aplica a la subred, la solicitud de conexión a TCP 3389 produce un error, porque la regla de permiso en la NIC no se evalúa. 
   
    La regla *denyRDP* es la razón por la qué falla la conexión RDP. Quitándola se debería resolver el problema.
   
   > [!NOTE]
   > Si la máquina virtual asociada a la NIC no está en estado de ejecución, o no se ha aplicado ningún NSG a la NIC o la subred, no se muestra ninguna regla.
   > 
   > 
8. Para editar las reglas NSG, haga clic en *Subnet1-NSG* en la sección **Associated NSGs** (NSG asociados).
   Se abrirá la hoja **Subnet1-NSG** . Puede editar directamente las reglas, haciendo clic en **Reglas de seguridad de entrada**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Después de quitar la regla de entrada *denyRDP* en **Subnet1-NSG** y de agregar la regla *allowRDP*, la lista de reglas vigentes presenta un aspecto como el de la siguiente imagen:
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Compruebe que el puerto TCP 3389 está abierto abriendo una conexión RDP con la máquina virtual o usando la herramienta PsPing. Puede obtener más información sobre PsPing consultando la [página de descarga de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Visualización de las reglas de seguridad vigentes para una interfaz de red
Si el flujo de tráfico de la máquina virtual se ve afectado por una NIC específica, puede ver una lista completa de las reglas vigentes para la NIC desde el contexto de interfaces de red mediante los pasos siguientes:

1. Inicie sesión en Azure Portal en https://portal.azure.com.
2. Haga clic en **Más servicios** y, luego, en **Interfaces de red** en la lista que aparece.
3. Seleccione una interfaz de red. En la siguiente imagen, se selecciona una NIC llamada *VM1-NIC1* .
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Observe que el **Ámbito** se establece en la interfaz de red seleccionada. Para leer más acerca la información adicional que se muestra, consulte el paso 6 de la sección sobre **solucionar problemas de los NSG para una máquina virtual** en este artículo.
   
   > [!NOTE]
   > Si se quita un NSG de una interfaz de red, la subred NSG todavía continúa vigente en dicha NIC. En este caso, el resultado mostraría solo las reglas del NSG de la subred. Las reglas solo aparecen si la NIC está conectada a una máquina virtual.
   > 
   > 
4. Puede editar directamente las reglas para los NSG asociados con una subred y una NIC. Para obtener información sobre cómo hacerlo, vea el paso 8 de la sección **Visualización de las reglas de seguridad vigentes para una máquina virtual** de este artículo.

## <a name="nsg"></a>Visualización de las reglas de seguridad vigentes para un grupo de seguridad de red (NSG)
Al modificar las reglas de NSG, puede revisar el impacto de las reglas que se agregan en una máquina virtual específica. Puede ver una lista completa de las reglas de seguridad vigentes para todas las NIC a las que se aplica un NSG determinado, sin tener que cambiar el contexto de la hoja de NSG determinada. Para solucionar problemas de las reglas vigentes dentro de un NSG, realice los pasos siguientes:

1. Inicie sesión en Azure Portal en https://portal.azure.com.
2. Haga clic en **Más servicios** y, luego, haga clic en **Grupos de seguridad de red** en la lista que aparece.
3. Seleccione un NSG. En la siguiente imagen, se ha seleccionado un NSG llamado VM1-nsg.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Tenga en cuenta las siguientes secciones en la imagen anterior:
   
   * **Ámbito:** establecido en el NSG seleccionado.
   * **Máquina Virtual:** cuando un NSG se aplica a una subred, se aplica a todas las interfaces de red conectadas a todas las máquinas virtuales conectadas a la subred. Esta lista muestra todas las máquinas virtuales a las que se aplica este NSG. Puede seleccionar cualquier máquina virtual en la lista.
     
     > [!NOTE]
     > Si un NSG se aplica a solo una subred vacía, no se mostrará ninguna máquina virtual. Si se aplica un NSG a una NIC que no está asociada a una máquina virtual, las NIC tampoco aparecerán. 
     > 
     > 
   * **Interfaz de red:** una VM puede tener varias interfaces de red. Puede seleccionar una interfaz de red conectada a la máquina virtual seleccionada.
   * **Grupos de seguridad de red asociados:** en cualquier momento, una NIC puede tener hasta dos grupos de seguridad de red vigentes, uno aplicado a la NIC y el otro a la subred. Aunque el ámbito está seleccionado como VM1-nsg, si la NIC tiene un NSG de subred vigente, la salida mostrará ambos NSG.
4. Puede editar directamente las reglas para los NSG asociados con una subred o una NIC. Para obtener información sobre cómo hacerlo, vea el paso 8 de la sección **Visualización de las reglas de seguridad vigentes para una máquina virtual** de este artículo.

Para leer más acerca de la información que se muestra, vea el paso 6 de la sección **Visualización de las reglas de seguridad vigentes para una máquina virtual** de este artículo.

> [!NOTE]
> Aunque una subred y una NIC cada una solo puede tener un NSG aplicado, un NSG puede estar asociado a varias NIC y varias subredes.
> 
> 

## <a name="considerations"></a>Consideraciones
Tenga en cuenta los puntos siguientes cuando tenga que solucionar problemas de conectividad:

* Las reglas predeterminadas de NSG bloquean el acceso de entrada desde Internet y solo permiten el tráfico entrante de la red virtual. Las reglas deben agregarse explícitamente para permitir el acceso de entrada desde Internet, según sea necesario.
* Si no hay ninguna regla de seguridad de NSG que sea la causa de un error de conectividad de una red de máquina virtual, el problema puede ser debido a:
  * Software de firewall que se esté ejecutando en el sistema operativo de la máquina virtual
  * Rutas configuradas para dispositivos virtuales o para el tráfico local. El tráfico de Internet puede redirigirse a la red local a través de tunelización forzada. Una conexión RDP o SSH desde Internet a su máquina virtual puede no funcionar con esta configuración, dependiendo de cómo el hardware de red local controle este tráfico. Consulte el artículo sobre [solución de problemas de rutas](virtual-network-routes-troubleshoot-powershell.md) para obtener información sobre cómo diagnosticar los problemas de enrutamiento que pueden estar impidiendo el flujo de tráfico de entrada y salida de la máquina virtual. 
* Si ha emparejado redes virtuales, de forma predeterminada, la etiqueta VIRTUAL_NETWORK se expandirá automáticamente para incluir prefijos para redes virtuales emparejadas. Puede ver estos prefijos en la lista **ExpandedAddressPrefix** para solucionar los problemas relacionados con la conectividad de emparejamiento de rede virtuales. 
* Las reglas de seguridad vigentes solo se muestran si hay un NSG asociado con la NIC o subred de máquina virtual. 
* Si no hay ningún NSG asociado a la NIC o subred y tiene una dirección IP pública asignada a la máquina virtual, todos los puertos estarán abiertos para el acceso entrante y saliente. Si la máquina virtual tiene una dirección IP pública, es muy recomendable aplicar los NSG a la subred o NIC.

