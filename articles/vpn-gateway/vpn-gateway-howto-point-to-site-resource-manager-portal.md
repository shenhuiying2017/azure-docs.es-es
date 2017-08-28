---
title: "Conexión de un equipo a una red virtual de Azure mediante la conectividad de punto a sitio y autenticación de certificado: Azure Portal | Microsoft Docs"
description: "Conecte de forma segura un equipo a su instancia de Azure Virtual Network mediante la creación de una conexión de puerta de enlace de VPN de punto a sitio utilizando la autenticación de certificados. Este artículo se aplica al modelo de implementación de Resource Manager y utiliza Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5c8e99f3ba52ef5d6f9f99ac24891c38e8970fff
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-azure-portal"></a>Configuración de una conexión de punto a sitio a una red virtual mediante la autenticación de certificado: Azure Portal

En este artículo se muestra cómo se crea una red virtual con una conexión de punto a sitio en el modelo de implementación de Resource Manager desde Azure Portal. Esta configuración utiliza certificados para autenticar al cliente que se conecta. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal de Azure clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Una puerta de enlace de VPN de punto a sitio (P2S) permite crear una conexión segura a la red virtual desde un equipo cliente individual. Las conexiones VPN de punto a sitio son útiles cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia. Una VPN P2S también es una solución útil en lugar de una VPN de sitio a sitio cuando hay solo unos pocos clientes que necesitan conectarse a una red virtual. 

P2S utiliza el protocolo de túnel de socket seguro (SSTP), que es un protocolo VPN basado en SSL. Se establece una conexión VPN P2S al iniciar la conexión desde el equipo cliente.

![Diagrama de punto a sitio](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

Las conexiones de autenticación de certificado de punto a sitio requieren lo siguiente:

* Una puerta de enlace de VPN RouteBased.
* La clave pública (archivo .cer) de un certificado raíz, que se carga en Azure. Una vez cargado el certificado, se considera un certificado de confianza y se usa para la autenticación.
* Se genera un certificado de cliente a partir del certificado raíz y se instala en cada equipo cliente que se vaya a conectar a la red virtual. Este certificado se usa para la autenticación de cliente.
* Un paquete de configuración de cliente de VPN. El paquete de configuración de cliente de VPN contiene la información necesaria para que el cliente se conecte a la red virtual. El paquete configura al cliente de VPN existente que sea nativo para el sistema operativo Windows. Cada cliente que se conecta debe configurarse mediante el paquete de configuración.

Las conexiones de punto a sitio no requieren un dispositivo VPN ni una dirección IP de acceso público local. Se crea la conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). En el lado servidor, se admiten las versiones 1.0, 1.1 y 1.2 de SSTP. El cliente decide qué versión va a usar. Para Windows 8.1 y versiones posteriores, SSTP usa 1.2 de forma predeterminada.

Para más información sobre las conexiones de punto a sitio, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.

#### <a name="example"></a>Valores del ejemplo

Puede usar los siguientes valores para crear un entorno de prueba o hacer referencia a ellos para comprender mejor los ejemplos de este artículo:

* **Nombre de la red virtual:** VNet1
* **Espacio de direcciones:** 192.168.0.0/16<br>En este ejemplo, se utiliza solo un espacio de direcciones. Puede tener más de un espacio de direcciones para la red virtual.
* **Nombre de subred:** FrontEnd
* **Intervalo de direcciones de subred:** 192.168.1.0/24
* **Suscripción:** si tiene más de una suscripción, compruebe que usa la correcta.
* **Grupo de recursos:** TestRG
* **Ubicación:** este de EE. UU.
* **GatewaySubnet:** 192.168.200.0/24<br>
* **Servidor DNS:** (opcional) dirección IP del servidor DNS que desea usar para la resolución de nombres.
* **Nombre de la puerta de enlace de red virtual:** VNet1GW
* **Tipo de puerta de enlace:** VPN
* **Tipo de VPN:** basada en rutas
* **Dirección IP pública:** VNet1GWpip
* **Tipo de conexión:** de punto a sitio
* **Grupo de direcciones de clientes:** 172.16.201.0/24<br>Los clientes de VPN que se conectan a la red virtual mediante esta conexión de punto a sitio reciben una dirección IP del grupo de clientes.

## <a name="createvnet"></a>1. Crear una red virtual

Antes de empezar, compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2. Adición de una subred de puerta de enlace

Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Los servicios de puerta de enlace usan las direcciones IP especificadas en la subred de puerta de enlace. Si es posible, cree una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a otros requisitos de configuración que puedan surgir en el futuro.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3. Especificación de un servidor DNS (opcional)

Después de crear la red virtual, puede agregar la dirección IP de un servidor DNS para controlar la resolución de nombres. El servidor DNS es opcional para esta configuración, pero es necesario si desea la resolución de nombres. La especificación de un valor no crea un servidor DNS nuevo. La dirección IP del servidor DNS que especifique debe ser un servidor DNS que pueda resolver los nombres de los recursos a los que se conecta. En este ejemplo, se usa una dirección IP privada, pero es probable que no sea la dirección IP del servidor DNS. Asegúrese de utilizar sus propios valores.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4. Creación de una puerta de enlace de red virtual

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

