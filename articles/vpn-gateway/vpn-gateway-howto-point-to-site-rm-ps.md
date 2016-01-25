<properties 
   pageTitle="Configuración de una conexión de puerta de enlace de VPN punto a sitio a una Red virtual de Azure | Microsoft Azure"
   description="Conéctese de forma segura a la Red virtual de Azure mediante la creación de una conexión VPN de punto a sitio. Esta configuración es útil cuando se necesita una conexión entre locales desde una ubicación remota sin usar un dispositivo VPN y se puede utilizar con configuraciones de red híbrida. En este artículo, se incluyen instrucciones de PowerShell para redes virtuales que se crearon con el modelo de implementación del Administrador de recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="cherylmc" />

# Configuración de una conexión punto a sitio a una red virtual mediante PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [PowerShell - Classic](vpn-gateway-point-to-site-create.md)

Una configuración punto a sitio permite crear una conexión segura a la red virtual desde un equipo cliente, de forma individual. Se establece una conexión VPN al iniciar la conexión desde el equipo cliente. La conexión punto a sitio es una solución excelente cuando desea conectarse a la red virtual desde una ubicación remota, como desde una casa o una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual. Las conexiones punto a sitio no requieren un dispositivo VPN o una dirección IP pública para que funcionen. Para obtener más información acerca de las conexiones punto a sitio, consulte [Preguntas frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y [Acerca de las conexiones entre locales](vpn-gateway-cross-premises-options.md).

Este artículo se aplica a las redes virtuales y a las puertas de enlace de VPN creadas con el modelo de implementación del **Administrador de recursos de Azure**. Si desea configurar una conexión punto a sitio para una red virtual que se creó con Administración de servicios (también conocido como el modelo de implementación clásica), consulte [este artículo](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Acerca de esta configuración

En este escenario, creará una red virtual con una conexión punto a sitio. Una conexión punto a sitio consta de los siguientes elementos: una red virtual con una puerta de enlace, un archivo .cer de certificado raíz cargado, un certificado de cliente y la configuración de VPN en el lado cliente.

Utilizaremos los siguientes valores para esta configuración:

- Nombre = **TestVNet**, con los espacios de direcciones 192.168.0.0/16 y 10.254.0.0/16. Tenga en cuenta que puede utilizar más de un espacio de direcciones para una red virtual.
- Nombre de subred = **FrontEnd**, con 192.168.1.0/24
- Nombre de subred = **BackEnd**, con 10.254.1.0/24
- Nombre de subred = **GatewaySubnet**, con 192.168.200.0/24. El nombre de subred *GatewaySubnet* es obligatorio para que funcione la puerta de enlace. 
- Grupo de direcciones de clientes de VPN: 172.16.201.0/24. Los clientes de VPN que se conectan a la red virtual con esta conexión punto a sitio recibirán una dirección IP de este grupo.
- Suscripción = Si tiene más de una suscripción, compruebe que tiene la correcta.
- Grupo de recursos = **TestRG**
- Ubicación = **Este de EE. UU.**
- Servidor DNS = Dirección IP del servidor DNS que desea utilizar para la resolución de nombres.
- Nombre de GW = **GW**
- Nombre de dirección IP pública = **GWIP**
- VpnType = **RouteBased**


## Antes de comenzar

Compruebe que tiene una suscripción a Azure y que instaló los cmdlets de Azure PowerShell necesarios para esta configuración (1.0.2 o posterior). Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).
	
**Acerca de la instalación de los módulos de cmdlets de PowerShell**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## Configurar una conexión punto a sitio para Azure

1. En la consola de PowerShell, inicie sesión en su cuenta de Azure. El cmdlet le pedirá las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de que esté disponible para Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenga una lista de las suscripciones de Azure.

		Get-AzureRmSubscription

3. Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. En esta configuración, las siguientes variables de PowerShell se declaran con los valores que desea utilizar. Los valores declarados se utilizan en los scripts de ejemplo. Declare los valores que desea utilizar. Use el ejemplo siguiente y sustituya los valores que aparecen con sus valores cuando sea necesario.

		$VNetName  = "TestVNet"
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

5. Cree un nuevo grupo de recursos.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Cree las configuraciones de subred para la red virtual con los nombres *FrontEnd*, *BackEnd* y *GatewaySubnet*. Tenga en cuenta que estos prefijos deben ser parte del espacio de direcciones de la red virtual declarados anteriormente.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Creación de la red virtual. Tenga en cuenta que el servidor DNS especificado debe ser un servidor DNS que pueda resolver los nombres de los recursos a los que se conecta. En este ejemplo, usamos una dirección IP pública, pero aquí querrá ingresar sus propios valores.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Especifique las variables de la red virtual que acaba de crear.

		$vnet   = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Solicite una dirección IP pública asignada de forma dinámica. Esta dirección IP es necesaria para que la puerta de enlace funciona correctamente. Más adelante, conectará la puerta de enlace con la configuración de dirección IP de puerta de enlace.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Cargue un archivo .cer de certificado raíz a Azure. Puede utilizar un certificado raíz desde el entorno de certificados empresarial o puede usar un certificado raíz autofirmado. Puede cargar hasta 20 certificados raíz. Si desea obtener instrucciones sobre cómo crear un certificado raíz autofirmado con *makecert*, consulte la sección que aparece al final de este artículo. Tenga en cuenta que el archivo .cer no puede contener la clave privada del certificado raíz.
	
	A continuación, podemos ver un ejemplo de cómo se ve. La parte difícil de cargar los datos del certificado público es que debe copiar y pegar toda la cadena, sin espacios. De lo contrario, la carga no funcionará. En este paso, deberá utilizar su propio archivo .cer de certificado. No intente copiar y pegar el ejemplo que aparece a continuación.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Cree la puerta de enlace de red virtual para su red virtual. GatewayType debe ser Vpn y VpnType debe ser RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configuración de cliente

Cada cliente que se conecta a Azure con la conexión punto a sitio debe cumplir con lo siguiente: El cliente de VPN debe estar configurado para conectarse y el cliente debe tener instalado un certificado de cliente. Los paquetes de configuración de cliente de VPN están disponibles para los clientes de Windows. Consulte las [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obtener más información.

1. Descargue el paquete de configuración de cliente de VPN. En este paso, utilice el siguiente ejemplo para descargar el paquete de configuración de cliente.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	El cmdlet de PowerShell devolverá un vínculo de dirección URL. Copie y pegue este vínculo en un explorador web para descargar el paquete a su equipo. A continuación, aparece un ejemplo del aspecto que tendrá la dirección URL en cuestión.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Genere e instale los certificados de cliente (*.pfx) que se creen a partir del certificado raíz en los equipos cliente. Puede utilizar cualquier método de instalación que le resulte cómodo. Si utiliza un certificado raíz autofirmado y no sabe cómo hacerlo, puede consultar las instrucciones que aparecen al final de este artículo.

3. Para conectarse a su red virtual, en el equipo cliente, navegue a las conexiones VPN y ubique la conexión VPN que acaba de crear. Tendrá el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Si esto ocurre, haga clic en **Continuar** para utilizar privilegios elevados.

4. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado**, compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

5. La conexión debería establecerse. Para comprobar la conexión, utilice el siguiente procedimiento.

## Comprobar la conexión

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecute *ipconfig/all*.

2. Vea los resultados. Observe que la dirección IP que recibió es una de las direcciones dentro del grupo de direcciones de cliente de VPN punto a sitio que especificó en la configuración. Los resultados deben ser algo parecido a esto:
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Para crear un certificado raíz autofirmado con makecert

Makecert es una forma de crear un certificado raíz autofirmado. Los pasos siguientes le guiarán a través del procedimiento.

1. En un equipo con Windows 10, descargue e instale el SDK de Windows 10 desde [este vínculo](https://dev.windows.com/es-ES/downloads/windows-10-sdk).

2. Después de la instalación, la utilidad makecert.exe se encuentra en: C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Ejemplo:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. A continuación, cree e instale un certificado raíz en el almacén de certificados personal de su equipo. En el ejemplo que aparece a continuación, se crea un archivo *.cer* correspondiente que cargará más adelante. Ejecute como administrador el siguiente comando, donde *RootCertificateName* es el nombre que desea utilizar para el certificado.

	Nota: Si ejecuta el ejemplo siguiente sin realizar ningún cambio, el resultado será un certificado raíz y el archivo *RootCertificateName.cer* correspondiente. Puede encontrar el archivo .cer en el directorio desde donde ejecutó el comando. El certificado estará en sus Certificados, en Usuario actual\\Personal\\Certificados.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Puesto que ha creado un certificado raíz desde el que se generarán los certificados de cliente, puede que desee exportar este certificado junto con su clave privada y guardarlo en una ubicación segura donde se pueda recuperar.

## Para generar un certificado de cliente desde un certificado raíz autofirmado

Los pasos que aparecen a continuación le ayudarán a generar un certificado de cliente desde un certificado raíz autofirmado y, luego, exportar e instalar el certificado de cliente.

### Generación de un certificado de cliente

Los pasos siguientes le guiarán por una forma de generar un certificado de cliente desde un certificado raíz autofirmado. Puede generar varios certificados de cliente desde el mismo certificado raíz. Luego, cada certificado de cliente se puede exportar e instalar en el equipo cliente.

1. En el mismo equipo que usó para crear el certificado raíz autofirmado, abra un símbolo del sistema como administrador.
2. Cambie el directorio a la ubicación donde desea guardar el archivo de certificado de cliente. *RootCertificateName* hace referencia al certificado raíz autofirmado que ha generado. Si ejecuta el ejemplo siguiente (cambiando el NombreCertificadoRaíz por el nombre de su certificado raíz), el resultado será un certificado de cliente denominado "NombreCertificadoCliente" en el almacén de certificados personales.
3. Escriba el siguiente comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos los certificados se almacenan en su almacén de certificados, en Usuario actual\\Personal\\Certificados, de su equipo. Puede generar tantos certificados de cliente como sean necesarios según este procedimiento.

### Exportar e instalar un certificado de cliente

Es un paso obligatorio la instalación de un certificado de cliente en cada equipo que desee conectar a la red virtual. Los pasos siguientes le guiarán por la instalación manual del certificado de cliente.

1. Para exportar un certificado de cliente, utilice *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**. Se abrirá el asistente para la exportación de certificados.
2. En el asistente, haga clic en **Siguiente**, seleccione **Sí, exportar la clave privada** y, luego, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, puede dejar seleccionados los valores predeterminados. A continuación, haga clic en **Siguiente**.  
4. En la página **Seguridad**, debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. Luego, haga clic en **Siguiente**.
5. En **Archivo que se va a exportar**, **vaya** a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
6. Haga clic en **Finalizar** para exportar el certificado.	
3. Ubique el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje la **Ubicación del almacén** como **Usuario actual** y, luego, haga clic en **Siguiente**.
4. En la página **Archivo** que se va a importar, no haga ningún cambio. Haga clic en **Siguiente**.
5. En la página **Protección de clave privada**, ingrese la contraseña del certificado, si usó una, o compruebe que la entidad de seguridad que instala el certificado es correcta y, luego, haga clic en **Siguiente**.
6. En la página **Almacén de certificados**, deje la ubicación predeterminada y, a continuación, haga clic en **Siguiente**.
7. Haga clic en **Finalizar** En la **Advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. El certificado se importó correctamente.

## Pasos siguientes

Puede agregar una máquina virtual a la red virtual. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-tutorial.md) para ver los pasos.

<!---HONumber=AcomDC_0114_2016-->