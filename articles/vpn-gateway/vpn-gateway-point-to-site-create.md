<properties
   pageTitle="Configuración de una conexión VPN de punto a sitio a una red virtual | Microsoft Azure"
   description="Conéctese de forma segura a la red virtual de Azure mediante la creación de una conexión VPN de punto a sitio. Instrucciones para redes virtuales creadas con el modelo de implementación de administración del servicio (clásico)."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="cherylmc"/>

# Configuración de una conexión VPN de punto a sitio a una red virtual


En este artículo se hace referencia a las conexiones de punto a sitio para redes virtuales que se crean usando el modo de implementación clásica (Administración de servicios).

**En este momento, no se admiten las conexiones de punto a sitio mediante una red virtual creada en el modo de implementación del Administrador de recursos de Azure.** Esta página se actualizará cuando esta característica se admita para el modelo de implementación del Administrador de recursos.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

El siguiente procedimiento le guiará paso a paso para crear una conexión de punto a sitio segura mediante una red virtual. Aunque la configuración de una conexión de punto a sitio conlleva varios pasos, es una excelente forma de tener una conexión segura desde su equipo a la red virtual sin adquirir ni configurar un dispositivo VPN. Hay tres partes principales en la configuración de una VPN de punto a sitio: la red virtual y la puerta de enlace de VPN, los certificados usados para la autenticación y el cliente VPN que se utiliza para conectarse a la red virtual. El orden en el que se configura cada una de ellas es importante, por lo que no debe omitir pasos ni adelantarse.


1. Creación de una red virtual y una puerta de enlace de VPN
2. Generación y carga de certificados
3. Configuración del cliente VPN

## 1\. Crear una red virtual y una puerta de enlace de VPN


Una conexión de punto a sitio requiere una red virtual con una puerta de enlace de enrutamiento dinámico. Los pasos siguientes le guían por los pasos siguientes:

Paso 1: Crear una red virtual

Paso 2: Crear una puerta de enlace de enrutamiento dinámico

### Paso 1: Crear una red virtual

1. Inicie sesión en el **Portal de Azure** (no en el Portal de vista previa).
1. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.
1. En la página **Detalles de la red virtual**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.
	- **Nombre**: Nombre de la red virtual. Por ejemplo, "VNetEast". Este será el nombre al que hará referencia al implementar máquinas virtuales e instancias de PaaS en esta red virtual.
	- **Ubicación**: La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el Este de EE.UU., seleccione esa ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.
1. En la página **Servidores DNS y conectividad VPN**, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha.
	- **Servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado del menú contextual. Este valor no crea un servidor DNS; permite especificar los servidores DNS que desea usar para la resolución de nombres para esta red virtual. Si desea usar el servicio de resolución de nombres predeterminado de Azure, deje esta sección en blanco.
	- **Configurar VPN de punto a sitio**: Active la casilla.
1. En la página **Conectividad de punto a sitio**, especifique el intervalo de direcciones IP desde la que los clientes de VPN recibirán una dirección IP cuando se conecten. Existen varias reglas con respecto a los intervalos de direcciones que se pueden especificar. Es muy importante asegurarse de que el intervalo que especifique no se superponga a ninguno de los intervalos que se encuentra en la red local.
1. Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente.
 - **Espacio de direcciones**: Incluidas Dirección IP inicial y CIDR (recuento de direcciones).
 - **Agregar espacio de direcciones**: Solo se agrega si es necesario para el diseño de la red.
1. En la página **Espacios de direcciones de la red virtual**, especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual. Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para el uso en la red virtual.
1. Escriba la información siguiente y, a continuación, haga clic en la marca de verificación para comenzar a crear la red virtual.
 - **Espacio de direcciones**: Agregue el intervalo de direcciones IP interno que desea usar para esta red virtual, incluida la dirección IP inicial y el recuento. Es importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de red, quien es posible que necesite definir un intervalo de direcciones IP desde el espacio de direcciones de red local para el uso en la red virtual.
 - **Agregar subred**: No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tengan DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
 - **Agregar subred de puerta de enlace**: La subred de puerta de enlace es necesaria para una VPN de punto a sitio. Haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se utiliza para la puerta de enlace de red virtual.
1. Una vez creada la red virtual, verá **Creada** como **Estado** en la página de redes del portal de Azure. Una vez creada la red virtual, puede crear la puerta de enlace de enrutamiento dinámico.

### Paso 2: Crear una puerta de enlace de enrutamiento dinámico

El tipo de puerta de enlace debe configurarse como dinámica. Las puertas de enlace de enrutamiento estáticas no funcionan con esta característica.

