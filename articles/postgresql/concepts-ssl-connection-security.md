---
title: "Configuración de la conectividad SSL en Azure Database for PostgreSQL | Microsoft Docs"
description: "Instrucciones e información para configurar Azure Database for PostgreSQL y las aplicaciones asociadas, a fin de usar correctamente las conexiones SSL."
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 05/15/2017
ms.openlocfilehash: 685aa4c2f75b7c3260ca737f7c786157480b2d90
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Configuración de la conectividad SSL en Azure Database for PostgreSQL
Azure Database for PostgreSQL prefiere conectar las aplicaciones cliente al servicio de PostgreSQL mediante la Capa de sockets seguros (SSL). El establecimiento de conexiones SSL entre el servidor de bases de datos y las aplicaciones cliente ofrece protección frente a ataques de tipo "Man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

De forma predeterminada, el servicio de base de datos de PostgreSQL está configurado para requerir la conexión SSL. Tiene la opción de deshabilitar el requisito de SSL para conectar el servicio de base de datos en caso de que la aplicación cliente no admita la conectividad SSL. 

## <a name="enforcing-ssl-connections"></a>Establecimiento de conexiones SSL
Para todos los servidores de Azure Database for PostgreSQL aprovisionados en Azure Portal o con la CLI de Azure, el establecimiento de conexiones SSL está habilitado de forma predeterminada. 

Del mismo modo, las cadenas de conexión que están predefinidas en la configuración de "Cadenas de conexión" del servidor en Azure Portal incluyen los parámetros necesarios para que los lenguajes comunes se conecten al servidor de base de datos mediante SSL. El parámetro SSL varía según el conector, por ejemplo, "ssl = true" o "sslmode = require" o "sslmode = required" y otras variaciones.

## <a name="configure-enforcement-of-ssl"></a>Configuración del establecimiento de SSL
Si lo desea, puede deshabilitar el establecimiento de la conectividad SSL. Microsoft Azure recomienda habilitar siempre la opción de configuración **Enforce SSL connection** (Establecer conexión SSL) para mayor seguridad.

### <a name="using-the-azure-portal"></a>Uso del portal de Azure
Visite el servidor de Azure Database for PostgreSQL y haga clic en **Seguridad de conexión**. Use el botón de alternancia para habilitar o deshabilitar la opción de configuración **Enforce SSL connection** (Establecer conexión SSL). A continuación, haga clic en **Guardar**. 

