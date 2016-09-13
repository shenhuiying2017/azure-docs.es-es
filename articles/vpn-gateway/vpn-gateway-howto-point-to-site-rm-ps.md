<properties 
   pageTitle="Configuración de una conexión de puerta de enlace de VPN de punto a sitio a una red virtual mediante el modelo de implementación de Resource Manager | Microsoft Azure"
   description="Conéctese de forma segura a su instancia de Azure Virtual Network mediante la creación de una conexión de puerta de enlace de VPN de punto a sitio."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc" />

# Configuración de una conexión de punto a sitio a una red virtual mediante PowerShell

> [AZURE.SELECTOR]
- [PowerShell: administrador de recursos](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Clásico](vpn-gateway-point-to-site-create.md)

Una configuración de punto a sitio (P2S) permite crear una conexión segura entre un equipo cliente individual y una red virtual. Una conexión P2S resulta útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual.

Este artículo le guiará a través de la creación de una red virtual con una conexión de punto a sitio mediante el **modelo de implementación de Resource Manager**. Los pasos para hacerlo requieren PowerShell. Actualmente, no se puede crear esta solución integral en el Portal de Azure.

Las conexiones punto a sitio no requieren un dispositivo VPN o una dirección IP pública para que funcionen. Se establece una conexión VPN al iniciar la conexión desde el equipo cliente. Para más información sobre las conexiones de punto a sitio, consulte las [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y [Planeamiento y diseño](vpn-gateway-plan-design.md).



**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modelos de implementación y herramientas para las conexiones de punto a sitio**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Diagrama de punto a sitio](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "De punto a sitio")


## Acerca de esta configuración

En este escenario, creará una red virtual con una conexión de punto a sitio. Las instrucciones también le ayudarán a generar los certificados que se requieren para esta configuración. Una conexión de punto a sitio consta de los siguientes elementos: una red virtual con una puerta de enlace de VPN, un archivo .cer de certificado raíz (clave pública), un certificado de cliente y la configuración de VPN en el cliente.

