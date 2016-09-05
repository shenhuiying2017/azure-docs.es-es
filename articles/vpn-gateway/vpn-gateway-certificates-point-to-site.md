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
   ms.date="08/15/2016"
   ms.author="cherylmc" />

# Funcionamiento de los certificados autofirmados para conexiones de punto a sitio

Este artículo le ayuda a crear un certificado autofirmado mediante makecert para luego generar certificados de cliente a partir de él. Los pasos están escritos para makecert en Windows 10. Se ha validado makecert para la creación de certificados que son compatibles con conexiones P2S.

Para conexiones P2S, el método preferido para los certificados es usar la solución de certificados de empresa, lo que garantiza que los certificados de cliente se emiten con el formato de valor de nombre normal 'name@yourdomain.com', en lugar de con el formato 'nombre de dominio NetBIOS\\nombre de usuario'.

Si no tiene una solución empresarial, se necesita un certificado autofirmado para permitir que los clientes P2S se conecten a una red virtual. Somos conscientes de que aunque makecert ha quedado obsoleto, sigue siendo un método válido para crear certificados autofirmados compatibles con conexiones P2S.

## Creación de un certificado autofirmado

Makecert es una forma de crear un certificado autofirmado. Los siguientes pasos le guiarán en la creación de un certificado autofirmado mediante makecert. Estos pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### Para crear un certificado autofirmado

1. Desde un equipo que ejecute Windows 10, descargue e instale el [Kit de desarrollo de software de Windows (SDK) para Windows 10](https://dev.windows.com/es-ES/downloads/windows-10-sdk).

2. Después de la instalación, la utilidad makecert.exe se encuentra en: C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>.
		
	Ejemplo:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. A continuación, cree e instale un certificado en el almacén de certificados personal de su equipo. En el ejemplo siguiente se crea un archivo *.cer* correspondiente que se carga en Azure al configurar P2S. Ejecute el siguiente comando, como administrador, donde *NombreDeCertificado* es el nombre que desea usar para el certificado.<br><br>Si ejecuta el ejemplo siguiente sin realizar ningún cambio, el resultado es un certificado y el correspondiente archivo *NombreDeCertificado.cer*. Puede encontrar el archivo .cer en el directorio desde donde ejecutó el comando. El certificado estará en sus Certificados, en Usuario actual\\Personal\\Certificados.

    	makecert -sky exchange -r -n "CN=CertificateName" -pe -a sha1 -len 2048 -ss My "CertificateName.cer"

4. El certificado autofirmado se utiliza para crear certificados de cliente. Al cargar el archivo .cer para el certificado autofirmado como parte de la configuración de P2S, le está indicando a Azure que confíe en los certificados que usan los equipos cliente.<br><br>Cualquier equipo con un certificado de cliente instalado que también esté configurado con los valores de cliente de VPN adecuados puede conectarse a la red virtual mediante P2S. Por ese motivo, querrá asegurarse de que los certificados de cliente solo se generen e instalen cuando sea adecuado, y que este certificado autofirmado tenga una copia de seguridad y esté almacenado de forma seguridad. Si es necesario, más adelante puede instalar este certificado autofirmado en otro equipo y generar más certificados de cliente o exportar el archivo .cer.
 

## Creación e instalación de certificados de cliente

El certificado autofirmado no es lo que se instala en los clientes. Debe generar un certificado de cliente a partir del certificado autofirmado. Luego, el certificado de cliente se exporta e instala en el equipo cliente. Los siguientes pasos no son específicos del modelo de implementación. Son válidos para el Administrador de recursos y la versión clásica.

### Parte 1: Generación de un certificado de cliente a partir de un certificado autofirmado

Los pasos siguientes le llevan por uno de los métodos para generar un certificado de cliente a partir de un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado. Luego, cada certificado de cliente se puede exportar e instalar en el equipo cliente.

1. En el mismo equipo que usó para crear el certificado autofirmado, abra un símbolo del sistema como administrador.

2. Cambie el directorio a la ubicación donde desea guardar el archivo de certificado de cliente. *NombreDeCertificado* hace referencia al certificado autofirmado que ha generado. Si ejecuta el ejemplo siguiente (cambiando el valor de NombreDeCertificado por el nombre del certificado), el resultado será un certificado de cliente denominado "NombreDeCertificadoDeCliente" en el almacén de certificados personales.

3. Escriba el siguiente comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "CertificateName" -is my -a sha1

4. Todos los certificados se almacenan en su almacén de certificados, en Usuario actual\\Personal\\Certificados, de su equipo. Puede generar tantos certificados de cliente como sean necesarios según este procedimiento.

### Parte 2: Exportación de un certificado de cliente

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

- Para ver los pasos del modelo de implementación de **Resource Manager**, consulte [Configuración de una conexión punto a sitio a una red virtual mediante PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
- Para ver los pasos del modelo de implementación **clásica**, consulte [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0824_2016-->