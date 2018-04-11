---
title: 'Creación e instalación de archivos de configuración de cliente VPN de punto a sitio para la autenticación con certificados de Azure: Azure PowerShell | Microsoft Docs'
description: Creación e instalación de archivos de configuración de cliente VPN de Windows, Linux (strongSwan) y Mac OS X para autenticación de certificados de P2S.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: 9b9528aba0be8fd46087d97bc294552db608f1c1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Creación e instalación de archivos de configuración de cliente VPN para configuraciones de punto a sitio con autenticación con certificados nativos de Azure

Los archivos de configuración de cliente VPN están contenidos en un archivo zip. Estos archivos proporcionan la configuración necesaria para que un cliente VPN nativo de Windows, Mac IKEv2 o Linux se conecte a una red virtual mediante una conexión de punto a sitio que use la autenticación con certificados de Azure nativa.

### <a name="workflow"></a>Flujo de trabajo de la conexión de punto a sitio

  1. Configure la puerta de enlace de VPN de Azure para la conectividad de punto a sitio.
  2. Genere el certificado raíz y los certificados de cliente. Cargue la clave pública del certificado raíz en Azure e instale los certificados de cliente en los dispositivos cliente. Los certificados se utilizan para autenticar los usuarios que se conectan.
  3. Obtenga la configuración de cliente VPN de la opción de autenticación de su elección y úsela para configurar el cliente VPN en su dispositivo Windows o Mac. De esta forma podrá conectarse a redes virtuales de Azure a través de una conexión de punto a sitio.

>[!NOTE]
>Los archivos de configuración de cliente son específicos de la configuración de VPN para la red virtual. Si después de generar el perfil de configuración de cliente VPN hay algún cambio en la configuración de VPN de punto a sitio, como el tipo de protocolo de VPN o de autenticación, debe generar archivos de configuración de cliente VPN nuevos en los dispositivos de los usuarios.
>
>

## <a name="generate"></a>Generación de los archivos de configuración de cliente VPN

