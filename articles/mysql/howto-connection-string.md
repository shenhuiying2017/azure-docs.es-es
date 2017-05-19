---
title: "Conexión de aplicaciones a Azure Database for MySQL | Microsoft Docs"
description: "En este documento se enumeran todas las cadenas de conexión admitidas actualmente para que las aplicaciones se conecten con Azure Database for MySQL"
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Conexión de aplicaciones a Azure Database for MySQL
En este documento se enumeran todos los tipos de cadena de conexión admitidos en Azure Database for MySQL, junto con plantillas y ejemplos. Puede tener distintos parámetros y configuraciones en la cadena de conexión.

- Consulte el documento sobre [cómo configurar SSL](./howto-configure-ssl.md) para obtener el certificado.
- {su_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

Por ejemplo, suponemos que el nombre del servidor es **wpdemo**, el nombre de la base de datos es **wpdb**, el nombre de usuario es **WPAdmin** y la contraseña es **orcas!2**.

Entonces, la cadena de conexión debería ser:

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>Obtener la cadena de conexión desde Azure Portal
En [Azure Portal](https://portal.azure.com), vaya a Azure Database for MySQL y haga clic en **Cadenas de conexión** para obtener la lista de cadenas para la instancia: ![cadenas de conexión en Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

La cadena proporciona detalles como el controlador, el servidor y otros parámetros de conexión de base de datos. Copie la cadena de conexión elegida y, a continuación, modifíquela con sus propios parámetros como el nombre de la base de datos o la contraseña, entre otros. Ahora puede usar esta cadena para conectarse al servidor.

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la biblioteca de conexiones, consulte [Conceptos: bibliotecas de conexiones](./concepts-connection-libraries.md).

