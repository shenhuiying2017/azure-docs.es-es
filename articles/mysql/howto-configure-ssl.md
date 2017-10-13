---
title: "Configuración de la conectividad SSL para conectarse de forma segura a Azure Database para MySQL | Microsoft Docs"
description: Instrucciones para configurar correctamente Azure Database for MySQL y las aplicaciones asociadas para que utilicen correctamente conexiones SSL
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 38e68712699b3e89a10c3d44d8ec313f531fcbdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL
Azure Database for MySQL permite conectar el servidor de Azure Database for MySQL con aplicaciones cliente mediante Capa de sockets seguros (SSL). Aplicar conexiones SSL entre el servidor de base de datos y las aplicaciones cliente ayuda a proteger contra los ataques de tipo "man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.

## <a name="step-1-obtain-ssl-certificate"></a>Paso 1: Obtener un certificado SSL
Descargue el certificado necesario para comunicarse a través de SSL con la base de datos de Azure para el servidor de MySQL desde [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) y guarde el archivo de certificado en la unidad local (en este tutorial se usa c:\ssl).
**Para Microsoft Internet Explorer y Microsoft Edge:** una vez finalizada la descarga, cambie el nombre del certificado a BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Paso 2: Enlazar SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Conexión al servidor mediante MySQL Workbench a través de SSL
Configure MySQL Workbench para conectarse de forma segura a través de SSL. En MySQL Workbench en el cuadro de diálogo Setup New Connection (Configuración de una conexión nueva), vaya a la pestaña **SSL**. En el campo **Archivo CA SSL**, escriba la ubicación del archivo **BaltimoreCyberTrustRoot.crt.pem**.
![guardar icono personalizado](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Conexión al servidor mediante la CLI de MySQL a través de SSL
En la interfaz de la línea de comandos de MySQL, ejecute el siguiente comando:
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Paso 3: Aplicar conexiones SSL en Azure 
### <a name="using-the-azure-portal"></a>Uso de Azure Portal
Mediante Azure Portal, vaya al servidor de Azure Database for MySQL y haga clic en **Seguridad de conexión**. Use el botón de alternancia para habilitar o deshabilitar la opción **Aplicar conexión SSL** y después haga clic en **Guardar**. Microsoft recomienda habilitar siempre la opción **Aplicar conexión SSL** para mejorar la seguridad.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Uso de la CLI de Azure
Puede habilitar o deshabilitar el parámetro **ssl-enforcement** con los valores Enabled o Disabled, respectivamente, en CLI de Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Paso 4: Comprobar la conexión SSL
Ejecute el comando **status** de MySQL para comprobar que se ha conectado al servidor de MySQL mediante SSL:
```dos
mysql> status
```
Confirme que la conexión está cifrada mediante la revisión de la salida, que debe mostrar: **SSL: Cipher in use is AES256-SHA** (SSL: el cifrado en uso es AES256-SHA). 

## <a name="sample-code"></a>Código de ejemplo
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>Pasos siguientes
Para revisar diversas opciones de conectividad de la aplicación, siga las [Bibliotecas de conexiones de Azure Database for MySQL](concepts-connection-libraries.md).
