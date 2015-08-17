<properties 
   pageTitle="Información general sobre la seguridad de Base de datos SQL" 
   description="Obtenga información acerca de la seguridad de la Base de datos SQL de Azure y SQL Server, incluidas las diferencias entre la nube y SQL Server local en cuanto a la autenticación, autorización, seguridad de conexión, cifrado y cumplimiento normativo." 
   services="sql-database" 
   documentationCenter="" 
   authors="tmullaney" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services" 
   ms.date="07/14/2015"
   ms.author="thmullan;jackr"/>


# Protección de bases de datos SQL

## Información general

Este artículo describe los fundamentos de la protección de la capa de datos de una aplicación que use Base de datos SQL de Azure. En concreto, estos artículos le ayudarán a empezar a trabajar con los recursos para limitar el acceso, proteger datos y supervisar actividades en una base de datos creada en [Introducción al tutorial de Base de datos SQL](sql-database-get-started.md). Para obtener una descripción completa de las características de seguridad disponibles en todas las versiones de SQL, consulte [Seguridad y protección (motor de base de datos)](https://msdn.microsoft.com/library/bb510589).

## Seguridad de conexión

Seguridad de conexión hace referencia a cómo restringir y proteger las conexiones a la base de datos mediante reglas de firewall y cifrado de las conexiones.

Las reglas de firewall las usan tanto el servidor como la base de datos para rechazar los intentos de conexión desde direcciones IP que no se hayan incluido explícitamente en la lista blanca. Para permitir que la aplicación o la dirección IP pública del equipo cliente intente conectarse a una nueva base de datos, primero debe crear una regla de firewall de nivel de servidor mediante el Portal de administración de Azure, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP que se permite que atraviesen el firewall del servidor tanto como sea posible. Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/ee621782).

Todas las conexiones a Base de datos SQL de Azure requieren cifrado (SSL/TLS) siempre que haya datos "en tránsito" hacia y desde la base de datos. En la cadena de conexión de su aplicación, debe especificar los parámetros para cifrar la conexión y *no* confiar en el certificado de servidor (esto se hace automáticamente si copia la cadena de conexión en el Portal de administración de Azure); de lo contrario, la conexión no comprobará la identidad del servidor y será vulnerable a los ataques de tipo "Man in the middle". Por ejemplo, en el caso del controlador de ADO.NET, los parámetros de la cadena de conexión son **Encrypt = True** y **TrustServerCertificate = False**. Para obtener más información, consulte [Validación de certificados y cifrado de conexiones de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## Autenticación

La autenticación indica a cómo demostrar su identidad al conectarse a la base de datos. Base de datos SQL admite la autenticación de SQL con un nombre de usuario y una contraseña.

Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo".

Sin embargo, como práctica recomendada su aplicación debe utilizar una cuenta diferente para autenticar; este modo puede limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de aplicación sea vulnerable a ataques de inyección SQL. El enfoque recomendado es crear un [usuario de base de datos contenido](https://msdn.microsoft.com/library/ff929188), lo que permite a la aplicación autenticarse directamente en una base de datos con un nombre de usuario y una contraseña. Puede crear un usuario de base de datos contenido mediante la ejecución del siguiente comando T-SQL mientras está conectado a su base de datos de usuario con el inicio de sesión de administrador de servidor:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

La cadena de conexión de la aplicación debe especificar este nombre de usuario y contraseña, en lugar del inicio de sesión de administrador de servidor, para conectarse a la base de datos.

Para obtener más información sobre la autenticación en Base de datos SQL, consulte [Administración de bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](https://msdn.microsoft.com/library/ee336235).


## Autorización
Autorización hace referencia a lo que se puede realizar en Base de datos SQL de Azure y la controlan los permisos y las pertenencias a grupos de roles de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. Base de datos SQL de Azure facilita la administración con roles en T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

La cuenta de administrador de servidor con la que se está conectando forma parte de db\_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

Existen varias formas de limitar aún más lo que los usuarios pueden hacer con Base de datos SQL de Azure:

* Los [roles de base de datos](https://msdn.microsoft.com/library/ms189121) que no sean db\_datareader y db\_datawriter se pueden utilizar para crear cuentas de usuario de aplicación más eficaces o cuentas de administración menos eficaces.
* Los [permisos](https://msdn.microsoft.com/library/ms191291) granulares permiten control qué operaciones se pueden realizar en columnas individuales, tablas, vistas, procedimientos y otros objetos de la base de datos.
* La [suplantación](https://msdn.microsoft.com/library/vstudio/bb669087) y la [firma de módulos](https://msdn.microsoft.com/library/bb669102) se pueden utilizar para elevar los permisos temporalmente de forma segura.
* La [seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131) permite filtrar las filas que puede ver el usuario.
* El [enmascaramiento de datos](sql-database-dynamic-data-masking-get-started.md) puede utilizarse para limitar la exposición de información confidencial.
* Los [procedimientos almacenados](https://msdn.microsoft.com/library/ms190782) puede utilizarse para limitar las acciones que se pueden realizar en la base de datos.

La administración bases de datos y servidores lógicos desde el Portal de administración de Azure o mediante la API del Administrador de recursos de Azure la controlan las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, consulte [Control de acceso basado en rol en el Portal de vista previa de Azure](../role-based-access-control-configure.md).


## Cifrado

Base de datos SQL de Azure puede ayudar a proteger los datos mediante el cifrado de los mismos cuando estén "en reposo" o almacenados en archivos de base de datos y copias de seguridad, con el [cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242). Para cifrar una base de datos, conéctese como propietario de la base de datos y ejecute:

```
CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_256 
   ENCRYPTION BY SERVER CERTIFICATE ##MS_TdeCertificate##;
   
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Si desea conocer otras formas de cifrar los datos secretos, considere:

* [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
* Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar el [Almacén de claves de Azure con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


## Auditoría

La auditoría y el seguimiento de eventos de la base de datos pueden ayudarle a mantener el cumplimiento de las reglamentaciones y a identificar cualquier actividad sospechosa. La auditoría de Base de datos SQL permite grabar los eventos de la base de datos en un registro de auditoría de una cuenta de Almacenamiento de Azure. La auditoría de Base de datos SQL también se integra con Microsoft Power BI, con el fin de facilitar la generación de análisis e informes detallados. Para obtener más información, consulte [Introducción a la auditoría de Base de datos SQL](sql-database-auditing-get-started.md).

## Cumplimiento normativo

Además de las anteriores características y funcionalidades que pueden ayudar a su aplicación a cumplir distintos requisitos de cumplimiento de normas de seguridad, la Base de datos SQL de Azure también participa en las auditorías regulares y ha sido certificada con una serie de estándares de cumplimiento normativo. Para obtener más información, consulte el [Centro de confianza de Microsoft Azure](http://azure.microsoft.com/support/trust-center/), donde podrá encontrar la lista más reciente de [certificaciones de cumplimiento de Base de datos SQL](http://azure.microsoft.com/support/trust-center/services/).
 

<!---HONumber=August15_HO6-->