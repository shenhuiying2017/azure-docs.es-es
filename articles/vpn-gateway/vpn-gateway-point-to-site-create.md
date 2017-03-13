---
title: "Configuración de una conexión de VPN Gateway de punto a sitio a Azure Virtual Network mediante el Portal clásico | Microsoft Docs"
description: "Conéctese de forma segura a su instancia de Azure Virtual Network mediante la creación de una conexión de puerta de enlace de VPN de punto a sitio."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 3ff2dcba568ed7ff83154cb6e1f1861ffb32a0d2
ms.lasthandoff: 03/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configuración de una conexión de punto a sitio a una red virtual mediante el portal clásico
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Clásico: Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Clásico - Portal clásico](vpn-gateway-point-to-site-create.md)
> 
> 

Una configuración de punto a sitio (P2S) permite crear una conexión segura entre un equipo cliente individual y una red virtual. Una conexión P2S resulta útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual.

Las conexiones punto a sitio no requieren un dispositivo VPN o una dirección IP pública para que funcionen. Se establece una conexión VPN al iniciar la conexión desde el equipo cliente. Para más información sobre las conexiones de punto a sitio, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.

Este artículo le guía en la creación de una red virtual con una conexión de punto a sitio mediante el modelo de implementación clásica del portal clásico.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Métodos y modelos de implementación para conexiones P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los dos modelos de implementación y los métodos de implementación disponible para las configuraciones de P2S. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Flujo de trabajo básico
![Diagrama de punto a sitio](./media/vpn-gateway-point-to-site-create/p2sclassic.png "punto a sitio")

Los pasos siguientes lo guían por el proceso para crear una conexión de punto a sitio segura con una red virtual. 

La configuración de una conexión de punto a sitio se divide en cuatro secciones. Es importante el orden en el que se configura cada una de estas secciones. No omita pasos ni se adelante.

* **Sección 1** Cree una red virtual y una puerta de enlace de VPN.
* **Sección 2** Cree los certificados usados para la autenticación y cárguelos.
* **Sección 3** Exporte e instale los certificados de cliente.
* **Sección 4** Configure el cliente de VPN.

## <a name="vnetvpn"></a>Sección 1: Creación de una red virtual y una puerta de enlace de VPN
### <a name="part-1-create-a-virtual-network"></a>Paso 1: Creación de una red virtual
1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com). Estos pasos utilizan el Portal clásico, no el Portal de Azure. Actualmente, no se puede crear una configuración P2S mediante el Portal de Azure.
2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.
3. En la página **Detalles de la red virtual** , escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.
   
   * **Nombre**: Nombre de la red virtual. Por ejemplo, "VNet1". Es el nombre al que hará referencia al implementar máquinas virtuales en esta red virtual.
   * **Ubicación**: La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el Este de EE.UU., seleccione esa ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.
4. En la página **Servidores DNS y conectividad VPN** , escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.
   
   * **Servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado del menú contextual. Mediante este valor no se crea un servidor DNS. Le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual. Si desea usar el servicio de resolución de nombres predeterminado de Azure, deje esta sección en blanco.
   * **Configurar VPN de punto a sitio**: Active la casilla.
5. En la página **Conectividad de punto a sitio** , especifique el intervalo de direcciones IP desde la que los clientes de VPN recibirán una dirección IP cuando se conecten. Existen varias reglas con respecto a los intervalos de direcciones que puede especificar. Es importante asegurarse de que el intervalo que especifique no se superponga a ninguno de los que se encuentran en la red local.
6. Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente.
   
   * **Espacio de direcciones**: Incluidas Dirección IP inicial y CIDR (recuento de direcciones).
   * **Agregar espacio de direcciones**: agregue el espacio de direcciones solo si es necesario para el diseño de la red.
7. En la página **Espacios de direcciones de la red virtual** , especifique el intervalo de direcciones que desea usar para la red virtual. Se trata de las direcciones IP dinámicas (DIP) que se asignarán a las máquinas virtuales y a otras instancias de rol que implemente en la red virtual.<br><br>Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los que se usan para la red local. Debe coordinarse con el administrador de la red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para usarlo en la red virtual.
8. Escriba la información siguiente y, a continuación, haga clic en la marca de verificación para comenzar a crear la red virtual.
   
   * **Espacio de direcciones**: Agregue el intervalo de direcciones IP interno que desea usar para esta red virtual, incluida la dirección IP inicial y el recuento. Es importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. 
   * **Agregar subred**: No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tengan DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
   * **Agregar subred de puerta de enlace**: la subred de puerta de enlace es necesaria para una VPN de punto a sitio. Haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se utiliza para la puerta de enlace de red virtual.