Antes de comenzar, asegúrese de que todos los usuarios que se vayan a conectar tienen un certificado válido instalado en el dispositivo del usuario. Para más información acerca de cómo instalar un certificado de cliente, consulte el artículo sobre la [instalación de certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

Para generar archivos de configuración de cliente, puede usar PowerShell o Azure Portal. Cualquiera de los métodos devuelve el mismo archivo ZIP. Descomprima el archivo para ver las siguientes carpetas:

  * **WindowsAmd64** y **WindowsX86**, que contienen los paquetes del instalador de Windows de 32 y 64 bits, respectivamente. El paquete del instalador **WindowsAmd64** es para todos los clientes de Windows de 64 bits, no solo de AMD.
  * La carpeta **Genérico** contiene información general que usará para crear su propia configuración de cliente VPN, La carpeta Genérico se proporciona si se ha configurado la opción IKEv2 o SSTP + IKEv2 en la puerta de enlace. Si solo se ha configurado SSTP, la carpeta Genérico no aparece.

### <a name="zipportal"></a>Generación de archivos mediante Azure Portal

1. En Azure Portal, navegue a la puerta de enlace de red virtual correspondiente a la red virtual a la que desea conectarse.
2. En la página de la puerta de enlace de red virtual, haga clic en **Configuración de punto a sitio**.
3. En la parte superior de la página Configuración de punto a sitio, haga clic en **Download VPN client** (Descargar cliente de VPN). La generación del paquete de configuración del cliente tarda unos minutos.
4. El explorador indica que hay disponible un archivo ZIP de configuración del cliente. Tiene el mismo nombre que su puerta de enlace. Descomprima el archivo para ver las carpetas.

### <a name="zipps"></a>Generación de archivos mediante PowerShell

1. Al generar archivos de configuración de cliente VPN, el valor de "-AuthenticationMethod" es "EapTIs". Genere los archivos de configuración de cliente VPN con el comando siguiente:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Copie la dirección URL en el explorador para descargar el archivo ZIP y, luego, descomprima el archivo para ver las carpetas.

## <a name="installwin"></a>Instalación: Windows

Puede utilizar el mismo paquete de configuración de cliente VPN en todos los equipos cliente Windows, siempre que la versión coincida con la arquitectura del cliente. Para la lista de sistemas operativos de cliente compatibles, consulte la sección de punto a sitio de las [preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Debe tener derechos de administrador en el equipo cliente de Windows desde el que desea conectarse.
>
>

Use estos pasos para configurar al cliente VPN de Windows nativo para la autenticación mediante certificado:

1. Seleccione los archivos de configuración de cliente VPN que correspondan a la arquitectura del equipo Windows. Si la arquitectura de procesador es de 64 bits, elija el paquete del instalador "VpnClientSetupAmd64". En caso de que sea de 32 bits, elija el paquete del instalador "VpnClientSetupX86". 
2. Haga doble clic en el paquete para instalarlo. Si ve una ventana emergente de SmartScreen, haga clic en **Más información** y en **Ejecutar de todas formas**.
3. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta. 
4. Antes de intentar conectarse, compruebe que ha instalado un certificado de cliente en el equipo cliente. Es necesario un certificado de cliente para la autenticación al usar el tipo de autenticación de certificados nativo de Azure. Para más información acerca de cómo generar certificados, consulte [Generación de certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obtener información acerca de cómo instalar un certificado de cliente, consulte [Instalación de certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Instalación: Mac (OS X)

Azure no proporciona el archivo mobileconfig para realizar la autenticación de certificados nativa de Azure. Tendrá que configurar manualmente el cliente de VPN nativo IKEv2 en cada equipo Mac que se conecte a Azure. La carpeta **Genérico** contiene toda la información necesaria para configurarlo. Si no ve la carpeta Genérico en la descarga, es posible que IKEv2 no se haya seleccionado como tipo de túnel. Una vez que se seleccione IKEv2, vuelva a generar el archivo ZIP para recuperar la carpeta Genérico. que son los archivos siguientes:

* **VpnSettings.xml**, con configuración importante, como el tipo de túnel y la dirección del servidor. 
* **VpnServerRoot.cer**, con el certificado raíz necesario para validar la puerta de enlace de VPN de Azure durante la instalación de la conexión de punto a sitio.

Siga los pasos siguientes para configurar el cliente de VPN nativo en equipos Mac para realizar una autenticación mediante certificado. Deberá completar estos pasos en cada equipo Mac que se conecte a Azure:

1. Importe el certificado raíz **VpnServerRoot** en Mac. Para ello, sobreescriba el archivo en Mac y haga doble clic en él.  
Haga clic en **Agregar** para importarlo.

  ![agregar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Al hacer doble clic en el certificado, es posible que no se muestre el cuadro de diálogo **Agregar**, pero el certificado se instalará en la tienda correcta. Puede buscar el certificado en la cadena de claves de inicio de sesión en la categoría de certificados.
  
2. Compruebe que ha instalado un certificado de cliente emitido por el certificado raíz que cargó en Azure cuando estableció la configuración de P2S. Es diferente al VPNServerRoot que instaló en el paso anterior. Se necesita y se usa un certificado de cliente para la autenticación. Para más información acerca de cómo generar certificados, consulte [Generación de certificados](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Para obtener información acerca de cómo instalar un certificado de cliente, consulte [Instalación de certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Abra el cuadro de diálogo **Red** en **Preferencias de red** y haga clic en **"+"** para crear un nuevo perfil de conexión de cliente VPN para establecer una conexión de punto a sitio a la red virtual de Azure.

  El valor de **Interfaz** es "VPN" y el de **Tipo de VPN** es "IKEv2". Especifique un nombre para el perfil en el campo **Nombre del servicio** y, a continuación, haga clic en **Crear** para crear el perfil de conexión de cliente VPN.

  ![red](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. En la carpeta **Genérico**, en el archivo **VpnSettings.xml**, copie el valor de la etiqueta **VpnServer**. Pegue este valor en los campos **Dirección del servidor** e **Id. remoto** del perfil.

  ![información del servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Haga clic en **Configuración de autenticación** y seleccione **Certificado**. 

  ![configuración de autenticación](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Haga clic en **Seleccionar...** para elegir el certificado de cliente que quiere usar en la autenticación. Este es el certificado que instaló en el paso 2.

  ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. En **Choose An Identity** (Elegir una identidad) se muestra una lista de certificados para elegir. Seleccione el certificado adecuado y haga clic en **Continuar**.

  ![identidad](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. En el campo **Id. local**, especifique el nombre del certificado (del paso 6). En este ejemplo, es "ikev2Client.com". A continuación, haga clic en el botón **Aplicar** para guardar los cambios.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. En el cuadro de diálogo **Red**, haga clic en **Aplicar** para guardar los cambios. A continuación, haga clic en **Conectar** para iniciar la conexión de punto a sitio a la red virtual de Azure.

## <a name="installlinux"></a>Instalación: Linux (strongSwan)

### <a name="extract-the-key-and-certificate"></a>Extracción de la clave y del certificado

Para strongSwan, debe extraer la clave y el certificado del certificado de cliente (archivo .pfx) y guardarlos en archivos .pem individuales.
Siga estos pasos:

1. Descargue e instale OpenSSL desde [OpenSSL](https://www.openssl.org/source/).
2. Abra una ventana de línea de comandos y cambie al directorio donde instaló OpenSSL, por ejemplo, "c:\OpenSLL-Win64\bin\'.
3. Ejecute el comando siguiente para extraer la clave privada y guárdela en un archivo nuevo llamado "privatekey.pem" del certificado de cliente:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  Ahora ejecute el comando siguiente para extraer el certificado público y guárdelo en un archivo nuevo:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>Instalación

Las siguientes instrucciones se crearon mediante strongSwan 5.5.1 en Ubuntu 17.0.4. Ubuntu 16.0.10 no admite la GUI de strongSwan. Si desea usar Ubuntu 16.0.10, deberá usar la linea de comandos. Los ejemplos siguientes pueden no coincidir con las pantallas que ve, en función de la versión de Linux y strongSwan que tenga.

1. Abra la herramienta **Terminal** para instalar **strongSwan** y su Network Manager (Administrador de red) ejecutando el comando del ejemplo. Si recibe un error relacionado con *libcharon-extra-plugins*, reemplácelo por "strongswan-plugin-eap-mschapv2".

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Seleccione el icono **Network Manager** (Administrador de red) (flecha arriba/flecha abajo) y seleccione **Edit Connections** (Editar conexiones).

  ![editar conexiones](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Haga clic en el botón **Add** (Agregar) para crear una conexión.

  ![agregar una conexión](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Seleccione **IPsec/IKEv2 (strongswan)** en el menú desplegable y haga clic en **Create** (Crear). Puede cambiar el nombre de la conexión en este paso.

  ![elegir un tipo de conexión](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Abra el archivo **VpnSettings.xml** de la carpeta **Generic** (Genérico) que está incluida en los archivos de configuración de cliente descargados. Busque la etiqueta denominada **VpnServer** y copie el nombre, que comienza con "azuregateway" y termina con ".cloudapp.net".

  ![copiar nombre](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Pegue este nombre en el campo **Address** (Dirección) de la nueva conexión VPN de la sección **Gateway** (Puerta de enlace). Luego, seleccione el icono de carpeta al final del campo **Certificate** (Certificado), vaya a la carpeta **Generic** (Genérico) y seleccione el archivo **VpnServerRoot**.
7. En la sección **Client** (Cliente) de la conexión, para **Authentication** (Autenticación), seleccione **Certificate/private key** (Certificado/clave privada). En **Certificate** (Certificado) y **Private key** (Clave privada), elija el certificado y la clave privada que se crearon anteriormente. En **Options** (Opciones), seleccione **Request an inner IP address** (Solicitar una dirección IP interna). A continuación, haga clic en **Agregar**.

  ![solicitar una dirección IP interna](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Haga clic en el icono **Network Manager** (Administrador de red) (flecha arriba/flecha abajo) y mantenga el puntero sobre **VPN Connections** (Conexiones VPN). Verá la conexión VPN que ha creado. Haga clic para iniciar la conexión.

## <a name="next-steps"></a>Pasos siguientes

Vuelva al artículo para [completar la configuración de la conexión de punto a sitio](vpn-gateway-howto-point-to-site-rm-ps.md).

Para información sobre cómo solucionar los problemas de punto a sitio, consulte [Solución de problemas: conexión de punto a sitio de Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) y [Solución de problemas de conexiones VPN de punto a sitio desde clientes de VPN de Mac OS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md).