![Seguridad de conexión - Deshabilitar el establecimiento de SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Puede confirmar la configuración; para ello, consulte la página de **información general** para ver el indicador de **estado de establecimiento de SSL**.

### <a name="using-azure-cli"></a>Uso de la CLI de Azure
Puede habilitar o deshabilitar el parámetro **ssl-enforcement** con los valores `Enabled` o `Disabled` respectivamente en la CLI de Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mypgserver-20170401 --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificación de que la aplicación o el marco admiten conexiones SSL
Muchos marcos de aplicaciones comunes que usan PostgreSQL para servicios de bases de datos, como Drupal y Django, no habilitan SSL de forma predeterminada durante la instalación. La conectividad SSL debe habilitarse después de la instalación o mediante comandos de la CLI específicos de la aplicación. Si el servidor PostgreSQL establece conexiones SSL y la aplicación asociada no está configurada correctamente, es posible que esta última no pueda conectarse al servidor de bases de datos. Consulte la documentación de la aplicación para obtener información sobre cómo habilitar las conexiones SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicaciones que requieren la verificación de certificados para la conectividad SSL
En algunos casos, las aplicaciones requieren un archivo de certificado local generado a partir de un archivo de certificado (.cer) de una entidad de certificación (CA) de confianza para conectarse de forma segura. Consulte los pasos siguientes para obtener el archivo .cer, descodificar el certificado y enlazarlo a la aplicación.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Descarga del archivo de certificado de la entidad de certificación (CA) 
El certificado necesario para comunicarse a través de SSL con el servidor de Azure Database for PostgreSQL se encuentra [aquí](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Descargue el archivo de certificado en una unidad local.

### <a name="download-and-install-openssl-on-your-machine"></a>Descarga e instalación de OpenSSL en el equipo 
Para descodificar el archivo de certificado necesario para que la aplicación se conecte de forma segura al servidor de bases de datos, debe instalar OpenSSL en el equipo local.

#### <a name="for-linux-os-x-or-unix"></a>Para Linux, OS X o Unix
Las bibliotecas OpenSSL se proporcionan en el código fuente directamente desde [OpenSSL Software Foundation](http://www.openssl.org). Las instrucciones siguientes lo guían a través de los pasos necesarios para instalar OpenSSL en un equipo Linux. En este artículo se usan comandos conocidos para trabajar en Ubuntu 12.04 y versiones posteriores.

Abra una sesión de terminal e instale OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extraiga los archivos del paquete descargado.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Vaya al directorio donde se extrajeron los archivos. La opción predeterminada debe ser la siguiente.

```bash
cd openssl-1.1.0e
```
Configure OpenSSL mediante la ejecución del comando siguiente. Si desea que los archivos se ubiquen en una carpeta distinta a /usr/local/openssl, asegúrese de cambiar lo siguiente según proceda.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Ahora que OpenSSL está configurado correctamente, debe compilarlo para convertir el certificado. Para ello, ejecute el siguiente comando:

```bash
make
```
Una vez completada la compilación, está listo para instalar OpenSSL como un ejecutable con el comando siguiente:
```bash
make install
```
Para confirmar que se ha instalado correctamente OpenSSL en el sistema, ejecute el siguiente comando y compruébelo para asegurarse de que obtendrá la misma salida.

```bash
/usr/local/openssl/bin/openssl version
```
Si la operación se realiza correctamente, debería aparecer el mensaje siguiente.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Para Windows
La instalación de OpenSSL en un PC Windows puede realizarse de las siguientes formas:
1. **(Recomendado)**  OpenSSL se instala de forma predeterminada con la funcionalidad de Bash para Windows integrada en Windows 10 y versiones posteriores. Encontrará instrucciones sobre cómo habilitar la funcionalidad de Bash para Windows en Windows 10 [aquí](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. Mediante la descarga de una aplicación de Win32/64 proporcionada por la comunidad. Aunque OpenSSL Software Foundation no proporciona ni respalda ningún instalador específico de Windows, sí se proporciona una lista de los instaladores disponibles [aquí](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Descodificación del archivo de certificado
El archivo descargado de la entidad de certificación raíz está en formato cifrado. Utilice OpenSSL para descodificar el archivo de certificado. Para ello, ejecute este comando de OpenSSL:

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Conexión a Azure Database for PostgreSQL con la autenticación de certificados SSL
Ahora que ha descodificado correctamente el certificado, ya puede conectarse al servidor de bases de datos de forma segura mediante SSL. Para permitir la verificación del certificado del servidor, el certificado debe estar en el archivo ~/.postgresql/root.crt del directorio principal del usuario. (En Microsoft Windows el archivo se denomina %APPDATA%\postgresql\root.crt). A continuación se proporcionan instrucciones para conectarse a Azure Database for PostgreSQL.

> [!NOTE]
> Actualmente, hay un problema conocido si usa "sslmode=verify-full" en la conexión al servicio, ya que se producirá un error de la conexión con el siguiente error: _El certificado de servidor para "&lt;region&gt;.control.database.windows.net" (y otros 7 nombres) no coincide con el nombre de host "&lt;NombreDelServidor&gt;.postgres.database.azure.com"._
> Si se requiere "sslmode=verify-full", use la convención de nomenclatura de servidor  **&lt;NombreDelServidor&gt;.database.windows.net** como el nombre de host de la cadena de conexión. Tenemos previsto quitar esta limitación en el futuro. Las conexiones que usan otros [modos SSL](https://www.postgresql.org/docs/9.6/static/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS) deben seguir utilizando la convención de nomenclatura de host preferida **&lt;NombreDelServidor&gt;.postgres.database.azure.com**.

#### <a name="using-psql-command-line-utility"></a>Con la utilidad de línea de comandos psql
En el ejemplo siguiente se muestra cómo conectarse correctamente a su servidor de PostgreSQL mediante la utilidad de línea de comandos psql. Use el archivo `root.crt` creado y las opciones `sslmode=verify-ca` o `sslmode=verify-full`.

Mediante la interfaz de la línea de comandos de PostgreSQL, ejecute el siguiente comando:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mypgserver-20170401.postgres.database.azure.com dbname=postgres user=mylogin@mypgserver-20170401"
```
Si la operación se realiza correctamente, debe recibir la salida siguiente:
```bash
Password for user mylogin@mypgserver-20170401:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Con la herramienta de GUI pgAdmin
Para configurar pgAdmin 4 para conectarse de forma segura a través de SSL, es necesario establecer `SSL mode = Verify-CA` o `SSL mode = Verify-Full` como sigue:

![Captura de pantalla del modo de conexión SSL requerido para pgAdmin](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Pasos siguientes
Revise las distintas opciones de conectividad de aplicaciones en [Bibliotecas de conexiones de Azure Database for PostgreSQL](concepts-connection-libraries.md).
