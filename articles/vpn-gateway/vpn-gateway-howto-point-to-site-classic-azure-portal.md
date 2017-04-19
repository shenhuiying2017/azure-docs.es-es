---
title: "Conexión de un equipo a una red virtual de Azure mediante la conectividad de punto a sitio: Azure Portal: clásico | Microsoft Docs"
description: "Conéctese de forma segura a Azure Virtual Network a través de una implementación clásica mediante la creación de una conexión de punto a sitio de VPN Gateway con Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 3d8806411f775d1e6e69af66326534f69c468771
ms.lasthandoff: 04/12/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Configuración de una conexión de punto a sitio a una red virtual mediante Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clásico: Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Una configuración de punto a sitio (P2S) permite crear una conexión segura entre un equipo cliente individual y una red virtual. P2S es una conexión VPN sobre SSTP (Protocolo de túnel de sockets seguros). Las conexiones de punto a sitio son útiles cuando desea conectarse a su red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si tiene pocos clientes que necesiten conectarse a una red virtual. Las conexiones P2S no requieren un dispositivo VPN ni una dirección IP pública. Para establecer la conexión VPN desde el equipo cliente.

Este artículo le guiará a través de la creación de una red virtual con una conexión de punto a sitio mediante el modelo de implementación clásico con Azure Portal. Para más información sobre las conexiones de punto a sitio, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Métodos y modelos de implementación para conexiones P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los dos modelos de implementación y los métodos de implementación disponible para las configuraciones de P2S. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flujo de trabajo básico
![Diagrama de punto a sitio](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Las siguientes secciones lo guían por los pasos necesarios para crear una conexión de punto a sitio segura a una red virtual.

1. Creación de Virtual Network y VPN Gateway
2. Generación de certificados
3. Carga del archivo .cer
4. Generación del paquete de configuración de cliente VPN
5. Configuración del equipo cliente
6. Conexión a Azure

### <a name="example-settings"></a>Configuración de ejemplo
Puede utilizar la siguiente configuración de ejemplo:

* **Nombre: VNet1**
* **Espacio de direcciones: 192.168.0.0/16**<br>En este ejemplo, se utiliza solo un espacio de direcciones. Puede tener más de un espacio de direcciones para la red virtual.
* **Nombre de subred: FrontEnd**
* **Intervalo de direcciones de subred: 192.168.1.0/24**
* **Suscripción:** si tiene más de una suscripción, compruebe que usa la correcta.
* **Grupo de recursos: TestRG**
* **Ubicación: este de EE. UU.**
* **Tipo de conexión: de punto a sitio**
* **Espacio de direcciones de clientes: 172.16.201.0/24**. Los clientes de VPN que se conectan a la red virtual mediante esta conexión de punto a sitio reciben una dirección IP del grupo especificado.
* **GatewaySubnet: 192.168.200.0/24**. La subred de puerta de enlace debe usar el nombre "GatewaySubnet".
* **Tamaño**: seleccione la SKU de puerta de enlace que desea utilizar.
* **Tipo de enrutamiento: dinámico**



## <a name="vnetvpn"></a>Sección 1: Creación de una red virtual y una puerta de enlace de VPN

Antes de empezar, compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial).
### <a name="createvnet"></a>Paso 1: Creación de una red virtual
Si no tiene una red virtual, créela. Las capturas de pantalla se proporcionan a modo de ejemplo. Asegúrese de reemplazar los valores por los suyos. Para crear una red virtual mediante Azure Portal, siga estos pasos:

