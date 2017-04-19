---
title: "Conexión de la red local a una red virtual de Azure: VPN de sitio a sitio (clásico): Portal | Microsoft Docs"
description: "Pasos para crear una conexión de IPsec desde la red local a una red virtual de Azure a través de la red pública de Internet. Estos pasos le ayudarán a crear una conexión de VPN Gateway de sitio a sitio entre locales mediante el portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 81eca4b41b6a0726e5fcf851074bfb7dfca16fb8
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Creación de una conexión de sitio a sitio mediante Azure Portal (clásico)

Una conexión de puerta de enlace de VPN de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada y que no se encuentre detrás de NAT. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales.

![Diagrama de la conexión entre locales de VPN Gateway de sitio a sitio](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

Este artículo lo guía por la creación de una red virtual y una conexión VPN Gateway de sitio a sitio a la red local mediante el modelo de implementación clásico y Azure Portal. También puede crear esta configuración para el modelo de implementación de Resource Manager, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clásico - Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clásico - Portal clásico](vpn-gateway-site-to-site-create.md)
>
>

#### <a name="additional-configurations"></a>Configuraciones adicionales
Si desea conectar las redes virtuales entre sí pero no está creando una conexión a una ubicación local, consulte [Configurar una conexión de red virtual a red virtual en el Portal de Azure clásico](virtual-networks-configure-vnet-to-vnet-connection.md). Si desea añadir una conexión de sitio a sitio a una red virtual que ya tiene una conexión, consulte [Adición de una conexión S2S a VNet con una conexión de puerta de enlace existente](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Antes de empezar

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes:

* Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md). Si no conoce la configuración de su dispositivo VPN o los intervalos de direcciones IP ubicados en la configuración de la red local, necesita trabajar con alguien que pueda proporcionarle estos detalles.
* Una dirección IP IPv4 pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.
* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](http://azure.microsoft.com/pricing/free-trial).
* Actualmente, se requiere PowerShell para especificar la clave compartida y crear la conexión de puerta de enlace VPN. Instale la versión más reciente de los cmdlets de PowerShell para Azure Service Management. Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs). Cuando trabaje con PowerShell para esta configuración, asegúrese de que está ejecutando como administrador. 

> [!NOTE]
> Al configurar una conexión de sitio a sitio, una dirección IP IPv4 pública es necesaria para el dispositivo VPN.
>

### <a name="values"></a>Valores de configuración de ejemplo para este ejercicio
Si utiliza este procedimiento para practicar, puede usar también estos valores de configuración de ejemplo:

* **Nombre de red virtual:** TestVNet1
* **Espacio de direcciones:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (opcional para este ejercicio)
* **Subredes:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (opcional para este ejercicio)
* **GatewaySubnet:** 10.11.255.0/27
* **Grupo de recursos:** TestRG1
* **Ubicación:** este de EE. UU.
* **Servidor DNS:** 8.8.8.8 (opcional para este ejercicio)
* **Nombre del sitio local:** Site2

## <a name="CreatVNet"></a>1. Crear una red virtual

Cuando se crea una red virtual que se usará para una conexión S2S, debe asegurarse de que los espacios de direcciones que especifique no se superponen con ninguno de los espacios de direcciones de cliente de los sitios locales a los que desea conectarse. Si tiene subredes superpuestas, la conexión no funcionará correctamente.

* Si ya dispone de una red virtual, compruebe que la configuración sea compatible con el diseño de la puerta de enlace de VPN. Preste especial atención a las subredes que se pueden superponer con otras redes. 

* Si no tiene una red virtual, créela. Las capturas de pantalla se proporcionan a modo de ejemplo. Asegúrese de reemplazar los valores por los suyos.

### <a name="to-create-a-virtual-network"></a>Creación de una red virtual

