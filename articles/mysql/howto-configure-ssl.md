---
title: "Configuración de la conectividad SSL para conectarse de forma segura a Azure Database para MySQL | Microsoft Docs"
description: Instrucciones para configurar correctamente Azure Database for MySQL y las aplicaciones asociadas para que utilicen correctamente conexiones SSL
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 1303e6cf6f6fdd10a7310d770127828276d5056e
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL
Azure Database for MySQL permite conectar el servidor de Azure Database for MySQL con aplicaciones cliente mediante Capa de sockets seguros (SSL). Aplicar conexiones SSL entre el servidor de base de datos y las aplicaciones cliente ayuda a proteger contra los ataques de tipo "man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

De forma predeterminada, el servicio de base de datos debe configurarse para requerir conexiones SSL al conectarse a Azure Database for MySQL.  Evite deshabilitar la opción SSL siempre que sea posible.

## <a name="enforcing-ssl-connections"></a>Aplicación de conexiones SSL
Al aprovisionar una nueva instancia de Azure Database for MySQL mediante Azure Portal o la CLI, el uso obligatorio de conexiones SSL está habilitado de forma predeterminada.

Del mismo modo, las cadenas de conexión que están predefinidas en la configuración de "Cadenas de conexión", en el servidor en Azure Portal, incluyen los parámetros necesarios para los lenguajes comunes que requieren SSL. El parámetro SSL varía según el conector, por ejemplo, "ssl = true" o "sslmode = require" o "sslmode = required" y otras variaciones.

## <a name="configure-enforcement-of-ssl"></a>Configuración de la aplicación de SSL
Puede deshabilitar o habilitar la aplicación de SSL. Microsoft Azure recomienda habilitar siempre la opción Aplicar conexión SSL para mejorar la seguridad.

### <a name="using-azure-portal"></a>Uso de Azure Portal
Mediante Azure Portal, vaya al servidor de Azure Database for MySQL y haga clic en **Seguridad de conexión**. Use el botón de alternancia para habilitar o deshabilitar la opción **Aplicar conexión SSL**. A continuación, haga clic en **Guardar**. Microsoft recomienda habilitar siempre **Aplicar conexión SSL** para mejorar la seguridad.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

Para confirmar la configuración, consulte la página **Información general** para ver el indicador **Estado de aplicación de SSL**.

### <a name="using-azure-cli"></a>Uso de la CLI de Azure
Puede habilitar o deshabilitar el parámetro **ssl-enforcement** con los valores Enabled o Disabled, respectivamente, en la CLI de Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Comprobación de que la aplicación o el entorno admiten conexiones SSL
Muchas aplicaciones comunes que usan MySQL para servicios de base de datos, como Wordpress, Drupal y Magento, no habilitan SSL de forma predeterminada durante la instalación. Habilite la conectividad SSL en esas aplicaciones después de la instalación o mediante comandos de la CLI específicos de la aplicación. Si el servidor MySQL exige conexiones SSL y la aplicación asociada no está configurada correctamente, se puede producir un error al conectarse con el servidor de bases de datos. Consulte la documentación de la aplicación para obtener información sobre cómo habilitar las conexiones SSL.

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>Aplicaciones que requieren un certificado local para la conectividad SSL
En algunos casos, las aplicaciones requieren un archivo de certificado local (.pem) generado a partir de un archivo de certificado (.cer) de una entidad de certificación (CA) para conectarse de forma segura.  Consulte los pasos siguientes para obtener el archivo .cer, generar el archivo local .pem y enlazarlo a la aplicación.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Descarga del archivo de certificado de la entidad de certificación (CA)
El certificado necesario para comunicarse a través de SSL con el servidor de Azure Database for MySQL se encuentra [aquí](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).  Descargue el archivo de certificado en la unidad local (en este tutorial usamos **c:\ssl**).

### <a name="download-and-install-openssl-on-your-pc"></a>Descarga e instalación de OpenSSL en el equipo
Para generar el archivo local **.pem** necesario para que la aplicación pueda conectarse de forma segura con el servidor de base de datos, debe instalar OpenSSL en el equipo local.  

