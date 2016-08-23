<properties
   pageTitle="Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico | Microsoft Azure"
   description="Conéctese de forma segura a la Red virtual de Azure mediante la creación de una conexión VPN de punto a sitio."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico

> [AZURE.SELECTOR]
- [PowerShell: administrador de recursos](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Clásico](vpn-gateway-point-to-site-create.md)

Una configuración de punto a sitio (P2S) permite crear una conexión segura entre un equipo cliente individual y una red virtual. Una conexión P2S resulta útil cuando desea conectarse a la red virtual desde una ubicación remota, como desde casa o desde una conferencia, o si solo tiene unos pocos clientes que necesitan conectarse a una red virtual.

Las conexiones punto a sitio no requieren un dispositivo VPN o una dirección IP pública para que funcionen. Se establece una conexión VPN al iniciar la conexión desde el equipo cliente. Para más información sobre las conexiones de punto a sitio, consulte las [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y [Planeamiento y diseño](vpn-gateway-plan-design.md).

En este artículo se hace referencia a las conexiones de punto a sitio con una red virtual creadas con el modelo de implementación clásica. En los pasos de este artículo se usa el Portal clásico. Actualmente, no se puede crear esta configuración mediante el Portal de Azure.

**Modelos de implementación y herramientas para las conexiones de punto a sitio**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

**Información acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

![Diagrama de punto a sitio](./media/vpn-gateway-point-to-site-create/point2site.png "De punto a sitio")

## Acerca de la creación de una conexión de punto a sitio
 
Los pasos siguientes lo guían por el proceso para crear una conexión de punto a sitio segura con una red virtual.

La configuración de una conexión de punto a sitio se divide en cuatro secciones. Es importante el orden en el que se configura cada una de estas secciones. No omita pasos ni se adelante.

- **Sección 1** Cree una red virtual y una puerta de enlace de VPN.
- **Sección 2** Cree los certificados usados para la autenticación y cárguelos.
- **Sección 3** Exporte e instale los certificados de cliente.
- **Sección 4** Configure el cliente de VPN.

## <a name="vnetvpn"></a>Sección 1: Creación de una red virtual y una puerta de enlace de VPN

### Paso 1: Creación de una red virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/). Estos pasos utilizan el Portal clásico, no el Portal de Azure. Actualmente, no se puede crear una configuración P2S mediante el Portal de Azure.

2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

3. En la página **Detalles de la red virtual**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.
	- **Nombre**: Nombre de la red virtual. Por ejemplo, "VNet1". Es el nombre al que hará referencia al implementar máquinas virtuales en esta red virtual.
	- **Ubicación**: La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el Este de EE.UU., seleccione esa ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.

4. En la página **Servidores DNS y conectividad VPN**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.
	- **Servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado del menú contextual. Mediante este valor no se crea un servidor DNS. Le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual. Si desea usar el servicio de resolución de nombres predeterminado de Azure, deje esta sección en blanco.
	- **Configurar VPN de punto a sitio**: Active la casilla.

5. En la página **Conectividad de punto a sitio**, especifique el intervalo de direcciones IP desde la que los clientes de VPN recibirán una dirección IP cuando se conecten. Existen varias reglas con respecto a los intervalos de direcciones que puede especificar. Es importante asegurarse de que el intervalo que especifique no se superponga a ninguno de los que se encuentran en la red local.

6. Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente.
 - **Espacio de direcciones**: Incluidas Dirección IP inicial y CIDR (recuento de direcciones).
 - **Agregar espacio de direcciones**: agregue el espacio de direcciones solo si es necesario para el diseño de la red.

7. En la página **Espacios de direcciones de la red virtual**, especifique el intervalo de direcciones que desea usar para la red virtual. Se trata de las direcciones IP dinámicas (DIP) que se asignarán a las máquinas virtuales y a otras instancias de rol que implemente en la red virtual.<br><br>Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los que se usan para la red local. Debe coordinarse con el administrador de la red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para usarlo en la red virtual.

8. Escriba la información siguiente y, a continuación, haga clic en la marca de verificación para comenzar a crear la red virtual.
 - **Espacio de direcciones**: Agregue el intervalo de direcciones IP interno que desea usar para esta red virtual, incluida la dirección IP inicial y el recuento. Es importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local.
 - **Agregar subred**: No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tengan DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
 - **Agregar subred de puerta de enlace**: la subred de puerta de enlace es necesaria para una VPN de punto a sitio. Haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se utiliza para la puerta de enlace de red virtual.

9. Una vez que se haya creado la red virtual, verá **Creado** en **Estado** en la página Redes del Portal de Azure clásico. Una vez creada la red virtual, puede crear la puerta de enlace de enrutamiento dinámico.

### Paso 2: Creación de una puerta de enlace de enrutamiento dinámico

El tipo de puerta de enlace debe configurarse como dinámica. Las puertas de enlace de enrutamiento estáticas no funcionan con esta característica.

1. En el Portal de Azure clásico, en la página **Redes**, haga clic en la red virtual que creó y vaya a la página **Panel**.

2. Haga clic en **Crear puerta de enlace**, en la parte inferior de la página **Panel**. Aparece un mensaje **¿Desea crear una puerta de enlace para la red virtual "VNet1"?** Haga clic en **Sí** para empezar a crear la puerta de enlace. Puede tardar unos 15 minutos en crear la puerta de enlace.

## <a name="generate"></a>Sección 2: Generación y carga de certificados

Los certificados se usan para autenticar a los clientes VPN para VPN de punto a sitio. Puede usar un certificado raíz generado con una solución de certificado de empresa o usar un certificado autofirmado. Puede cargar hasta 20 certificados raíz en Azure. Una vez cargado el archivo .cer, Azure puede usar la información que contiene para autenticar a los clientes que tengan instalado un certificado de cliente. El certificado de cliente se debe generar a partir del mismo certificado que el archivo .cer representa.

En esta sección, hará lo siguiente:

- Obtenga el archivo .cer para un certificado raíz. Puede ser un certificado autofirmado o puede usar su sistema de certificados de empresa.
- Cargue el archivo .cer en Azure.
- Genere certificados de cliente.

### <a name="root"></a>Parte 1: Obtención del archivo .cer para el certificado raíz

Si usa un sistema de certificados de empresa, obtenga el archivo .cer del certificado raíz que se va a utilizar. En la [Parte 3](#createclientcert), generará los certificados de cliente a partir del certificado raíz.

Si no usa una solución de certificados de empresa, deberá generar un certificado raíz autofirmado. Para conocer los pasos específicos para Windows 10, consulte el artículo [Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md). El artículo lo guía por el uso de makecert para generar un certificado autofirmado y después exportar el archivo .cer.

### <a name="upload"></a>Parte 2: Carga del archivo .cer de certificado raíz en el Portal de Azure clásico

Agregue un certificado de confianza a Azure. Al agregar un archivo X.509 con codificación Base64 (.cer) a Azure, se está indicando que se confíe en el certificado raíz que representa el archivo.

1. En el Portal de Azure clásico, en la página **Certificados** de la red virtual, haga clic en **Cargar un certificado raíz**.

2. En la página **Carga del certificado**, busque el certificado raíz .cer y, a continuación, haga clic en la marca de verificación.

### <a name="createclientcert"></a>Parte 3: Generación de un certificado de cliente

A continuación, genere los certificados de cliente. Puede generar un certificado único para cada cliente que se vaya a conectar o puede usar el mismo en varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado si es necesario. De lo contrario, si todos usan el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, tendrá que generar e instalar nuevos certificados para todos los clientes que usen el certificado para autenticarse.

- Si usa una solución de certificado de empresa, genere un certificado de cliente con el formato de valor de nombre común "nombre@suDominio.com", en lugar del formato NetBIOS "DOMINIO\\nombreDeUsuario'".

- Si va a usar un certificado autofirmado, consulte [Trabajar con certificados raíz autofirmados para las configuraciones de punto a sitio](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

## <a name="installclientcert"></a>Sección 3: Exportación e instalación del certificado de cliente

Instale un certificado de cliente en cada equipo que desee conectar a la red virtual. Se necesita un certificado de cliente para la autenticación. Puede automatizar la instalación del certificado de cliente o instalarlo manualmente. En los pasos siguientes aprenderá a exportar e instalar el certificado de cliente de forma manual.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Es un archivo *.pfx*. Asegúrese de anotar o recordar la contraseña (clave) que establezca para este certificado.
3. Copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Escriba la contraseña cuando se le solicite. No modifique la ubicación de instalación.

## <a name="vpnclientconfig"></a>Sección 4: Configuración del cliente de VPN

Para conectarse a la red virtual, también necesita configurar un cliente de VPN. El cliente requiere un certificado de cliente y la configuración de cliente VPN adecuada para conectarse. Para configurar un cliente de VPN, realice los pasos siguientes, en orden.

### Parte 1: Creación del paquete de configuración de cliente de VPN

1. En el Portal de Azure clásico, en la página **Panel** de la red virtual, vaya al menú de vista rápida en la esquina derecha. Para ver la lista de sistemas operativos cliente compatibles, consulte la sección [Conexiones de punto a sitio](vpn-gateway-vpn-faq.md#point-to-site-connections) de las Preguntas más frecuentes sobre la puerta de enlace de VPN.<br><br>Seleccione el paquete de descarga que se corresponda con el sistema operativo cliente en el que se va a instalar:
 - Para clientes de 32 bits, seleccione **Descargar paquete de VPN de cliente de 32 bits**.
 - Para clientes de 64 bits, seleccione **Descargar paquete de VPN de cliente de 64 bits**.

2. Su paquete de cliente tarda unos minutos en crearse. Una vez que se haya completado el paquete, puede descargar el archivo. El archivo *.exe* que descargue se puede almacenar de forma segura en el equipo local.

3. Después de generar y descargar el paquete del cliente VPN del Portal de Azure clásico, podrá instalar el paquete del cliente en el equipo cliente desde el cual desee conectarse a la red virtual. Si planea instalar el paquete de cliente VPN en varios equipos cliente, asegúrese de que cada uno de ellos también tenga instalado un certificado de cliente. El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional.

### Paso 2: Instalación del paquete de configuración de VPN en el cliente e inicio de la conexión

1. Copie el archivo de configuración localmente en el equipo que desee conectar a la red virtual y haga doble clic en el archivo .exe. Una vez instalado el paquete, puede iniciar la conexión VPN. El paquete de configuración no está firmado por Microsoft. Puede que desee firmar el paquete mediante el servicio de firma de su organización, o bien firmarlo usted mismo con [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Se puede utilizar el paquete sin firmar. Sin embargo, si el paquete no está firmado, aparece una advertencia cuando se instala.
2. En el equipo cliente, vaya a las conexiones VPN y busque la que creó. Tendrá el mismo nombre que su red virtual. Haga clic en **Conectar**.
3. Aparece un mensaje emergente que se usa para crear un certificado autofirmado para el punto de conexión de la puerta de enlace. Haga clic en **Continuar** para usar privilegios elevados.
4. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión.
5. Si ve una pantalla para **Seleccionar certificado**, compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.
6. Ahora está conectado a la red virtual y tiene acceso total a todos los servicios y máquinas virtuales hospedados en la red virtual.

### Parte 3: Comprobación de la conexión VPN

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

## Pasos siguientes

Puede agregar máquinas virtuales a la red virtual. Vea [Creación de una máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Si desea obtener más información acerca de las redes virtuales, consulte la página [Virtual Network documentation](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=AcomDC_0817_2016-->