1. Desde un explorador, vaya al [Portal de Azure](http://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Nuevo**. En el campo **Buscar en el Marketplace**, escriba "Red virtual". En la lista de resultados, busque **Red virtual** y haga clic en él para abrir la hoja **Red virtual**.

    ![Hoja Buscar red virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Cerca de la parte inferior de la hoja Red virtual, en la lista **Seleccionar un modelo de implementación**, seleccione **Clásico** y haga clic en **Crear**.

    ![Seleccionar modelo de implementación](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. En la hoja **Crear red virtual** , configure los valores de la red virtual. En esta hoja, se agrega el primer espacio de direcciones y un intervalo de direcciones de subred únicas. Cuando termine de crear la red virtual, puede volver atrás y agregar espacios de direcciones y subredes adicionales.

    ![Hoja Crear red virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Hoja Crear red virtual")
5. Compruebe que la **Suscripción** es la correcta. Puede cambiar las suscripciones mediante la lista desplegable.
6. Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente, o bien cree uno nuevo escribiendo un nombre para el nuevo grupo. Para más información acerca de los grupos de recursos, visite [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. A continuación, seleccione la configuración de **Ubicación** de la red virtual. La ubicación determina dónde van a residir los recursos que se implementen en esta red virtual.
8. Seleccione **Anclar al panel** si desea encontrar la red virtual fácilmente en el panel y, luego, haga clic en **Crear**.

    ![Anclar al panel](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Anclar al panel")
9. Después de hacer clic en "Crear", aparece un icono en el panel que refleja el progreso de la red virtual. El icono cambiará a medida que se vaya creando la red virtual.

    ![Icono de creación de red virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Creación de red virtual")

Una vez que se haya creado la red virtual, verá **Creado** en **Estado** en la página Redes del Portal de Azure clásico.

## <a name="additionaladdress"></a>2. Incorporación de un espacio de direcciones adicional

Después de crear la red virtual, puede agregar un espacio de direcciones adicional. La incorporación de un espacio de direcciones adicional no es un paso necesario de la configuración de S2S, pero si necesita varios espacios de direcciones, siga estos pasos:

1. Busque las redes virtuales en el portal.
2. En la hoja de su red virtual, en la sección **Configuración**, haga clic en **Espacio de direcciones**.
3. En la hoja Espacio de direcciones, haga clic en **+Agregar** y especifique el espacio de direcciones adicional.
 
## <a name="dns"></a>3. Especificación de un servidor DNS
La configuración de DNS no es un paso necesario de la configuración de S2S, pero el servidor DNS se necesita para resolver nombres.

Después de crear la red virtual, puede agregar la dirección IP de un servidor DNS para controlar la resolución de nombres. Abra la configuración de su red virtual, haga clic en los servidores DNS y agregue la dirección IP del servidor DNS que desea utilizar para la resolución de nombres. Mediante este valor no se crea un servidor DNS. En la configuración de ejemplo, usamos un servidor DNS público. Normalmente, querrá usar un servidor DNS privado. Asegúrese de agregar un servidor DNS con el que se puedan comunicar sus recursos.

1. Busque las redes virtuales en el portal.
2. En la hoja de su red virtual, en la sección **Configuración**, haga clic en **Servidores DNS**.
3. Agregue un servidor DNS.
4. Para guardar la configuración, haga clic en **Guardar** en la parte superior de la página.
 
## <a name="localsite"></a>4. Configuración del sitio local

Normalmente, sitio local suele hacer referencia a la ubicación local. Contiene la dirección IP del dispositivo VPN al que se creará una conexión y los intervalos de direcciones IP que se enrutarán a través de la puerta de enlace VPN en el dispositivo VPN.

1. En el portal, navegue a la red virtual para la que desea crear una puerta de enlace.
2. En la hoja de la red virtual, en la hoja **Introducción**, en la sección Conexiones VPN, haga clic en **Puerta de enlace** para abrir la hoja **Nueva conexión VPN**.

    ![Haga clic aquí para configurar la puerta de enlace](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Haga clic aquí para configurar la puerta de enlace")
3. En la hoja **Nueva conexión VPN**, seleccione **De sitio a sitio**.

    ![Haga clic de sitio a sitio](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "de sitio a sitio")
4. Haga clic en **Sitio local: Configurar valores obligatorios** para abrir la hoja **Sitio local**. Configure las opciones y haga clic en **Aceptar** para guardar la configuración.
    - **Nombre:** cree un nombre para el sitio local para que sea fácil de identificar.
    - **Dirección IP de puerta de enlace VPN:** es la dirección IP pública del dispositivo VPN para la red local. El dispositivo VPN requiere una dirección IP IPv4 pública. Especifique una dirección IP pública válida para el dispositivo VPN al que desea conectarse. No puede encontrarse detrás de NAT y debe estar al alcance de Azure.
    - **Espacio de direcciones de cliente:** lista de los intervalos de direcciones IP que desea enrutar a la red local a través de esta puerta de enlace. Puede agregar varios intervalos de espacios de direcciones. Asegúrese de que los intervalos que especifique aquí no se superponen con los intervalos de otras redes a la que se conecta su red virtual, ni con los intervalos de direcciones de la propia red virtual.

    ![Sitio local](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configuración de sitio local")

## <a name="gatewaysubnet"></a>5. Configuración de la subred de puerta de enlace

Debe crear una subred de puerta de enlace para la puerta de enlace VPN. La subred de puerta de enlace contiene las direcciones IP que usan los servicios de VPN Gateway.

1. En la hoja **Nueva conexión VPN**, seleccione la casilla **Crear puerta de enlace inmediatamente**. Aparecerá la hoja "Configuración de puerta de enlace opcional". Si no selecciona la casilla, no verá la hoja para configurar la subred de puerta de enlace.

    ![Configuración de puerta de enlace: subred, tamaño, tipo de enrutamiento](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Configuración de puerta de enlace: subred, tamaño, tipo de enrutamiento")
2. Haga clic en **Configuración de puerta de enlace opcional: subred, tamaño y tipo de enrutamiento** para abrir la hoja **Configuración de puerta de enlace**.
3. En la hoja **Configuración de puerta de enlace**, haga clic en **Subred: Configurar los valores obligatorios** para abrir la hoja **Agregar subred**.

    ![Configuración de puerta de enlace: subred de puerta de enlace](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Configuración de puerta de enlace: subred de puerta de enlace")
4. En la hoja **Agregar subred**, agregue la subred de puerta de enlace. Al agregar la subred de puerta de enlace, si es posible, es mejor crear una subred de puerta de enlace con un bloque CIDR de /28 o /27. Esto garantizará que tiene suficientes direcciones IP para adaptarse a futuros requisitos de configuración adicionales.  Haga clic en **Aceptar** para guardar la configuración.

    ![Agregar subred de puerta de enlace](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Agregar subred de puerta de enlace")

## <a name="sku"></a>6. Especificación del tipo de VPN y SKU
1. Seleccione el **tamaño** de la puerta de enlace. Es la SKU de la puerta de enlace que va a usa para crear la puerta de enlace de red virtual. En el portal, 'Default SKU' = **Basic**. Para más información acerca de las SKU de puerta de enlace, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Seleccionar tipo de VPN y SKU](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Seleccionar tipo de VPN y SKU")
2. Seleccione el **tipo de enrutamiento** de su puerta de enlace. Esto también se conoce como tipo de VPN. Es importante seleccionar el tipo de puerta de enlace correcto porque la puerta de enlace no se puede convertir de un tipo a otro. El dispositivo VPN debe ser compatible con el tipo de enrutamiento que seleccione. Para más información sobre el tipo de VPN, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Verá que muchos artículos hacen referencia a los tipos de VPN 'RouteBased' y 'PolicyBased'. 'Dynamic' corresponde a 'RouteBased' y 'Static' corresponde a 'PolicyBased'.
3. Haga clic en **Aceptar** para guardar la configuración.
4. En la hoja **Nueva conexión VPN**, haga clic en **Aceptar** en la parte inferior de la hoja para empezar a crear la puerta de enlace de red virtual. Esta operación puede tardar hasta 45 minutos en completarse.

## <a name="vpndevice"></a>7. Configurar el dispositivo VPN

Las conexiones de sitio a sitio a una red local requieren un dispositivo VPN. Aunque no se proporcionan todos los pasos de configuración para todos los dispositivos VPN, se puede encontrar información en los siguientes vínculos útiles:

- Para obtener más información sobre dispositivos VPN compatibles, vea [Dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- Para obtener vínculos a los valores de configuración de dispositivo, vea [Dispositivos VPN validados](vpn-gateway-about-vpn-devices.md#devicetable). Estos vínculos se proporcionan dentro de lo posible. Siempre es mejor ponerse en contacto con el fabricante del dispositivo para obtener la información de configuración más reciente.
- Para obtener información sobre cómo modificar los ejemplos de configuración de dispositivo, vea [Edición de ejemplos](vpn-gateway-about-vpn-devices.md#editing).
- Para los parámetros de IPsec/IKE, vea [Parámetros](vpn-gateway-about-vpn-devices.md#ipsec).
- Antes de configurar el dispositivo VPN, compruebe si hay [problemas conocidos de compatibilidad de dispositivos](vpn-gateway-about-vpn-devices.md#known) para el dispositivo VPN que desea usar.

Al configurar el dispositivo VPN, necesitará los elementos siguientes:

- La dirección IP pública de la puerta de enlace de red virtual. Para encontrarla, vaya a la hoja **Introducción** de la red virtual.
- Una clave compartida. Se trata de la misma clave compartida que se especifica al crear la conexión VPN de sitio a sitio. En nuestros ejemplos, se utiliza una clave compartida muy básica. Debe generar una clave más compleja para su uso.

## <a name="CreateConnection"></a>8. Creación de la conexión
En este paso, se establece la clave compartida y se crea la conexión. La clave que se establezca debe ser la misma que se usó en la configuración del dispositivo VPN.

> [!NOTE]
> Actualmente, este paso no está disponible en Azure Portal. Debe usar la versión para Service Management (SM) de los cdmlets de Azure PowerShell.                                        >
>

### <a name="step-1-connect-to-your-azure-account"></a>Paso 1. Conexión a la cuenta de Azure

1. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta. Use el siguiente ejemplo para conectarse:

  ```powershell
  Login-AzureRmAccount
  ```
2. Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Si tiene varias suscripciones, seleccione la que quiera usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Agregue la versión para SM de los cmdlets de PowerShell.

  ```powershell
  Add-AzureAccount
  ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Paso 2: Establecimiento de la clave compartida y creación de la conexión

Al trabajar con PowerShell y el modelo de implementación clásico, a veces los nombres de recursos en el portal no son los nombres que Azure espera ver cuando se usa PowerShell. Los pasos siguientes ayudan a exportar el archivo de configuración de red para obtener los valores exactos de los nombres.

1. Cree un directorio en el equipo y, a continuación, exporte el archivo de configuración de red al directorio. En este ejemplo, se exporta el archivo de configuración de red a C:\AzureNet.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Abra el archivo de configuración de red con un editor xml y compruebe los valores de “LocalNetworkSite name” y “VirtualNetworkSite name”. Modifique el ejemplo para que refleje los valores. Al especificar el nombre que contiene espacios, escriba el valor entre comillas simples.

3. Establezca la clave compartida y cree la conexión. “-SharedKey” es un valor que puede generar y especificar. En el ejemplo, hemos usado “abc123” pero puede generar y usar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que se especificó al configurar el dispositivo VPN.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
  -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
  ```
Cuando se crea la conexión, el resultado es: **Status: Successful**.

## <a name="verify"></a>9. Comprobación de la conexión

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.