## <a name="generatecert"></a>5. Generación de certificados

Azure usa certificados para autenticar a los clientes para conectarse a una red virtual a través de una conexión VPN de punto a sitio. Una vez que obtenga el certificado raíz, [cargue](#uploadfile) la información de clave pública en Azure. En ese momento, Azure considera que el certificado raíz es "de confianza" para conectarse de P2S a la red virtual. También genere certificados de cliente desde el certificado raíz de confianza y, a continuación, vuelva a instalarlos en cada equipo cliente. El certificado de cliente se utiliza para autenticar al cliente cuando se inicia una conexión con la red virtual. 

### <a name="getcer"></a>1. Obtención del archivo .cer del certificado raíz

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. Generación de un certificado de cliente

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6. Incorporación del grupo de direcciones del cliente

El grupo de direcciones de cliente es un intervalo de direcciones IP privadas que usted especifica. Los clientes que se conectan a través de una VPN de punto a sitio reciben una dirección IP de este intervalo. Use un intervalo de direcciones IP privadas que no se superponga a la ubicación local desde la que se va a conectar ni a la red virtual a la que desea conectarse.

1. Una vez creada la puerta de enlace de red virtual, navegue hasta la sección **Valores** de la página de la puerta de enlace de red virtual. En la sección **Valores**, haga clic en **Configuración de punto a sitio** para abrir la página **Configuración de punto a sitio**.

  ![Página de punto a sitio](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png)
2. En la página **Configuración de punto a sitio**, puede eliminar el intervalo automático y, a continuación, agregar el intervalo de direcciones IP privadas que desea utilizar. Haga clic en **Guardar** para validar y guardar la configuración.

  ![Grupo de direcciones de clientes](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>7. Cargue la información del certificado de datos público del certificado raíz

Una vez creada la puerta de enlace, se carga la información de clave pública para el certificado raíz en Azure. Una vez que se han cargado los datos de certificado público, Azure puede usarlos para autenticar a los clientes que tienen instalado un certificado de cliente generado a partir del certificado raíz de confianza. Puede cargar más certificados raíz de confianza, hasta un total de 20.

1. Los certificados se agregan en la página **Configuración de punto a sitio** de la sección **Certificado raíz**.  
2. Asegúrese de exportar el certificado raíz como archivo X.509 codificado base 64 (.cer). Debe exportar el certificado en este formato para poder abrirlo con un editor de texto.
3. Abra el certificado con un editor de texto como Bloc de notas. Al copiar los datos del certificado, asegúrese de copiar el texto como una línea continua sin retornos de carro ni avances de línea. Es posible que para ver los retornos de carro y los avances de línea deba cambiar la vista del editor de texto de forma que se muestren los símbolos y todos los caracteres. Copie solo la siguiente sección como una línea continua:

  ![Datos del certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)
4. Pegue los datos del certificado en la sección **Public Certificate Data** (Datos de certificado público). Asigne un **nombre** al certificado y luego haga clic en **Guardar**. Puede agregar hasta 20 certificados raíz de confianza.

  ![Carga del certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>8. Generación e instalación del paquete de configuración de cliente de VPN

Para conectarse a una red virtual mediante una VPN de punto a sitio, cada cliente debe instalar un paquete de configuración de cliente que se configura al cliente VPN nativo con los valores y archivos necesarios para conectarse a la red virtual. El paquete de configuración de cliente VPN configura el cliente de VPN de Windows nativo, pero no instala un cliente VPN nuevo o diferente.

Puede utilizar el mismo paquete de configuración de cliente VPN en todos los equipos cliente, siempre que la versión coincida con la arquitectura del cliente. Para ver la lista de sistema operativos cliente que se admiten, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.

### <a name="step-1---generate-and-download-the-client-configuration-package"></a>Paso 1: Generación y descarga del paquete de configuración del cliente

1. En la página **Configuración de punto a sitio**, haga clic en **Download VPN client** (Descargar cliente de VPN) para abrir la página **Download VPN client** (Descargar cliente de VPN). Se tarda uno o dos minutos en generar el paquete.

  ![Descarga del cliente de VPN 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Seleccione el paquete correcto para el cliente y después haga clic en **Descargar**. Guarde el archivo del paquete de configuración. Instale el paquete de configuración de cliente VPN en cada equipo cliente que se conecta a la red virtual.

  ![Descarga del cliente de VPN 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>Parte 2: Instalación del paquete de configuración del cliente

1. Copie el archivo de configuración localmente en el equipo que desee conectar a la red virtual. 
2. Haga doble clic en el archivo .exe para instalar el paquete en el equipo cliente. Como usted creó el paquete de configuración, no está firmado y puede ver una advertencia. Si aparece un elemento emergente de Windows SmartScreen, haga clic en **Más información** (a la izquierda) y en **Ejecutar de todas formas** para instalar el paquete.
3. Instale el paquete en el equipo cliente. Si aparece un elemento emergente de Windows SmartScreen, haga clic en **Más información** (a la izquierda) y en **Ejecutar de todas formas** para instalar el paquete.
4. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. La conexión VPN muestra el nombre de la red virtual a la que se conecta.

## <a name="installclientcert"></a>9. Instalación de un certificado de cliente exportado

Si desea crear una conexión P2S desde un equipo cliente distinto del que usó para generar los certificados de cliente, debe instalar un certificado de cliente. Al instalar un certificado de cliente, necesita la contraseña que se creó cuando se exportó el certificado de cliente. Normalmente, solo es necesario hacer doble clic en el certificado e instalarlo.

Asegúrese de que se exportó el certificado de cliente como un .pfx junto con la cadena de certificados completa (que es el valor predeterminado). En caso contrario, la información del certificado raíz no está presente en el equipo cliente y el cliente no podrá realizar la autenticación correctamente. Para más información, consulte [Instalación de un certificado de cliente exportado](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>10. Conexión a Azure

1. Para conectarse a su red virtual, en el equipo cliente, vaya a las conexiones VPN y ubique la que creó. Tiene el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Haga clic en **Continuar** para usar privilegios elevados.

2. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado** , compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

  ![El cliente VPN se conecta a Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. Se ha establecido la conexión.

  ![Conexión establecida](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>Solución de problemas de conexiones P2S

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify"></a>11. Comprobación de la conexión

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecute *ipconfig/all*.
2. Vea los resultados. Observe que la dirección IP que recibió es una de las direcciones dentro del grupo de direcciones de cliente de VPN punto a sitio que especificó en la configuración. Los resultados son similares a los del ejemplo siguiente:

  ```
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
  ```

## <a name="connectVM"></a>Conexión a una máquina virtual

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Incorporación o eliminación de certificados raíz de confianza

Puede agregar y quitar certificados raíz de confianza de Azure. Al quitar un certificado raíz, los clientes que tienen un certificado generado a partir de dicha raíz no podrán autenticarse y, por tanto, no podrán conectarse. Si desea que un cliente se autentique y se conecte, es preciso que instale un nuevo certificado de cliente generado a partir de un certificado raíz que sea de confianza (se cargue) en Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para agregar un certificado raíz de confianza

Puede agregar hasta 20 archivos .cer de certificado raíz de confianza a Azure. Para ver instrucciones, consulte la sección [Carga del archivo .cer del certificado raíz](#uploadfile) de este artículo.

### <a name="to-remove-a-trusted-root-certificate"></a>Eliminación de un certificado raíz de confianza

1. Para quitar un certificado raíz de confianza, vaya a la página **Configuración de punto a sitio** para la puerta de enlace de red virtual.
2. En la sección **Certificado raíz** de la página, busque el certificado que desea quitar.
3. Haga clic en los puntos suspensivos junto al certificado y en "Quitar".

## <a name="revokeclient"></a>Revocación de un certificado de cliente

Puede revocar certificados de cliente. La lista de revocación de certificados permite denegar de forma selectiva la conectividad de punto a sitio basada en certificados de cliente individuales. Esto difiere de la forma en que se quita un certificado raíz de confianza. Si quita de Azure un archivo .cer de certificado raíz de confianza, se revoca el acceso para todos los certificados de cliente generados y firmados con el certificado raíz revocado. Al revocarse un certificado de cliente, en lugar del certificado raíz, se permite que el resto de los certificados que se generaron a partir del certificado raíz sigan usándose para la autenticación.

Lo más habitual es usar el certificado raíz para administrar el acceso a nivel de equipo u organización, mientras que los certificados de cliente revocados se usan para un control de acceso específico para usuarios individuales.

### <a name="to-revoke-a-client-certificate"></a>Para revocar un certificado de cliente

Puede revocar un certificado de cliente si agrega la huella digital a la lista de revocación.

1. Recupere la huella digital del certificado de cliente. Para más información, consulte [Cómo recuperar la huella digital de un certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie la información en un editor de texto y quite todos los espacios de forma que sea una sola cadena continua.
3. Vaya a la página **Configuración de punto a sitio** de la puerta de enlace de red virtual. Se trata de la misma hoja que utilizó para [cargar un certificado raíz de confianza](#uploadfile).
4. En la sección **Certificados revocados**, especifique un nombre descriptivo para el certificado (no es necesario que sea el CN del certificado).
5. Copie y pegue la cadena de huella digital en el campo **Huella digital**.
6. Se valida la huella digital y se agrega automáticamente a la lista de revocación. Aparece un mensaje en la pantalla que indica que se está actualizando la lista. 
7. Una vez finalizada la actualización, el certificado no se puede usar para conectarse. Los clientes que intenten conectarse con este certificado reciben un mensaje que indica que el certificado ya no es válido.

## <a name="faq"></a>Preguntas más frecuentes sobre la conexión de punto a sitio

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información. Para más información acerca de las redes y las máquinas virtuales, consulte [Información general sobre las redes de máquina virtual con Linux y Azure](../virtual-machines/linux/azure-vm-network-overview.md).