1. En el portal de Azure, en la página **Redes**, haga clic en la red virtual recién creada y vaya a la página **Panel**.
1. Haga clic en **Crear puerta de enlace**, en la parte inferior de la página **Panel**. Aparecerá un mensaje que lo solicita **¿Desea crear una puerta de enlace para la red virtual "yournetwork"?**. Haga clic en **Sí** para empezar a crear la puerta de enlace. Puede tardar unos 15 minutos en crear la puerta de enlace.

## 2\. Generación y carga de certificados

Los certificados se utilizan para autenticar a los clientes VPN para VPN de punto a sitio. Anteriormente, era necesario para generar su propio certificado autofirmado. Ahora, puede usar los certificados que se generaron mediante una solución empresarial. Puede cargar hasta 20 certificados raíz en Azure.

Si desea usar un certificado autofirmado, los pasos siguientes le guiarán a través del proceso. Si planea usar una solución de certificados de empresa, los pasos dentro de cada sección serán diferentes, pero todavía será necesario hacer lo siguiente:


Paso 1: Identificar o generar un certificado raíz

Paso 2: Cargar el archivo .cer de certificado raíz en el portal de Azure

Paso 3: Generar un certificado de cliente

Paso 4: Exportar e instalar el certificado de cliente


### Paso 1: Identificar o generar un certificado raíz

Si no usa una solución de certificados de empresa, deberá generar un certificado raíz autofirmado. Los pasos siguientes funcionan en Windows 8. Estamos en proceso de actualizarlos con nuevos pasos para Windows 10.

