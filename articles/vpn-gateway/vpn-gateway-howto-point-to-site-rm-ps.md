---
title: "Conexión de un equipo a una red virtual de Azure mediante la conectividad de punto a sitio: PowerShell | Microsoft Docs"
description: "Conecte de forma segura un equipo a su instancia de Azure Virtual Network mediante la creación de una conexión de VPN Gateway de punto a sitio."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8cbf4e8ec2b8904d16c6a74b40cbf6d2ec6a1330
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configuración de una conexión de punto a sitio a una red virtual mediante PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clásico: Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Una configuración de punto a sitio (P2S) permite crear una conexión segura entre un equipo cliente individual y una red virtual. P2S es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Las conexiones de punto a sitio son útiles cuando desea conectarse a su red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si tiene pocos clientes que necesiten conectarse a una red virtual. Las conexiones P2S no requieren un dispositivo VPN ni una dirección IP pública. Para establecer la conexión VPN desde el equipo cliente.

Este artículo le guiará a través de la creación de una red virtual con una conexión de punto a sitio mediante el modelo de implementación de Resource Manager con PowerShell. Para más información sobre las conexiones de punto a sitio, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Métodos y modelos de implementación para conexiones P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los dos modelos de implementación y los métodos de implementación disponible para las configuraciones de P2S. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flujo de trabajo básico
![Conexión de un equipo a una red virtual de Azure: diagrama de conexión de punto a sitio](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

En este escenario, creará una red virtual con una conexión punto a sitio. Las instrucciones también le ayudarán a generar los certificados que se requieren para esta configuración. Una conexión de punto a sitio consta de los siguientes elementos: una red virtual con una puerta de enlace de VPN, un archivo .cer de certificado raíz (clave pública), un certificado de cliente y la configuración de VPN en el cliente. 

Se usarán los siguientes valores para esta configuración. Las variables se configurarán en la sección [1](#declare) del artículo. Puede utilizar los pasos como un tutorial y utilizar los valores sin cambiarlos o modificarlos para reflejar su entorno. 

### <a name="example"></a>Valores del ejemplo
* **Nombre: VNet1**
* **Espacio de direcciones: 192.168.0.0/16** y **10.254.0.0/16**<br>En este ejemplo, se utiliza más de un espacio de direcciones para ilustrar que esta configuración funcionará con varios espacios de direcciones. Sin embargo, para esta configuración no se necesitan varios espacios de direcciones.
* **Nombre de subred: FrontEnd**
  * **Intervalo de direcciones de subred: 192.168.1.0/24**
* **Nombre de subred: BackEnd**
  * **Intervalo de direcciones de subred: 10.254.1.0/24**
* **Nombre de subred: GatewaySubnet**<br>El nombre de subred *GatewaySubnet* es obligatorio para que VPN Gateway funcione.
  * **Intervalo de direcciones de GatewaySubnet: 192.168.200.0/24** 
* **Grupo de direcciones de clientes de VPN: 172.16.201.0/24**<br>Los clientes de VPN que se conectan a la red virtual mediante esta conexión de punto a sitio reciben una dirección IP del grupo de clientes de VPN.
* **Suscripción:** si tiene más de una suscripción, compruebe que usa la correcta.
* **Grupo de recursos: TestRG**
* **Ubicación: este de EE. UU.**
* **Servidor DNS: dirección IP** del servidor DNS que desea usar para la resolución de nombres.
* **Nombre de puerta de enlace: Vnet1GW**
* **Nombre IP pública: VNet1GWPIP**
* **VPNType: RouteBased**

## <a name="before-beginning"></a>Antes de comenzar
* Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial).
* Instale la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar los cmdlets de PowerShell. Cuando trabaje con PowerShell para esta configuración, asegúrese de que está ejecutando como administrador. 

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
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="ConfigureVNet"></a>Parte 2: Configuración de una red virtual
1. Cree un grupo de recursos.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Cree las configuraciones de subred para la red virtual con los nombres *FrontEnd*, *BackEnd* y *GatewaySubnet*. Estos prefijos deben formar parte del espacio de direcciones de la red virtual que declaró anteriormente.
   
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


## <a name="Certificates"></a>Parte 3: Certificados
Azure usa los certificados para autenticar a los clientes de VPN para las VPN de punto a sitio. Después de crear el certificado raíz, exporte los datos (no la clave privada) del certificado público en forma de archivo X.509 .cer codificado en Base 64. Después, cargue los datos del certificado público del certificado raíz en Azure.

Todos los equipos cliente que se conecten a una red virtual mediante una conexión de punto a sitio debe tener instalado un certificado de cliente. El certificado de cliente se genera a partir del certificado raíz y se instala cada equipo cliente. Si no se ha instalado ningún certificado de cliente válido y el cliente intenta conectarse a la red virtual, la autenticación no se realiza.

### <a name="cer"></a>Paso 1: Obtención del archivo .cer para el certificado raíz

####<a name="enterprise-certificate"></a>Certificado de empresa
 
Si usa una solución empresarial, puede utilizar la cadena de certificados existente. Obtenga el archivo .cer para el certificado raíz que desee usar.

####<a name="self-signed-root-certificate"></a>Certificado raíz autofirmado

Si no usa una solución de certificación de empresa, será preciso que cree un certificado raíz autofirmado. Para crear un certificado raíz autofirmado que contenga los campos necesarios para la autenticación P2S, puede usar PowerShell. En [Funcionamiento de los certificados raíz autofirmados para conexiones de punto a sitio](vpn-gateway-certificates-point-to-site.md) puede obtener ayuda con los pasos necesarios para crear un certificado raíz autofirmado.

> [!NOTE]
> Anteriormente, makecert era el método recomendado para crear certificados raíz autofirmados y generar certificados de cliente para conexiones de punto a sitio. Ahora puede usar PowerShell para crear estos certificados. Una ventaja del uso de PowerShell es la posibilidad de crear certificados SHA-2. Para ver los paquetes requeridos, consulte [Funcionamiento de los certificados raíz autofirmados para conexiones de punto a sitio](vpn-gateway-certificates-point-to-site.md).
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar la clave pública para un certificado raíz autofirmado

Para cargarse en Azure, las conexiones de punto a sitio requieren la clave pública (.cer). Los pasos siguientes ayudan a exportar el archivo .cer para el certificado raíz autofirmado.

1. Para obtener un archivo .cer del certificado, abra **certmgr.msc**. Busque el certificado raíz autofirmado; normalmente se encuentra en Certificados - Usuario actual\Personal\Certificados y haga clic en el botón derecho. Haga clic en **Todas las tareas** y, luego, en **Exportar**. Se abre el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**. Seleccione **No exportar la clave privada** y, después, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base 64 (.CER)** y, luego, haga clic en **Siguiente**. 
4. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** para exportar el certificado. Verá **La exportación se realizó correctamente**. Haga clic en **Aceptar** para cerrar el asistente.

### <a name="generate"></a>Paso 2: Generación de un certificado de cliente
Puede generar un certificado único para cada cliente que se vaya a conectar o puede usar el mismo en varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado si es necesario. Sin embargo, si todos usan el mismo certificado de cliente y necesita revocar el de un solo cliente, tendrá que generar e instalar certificados nuevos para todos los clientes que usen el certificado para autenticarse.

####<a name="enterprise-certificate"></a>Certificado de empresa
- Si usa una solución de certificación de empresa, genere un certificado de cliente con el formato de valor de nombre común "name@yourdomain.com", en lugar del formato "nombreDeDominio\nombreDeUsuario".
- Asegúrese de que el certificado de cliente que emita se base en la plantilla de certificado "Usuario" que tenga "Autenticación de usuario" como primer elemento de la lista de uso, y no Inicio de sesión de tarjeta inteligente, etc. Para comprobar el certificado, haga doble clic en el certificado de cliente y vea **Detalles > Uso mejorado de claves**.

####<a name="self-signed-root-certificate"></a>Certificado raíz autofirmado 
Si utiliza un certificado raíz autofirmado, consulte [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Generación de un certificado de cliente mediante PowerShell) para ver los pasos necesarios para generar un certificado de cliente que sea compatible con conexiones de punto a sitio.


### <a name="exportclientcert"></a>Paso 3: Exportación del certificado de cliente

Si genera un certificado de cliente desde un certificado raíz autofirmado mediante las instrucciones de [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), este se instala automáticamente en el equipo que utilizó para generarlo. Si desea instalar un certificado de cliente en otro equipo cliente, es preciso que lo exporte.
 
1. Para exportar un certificado de cliente, abra **certmgr.msc**. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**. Se abre el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**, seleccione **Exportar la clave privada** y, luego, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, deje seleccionados los valores predeterminados. Asegúrese de que **Incluir todos los certificados en la ruta de certificación si es posible** esté seleccionada. A continuación, haga clic en **Siguiente**.
4. En la página **Seguridad** , debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. A continuación, haga clic en **Siguiente**.
5. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
6. Haga clic en **Finalizar** para exportar el certificado.

### <a name="upload"></a>Paso 4: Carga del archivo .cer de certificado raíz

Una vez creada la puerta de enlace, puede cargar el archivo .cer para un certificado raíz de confianza en Azure. Se pueden cargar archivos para un máximo de 20 certificados raíz. No cargue la clave privada para el certificado raíz en Azure. Una vez que se carga el archivo .cer, Azure lo utiliza para autenticar a los clientes que se conectan a la red virtual.

Declare la variable para el nombre del certificado, y reemplace el valor por el suyo propio:

        $P2SRootCertName = "Mycertificatename.cer"

Reemplace la ruta de acceso del archivo por la del suyo y ejecute los cmdlets.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Parte 4: Creación de VPN Gateway
Configure y cree la puerta de enlace de red virtual para la red virtual. *-GatewayType* debe ser **Vpn** y, *-VpnType*, **RouteBased**. Esto puede tardar hasta 45 minutos en completarse.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Parte 5: Descarga del paquete de configuración de cliente de VPN
Los clientes que se conectan a Azure mediante una conexión de punto a sitio deben tener un certificado de cliente y un paquete de configuración de cliente de VPN instalado. Los paquetes de configuración de cliente de VPN están disponibles para los clientes de Windows.

El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional. Los valores son específicos de la red virtual a la que desea conectarse. Para ver la lista de sistema operativos cliente que se admiten, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.

1. Una vez creada la puerta de enlace, se puede descargar el paquete de configuración de cliente. En este ejemplo se descarga el paquete para clientes de 64 bits. Si desea descargar al cliente de 32 bits, reemplace 'Amd64' por 'x86'. El cliente de VPN también se puede descargar desde Azure Portal.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. El cmdlet de PowerShell devuelve un vínculo de dirección URL. Esto es un ejemplo del aspecto que tiene la dirección URL en cuestión:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Copie y pegue este vínculo en un explorador web para descargar el paquete. A continuación, instale el paquete en el equipo cliente. Si aparece un elemento emergente de SmartScreen, haga clic en **Más información**y, luego, en **Ejecutar de todas formas** para instalar el paquete.
4. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. Podrá ver la conexión en la lista. Mostrará el nombre de la red virtual a la que se conectará y tendrá un aspecto similar al de este ejemplo: 
   
    ![Cliente de VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png)

## <a name="clientcertificate"></a>Parte 6: Instalación de un certificado de cliente exportado

Si desea crear una conexión P2S desde un equipo cliente distinto del que usó para generar los certificados de cliente, debe instalar un certificado de cliente. Al instalar el certificado de cliente, necesitará la contraseña que se creó cuando se exportó el certificado de cliente.

1. Busque el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje **Ubicación de almacén** como **Usuario actual** y, luego, haga clic en **Siguiente**.
2. En la página **File to import** (Archivo para importar), no haga ningún cambio. Haga clic en **Next**.
3. En la página **Protección de clave privada**, escriba la contraseña del certificado, si ha usado una, o compruebe que la entidad de seguridad que instala el certificado sea correcta y, luego, haga clic en **Siguiente**.
4. En la página **Almacén de certificados**, deje la ubicación predeterminada y, luego, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** En la **Advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. Al haberse generado el certificado, puede hacer clic en "Sí" de forma segura. El certificado se importó correctamente.

## <a name="connect"></a>Parte 7: Conexión a Azure
1. Para conectarse a su red virtual, en el equipo cliente, vaya a las conexiones VPN y ubique la que creó. Tiene el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Si esto ocurre, haga clic en **Continuar** para usar privilegios elevados. 
2. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado** , compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.
   
    ![Conexión del cliente de VPN a Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. La conexión debería establecerse.
   
    ![Conexión establecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

> [!NOTE]
> Si va a usar un certificado que se emitió con una solución de CA empresarial y está teniendo problemas de autenticación, compruebe el orden de autenticación en el certificado de cliente. Para comprobar el orden de la lista de autenticación, haga doble clic en el certificado de cliente y vaya a **Detalles > Uso mejorado de claves**. Asegúrese de que la lista muestre "Autenticación de cliente" como primer elemento. Si no es así, debe emitir un certificado de cliente basado en la plantilla Usuario que tenga Autenticación de cliente como primer elemento de la lista. 
>
>

## <a name="verify"></a>Parte 8: Comprobación de la conexión
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

## <a name="addremovecert"></a>Adición o eliminación de un certificado raíz de confianza

Puede agregar y quitar certificados raíz de confianza de Azure. Cuando se quita un certificado de confianza, los certificados de cliente que se generaron a partir del certificado raíz ya no podrán conectarse a Azure mediante la conexión de punto a sitio. Si desea que los clientes se conecten, deben instalar un nuevo certificado de cliente que se genere a partir de un certificado de confianza en Azure.


### <a name="to-add-a-trusted-root-certificate"></a>Para agregar un certificado raíz de confianza
Puede agregar hasta 20 archivos .cer de certificado raíz de confianza a Azure. Siga los siguientes pasos para agregar un certificado raíz.

1. Cree y prepare el nuevo certificado raíz que agregará a Azure. Exporte la clave pública como archivo X.509 con codificación Base64 (.cer) y ábralo con un editor de texto. A continuación, copie únicamente la sección que se muestra a continuación. 
   
    Copie los valores como se muestra en el ejemplo siguiente:
   
    ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > Al copiar los datos del certificado, asegúrese de copiar el texto como una línea continua sin retornos de carro ni avances de línea. Es posible que para ver los retornos de carro y los avances de línea deba cambiar la vista del editor de texto de forma que se muestren los símbolos y todos los caracteres.                                                                                                                                                                            
    >


2. Especifique el nombre del certificado y la información de clave como una variable. Reemplace la información por la suya propia como se muestra en el siguiente ejemplo:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Agregue el nuevo certificado raíz. Solo puede agregar un certificado raíz a la vez.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Puede comprobar que el nuevo certificado se agregó correctamente mediante el siguiente ejemplo:
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Eliminación de un certificado raíz de confianza
Puede quitar un certificado raíz de confianza de Azure. Cuando se quita un certificado de confianza, los certificados de cliente que se generaron a partir del certificado raíz ya no podrán conectarse a Azure mediante la conexión de punto a sitio. Si desea que los clientes se conecten, deben instalar un nuevo certificado de cliente que se genere a partir de un certificado de confianza en Azure.

1. Declare las variables.
   
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Quite el certificado.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Use el siguiente ejemplo para comprobar que el certificado se quitó correctamente. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Revocación de un certificado de cliente
Puede revocar certificados de cliente. La lista de revocación de certificados permite denegar de forma selectiva la conectividad de punto a sitio basada en certificados de cliente individuales. Esto difiere de la forma en que se quita un certificado raíz de confianza. Si quita de Azure un archivo .cer de certificado raíz de confianza, se revoca el acceso para todos los certificados de cliente generados y firmados con el certificado raíz revocado. Al revocarse un certificado de cliente, en lugar del certificado raíz, se permite que el resto de los certificados que se generaron con el certificado raíz sigan usándose para la autenticación de la conexión de punto a sitio.

Lo más habitual es usar el certificado raíz para administrar el acceso a nivel de equipo u organización, mientras que los certificados de cliente revocados se usan para un control de acceso específico para usuarios individuales.

### <a name="to-revoke-a-client-certificate"></a>Para revocar un certificado de cliente

1. Recupere la huella digital del certificado de cliente. Para más información, consulte [Cómo recuperar la huella digital de un certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie la información en un editor de texto y quite todos los espacios de forma que sea una sola cadena continua. Esto se declarará como una variable.
3. Declare las variables. Asegúrese de declarar la huella digital que recuperó en el paso anterior.
   
        $RevokedClientCert1 = "NameofCertificate"
        $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"
4. Agregue la huella digital a la lista de certificados revocados. Cuando haya agregado la huella digital, aparecerá un aviso que le indica que la acción se ha completado "correctamente".
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
        -Thumbprint $RevokedThumbprint1
5. Compruebe que la huella digital se agregó a la lista de revocación de certificados.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
6. Una vez agregada la huella digital, el certificado ya no podrá utilizarse para conectarse. Los clientes que intenten conectarse con este certificado recibirán un mensaje que indica que el certificado ya no es válido.

### <a name="to-reinstate-a-client-certificate"></a>Restablecimiento de un certificado de cliente
Puede restablecer un certificado de cliente quitando la huella digital de la lista de certificados de cliente revocados.

1. Declare las variables. Asegúrese de que declara la huella digital correcta para el certificado que desea restablecer.
 
        $RevokedClientCert1 = "NameofCertificate"
        $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
        $GWName = "Name_of_virtual_network_gateway"
        $RG = "Name_of_resource_group"

2. Quite la huella digital del certificado de la lista de revocación de certificados.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Compruebe si la huella digital se quita de la lista revocada.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
## <a name="faq"></a>Preguntas más frecuentes sobre la conexión de punto a sitio

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información. Para más información acerca de las redes y las máquinas virtuales, consulte [Información general sobre las redes de máquina virtual con Linux y Azure](../virtual-machines/linux/azure-vm-network-overview.md).



