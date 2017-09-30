---
title: "Creación e instalación de archivos de configuración de cliente VPN de punto a sitio para la autenticación con certificados de Azure: Azure PowerShell | Microsoft Docs"
description: "La información de este artículo le ayuda a crear e instalar archivos de configuración de cliente VPN para conexiones de punto a sitio en las que se usa autenticación con certificados."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 95f14f2b77565b53c6e270f6afbf9873cdac606a
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Creación e instalación de archivos de configuración de cliente VPN para configuraciones de punto a sitio con autenticación con certificados nativos de Azure

Los archivos de configuración de cliente VPN están contenidos en un archivo zip. Estos archivos proporcionan la configuración necesaria para que un cliente VPN IKEv2 nativo Windows o Mac se conecte a una red virtual mediante una conexión de punto a sitio que use la autenticación con certificados de Azure nativa.

>[!NOTE]
>Actualmente, IKEv2 para P2S se encuentra en versión preliminar.
>

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

1. Al generar archivos de configuración de cliente VPN, el valor de "-AuthenticationMethod" es "EapTIs". Genere los archivos de configuración de cliente VPN con el comando siguiente:

  ```powershell
  New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapTls"
  ```
2. El comando anterior devuelve un vínculo que se usa para descargar los archivos de configuración del cliente. Copie y pegue el vínculo en un explorador web para descargar el archivo "VpnClientConfiguration.zip". Descomprima el archivo para ver las siguientes carpetas:

  * **WindowsAmd64** y **WindowsX86**, que contienen los paquetes del instalador de Windows de 32 y 64 bits, respectivamente. El paquete del instalador **WindowsAmd64** es para todos los clientes de Windows de 64 bits, no solo de AMD.
  * La carpeta **Genérico** contiene información general que usará para crear su propia configuración de cliente VPN, Ignórela. La carpeta Genérico se proporciona únicamente si se ha configurado la opción IKEv2 o SSTP + IKEv2 en la puerta de enlace. Si solo se ha configurado SSTP, la carpeta Genérico no aparece.

### <a name="to-retrieve-client-configuration-files"></a>Recuperación de los archivos de configuración de cliente

Si ya ha generado los archivos de configuración de cliente y solo necesita recuperarlos, puede usar el cmdlet "Get-AzureRmVpnClientConfiguration". El cmdlet "Get-AzureRmVpnClientConfiguration" devuelve una dirección URL (vínculo) desde donde puede descargar el archivo VpnClientConfiguration.zip. Si realiza cambios en la configuración de VPN de punto a sitio, como el tipo de protocolo de VPN o de autenticación, la configuración no se actualiza automáticamente. En su lugar, debe ejecutar el cmdlet "New-AzureRmVpnClientConfiguration" para volver a crear la configuración.

Para recuperar los archivos de configuración de cliente generados anteriormente, use el ejemplo siguiente:

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
## <a name="installwin"></a>Instalación del paquete de configuración de cliente VPN de Windows

Puede utilizar el mismo paquete de configuración de cliente VPN en todos los equipos cliente Windows, siempre que la versión coincida con la arquitectura del cliente. Para la lista de sistemas operativos de cliente compatibles, consulte la sección de punto a sitio de las [preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#P2S).

Use estos pasos para configurar al cliente VPN de Windows nativo para la autenticación mediante certificado:

1. Seleccione los archivos de configuración de cliente VPN que correspondan a la arquitectura del equipo Windows. Si la arquitectura de procesador es de 64 bits, elija el paquete del instalador "VpnClientSetupAmd64". En caso de que sea de 32 bits, elija el paquete del instalador "VpnClientSetupX86". 
2. Haga doble clic en el paquete para instalarlo. Si ve una ventana emergente de SmartScreen, haga clic en **Más información** y en **Ejecutar de todas formas**.
3. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta. 

## <a name="installmac"></a>Instalación de la configuración de cliente VPN de Mac (OSX)

Para todos los dispositivos Mac que se conecten a una red virtual de Azure, se debe crear una configuración de cliente VPN independiente. No se pueden reutilizar archivos de configuración para varios dispositivos Mac. Esto es porque, para estos dispositivos, debe especificar el certificado de usuario en los archivos de configuración de cliente VPN. La carpeta **Genérico** contiene toda la información necesaria para crear la configuración de cliente VPN, que son los archivos siguientes:

* **VpnSettings.xml**, con configuración importante, como el tipo de túnel y la dirección del servidor. 
* **VpnServerRoot.cer**, con el certificado raíz necesario para validar la puerta de enlace de VPN de Azure durante la instalación de la conexión de punto a sitio.

Use los pasos siguientes para configurar el cliente VPN nativo en Mac para la autenticación mediante certificado:

1. Importe el certificado raíz **VpnServerRoot** en Mac. Para ello, sobreescriba el archivo en Mac y haga doble clic en él.  
Haga clic en **Agregar** para importarlo.

  ![agregar certificado](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
2. Abra el cuadro de diálogo **Red** en **Preferencias de red** y haga clic en **"+"** para crear un nuevo perfil de conexión de cliente VPN para establecer una conexión de punto a sitio a la red virtual de Azure.

  El valor de **Interfaz** es "VPN" y el de **Tipo de VPN** es "IKEv2". Especifique un nombre para el perfil en el campo **Nombre del servicio** y, a continuación, haga clic en **Crear** para crear el perfil de conexión de cliente VPN.

  ![red](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. En la carpeta **Genérico**, en el archivo **VpnSettings.xml**, copie el valor de la etiqueta **VpnServer**. Pegue este valor en los campos **Dirección del servidor** e **Id. remoto** del perfil.

  ![información del servidor](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. Haga clic en **Configuración de autenticación** y seleccione **Certificado**. 

  ![configuración de autenticación](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. Haga clic en **Seleccionar...** para elegir el certificado que quiere usar en la autenticación.

  ![certificado](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. En **Choose An Identity** (Elegir una identidad) se muestra una lista de certificados para elegir. Seleccione el certificado adecuado y haga clic en **Continuar**.

  ![identidad](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. En el campo **Id. local**, especifique el nombre del certificado (del paso 5). En este ejemplo, es "ikev2Client.com". A continuación, haga clic en el botón **Aplicar** para guardar los cambios.

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. En el cuadro de diálogo **Red**, haga clic en **Aplicar** para guardar los cambios. A continuación, haga clic en **Conectar** para iniciar la conexión de punto a sitio a la red virtual de Azure.

## <a name="next-steps"></a>Pasos siguientes

Vuelva al artículo para [completar la configuración de la conexión de punto a sitio](vpn-gateway-howto-point-to-site-rm-ps.md).