1. Una forma de crear un certificado X.509 es mediante la herramienta de creación de certificados (makecert.exe). Para usar makecert, descargue e instale [Microsoft Visual Studio Express](https://www.visualstudio.com/products/visual-studio-express-vs.aspx), que es gratuito.
2. Vaya a la carpeta de Visual Studio Tools e inicie el símbolo del sistema como administrador.
3. El comando del ejemplo siguiente creará e instalará un certificado raíz en el almacén de certificados personales de su equipo y también creará un archivo *.cer* correspondiente que podrá cargar más adelante en el portal de Azure.
4. Cambie al directorio en el que el archivo .cer se va a ubicar y ejecute el comando siguiente, donde *RootCertificateName* es el nombre que desea usar para el certificado. Si ejecuta el ejemplo siguiente sin realizar ningún cambio, el resultado será un certificado raíz y el archivo *NombreCertificadoRaíz.cer* correspondiente.

>[AZURE.NOTE]Puesto que ha creado un certificado raíz desde el que se generarán los certificados de cliente, puede que desee exportar este certificado junto con su clave privada y guardarlo en una ubicación segura donde se pueda recuperar.

    makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

### Paso 2: Cargar el archivo de certificado raíz en el Portal de Azure

Deberá cargar el archivo .cer correspondiente para cada certificado de raíz en Azure. Puede cargar hasta 20 certificados.

1. Cuando generó un certificado raíz autofirmado en el procedimiento anterior, también creó un archivo *.cer*. Ahora podrá cargar este archivo en el portal de Azure. Tenga en cuenta que el archivo .cer no contiene la clave privada del certificado raíz. Puede cargar hasta 20 certificados raíz.
1. En el portal de Azure, en la página **Certificados** de la red virtual, haga clic en **Cargar un certificado raíz**.
1. En la página **Carga del certificado**, busque el certificado raíz .cer y, a continuación, haga clic en la marca de verificación.

### Paso 3: Generar un certificado de cliente

Los pasos siguientes sirven para generar un certificado de cliente desde el certificado raíz autofirmado. Si usa una solución de certificados de empresa, siga las instrucciones para la solución que está usando.

1. En el mismo equipo que usó para crear el certificado raíz autofirmado, abra una ventana del símbolo del sistema de Visual Studio como administrador.
2. Cambie el directorio a la ubicación donde desea guardar el archivo de certificado de cliente. *RootCertificateName* hace referencia al certificado raíz autofirmado que ha generado. Si ejecuta el ejemplo siguiente (cambiando el NombreCertificadoRaíz por el nombre de su certificado raíz), el resultado será un certificado de cliente denominado "NombreCertificadoCliente" en el almacén de certificados personales.
3. Escriba el siguiente comando:

    makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos los certificados se almacenan en el almacén de certificados personales del equipo. Compruebe *certmgr* para verificarlo. Puede generar tantos certificados de cliente como sean necesarios según este procedimiento. Se recomienda crear certificados de cliente único para cada equipo que desee conectar a la red virtual.

### Paso 4: Exportar e instalar el certificado de cliente

Es un paso obligatorio la instalación de un certificado de cliente en cada equipo que desee conectar a la red virtual. Los pasos siguientes le guiarán por la instalación manual del certificado de cliente.

1. Se debe instalar un certificado de cliente en cada equipo que desee conectar a la red virtual. Esto significa que probablemente creará varios certificados de cliente y, a continuación, tendrá que exportarlos. Para exportar los certificados de cliente, utilice *certmgr.msc*. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**.
2. Exporte el *certificado de cliente* con la clave privada. Este será un archivo *.pfx*. Asegúrese de anotar o recordar la contraseña (clave) que establezca para este certificado.
3. Copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Escriba la contraseña cuando se le solicite. No modifique la ubicación de instalación.

## 3\. Configuración del cliente VPN

Para conectarse a la red virtual, también necesitará configurar el cliente VPN. El cliente requiere un certificado de cliente y la configuración de cliente VPN adecuada para conectarse.

Para configurar el cliente VPN, haga lo siguiente, en este orden:

Paso 1: Crear un paquete de configuración de cliente VPN

Paso 2: Instalar el paquete de configuración de VPN en el cliente e iniciar la conexión

Paso 3: Comprobar la conexión

### Paso 1: Crear el paquete de configuración de cliente VPN

1. En el portal de Azure, en la página **Panel** correspondiente a la red virtual, vaya al menú de vista rápida, en la esquina derecha, y haga clic en el paquete VPN que pertenece al cliente que desea conectar a la red virtual.
2. 
Se admiten los siguientes sistemas operativos de cliente:
 - Windows 7 (32 bits y 64 bits)
 - Windows Server 2008 R2 (solo 64 bits)
 - Windows 8 (32 bits y 64 bits)
 - Windows 8.1 (32 bits y 64 bits)
 - Windows Server 2012 (solo 64 bits)
 - Windows Server 2012 R2 (solo 64 bits)
 - Windows 10 (actualmente en validación)

1. Seleccione el paquete de descarga que se corresponde con el sistema operativo de cliente en el que se va a instalar:
 - Para clientes de 32 bits, seleccione **Descargar paquete de VPN de cliente de 32 bits**.
 - Para clientes de 64 bits, seleccione **Descargar paquete de VPN de cliente de 64 bits**.
1. Su paquete de cliente puede tardar unos minutos en crearse. Una vez que se haya completado el paquete, podrá descargar el archivo. El archivo *.exe* que descargue se puede almacenar de forma segura en el equipo local.
1. Después de generar y descargar el paquete de VPN de cliente del portal de Azure, podrá instalar el paquete de cliente en el equipo cliente desde el cual desee conectarse a la red virtual. Si planea instalar el paquete de cliente VPN en varios equipos cliente, asegúrese de que cada uno de ellos también tenga instalado un certificado de cliente. El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional.

### Paso 2: Instalar el paquete de configuración de VPN en el cliente e iniciar la conexión

1. Copie el archivo de configuración localmente en el equipo que desea conectar a la red virtual y haga doble clic en el archivo .exe. Una vez instalado el paquete, puede iniciar la conexión VPN. Tenga en cuenta que el paquete de configuración no está firmado por Microsoft. Puede que desee firmar el paquete mediante el servicio de firma de su organización, o bien firmarlo usted mismo con [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Se puede utilizar el paquete sin firmar. Sin embargo, si el paquete no está firmado, aparecerá una advertencia cuando se instala el paquete.
2. En el equipo cliente, navegue a las conexiones VPN y busque la conexión VPN que acaba de crear. Tendrá el mismo nombre que su red virtual. Haga clic en **Conectar**.
3. Aparece un mensaje emergente que se usa para crear un certificado autofirmado para el extremo de la puerta de enlace. Haga clic en **Continuar** para usar privilegios elevados.
4. En la página de estado **Conexión**, haga clic en **Conectar** para iniciar la conexión.
5. Si ve una pantalla para **Seleccionar certificado**, compruebe que el certificado de cliente que se muestra es el que desea utilizar para conectarse. Si no es así, use la flecha de la lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.
6. Ahora está conectado a la red virtual y tiene acceso total a todos los servicios y máquinas virtuales hospedados en la red virtual.

### Paso 3: Comprobar la conexión VPN

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

## Pasos siguientes

Puede obtener más información acerca de la conectividad de red virtual entre locales en el artículo [Información sobre la conectividad segura entre locales de redes virtuales](vpn-gateway-cross-premises-options.md).

Si desea configurar una conexión VPN de sitio a sitio, consulte [Configurar una red virtual con una conexión de puerta de enlace de VPN de sitio a sitio](vpn-gateway-site-to-site-create.md).

Puede agregar máquinas virtuales a la red virtual. Consulte [Creación de una máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Si desea obtener más información acerca de las redes virtuales, consulte la página [Documentación de redes virtuales](https://azure.microsoft.com/documentation/services/virtual-network/).

<!---HONumber=Nov15_HO3-->