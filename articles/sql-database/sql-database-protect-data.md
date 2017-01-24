---
title: "Información general de la protección de datos de Azure SQL Database | Microsoft Docs"
description: Aprenda a proteger los datos de una instancia de Azure SQL Database.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>Protección de datos en SQL Database

SQL Database protege los datos mediante el cifrado.  

## <a name="overview"></a>Información general

SQL Database protege los datos mediante el cifrado de los datos en movimiento a través de [Seguridad de la capa de transporte](https://support.microsoft.com/en-us/kb/3135244), de los datos en reposo a través de [Cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242) y de los datos de uso a través de [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Para obtener una explicación del uso de estas características de protección de datos en SQL Database, consulte [Protecting data within your SQL Database](sql-database-protect-data.md) (Protección de datos en SQL Database).

Si desea conocer otras formas de cifrar datos, considere:

* [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
* Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar el [Almacén de claves de Azure con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


SQL Database protege los datos, ya que proporciona funcionalidades de auditoría y de detección de amenazas. 

### <a name="auditing"></a>Auditoría
SQL Database Auditing realiza un seguimiento de las actividades de la base de datos y ayuda a mantener el cumplimiento normativo, para lo que graba eventos de base de datos en un registro de auditoría de su cuenta de Azure Storage. La auditoría permite conocer las actividades en curso de la base de datos y analizar e investigar la actividad histórica para identificar posibles amenazas o supuestas infracciones de seguridad y abusos. Para más información, consulte [Introducción a SQL Database Auditing](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Detección de amenazas
La detección de amenazas complementa la auditoría, ya que proporciona una capa adicional de inteligencia de seguridad integrada en el servicio Azure SQL Database. Funciona ininterrumpidamente para aprender, generar perfiles y detectar actividades anómalas de la base de datos. Recibe alertas de actividades sospechosas, vulnerabilidades potenciales, ataques pon inyección de código SQL y patrones anómalos de acceso a bases de datos. Para responder a dichas alertas, siga las instrucciones con información y con acciones que se pueden realizar que se proporcionan. Para más información, consulte [Introducción a Detección de amenazas de Base de datos SQL](sql-database-threat-detection-get-started.md).  

## <a name="connection-security"></a>Seguridad de conexión
Seguridad de conexión hace referencia a cómo restringir y proteger las conexiones a la base de datos mediante reglas de firewall y cifrado de las conexiones.

Las reglas de firewall las usan tanto el servidor como la base de datos para rechazar los intentos de conexión desde direcciones IP que no se hayan incluido explícitamente en la lista blanca. Para permitir que la aplicación o la dirección IP pública del equipo cliente intente conectarse a una nueva base de datos, primero debe crear una regla de firewall de nivel de servidor mediante el Portal de Azure clásico, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP que se permite que atraviesen el firewall del servidor tanto como sea posible. Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/ee621782).

Todas las conexiones a Base de datos SQL de Azure requieren cifrado (SSL/TLS) siempre que haya datos "en tránsito" hacia y desde la base de datos. En la cadena de conexión de su aplicación, debe especificar los parámetros necesarios para cifrar la conexión y *no* confiar en el certificado de servidor (esto se hace automáticamente si copia la cadena de conexión de fuera del Portal de Azure clásico). De lo contrario, la conexión no comprueba la identidad del servidor y es vulnerable a ataques del tipo "Man in the middle". Por ejemplo, en el caso del controlador de ADO.NET, los parámetros de la cadena de conexión son **Encrypt=True** y **TrustServerCertificate=False**. Para más información, consulte [Validación de certificados y cifrado de conexiones de Azure SQL Database](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Autenticación
La autenticación indica a cómo demostrar su identidad al conectarse a la base de datos. Base de datos SQL admite dos tipos de autenticación:

* **Autenticación de SQL**, la cual usa un nombre de usuario y una contraseña.
* **Autenticación de Azure Active Directory**, la cual usa las identidades administradas por Azure Active Directory y es compatible con los dominios administrados e integrados

Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo". Si desea usar la autenticación de Azure Active Directory, debe crear otro administrador de servidor llamado "administrador de Azure AD" con permiso para administrar usuarios y grupos de Azure AD. Este administrador también puede realizar todas las operaciones de un administrador de servidor normal. Consulte el tutorial [Conectar a la Base de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md) , para obtener información acerca de cómo crear un administrador de Azure AD y así habilitar la autenticación de Azure Active Directory.

Como procedimiento recomendado, la aplicación debe usar una cuenta distinta para autenticar. De esta forma, puede limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de la aplicación sea vulnerable a ataques por inyección de código SQL. Se recomienda crear un [usuario de base de datos independiente](https://msdn.microsoft.com/library/ff929188), ya que esto permitirá que la aplicación se autentique directamente en la base de datos. Para crear un usuario de base de datos independiente que utilice la autenticación de SQL, ejecute el comando de T-SQL siguiente cuando haya iniciado sesión como administrador de servidor en la base de datos de usuario a la que está conectado:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Si creó un administrador de Azure AD, puede crear un usuario de base de datos independiente que use la autenticación de Azure Active Directory mediante la ejecución del comando de T-SQL siguiente mientras esté conectado a la base de datos de usuario como administrador de Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

En cualquiera de los casos, la cadena de conexión de la aplicación debe especificar estas credenciales de usuario para conectarse a la base de datos, en lugar del inicio de sesión de administrador de servidor.

Para obtener más información sobre la autenticación en Base de datos SQL, consulte [Administración de bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](sql-database-manage-logins.md).

## <a name="authorization"></a>Autorización
Autorización hace referencia a lo que se puede realizar en Base de datos SQL de Azure y la controlan los permisos y las pertenencias a grupos de roles de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. Base de datos SQL de Azure facilita la administración con roles en T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

La cuenta de administrador de servidor con la que se está conectando forma parte de db_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

Existen varias formas de limitar aún más lo que los usuarios pueden hacer con Base de datos SQL de Azure:

* Los [roles de base de datos](https://msdn.microsoft.com/library/ms189121) que no sean db_datareader y db_datawriter se pueden usar para crear cuentas de usuario de aplicación más eficaces o cuentas de administración menos eficaces.
* Los [permisos](https://msdn.microsoft.com/library/ms191291) granulares permiten control qué operaciones se pueden realizar en columnas individuales, tablas, vistas, procedimientos y otros objetos de la base de datos.
* La [suplantación](https://msdn.microsoft.com/library/vstudio/bb669087) y la [firma de módulos](https://msdn.microsoft.com/library/bb669102) se pueden usar para elevar los permisos temporalmente de forma segura.
* [seguridad del nivel de fila](https://msdn.microsoft.com/library/dn765131) permite filtrar las filas que puede ver el usuario.
* [enmascaramiento de datos](sql-database-dynamic-data-masking-get-started.md) puede utilizarse para limitar la exposición de información confidencial.
* [procedimientos almacenados](https://msdn.microsoft.com/library/ms190782) puede utilizarse para limitar las acciones que se pueden realizar en la base de datos.

La administración de bases de datos y servidores lógicos desde el Portal de Azure clásico o mediante la API del Administrador de recursos de Azure la controlan las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, consulte [Control de acceso basado en rol en el Portal de Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encryption"></a>Cifrado
Base de datos SQL de Azure puede ayudar a proteger los datos mediante el cifrado de los mismos cuando estén "en reposo" o almacenados en archivos de base de datos y copias de seguridad, con el [cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242). Para cifrar una base de datos, conéctese como propietario de la base de datos y ejecute:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Si desea conocer otras formas de cifrar los datos secretos, considere:

* [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
* Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar el [Almacén de claves de Azure con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Siempre cifrado](https://msdn.microsoft.com/library/mt163865.aspx) (en vista previa) hace que el cifrado sea transparente para las aplicaciones y permite que los clientes cifren información confidencial dentro de aplicaciones cliente sin compartir las claves de cifrado con Base de datos SQL.

## <a name="auditing"></a>Auditoría
La auditoría y el seguimiento de eventos de la base de datos pueden ayudarle a mantener el cumplimiento de las reglamentaciones y a identificar cualquier actividad sospechosa. La auditoría de Base de datos SQL permite grabar los eventos de la base de datos en un registro de auditoría de una cuenta de Almacenamiento de Azure. La auditoría de Base de datos SQL también se integra con Microsoft Power BI, con el fin de facilitar la generación de análisis e informes detallados. Para obtener más información, consulte [Introducción a la auditoría de Base de datos SQL](sql-database-auditing-get-started.md).

Detección de amenazas de Base de datos SQL proporciona un nivel adicional de seguridad añadido a la auditoría. Le permite responder a las amenazas a medida que se producen proporcionando alertas de seguridad sobre actividades anómalas. Para más información, consulte [Introducción a Detección de amenazas de Base de datos SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Cumplimiento normativo
Además de las anteriores características y funcionalidades que pueden ayudar a su aplicación a cumplir distintos requisitos de cumplimiento de normas de seguridad, la Base de datos SQL de Azure también participa en las auditorías regulares y ha sido certificada con una serie de estándares de cumplimiento normativo. Para más información, consulte el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/), donde podrá encontrar la lista más reciente de [certificaciones de cumplimiento de SQL Database](https://azure.microsoft.com/support/trust-center/services/).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de todas los características de seguridad de SQL Database, consulte [SQL security overview](sql-database-security-overview.md) (Información general acerca de la seguridad de SQL).
- Para obtener información acerca del uso de las características de control de acceso en SQL Database, consulte [Control de acceso](sql-database-control-access.md).
- Para obtener información acerca de la supervisión proactiva, consulte [Introducción a SQL Database Auditing](sql-database-auditing-get-started.md) e [Introducción a la detección de amenazas de SQL Database](sql-database-threat-detection-get-started.md).




<!--HONumber=Dec16_HO4-->