Se usarán los siguientes valores para esta configuración. Las variables se configurarán en la sección [1](#declare) del artículo. Puede utilizar los pasos como un tutorial y utilizar los valores sin cambiarlos o modificarlos para reflejar su entorno.

- Nombre: **VNet1**, con los espacios de direcciones **192.168.0.0/16** y **10.254.0.0/16**. Tenga en cuenta que puede utilizar más de un espacio de direcciones para una red virtual.
- Nombre de subred: **FrontEnd**, con **192.168.1.0/24**
- Nombre de subred: **BackEnd**, con **10.254.1.0/24**
- Nombre de subred: **GatewaySubnet**, con **192.168.200.0/24**. El nombre de subred *GatewaySubnet* es obligatorio para que funcione la puerta de enlace.
- Grupo de direcciones de clientes de VPN: **172.16.201.0/24**. Los clientes de VPN que se conectan a la red virtual mediante esta conexión de punto a sitio reciben una dirección IP de este grupo.
- Suscripción: si tiene más de una suscripción, compruebe que tiene la correcta.
- Grupo de recursos: **TestRG**
- Ubicación: **Este de EE. UU.**
- Servidor DNS: **Dirección IP** del servidor DNS que desea usar para la resolución de nombres.
- Nombre de GW: **GW**
- Nombre de dirección IP pública: **GWIP**
- VPNType: **RouteBased**


## Antes de comenzar

- Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [beneficios como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/).
	
- Instale los cmdlets de PowerShell de Azure Resource Manager (1.0.2 o posterior). Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell. Cuando trabaje con PowerShell para esta configuración, asegúrese de que está ejecutando como administrador.

## <a name="declare"></a>Parte 1: Inicio de sesión y establecimiento de variables

En esta sección, iniciará sesión y declarará los valores utilizados para esta configuración. Los valores declarados se usan en los scripts de ejemplo. Cambie los valores para reflejar su propio entorno. O bien, use los valores declarados y siga los pasos como si se tratara de un ejercicio.

1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. El cmdlet le pedirá las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de ponerla a disposición para Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenga una lista de las suscripciones de Azure.

		Get-AzureRmSubscription

3. Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Declare las variables que desea utilizar. Use el ejemplo siguiente y sustituya los valores por los suyos propios cuando sea necesario.

		$VNetName  = "VNet1"
		$FESubName = "FrontEnd"
		$BESubName = "Backend"
		$GWSubName = "GatewaySubnet"
		$VNetPrefix1 = "192.168.0.0/16"
		$VNetPrefix2 = "10.254.0.0/16"
		$FESubPrefix = "192.168.1.0/24"
		$BESubPrefix = "10.254.1.0/24"
		$GWSubPrefix = "192.168.200.0/26"
		$VPNClientAddressPool = "172.16.201.0/24"
		$RG = "TestRG"
		$Location = "East US"
		$DNS = "8.8.8.8"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"


## Parte 2: Configuración de una red virtual 


1. Cree un grupo de recursos.

		New-AzureRmResourceGroup -Name $RG -Location $Location

2. Cree las configuraciones de subred para la red virtual con los nombres *FrontEnd*, *BackEnd* y *GatewaySubnet*. Estos prefijos deben formar parte del espacio de direcciones de la red virtual declarado antes.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Creación de la red virtual. El servidor DNS especificado debe poder resolver los nombres de los recursos a los que se conecta. En este ejemplo, se usa una dirección IP pública. Asegúrese de utilizar sus propios valores.
	
		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Especifique las variables de la red virtual que creó.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Solicite una dirección IP pública asignada de forma dinámica. Esta dirección IP es necesaria para que la puerta de enlace funciona correctamente. Más adelante, conectará la puerta de enlace con la configuración de dirección IP de puerta de enlace.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## Parte 3: Incorporación de certificados de confianza

Azure permite autenticar a los clientes que desean conectarse a través de conexiones de punto a sitio mediante certificados. Importe el archivo .cer (la clave pública) de un certificado raíz en Azure. Al agregar un archivo X.509 con codificación Base64 (.cer) a Azure, se está indicando que se confíe en el certificado raíz que representa el archivo.

Si usa una solución empresarial, puede utilizar la cadena de certificados existente. Si no está utilizando una solución de CA de empresa, puede crear un certificado raíz autofirmado. Un método para crear un certificado autofirmado es makecert. Para obtener instrucciones sobre cómo crear un certificado raíz autofirmado mediante *makecert*, consulte [Uso de certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md).

Independientemente de cómo obtenga el certificado, cargue el archivo .cer del certificado en Azure y genere también los certificados de cliente para instalarlos en los clientes que desea conectar. No instale el certificado autofirmado directamente en el cliente. Puede generar certificados de cliente más adelante, en la sección [Configuración de cliente](#cc) de este artículo.
		
Puede agregar hasta 20 certificados de confianza a Azure. Para obtener la clave pública, exporte el certificado como archivo X.509 con codificación Base64 (.cer). Una forma de exportar a .cer consiste en abrir **certmger.msc** y buscar el certificado en Personal/Certificados. Haga clic con el botón derecho y exporte sin la clave privada como archivo "X.509 con codificación Base 64 (.cer)". Apunte la ruta de acceso donde exportó el archivo .cer. Se muestra un ejemplo de cómo obtener la representación de cadena Base64 del certificado.

Agregue el certificado de confianza a Azure. En este paso, tiene que utilizar la ruta de su archivo .cer. Preste especial atención a la variable $P2SRootCertName = "ARMP2SRootCert.cer" que se estableció en la parte 1 de este artículo. Si el nombre del certificado es diferente, ajuste la variable según corresponda.
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## Parte 4: Creación de la puerta de enlace de VPN

Configure y cree la puerta de enlace de red virtual para la red virtual. *-GatewayType* debe ser **Vpn** y, *VpnType*, **RouteBased**. Esto puede tardar hasta 45 minutos en completarse.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Parte 5: Descarga del paquete de configuración de cliente de VPN

Los clientes que se conectan a Azure mediante una conexión de punto a sitio deben tener un certificado de cliente y un paquete de configuración de cliente de VPN instalado. Los paquetes de configuración de cliente de VPN están disponibles para los clientes de Windows. El paquete de cliente de VPN contiene información para configurar el software de cliente de VPN que está integrado en Windows y que es específico de la VPN a la que desea conectarse. El paquete no instala software adicional. Consulte las [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para más información.

1. Una vez creada la puerta de enlace, utilice el siguiente ejemplo para descargar el paquete de configuración de cliente:

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. El cmdlet de PowerShell devuelve un vínculo de dirección URL. Esto es un ejemplo del aspecto que tiene la dirección URL en cuestión:

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copie y pegue este vínculo en un explorador web para descargar el paquete. A continuación, instale el paquete en el equipo cliente.

4. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. Podrá ver la conexión en la lista. Mostrará el nombre de la red virtual a la que se conectará y tendrá un aspecto similar al siguiente:

	![Cliente de VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Cliente de VPN")

## <a name="cc"></a>Parte 6: Generación de un certificado de cliente

A continuación, genere los certificados de cliente. Puede generar un certificado único para cada cliente que se vaya a conectar o puede usar el mismo en varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado si es necesario. De lo contrario, si todos usan el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, tendrá que generar e instalar nuevos certificados para todos los clientes que usen el certificado para autenticarse.

- Si usa una solución de certificado de empresa, genere un certificado de cliente con el formato de valor de nombre común "nombre@suDominio.com", en lugar del formato NetBIOS "DOMINIO\\nombreDeUsuario'".

- Si va a usar un certificado autofirmado, consulte [Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

## Parte 7: Instalación del certificado de cliente

Instale un certificado de cliente en cada equipo que desee conectar a la red virtual. Se necesita un certificado de cliente para la autenticación. Puede automatizar la instalación del certificado de cliente o instalarlo manualmente. En los pasos siguientes aprenderá a exportar e instalar el certificado de cliente de forma manual.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Es un archivo *.pfx*. Asegúrese de anotar o recordar la contraseña (clave) que establezca para este certificado.
3. Copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Escriba la contraseña cuando se le solicite. No modifique la ubicación de instalación.


## Parte 8: Conexión a Azure

1. Para conectarse a su red virtual, en el equipo cliente, vaya a las conexiones VPN y ubique la que creó. Tiene el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Si esto ocurre, haga clic en **Continuar** para usar privilegios elevados.

2. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado**, compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

	![Cliente de VPN 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Conexión de cliente de VPN")

3. La conexión debería establecerse.

	![Cliente de VPN 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Conexión de cliente de VPN 2")

## Parte 9: Comprobación de la conexión

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecute *ipconfig/all*.

2. Vea los resultados. Observe que la dirección IP que recibió es una de las direcciones dentro del grupo de direcciones de cliente de VPN punto a sitio que especificó en la configuración. Los resultados deben ser algo parecido a esto:
    
		PPP adapter VNet1:
			Connection-specific DNS Suffix .:
			Description.....................: VNet1
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Procedimiento para agregar o quitar un certificado raíz

Los certificados se usan para autenticar a los clientes VPN para VPN de punto a sitio. Los siguientes pasos le enseñan a agregar y quitar certificados raíz. Al agregar un archivo X.509 con codificación Base64 (.cer) a Azure, se está indicando que se confíe en el certificado raíz que representa el archivo.

Puede agregar o quitar certificados raíz de confianza mediante PowerShell o en el Portal de Azure. Si desea hacer esto mediante el Portal de Azure, vaya a la **puerta de enlace de red virtual > Configuración > Configuración de punto a sitio > Certificados raíz**. Los pasos siguientes le guiarán a través de estas tareas mediante PowerShell.

### Adición de un certificado raíz de confianza

Puede agregar hasta 20 archivos .cer de certificado raíz de confianza a Azure. Siga los siguientes pasos para agregar un certificado raíz.

1. Cree y prepare el nuevo certificado raíz que agregará a Azure. Exporte la clave pública como archivo X.509 con codificación Base64 (.cer) y ábralo con un editor de texto. A continuación, copie únicamente la sección que se muestra a continuación.
 
	Copie los valores como se muestra en el ejemplo siguiente.

	![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificado")
	
2. En el ejemplo siguiente, especifique el nombre del certificado y la información de clave como una variable. Reemplace la información por la suya propia.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Agregue el nuevo certificado raíz. Solo puede agregar un certificado raíz a la vez.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Puede comprobar que el nuevo certificado se agregó correctamente mediante el siguiente cmdlet.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

### Eliminación de un certificado raíz de confianza

Puede quitar un certificado raíz de confianza de Azure. Cuando se quita un certificado de confianza, los certificados de cliente que se generaron a partir del certificado raíz ya no podrán conectarse a Azure mediante la conexión de punto a sitio. Si desea que los clientes se conecten, deben instalar un nuevo certificado de cliente que se genere a partir de un certificado de confianza en Azure.

1. Para quitar un certificado raíz de confianza, modifique el ejemplo siguiente:

	Declare las variables.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Quite el certificado.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Use el siguiente cmdlet para comprobar que el certificado se quitó correctamente.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

## Administración de la lista de certificados de cliente revocados

Puede revocar certificados de cliente. La lista de revocación de certificados permite denegar de forma selectiva la conectividad de punto a sitio basada en certificados de cliente individuales. Si quita un archivo .cer de certificado de raíz de Azure, se revoca el acceso para todos los certificados de cliente generados y firmados con el certificado raíz revocado. Si desea revocar un certificado de cliente concreto y no el de raíz, puede hacerlo. De ese modo, los demás certificados generados a partir del certificado raíz seguirán siendo válidos. Lo más habitual es usar el certificado raíz para administrar el acceso a nivel de equipo u organización, mientras que los certificados de cliente revocados se usan para un control de acceso específico para usuarios individuales.

### Revocar un certificado de cliente

1. Obtener la huella digital del certificado de cliente que se va a revocar.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Agregar la huella digital a la lista de huellas digitales revocadas.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Compruebe que la huella digital se agregó a la lista de revocación de certificados. Debe agregar una huella digital a la vez.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Restablecer un certificado de cliente

Puede restablecer un certificado de cliente quitando la huella digital de la lista de certificados de cliente revocados.

1.  Quite la huella digital de la lista de huellas digitales de certificados de cliente revocados.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Compruebe si la huella digital se quita de la lista revocada.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Pasos siguientes

Puede agregar una máquina virtual a la red virtual. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para ver los pasos.

<!---HONumber=AcomDC_0907_2016-->