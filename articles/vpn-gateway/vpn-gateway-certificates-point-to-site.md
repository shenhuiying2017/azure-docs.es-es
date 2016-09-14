<properties 
   pageTitle="Creación de certificados autofirmados para conexiones entre entornos de red virtual de punto a sitio mediante makecert | Microsoft Azure"
   description="En este artículo se muestran cuáles son los pasos para crear certificados autofirmados mediante makecert en Windows 10."
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
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Funcionamiento de los certificados autofirmados para conexiones de punto a sitio

Este artículo le ayuda a crear un certificado autofirmado mediante **makecert** para luego generar certificados de cliente a partir de él. Los pasos están escritos para makecert en Windows 10. Se ha validado makecert para la creación de certificados que son compatibles con conexiones P2S.

Para conexiones P2S, el método preferido para los certificados es usar la solución de certificados de empresa, lo que garantiza que los certificados de cliente se emiten con el formato de valor de nombre normal 'name@yourdomain.com', en lugar de con el formato 'nombre de dominio NetBIOS\\nombre de usuario'.

Si no tiene una solución empresarial, se necesita un certificado autofirmado para permitir que los clientes P2S se conecten a una red virtual. Somos conscientes de que aunque makecert ha quedado obsoleto, sigue siendo un método válido para crear certificados autofirmados compatibles con conexiones P2S. Trabajamos en otra solución para crear certificados autofirmados pero, por el momento, el método de preferencia es makecert.

## Creación de un certificado autofirmado

Makecert es una forma de crear un certificado autofirmado. Los siguientes pasos le guiarán en la creación de un certificado autofirmado mediante makecert. Estos pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### Para crear un certificado autofirmado

1. Desde un equipo que ejecute Windows 10, descargue e instale el [Kit de desarrollo de software de Windows (SDK) para Windows 10](https://dev.windows.com/es-ES/downloads/windows-10-sdk).

2. Después de la instalación, la utilidad makecert.exe se encuentra en: C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Ejemplo: `C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. A continuación, cree e instale un certificado en el almacén de certificados personal de su equipo. En el ejemplo siguiente se crea un archivo *.cer* correspondiente que se carga en Azure al configurar P2S. Ejecute el comando siguiente como administrador. Reemplace *ARMP2SRootCert* y *ARMP2SRootCert.cer* por el nombre que desea usar en el certificado.<br><br>El certificado se ubicará en la carpeta Certificados: Usuario actual\\Personal\\Certificados.

    	makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Para obtener la clave pública

Como parte de la configuración de Puerta de enlace de VPN para las conexiones de punto a sitio, la clave pública del certificado raíz se carga a Azure.

1. Para obtener un archivo .cer del certificado, abra **certmgr.msc**. Haga clic con el botón derecho en el certificado raíz autofirmado, haga clic en **Todas las tareas** y, luego, en **Exportar**. Se abre el **Asistente para exportar certificados**.

2. En el asistente, haga clic en **Siguiente**, seleccione **No exportar la clave privada** y, luego, haga clic en **Siguiente**.

3. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base 64 (.CER).** A continuación, haga clic en **Siguiente**.

4. En **File to Export** (Archivo para exportar), haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.

5. Haga clic en **Finalizar** para exportar el certificado.

 
### Exportación del certificado autofirmado (opcional)

Puede que desee exportar el certificado autofirmado y almacenarlo de manera segura. Si es necesario, más adelante puede instalarlo en otro equipo y generar más certificados de cliente o exportar otro archivo .cer. Cualquier equipo que tenga instalado un certificado de cliente y que además esté configurado con los ajustes de cliente de VPN adecuados puede conectarse a la red virtual vía P2S. Por ese motivo, querrá asegurarse de que los certificados de cliente solo se generen e instalen cuando sea necesario, y que el certificado autofirmado se almacene de manera segura.

Para exportar el certificado autofirmado como un archivo .pfx, seleccione el certificado raíz y use los mismos pasos descritos en [Exportar un certificado de cliente](#clientkey) para realizar la exportación.

## Creación e instalación de certificados de cliente

No instale el certificado autofirmado directamente en el equipo cliente. Debe generar un certificado de cliente a partir del certificado autofirmado. Luego, el certificado de cliente se exporta e instala en el equipo cliente. Los siguientes pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### Parte 1: Generación de un certificado de cliente a partir de un certificado autofirmado

Los pasos siguientes le llevan por uno de los métodos para generar un certificado de cliente a partir de un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado. Luego, cada certificado de cliente se puede exportar e instalar en el equipo cliente.

1. En el mismo equipo que usó para crear el certificado autofirmado, abra un símbolo del sistema como administrador.

2. En este ejemplo, "ARMP2SRootCert" hace referencia al certificado autofirmado que generó.
	- Cambie *"ARMP2SRootCert"* por el nombre del certificado raíz autofirmado desde el que genera el certificado de cliente.
	- Cambie *ClientCertificateName* por el nombre con el cual desea generar un certificado de cliente.


	Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecute el mismo ejemplo sin modificarlo, el resultado es un certificado de cliente denominado ClientCertificateName en el almacén de certificados personal que se generó a partir del certificado raíz ARMP2SRootCert.

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Todos los certificados se almacenan en su almacén de certificados, en Usuario actual\\Personal\\Certificados, de su equipo. Puede generar tantos certificados de cliente como sean necesarios según este procedimiento.

### <a name="clientkey"></a>Parte 2: Exportación de un certificado de cliente

1. Para exportar un certificado de cliente, abra **certmgr.msc**. Haga clic con el botón derecho en el certificado de cliente que desee exportar, haga clic en **Todas las tareas** y, a continuación, en **Exportar**. Se abre el **Asistente para exportar certificados**.

2. En el asistente, haga clic en **Siguiente**, seleccione **Yes, export the private key** (Sí, exportar la clave privada) y, luego, haga clic en **Siguiente**.

3. En la página **Formato de archivo de exportación**, puede dejar seleccionados los valores predeterminados. A continuación, haga clic en **Siguiente**.
 
4. En la página **Seguridad**, debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. A continuación, haga clic en **Siguiente**.

5. En **File to Export** (Archivo para exportar), haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.

6. Haga clic en **Finalizar** para exportar el certificado.

### Parte 3: Instalación de un certificado de cliente

Cada cliente que quiera conectar a la red virtual mediante una conexión de punto a sitio debe tener instalado un certificado de cliente. Este certificado es adicional al paquete de configuración de VPN necesario. En los pasos siguientes aprenderá a instalar el certificado de cliente de forma manual.

1. Busque el archivo *.pfx* y cópielo en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Deje **Ubicación de almacén** como **Usuario actual** y, luego, haga clic en **Siguiente**.

2. En la página **File to import** (Archivo para importar), no haga ningún cambio. Haga clic en **Siguiente**.

3. En la página **Private key protection** (Protección de clave privada), escriba la contraseña del certificado, si usó una, o compruebe que la entidad de seguridad que instala el certificado sea correcta y, luego, haga clic en **Siguiente**.

4. En la página **Almacén de certificados**, deje la ubicación predeterminada y, luego, haga clic en **Siguiente**.

5. Haga clic en **Finalizar** En la **advertencia de seguridad** para la instalación de certificados, haga clic en **Sí**. El certificado se importó correctamente.

## Pasos siguientes

Continúe con la configuración de punto a sitio.

- Para ver los pasos del modelo de implementación de **Resource Manager**, consulte [Configuración de una conexión de punto a sitio a una red virtual mediante PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
- Para ver los pasos del modelo de implementación **clásica**, consulte [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0831_2016-->