9. Una vez que se haya creado la red virtual, verá **Creado** en **Estado** en la página Redes del Portal de Azure clásico. Una vez creada la red virtual, puede crear la puerta de enlace de enrutamiento dinámico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Paso 2: Creación de una puerta de enlace de enrutamiento dinámico
El tipo de puerta de enlace debe configurarse como dinámica. Las puertas de enlace de enrutamiento estáticas no funcionan con esta característica.

1. En el Portal de Azure clásico, en la página **Redes**, haga clic en la red virtual que creó y vaya a la página **Panel**.
2. En la parte inferior de la página **Panel**, haga clic en **Crear puerta de enlace**. Aparece un mensaje **¿Desea crear una puerta de enlace para la red virtual "VNet1"?** Haga clic en **Sí** para empezar a crear la puerta de enlace. La puerta de enlace puede tardar hasta 45 minutos en crearse.

## <a name="generate"></a>Sección 2: Generación y carga de certificados
Los certificados se usan para autenticar a los clientes VPN para VPN de punto a sitio. Puede usar un certificado raíz generado con una solución de certificado de empresa o usar un certificado autofirmado. Puede cargar hasta 20 certificados raíz en Azure. Una vez cargado el archivo .cer, Azure puede usar la información que contiene para autenticar a los clientes que tengan instalado un certificado de cliente. El certificado de cliente se debe generar a partir del mismo certificado que el archivo .cer representa.

En esta sección, hará lo siguiente:

* Obtenga el archivo .cer para un certificado raíz. Puede ser un certificado autofirmado o puede usar su sistema de certificados de empresa.
* Cargue el archivo .cer en Azure.
* Genere certificados de cliente.

### <a name="root"></a>Parte 1: Obtención del archivo .cer para el certificado raíz
Si usa una solución empresarial, puede utilizar la cadena de certificados existente. Obtenga el archivo .cer para el certificado raíz que desee usar.

Si no usa una solución de certificación de empresa, será preciso que cree un certificado raíz autofirmado. Para crear un certificado autofirmado que contenga los campos necesarios para la autenticación P2S, use makecert. [Creación de certificados raíz autofirmados para conexiones P2S](vpn-gateway-certificates-point-to-site.md) lo guiará por los pasos para crear un certificado raíz autofirmado. Sabemos que makecert está en desuso pero, de momento, es la solución admitida.

>[!NOTE]
>Aunque es posible usar PowerShell para crear certificados autofirmados, el certificado que se genera mediante PowerShell no contiene los campos necesarios para la autenticación de punto a sitio.
>


#### <a name="to-obtain-the-cer-file-from-a-self-signed-root-certificate"></a>Para obtener el archivo .cer de un certificado raíz autofirmado

1. Para obtener un archivo .cer de un certificado raíz autofirmado, abra **certmgr.msc** y busque el certificado raíz que creó. El certificado se encuentra normalmente en "Certificates-Current User/Personal/Certificates" y su nombre es el que le asignara cuando lo creó. Haga clic con el botón derecho en el certificado raíz autofirmado, luego en **Todas las tareas** y, por último, en **Exportar**. Se abre el **Asistente para exportar certificados**.
2. En el asistente, haga clic en **Siguiente**, seleccione **No exportar la clave privada** y, después, en **Siguiente**.
3. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base&64; (.CER)**. A continuación, haga clic en **Siguiente**.
4. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.
5. Haga clic en **Finalizar** para exportar el certificado.

### <a name="upload"></a>Parte 2: Carga del archivo .cer de certificado raíz en el Portal de Azure clásico
Agregue un certificado de confianza a Azure. Al agregar un archivo X.509 con codificación Base64 (.cer) a Azure, se está indicando que se confíe en el certificado raíz que representa el archivo.

1. En el Portal de Azure clásico, en la página **Certificados** de la red virtual, haga clic en **Cargar un certificado raíz**.
2. En la página **Carga del certificado** , busque el certificado raíz .cer y, a continuación, haga clic en la marca de verificación.

### <a name="createclientcert"></a>Parte 3: Generación de un certificado de cliente
A continuación, genere los certificados de cliente. Puede generar un certificado único para cada cliente que se vaya a conectar o puede usar el mismo en varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado si es necesario. De lo contrario, si todos usan el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, tendrá que generar e instalar nuevos certificados para todos los clientes que usen el certificado para autenticarse.

####<a name="enterprise-certificate"></a>Certificado de empresa
- Si usa una solución de certificación de empresa, genere un certificado de cliente con el formato de valor de nombre común "name@yourdomain.com", en lugar del formato "nombreDeDominio\nombreDeUsuario".
- Asegúrese de que el certificado de cliente que emita se base en la plantilla de certificado "Usuario" que tenga "Autenticación de usuario" como primer elemento de la lista de uso, y no Inicio de sesión de tarjeta inteligente, etc. Para comprobar el certificado, haga doble clic en el certificado de cliente y vea **Detalles > Uso mejorado de claves**.