Las secciones siguientes describen el enfoque que se podría usar. Puede usar un equipo Linux o Windows, según cuál sea el sistema operativo de su preferencia. Solo necesita seguir un método.

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Usuarios de Linux: descarga e instalación de OpenSSL en un equipo Linux
Las bibliotecas OpenSSL están disponibles en código fuente directamente en [OpenSSL Software Foundation](http://www.openssl.org).  Las instrucciones siguientes le guían por los pasos necesarios para instalar OpenSSL en su equipo Linux.  En esta guía, mostramos comandos para Ubuntu 12.04 y versiones posteriores.

Abra una sesión de terminal e instale OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
Extraiga los archivos del paquete descargado.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Vaya al directorio donde se extrajeron los archivos.  De forma predeterminada, sería como se indica a continuación.

```bash
cd openssl-1.1.0e
```
Configure OpenSSL ejecutando el comando siguiente: si desea que los archivos estén en una carpeta diferente de /usr/local/openssl, no olvide cambiar el comando siguiente según corresponda.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Ahora que OpenSSL está configurado correctamente, debe compilarlo para convertir el certificado.  Para ello, ejecute el siguiente comando:

```bash
make
```
Una vez completada la compilación, está listo para instalar OpenSSL como un ejecutable con el comando siguiente:
```bash
make install
```
Para confirmar que OpenSSL se instaló correctamente en el sistema, ejecute el siguiente comando. Compruebe que obtiene la misma salida.

```bash
/usr/local/openssl/bin/openssl version
```
Si la operación se realiza correctamente, debería aparecer el mensaje siguiente:
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Usuarios de Windows: descarga e instalación de OpenSSL en un equipo Windows
Para generar el archivo local **.pem** necesario para que la aplicación pueda conectarse de forma segura con el servidor de base de datos, debe instalar OpenSSL en el equipo local.

Las bibliotecas OpenSSL están disponibles en código fuente directamente en [OpenSSL Software Foundation](http://www.openssl.org). Las instrucciones siguientes le guían por los pasos necesarios para instalar OpenSSL en su equipo Linux.

La instalación de OpenSSL en un equipo Windows se puede realizar de las siguientes maneras:

1. **(Recomendado)**  OpenSSL se instala de forma predeterminada con la funcionalidad de Bash para Windows integrada en Windows 10 y versiones posteriores.  Encontrará instrucciones sobre cómo habilitar la funcionalidad de Bash para Windows en Windows 10 [aquí](https://msdn.microsoft.com/commandline/wsl/install_guide).

2. Mediante la descarga de una aplicación de Win32/64 proporcionada por la comunidad. Aunque OpenSSL Software Foundation no proporciona ni se responsabiliza de ningún instalador de Windows específico, ofrece una lista de instaladores disponibles [aquí](https://wiki.openssl.org/index.php/Binaries).

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>Conversión del certificado .cer en un archivo local .pem
El archivo descargado de la entidad de CA raíz está en formato **.crt**. Use OpenSSL para convertir el archivo de certificado en un archivo **.pem**.  Para ello, ejecute la herramienta de línea de comandos openssl.exe y ejecute el siguiente comando:

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.crt -out MyServerCACert.pem
```
Ahora que ha creado correctamente el archivo de certificado (MyServerCACert.pem), puede conectarse a su servidor de bases de datos de forma segura mediante SSL.

Los ejemplos siguientes muestran cómo conectarse a su servidor de MySQL mediante la interfaz de la línea de comandos de MySQL y mediante MySQL Workbench, con el archivo **MyServerCACert.pem**.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conexión al servidor mediante la CLI de MySQL a través de SSL
En la interfaz de la línea de comandos de MySQL, ejecute el siguiente comando:

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
Ejecute el comando **status** de MySQL para comprobar que se ha conectado al servidor de MySQL mediante SSL:

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> Actualmente, existe un problema conocido cuando se usa la opción "--ssl-mode=VERIFY_IDENTITY" en la conexión de mysql.exe al servicio. La conexión no se establece y genera el siguiente error: _ERROR 2026 (HY000): SSL connection error: SSL certificate validation failure_ Cambie a la versión anterior "--ssl-mode=VERIFY_CA" o a un [modo SSL](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) inferior. Si necesita usar "--ssl-mode=VERIFY_IDENTITY", use el nombre del servidor regional en su lugar hasta que se solucione este problema. Haga ping al nombre del servidor para determinar el servidor regional, como `westeurope1-a.control.database.windows.net`, y use el nombre del servidor regional en la conexión. Tenemos previsto quitar esta limitación en el futuro.

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Conexión al servidor mediante MySQL Workbench a través de SSL
Configure MySQL Workbench para conectarse de forma segura a través de SSL. Vaya a la pestaña **SSL** en MySQL Workbench en el cuadro de diálogo Configuración de una conexión nueva. Escriba la ubicación del archivo **MyServerCACert.pem** en el campo **Archivo CA SSL:**.
![guardar icono personalizado](./media/howto-configure-ssl/mysql-workbench-ssl.png)

## <a name="next-steps"></a>Pasos siguientes
Para revisar diversas opciones de conectividad de la aplicación, siga las [Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md).

