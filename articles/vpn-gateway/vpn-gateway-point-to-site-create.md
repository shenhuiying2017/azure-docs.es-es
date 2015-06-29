<properties
   pageTitle="Configuración de una conexión VPN de punto a sitio a una red virtual de Azure"
   description="Conéctese a la seguridad de red virtual mediante la creación de una conexión VPN de sitio a punto."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/12/2015"
   ms.author="cherylmc"/>

# Configuración de una conexión VPN de punto a sitio a una red virtual de Azure

La configuración de una conexión de punto a sitio conlleva varios pasos, pero es una excelente manera de tener una conexión segura desde su equipo a la red virtual sin adquirir y configurar un dispositivo VPN. Hay tres partes principales en la configuración de una VPN de punto a sitio: la red virtual y la puerta de enlace, los certificados usados para la autenticación y el cliente VPN que se utiliza para conectarse a la red virtual. El orden en el que se configura cada una de ellas es importante, por lo que no debe omitir pasos ni adelantarse.

1. [Configuración de una red virtual y una puerta de enlace de enrutamiento dinámico](#configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Creación de certificados](#create-your-certificates)
3. [Configuración del cliente VPN](#configure-your-VPN-client)

## Configuración de una red virtual y una puerta de enlace de enrutamiento dinámico

Una conexión de punto a sitio requiere una red virtual con una puerta de enlace de enrutamiento dinámico. Los pasos siguientes le guiarán a través de la creación de ambos componentes.

### Crear una red virtual

1. Inicie sesión en el **Portal de administración**.
1. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.
1. En la página **Detalles de la red virtual**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha. Para obtener más información sobre la configuración de la página de detalles, consulte la [página Detalles de red virtual](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails).
	- **Nombre**: Nombre de la red virtual. Por ejemplo, "VNetEast". Este será el nombre al que hará referencia al implementar máquinas virtuales e instancias de PaaS en esta red virtual.
	- **Ubicación**: La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el Este de EE.UU., seleccione esa ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.
1. En la página **Servidores DNS y conectividad VPN**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha. Para obtener más información, consulte la [página Servidores DNS y conectividad VPN](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS).
	- **Servidores DNS**: Escriba el nombre y la dirección IP del servidor DNS o seleccione un servidor DNS previamente registrado en la lista desplegable. Este valor no crea un servidor DNS; permite especificar los servidores DNS que desea usar para la resolución de nombres para esta red virtual. Si desea usar el servicio de resolución de nombres predeterminado de Azure, deje esta sección en blanco.
	- **Configurar VPN de punto a sitio**: Active la casilla.
1. En la página **Conectividad de punto a sitio**, especifique el intervalo de direcciones IP desde la que los clientes de VPN recibirán una dirección IP cuando se conecten. Existen varias reglas con respecto a los intervalos de direcciones que se pueden especificar. Es muy importante asegurarse de que el intervalo que especifique no se superponga a ninguno de los intervalos que se encuentra en la red local. Consulte la página [Conectividad punto a sitio](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT) para obtener más información.
1. Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente.
 - **Espacio de direcciones**: Incluidas Dirección IP inicial y CIDR (recuento de direcciones).
 - **Agregar espacio de direcciones**: Solo se agrega si es necesario para el diseño de la red.
1. En la página **Espacios de direcciones de la red virtual**, especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual. Existen varias reglas relativas al espacio de direcciones de la red virtual, por lo que puede que desee consultar la página Espacios de direcciones de la red virtual para obtener más información. Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para el uso en la red virtual.
1. Escriba la información siguiente y, a continuación, haga clic en la marca de verificación para comenzar a crear la red virtual.
 - **Espacio de direcciones**: Agregue el intervalo de direcciones IP interno que desea usar para esta red virtual, incluida la dirección IP inicial y el recuento. Existen varias reglas relativas al espacio de direcciones de la red virtual, por lo que puede que desee consultar la página [Espacios de direcciones de la red virtual](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS) para obtener más información. Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para el uso en la red virtual.
 - **Agregar subred**: No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tengan DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
 - **Agregar subred de puerta de enlace**: La subred de puerta de enlace es necesaria para una VPN de punto a sitio. Haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se utiliza para la puerta de enlace de red virtual.
1. Cuando se haya creado la red virtual, verá **Creada** bajo **Estado** en la página Redes del Portal de administración. Una vez creada la red virtual, puede crear la puerta de enlace de enrutamiento dinámico.

### Creación de una puerta de enlace de enrutamiento dinámico

1. En el **Portal de administración de**, en la página **Redes**, haga clic en la red virtual recién creada y navegue hasta la página **Panel**.
1. Haga clic en **Crear puerta de enlace** en la parte inferior de la página Panel. Aparecerá un mensaje que lo solicita **¿Desea crear una puerta de enlace para la red virtual "yournetwork"?**. Haga clic en **Sí** para empezar a crear la puerta de enlace. Puede tardar unos 15 minutos en crear la puerta de enlace.

## Creación de certificados

Los certificados se utilizan para autenticar a los clientes VPN para VPN de punto a sitio. Este procedimiento consta de varios pasos. Utilice los vínculos siguientes para completar cada paso, por orden.

1. [Generación de un certificado autofirmado raíz](#generate-a-self-signed-root-certificate): Actualmente solo se admiten certificados raíz autofirmados
2. [Carga del archivo de certificado raíz en el Portal de administración](#upload-the-root-certificate-file-to-the-Management-Portal)
3. [Generación de un certificado de cliente](#generate-a-client-certificate)
4. [Exportación e instalación del certificado de cliente](#export-and-install-the-client-certificate)

### Creación de un certificado raíz autofirmado

1. Una forma de crear un certificado X.509 es mediante la herramienta de creación de certificados (makecert.exe). Para utilizar makecert, descargue e instale [Microsoft Visual Studio Express 2013 para escritorio de Windows](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), que es gratuito.
2. Navegue hasta la carpeta **Visual Studio Tools** e inicie el símbolo del sistema como administrador.
3. El comando del ejemplo siguiente creará e instalará un certificado raíz en el almacén de certificados Personal en su equipo y también creará un archivo *.cer* correspondiente que podrá cargar más adelante en el Portal de administración.
4. Cambie al directorio en el que desee que se encuentre el archivo .cer y ejecute el comando mostrado a continuación, donde *RootCertificateName* es el nombre que desea usar para el certificado. Si ejecuta el ejemplo siguiente sin realizar ningún cambio, el resultado será un certificado raíz y el archivo *RootCertificateName.cer* correspondiente.

>[AZURE.NOTE]Puesto que ha creado un certificado raíz desde el que se generarán los certificados de cliente, puede que desee exportar este certificado junto con su clave privada y guardarlo en una ubicación segura donde se pueda recuperar.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Carga del archivo de certificado raíz en el Portal de administración

1. Cuando generó un certificado raíz autofirmado en el procedimiento anterior, también se crea un archivo *.cer*. Ahora podrá cargar este archivo en el Portal de administración. Tenga en cuenta que el archivo .cer no contiene la clave privada del certificado raíz.
1. En el Portal de administración, en la página **Certificados** de la red virtual, haga clic en **Cargar un certificado raíz**.
1. En la página **Carga del certificado**, busque el certificado raíz .cer y, a continuación, haga clic en la marca de verificación.

### Generación de un certificado de cliente

1. En el mismo equipo que usó para crear el certificado raíz autofirmado, abra una ventana del símbolo del sistema de Visual Studio como administrador.
2. Cambie el directorio a la ubicación donde desea guardar el archivo de certificado de cliente. *RootCertificateName* hace referencia al certificado raíz autofirmado que ha generado. Si ejecuta el ejemplo siguiente (cambiando RootCertificateName por el nombre de su certificado raíz), el resultado será un certificado de cliente denominado "ClientCertificateName" en el almacén de certificados personales.
3. Escriba el siguiente comando:

    makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos los certificados se almacenan en el almacén de certificados personales del equipo. Compruebe *certmgr* para verificarlo. Puede generar tantos certificados de cliente como sean necesarios según este procedimiento. Se recomienda crear certificados de cliente único para cada equipo que desee conectar a la red virtual.

### Exportación e instalación del certificado de cliente

1. Se debe instalar un certificado de cliente en cada equipo que desee conectar a la red virtual. Esto significa que probablemente creará varios certificados de cliente y, a continuación, tendrá que exportarlos. Para exportar los certificados de cliente, utilice *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desea exportar, haga clic en **Todas las tareas**, y, a continuación, haga clic en **Exportar**.
2. Exporte el *certificado de cliente* con la clave privada. Este será un archivo *.pfx*. Asegúrese de anotar o recordar la contraseña (clave) que establezca para este certificado.
3. Copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Escriba la contraseña cuando se le solicite. No modifique la ubicación de instalación.

## Configuración del cliente VPN

Para conectarse a la red virtual, también necesitará configurar el cliente VPN. El cliente requiere un certificado de cliente y la configuración de cliente VPN adecuada para conectarse.

### Creación del paquete de configuración de cliente VPN

1. En el Portal de administración, en la página Panel de la red virtual, navegue al **menú de vista rápida** en la esquina derecha y haga clic en el paquete VPN que pertenece al cliente al que desea conectar a su red virtual. Se admiten los siguientes sistemas operativos de cliente:
 - Windows 7 (32 bits y 64 bits)
 - Windows Server 2008 R2 (solo 64 bits)
 - Windows 8 (32 bits y 64 bits)
 - Windows 8.1 (32 bits y 64 bits)
 - Windows Server 2012 (solo 64 bits)
 - Windows Server 2012 R2 (solo 64 bits)

1. Seleccione el paquete de descarga que se corresponde con el sistema operativo de cliente en el que se va a instalar:
 - Para los clientes de 32 bits, seleccione **Descargar el paquete de VPN de cliente de 32 bits**
 - Para los clientes de 64 bits, seleccione **Descargar el paquete de VPN de cliente de 64 bits**
1. Su paquete de cliente puede tardar unos minutos en crearse. Una vez que se haya completado el paquete, podrá descargar el archivo. El archivo *.exe* que descargue se puede almacenar de forma segura en el equipo local.
1. Después de generar y descargar el paquete de cliente VPN desde el Portal de administración, puede instalar el paquete de cliente en el equipo cliente desde el que desea conectar a la red virtual. Si planea instalar el paquete de cliente VPN en varios equipos cliente, asegúrese de que cada uno de ellos también tiene instalado un certificado de cliente. El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional.

### Instalación del paquete de configuración de VPN en el cliente e inicio de la conexión

1. Copie el archivo de configuración localmente en el equipo que desea conectar a la red virtual y haga doble clic en el archivo .exe. Una vez instalado el paquete, puede iniciar la conexión VPN. Tenga en cuenta que el paquete de configuración no está firmado por Microsoft. Puede que desee firmar el paquete mediante el servicio de firma de su organización o firmarlo usted mismo mediante [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764(v=vs.85).aspx). Se puede utilizar el paquete sin firmar. Sin embargo, si el paquete no está firmado, aparecerá una advertencia cuando se instala el paquete.
2. En el equipo cliente, navegue a las conexiones VPN y busque la conexión VPN que acaba de crear. Tendrá el mismo nombre que su red virtual. Haga clic en **Conectar**.
3. Aparece un mensaje emergente que se usa para crear un certificado autofirmado para el extremo de la puerta de enlace. Haga clic en **Continuar** para usar privilegios elevados.
4. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión.
5. Si ve una pantalla para **Seleccionar certificado**, compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.
6. Ahora está conectado a la red virtual y tiene acceso total a todos los servicios y máquinas virtuales hospedados en la red virtual.

### Comprobación de la conexión VPN

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecute *ipconfig/all*.
2. Vea los resultados. Observe que la dirección IP recibida es una de las direcciones en el intervalo de direcciones de conectividad de punto a sitio que especificó cuando creó la red virtual. Los resultados deben ser algo parecido a esto:

Ejemplo:



    PPP adapter VNetEast:
		Connection-specific DNS Suffix .:
		Description.....................: VNetEast
		Physical Address................:
		DHCP Enabled....................: No
		Autoconfiguration Enabled.......: Yes
		IPv4 Address....................: 192.168.130.2(Preferred)
		Subnet Mask.....................: 255.255.255.255
		Default Gateway.................:
		NetBIOS over Tcpip..............: Enabled



## Otras referencias


Puede obtener más información acerca de la conectividad de red virtual entre entornos en este artículo: [Sobre la conectividad segura entre locales de redes virtuales](https://msdn.microsoft.com/library/azure/dn133798.aspx)

Si desea configurar una conexión VPN de sitio a sitio, consulte [Configuración de una conexión VPN de sitio a sitio](vpn-gateway-site-to-site-create.md)

Puede agregar máquinas virtuales a la red virtual. Consulte [Creación de una máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md)

Si desea configurar una conexión de red virtual mediante RRAS, consulte [Conexión VPN sitio a sitio en la Red virtual de Azure mediante el Servicio de enrutamiento y acceso remoto (RRAS) de Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)
 

<!---HONumber=58_postMigration-->