####<a name="self-signed-certificate"></a>Certificado autofirmado 
Si va a usar un certificado autofirmado, consulte [Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

## <a name="installclientcert"></a>Sección 3: Exportación e instalación del certificado de cliente
Instale un certificado de cliente en cada equipo que desee conectar a la red virtual. Se necesita un certificado de cliente para la autenticación. Puede automatizar la instalación del certificado de cliente o instalarlo manualmente. En los pasos siguientes aprenderá a exportar e instalar el certificado de cliente de forma manual.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Es un archivo *.pfx* . Asegúrese de anotar o recordar la contraseña (clave) que establezca para este certificado.
3. Copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Escriba la contraseña cuando se le solicite. No modifique la ubicación de instalación.

## <a name="vpnclientconfig"></a>Sección 4: Configuración del cliente de VPN
Para conectarse a la red virtual, también necesita configurar un cliente de VPN. El cliente requiere un certificado de cliente y la configuración de cliente VPN adecuada para conectarse. Para configurar un cliente de VPN, realice los pasos siguientes, en orden.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: Creación del paquete de configuración de cliente de VPN
1. En el Portal de Azure clásico, en la página **Panel** de la red virtual, vaya al menú de vista rápida en la esquina derecha. El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional. Los valores son específicos de la red virtual a la que desea conectarse. Para ver la lista de sistema operativos cliente que se admiten, consulte [Preguntas frecuentes sobre la conexión de punto a sitio](#faq) al final de este artículo.<br><br>Seleccione el paquete de descarga que se corresponde con el sistema operativo del cliente en el que se va a instalar:
   
   * Para clientes de 32 bits, seleccione **Descargar paquete de VPN de cliente de 32 bits**.
   * Para clientes de 64 bits, seleccione **Descargar paquete de VPN de cliente de 64 bits**.
2. Su paquete de cliente tarda unos minutos en crearse. Una vez que se haya completado el paquete, puede descargar el archivo. El archivo *.exe* que descargue se puede almacenar de forma segura en el equipo local.
3. Después de generar y descargar el paquete del cliente VPN del Portal de Azure clásico, podrá instalar el paquete del cliente en el equipo cliente desde el cual desee conectarse a la red virtual. Si planea instalar el paquete de cliente VPN en varios equipos cliente, asegúrese de que cada uno de ellos también tenga instalado un certificado de cliente.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Paso 2: Instalación del paquete de configuración de VPN en el cliente
1. Copie el archivo de configuración localmente en el equipo que desee conectar a la red virtual y haga doble clic en el archivo .exe. 
2. Una vez instalado el paquete, puede iniciar la conexión VPN. El paquete de configuración no está firmado por Microsoft. Puede que desee firmar el paquete mediante el servicio de firma de su organización, o bien firmarlo usted mismo con [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Se puede utilizar el paquete sin firmar. Sin embargo, si el paquete no está firmado, aparece una advertencia cuando se instala.
3. En el equipo cliente, vaya a **Configuración de red** y haga clic en **VPN**. Podrá ver la conexión en la lista. Mostrará el nombre de la red virtual a la que se conectará y tendrá un aspecto similar al siguiente: 
   
    ![cliente de VPN](./media/vpn-gateway-point-to-site-create/vpn.png "cliente de VPN")

### <a name="part-3-connect-to-azure"></a>Parte 3: Conexión a Azure
1. Para conectarse a su red virtual, en el equipo cliente, vaya a las conexiones VPN y ubique la que creó. Tiene el mismo nombre que su red virtual. Haga clic en **Conectar**. Es posible que aparezca un mensaje emergente que haga referencia al uso del certificado. Si esto ocurre, haga clic en **Continuar** para usar privilegios elevados. 
2. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla para **Seleccionar certificado** , compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.
   
    ![cliente de VPN 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "conexión de cliente de VPN")
3. La conexión debería establecerse.
   
    ![cliente de VPN 3](./media/vpn-gateway-point-to-site-create/connected.png "conexión de cliente de VPN 2")

> [!NOTE]
> Si va a usar un certificado que se emitió con una solución de CA empresarial y está teniendo problemas de autenticación, compruebe el orden de autenticación en el certificado de cliente. Para comprobar el orden de la lista de autenticación, haga doble clic en el certificado de cliente y vaya a **Detalles > Uso mejorado de claves**. Asegúrese de que la lista muestre "Autenticación de cliente" como primer elemento. Si no es así, debe emitir un certificado de cliente basado en la plantilla Usuario que tenga Autenticación de cliente como primer elemento de la lista. 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Comprobación de la conexión VPN
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

## <a name="faq"></a>Preguntas más frecuentes sobre la conexión de punto a sitio

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.

Si desea obtener más información acerca de las redes virtuales, consulte la página [Virtual Network documentation](/azure/virtual-network) .