1. Desde un explorador, vaya al [Portal de Azure](http://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Nuevo**. En el campo **Buscar en el Marketplace**, escriba "Red virtual". En la lista de resultados, busque **Red virtual** y haga clic en él para abrir la hoja **Red virtual**.

    ![Hoja Buscar red virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Cerca de la parte inferior de la hoja Red virtual, en la lista **Seleccionar un modelo de implementación**, seleccione **Clásico** y haga clic en **Crear**.

    ![Seleccionar modelo de implementación](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. En la hoja **Crear red virtual** , configure los valores de la red virtual. En esta hoja, se agrega el primer espacio de direcciones y un intervalo de direcciones de subred únicas. Cuando termine de crear la red virtual, puede volver atrás y agregar espacios de direcciones y subredes adicionales.

    ![Hoja Crear red virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Compruebe que la **Suscripción** es la correcta. Puede cambiar las suscripciones mediante la lista desplegable.
6. Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente, o bien cree uno nuevo escribiendo un nombre para el nuevo grupo. Si va a crear un nuevo grupo de recursos, asigne un nombre al grupo de recursos según los valores de configuración planeados. Para más información acerca de los grupos de recursos, visite [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. A continuación, seleccione la configuración de **Ubicación** de la red virtual. La ubicación determina dónde van a residir los recursos que se implementen en esta red virtual.
8. Seleccione **Anclar al panel** si desea encontrar la red virtual fácilmente en el panel y, luego, haga clic en **Crear**.

    ![Anclar al panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Después de hacer clic en Crear, aparece un icono en el panel que reflejará el progreso de la red virtual. El icono cambiará a medida que se vaya creando la red virtual.

    ![Icono de Crear red virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Después de crear la red virtual, puede agregar la dirección IP de un servidor DNS para controlar la resolución de nombres. Abra la configuración de la red virtual, haga clic en los servidores DNS y agregue la dirección IP del servidor DNS que desea utilizar. Este valor no crea un servidor DNS nuevo. Asegúrese de agregar un servidor DNS con el que se puedan comunicar sus recursos.

Una vez que se haya creado la red virtual, verá **Creado** en **Estado** en la página Redes del Portal de Azure clásico.

### <a name="gateway"></a>Parte 2: Creación de una subred de puerta de enlace y una puerta de enlace de enrutamiento dinámico
En este paso creará una subred de puerta de enlace y una puerta de enlace de enrutamiento dinámico. En Azure Portal para el modelo de implementación clásico, la creación de la subred de puerta de enlace y de la puerta de enlace puede realizarse a través de las mismas hojas de configuración.

1. En el portal, navegue a la red virtual para la que desea crear una puerta de enlace.
2. En la hoja de la red virtual, en la hojas **Introducción**, en la sección de conexiones VPN, haga clic en **Puerta de enlace**.

    ![Haga clic para crear una puerta de enlace.](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. En la hoja **Nueva conexión VPN**, seleccione **De punto a sitio**.

    ![Tipo de conexión de punto a sitio](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. En **Espacio de direcciones del cliente**, agregue el intervalo de direcciones IP. Es el intervalo del que los clientes de VPN reciben una dirección IP al conectarse. Elimine el intervalo que se rellena automáticamente y agregue el suyo propio.

    ![Espacio de direcciones del cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Active la casilla **Crear puerta de enlace inmediatamente**.

    ![Crear puerta de enlace inmediatamente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Haga clic en **Configuración de puerta de enlace opcional** para abrir la hoja **Configuración de puerta de enlace**.

    ![Haga clic en Configuración de puerta de enlace opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Haga clic en **Configurar los valores necesarios de la subred** para agregar la **subred de puerta de enlace**. Aunque es posible crear una subred de puerta de enlace tan pequeña como /29, se recomienda que cree una subred mayor que incluya más direcciones seleccionando al menos /28 o /27. Esto permitirá suficientes direcciones para dar cabida a posibles configuraciones adicionales que desee en el futuro.

   > [!IMPORTANT]
   > Cuando trabaje con subredes de la puerta de enlace, evite asociar un grupo de seguridad de red (NSG) a la subred de la puerta de enlace. La asociación de un grupo de seguridad de red a esta subred puede causar que la puerta de enlace de VPN deje de funcionar como cabría esperar. Para más información acerca de los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)
   >
   >

    ![Agregue GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Seleccione el **tamaño** de la puerta de enlace. El tamaño es la SKU de la puerta de enlace para la puerta de enlace de red virtual. En el portal, la SKU predeterminado es **Básica**. Para más información acerca de las SKU de puerta de enlace, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![tamaño de puerta de enlace](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Seleccione el **tipo de enrutamiento** de su puerta de enlace. Las configuraciones P2S requieren un tipo de enrutamiento **dinámico**. Haga clic en **Aceptar** cuando haya terminado de configurar la hoja.

    ![Configurar tipo de enrutamiento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. En la hoja **Nueva conexión VPN**, haga clic en **Aceptar** en la parte inferior de la hoja para empezar a crear la puerta de enlace de red virtual. Una puerta de enlace de VPN Gateway puede tardar hasta 45 minutos en completarse.

## <a name="generatecerts"></a>Sección 2: Creación de certificados
Azure usa los certificados para autenticar a los clientes de VPN para las VPN de punto a sitio. Después de crear el certificado raíz, exporte los datos (no la clave privada) del certificado público en forma de archivo X.509 .cer codificado en Base 64. Después, cargue los datos del certificado público del certificado raíz en Azure.

Todos los equipos cliente que se conecten a una red virtual mediante una conexión de punto a sitio debe tener instalado un certificado de cliente. El certificado de cliente se genera a partir del certificado raíz y se instala cada equipo cliente. Si no se ha instalado ningún certificado de cliente válido y el cliente intenta conectarse a la red virtual, la autenticación no se realiza.

### <a name="cer"></a>Parte 1: Obtención de la clave pública (.cer) para el certificado raíz

#### <a name="enterprise-certificate"></a>Certificado de empresa
 
Si usa una solución empresarial, puede utilizar la cadena de certificados existente. Obtenga el archivo .cer para el certificado raíz que desee usar.

#### <a name="self-signed-root-certificate"></a>Certificado raíz autofirmado

Si no usa una solución de certificación de empresa, será preciso que cree un certificado raíz autofirmado. Para crear un certificado autofirmado que contenga los campos necesarios para la autenticación P2S, puede usar PowerShell. En [Funcionamiento de los certificados autofirmados para conexiones de punto a sitio](vpn-gateway-certificates-point-to-site.md) puede obtener ayuda con los pasos necesarios para crear un certificado raíz autofirmado.

> [!NOTE]
> Anteriormente, makecert era el método recomendado para crear certificados raíz autofirmados y generar certificados de cliente para conexiones de punto a sitio. Ahora puede usar PowerShell para crear estos certificados. Una ventaja del uso de PowerShell es la posibilidad de crear certificados SHA-2. Para ver los paquetes requeridos, consulte [Funcionamiento de los certificados autofirmados para conexiones de punto a sitio](vpn-gateway-certificates-point-to-site.md).
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar la clave pública para un certificado raíz autofirmado

Para cargarse en Azure, las conexiones de punto a sitio requieren la clave pública (.cer). Los pasos siguientes ayudan a exportar el archivo .cer para el certificado raíz autofirmado.

1. Para obtener un archivo .cer del certificado, abra **Administrar certificados de usuario**.
2. Busque el certificado raíz autofirmado "P2SRootCert" en "Certificados - Usuario actual\Personal\Certificados" y haga clic con el botón derecho. Haga clic en **Todas las tareas** y en **Exportar** para abrir el **Asistente para exportar certificados**.
3. En el asistente, haga clic en **Siguiente**. Seleccione **No exportar la clave privada** y, después, haga clic en **Siguiente**.
4. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base 64 (.CER)** y haga clic en **Siguiente**. 
5. En la página **Archivo que se va a exportar**, vaya a "C:", cree un subdirectorio llamado "cert" y selecciónelo. Asigne al archivo de certificado el nombre "P2SRootCert.cer" y haga clic en **Guardar**. 
6. Haga clic en **Siguiente** y en **Finalizar** para exportar el certificado. Aparece **La exportación se realizó correctamente**. Haga clic en **Aceptar** para cerrar el asistente.

### <a name="genclientcert"></a>Parte 2: Generación de un certificado de cliente

Puede generar un certificado único para cada cliente o puede usar el mismo en varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado. Sin embargo, si todos usan el mismo certificado de cliente y necesita revocarlo, debe generar e instalar certificados nuevos para todos los clientes que usen ese certificado para autenticarse.

#### <a name="enterprise-certificate"></a>Certificado de empresa
- Si usa una solución de certificación de empresa, genere un certificado de cliente con el formato de valor de nombre común "name@yourdomain.com", en lugar del formato "nombreDeDominio\nombreDeUsuario".
- Asegúrese de que el certificado de cliente se base en la plantilla de certificado "Usuario" que tenga "Autenticación de cliente" como primer elemento de la lista de uso, y no Inicio de sesión de tarjeta inteligente, etc. Para comprobar el certificado, haga doble clic en el certificado de cliente y vea **Detalles > Uso mejorado de claves**.

#### <a name="self-signed-root-certificate"></a>Certificado raíz autofirmado 
Si utiliza un certificado raíz autofirmado, consulte [Generate a client certificate using PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) (Generación de un certificado de cliente mediante PowerShell) para ver los pasos necesarios para generar un certificado de cliente que sea compatible con conexiones de punto a sitio.

### <a name="exportclientcert"></a>Parte 3: Exportación del certificado de cliente
Si genera un certificado de cliente desde un certificado raíz autofirmado mediante las instrucciones de [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), este se instala automáticamente en el equipo que utilizó para generarlo. Si desea instalar un certificado de cliente en otro equipo cliente, es preciso que lo exporte.
 
1. Para exportar un certificado de cliente, abra **Administrar certificados de usuario**. Haga clic con el botón derecho en el certificado de cliente que desee exportar y haga clic en **Todas las tareas** y en **Exportar** para abrir el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**, seleccione **Exportar la clave privada** y, luego, haga clic en **Siguiente**.
3. En la página **Formato de archivo de exportación**, deje seleccionados los valores predeterminados. Asegúrese de que **Incluir todos los certificados en la ruta de certificación (si es posible)** esté seleccionado para exportar también la información necesaria del certificado raíz. A continuación, haga clic en **Siguiente**.
4. En la página **Seguridad** , debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. A continuación, haga clic en **Siguiente**.
5. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
6. Haga clic en **Finalizar** para exportar el certificado.

## <a name="upload"></a>Sección 3: Carga del archivo .cer de certificado raíz
Una vez creada la puerta de enlace, puede cargar el archivo .cer para un certificado raíz de confianza en Azure. Se pueden cargar archivos para un máximo de 20 certificados raíz. No cargue la clave privada para el certificado raíz en Azure. Una vez que se carga el archivo .cer, Azure lo utiliza para autenticar a los clientes que se conectan a la red virtual.

1. En la sección **Conexiones VPN** de la hoja de la red virtual, haga clic en el gráfico de **clientes** para abrir la hoja **Conexión VPN de punto a sitio**.

    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. En la hoja **Conexión de punto a sitio**, haga clic en **Administrar certificados** para abrir la hoja **Certificados**.<br>

    ![Hoja Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. En la hoja **Certificados**, haga clic en **Cargar** para abrir la hoja **Cargar certificado**.<br>

    ![Hoja Cargar certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Haga clic en la imagen de la carpeta para buscar el archivo .cer. Seleccione el archivo y haga clic en **Aceptar**. Actualice la página para ver el certificado cargado en la hoja **Certificados**.

    ![Carga del certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Sección 4: Configuración del cliente
Para conectarse a una red virtual mediante una VPN de punto a sitio, cada cliente debe instalar un paquete de configuración de cliente de VPN. El paquete no instala un cliente de VPN. Configura al cliente de VPN nativo de Windows con los parámetros necesarios para conectarse a la red virtual. Para ver la lista de sistema operativos cliente que se admiten, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.

### <a name="part-1-generate-and-install-the-vpn-client-configuration-package"></a>Parte 1: Generación e instalación del paquete de configuración de cliente de VPN
1. En Azure Portal, en la hoja **Información general** de la red virtual, en **Conexiones VPN**, haga clic en el gráfico del cliente para abrir la hoja **Conexión VPN de punto a sitio**.
2. En la parte superior de la hoja **Conexión VPN de punto a sitio**, haga clic en el paquete de descarga que se corresponde con el sistema operativo del cliente en el que se va a instalar:

   * Para los clientes de 64 bits, seleccione **Cliente de VPN (64 bits)**.
   * Para los clientes de 32 bits, seleccione **Cliente de VPN (32 bits)**.

     ![Descargar paquete de configuración de cliente de VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Una vez generado el paquete, descárguelo e instálelo en el equipo cliente. Si ve una ventana emergente de SmartScreen, haga clic en **Más información** y en **Ejecutar de todas formas**. También puede guardar el paquete para instalarlo en otros equipos cliente.

### <a name="part-2-install-an-exported-client-certificate"></a>Parte 2: Instalación de un certificado de cliente exportado

Si desea crear una conexión P2S desde un equipo cliente distinto del que usó para generar los certificados de cliente, debe instalar un certificado de cliente. Al instalar el certificado de cliente, necesitará la contraseña que se creó cuando se exportó el certificado de cliente.

1. Busque el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje **Ubicación de almacén** como **Usuario actual** y, luego, haga clic en **Siguiente**.
2. En la página **File to import** (Archivo para importar), no haga ningún cambio. Haga clic en **Next**.
3. En la página **Protección de clave privada**, escriba la contraseña del certificado, si ha usado una, o compruebe que la entidad de seguridad que instala el certificado sea correcta y, luego, haga clic en **Siguiente**.
4. En la página **Almacén de certificados**, deje la ubicación predeterminada y, luego, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** En la **Advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. Al haberse generado el certificado, puede hacer clic en "Sí" de forma segura. El certificado se importó correctamente.


## <a name="connect"></a>Sección 6: Conexión a Azure
### <a name="connect-to-your-vnet"></a>Conexión a la red virtual
1. Para conectarse a su red virtual, en el equipo cliente, vaya a las conexiones VPN y ubique la que creó. Tiene el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Si esto ocurre, haga clic en **Continuar** para usar privilegios elevados.
2. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado** , compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

    ![Conexión de cliente de VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. La conexión debería establecerse.

    ![Conexión establecida](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

Si tiene problemas para conectarse, compruebe lo siguiente:

- Abra **Administrar certificados de usuario** y vaya a **Entidades de certificación raíz de confianza\Certificados**. Verifique que aparece el certificado raíz. El certificado raíz debe estar presente para que funcione la autenticación. Al exportar un archivo .pfx de certificado de cliente con el valor predeterminado "Incluir todos los certificados en la ruta de certificación (si es posible)", también se exporta la información del certificado raíz. Cuando se instala el certificado de cliente, el certificado raíz también se instala en el equipo cliente. 

- Si va a usar un certificado que se emitió con una solución de CA empresarial y está teniendo problemas de autenticación, compruebe el orden de autenticación en el certificado de cliente. Para comprobar el orden de la lista de autenticación, haga doble clic en el certificado de cliente y vaya a **Detalles > Uso mejorado de claves**. Asegúrese de que la lista muestre "Autenticación de cliente" como primer elemento. Si no es así, debe emitir un certificado de cliente basado en la plantilla Usuario que tenga Autenticación de cliente como primer elemento de la lista. 


### <a name="verify-the-vpn-connection"></a>Comprobación de la conexión VPN
1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecute *ipconfig/all*.
2. Vea los resultados. Observe que la dirección IP recibida es una de las direcciones en el intervalo de direcciones de conectividad de punto a sitio que especificó cuando creó la red virtual. Los resultados deben ser algo parecido a esto:

Ejemplo:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

 
 Si tiene problemas para conectarse a una máquina virtual a través de P2S, use "ipconfig" para comprobar la dirección IPv4 asignada al adaptador de Ethernet en el equipo desde el que intenta conectarse. Si la dirección IP está dentro del intervalo de direcciones de la red virtual a la que se va a conectar o dentro del intervalo de direcciones de su VPNClientAddressPool, esto se conoce como un espacio de direcciones superpuesto. Cuando el espacio de direcciones se superpone de esta manera, el tráfico de red no llega a Azure, sino que se mantiene en la red local. Si los espacios de direcciones de red no se superponen y sigue sin poder conectarse a la máquina virtual, vea [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual](../virtual-machines/windows/troubleshoot-rdp-connection.md).

## <a name="add"></a>Incorporación o eliminación de certificados raíz de confianza

Puede agregar y quitar certificados raíz de confianza de Azure. Cuando se quita un certificado de confianza, los certificados de cliente que se generaron a partir del certificado raíz ya no podrán conectarse a Azure mediante la conexión de punto a sitio. Si desea que los clientes se conecten, deben instalar un nuevo certificado de cliente que se genere a partir de un certificado de confianza en Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para agregar un certificado raíz de confianza

Puede agregar hasta 20 archivos .cer de certificado raíz de confianza a Azure. Para ver instrucciones, consulte [Sección 3: Carga del archivo .cer de certificado raíz](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Eliminación de un certificado raíz de confianza


1. En la sección **Conexiones VPN** de la hoja de la red virtual, haga clic en el gráfico de **clientes** para abrir la hoja **Conexión VPN de punto a sitio**.

    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. En la hoja **Conexión de punto a sitio**, haga clic en **Administrar certificados** para abrir la hoja **Certificados**.<br>

    ![Hoja Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. En la hoja **Certificados**, haga clic en los puntos suspensivos junto al certificado que desea quitar y en **Eliminar**.

     ![Eliminación de un certificado raíz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Revocación de un certificado de cliente
Puede revocar certificados de cliente. La lista de revocación de certificados permite denegar de forma selectiva la conectividad de punto a sitio basada en certificados de cliente individuales. Esto difiere de la forma en que se quita un certificado raíz de confianza. Si quita de Azure un archivo .cer de certificado raíz de confianza, se revoca el acceso para todos los certificados de cliente generados y firmados con el certificado raíz revocado. Al revocarse un certificado de cliente, en lugar del certificado raíz, se permite que el resto de los certificados que se generaron con el certificado raíz sigan usándose para la autenticación de la conexión de punto a sitio.

Lo más habitual es usar el certificado raíz para administrar el acceso a nivel de equipo u organización, mientras que los certificados de cliente revocados se usan para un control de acceso específico para usuarios individuales.

### <a name="to-revoke-a-client-certificate"></a>Para revocar un certificado de cliente

Puede revocar un certificado de cliente si agrega la huella digital a la lista de revocación.

1. Recupere la huella digital del certificado de cliente. Para más información, consulte [Cómo recuperar la huella digital de un certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie la información en un editor de texto y quite todos los espacios de forma que sea una sola cadena continua.
3. Vaya a la hoja **"Nombre de la red virtual clásica" > Conexión VPN de punto a sitio > Certificados** y haga clic en **Lista de revocación** para abrir la hoja Lista de revocación. 
4. En la hoja **Lista de revocación**, haga clic en **+ Agregar certificado** para abrir la hoja **Agregar certificado a la lista de revocación**.
5. En la hoja **Agregar certificado a la lista de revocación**, pegue la huella digital del certificado como una línea continua de texto, sin espacios en blanco. Haga clic en **Aceptar** en la parte inferior de la hoja.
6. Una vez finalizada la actualización, el certificado no se puede usar para conectarse. Los clientes que intenten conectarse con este certificado recibirán un mensaje que indica que el certificado ya no es válido.


## <a name="faq"></a>Preguntas más frecuentes sobre la conexión de punto a sitio

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información. Para más información acerca de las redes y las máquinas virtuales, consulte [Información general sobre las redes de máquina virtual con Linux y Azure](../virtual-machines/linux/azure-vm-network-overview.md).

