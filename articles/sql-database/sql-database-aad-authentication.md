---
title: "Conexión a SQL Database o a SQL Data Warehouse mediante autenticación de Azure Active Directory | Microsoft Docs"
description: "Aprenda a conectarse a una Base de datos SQL mediante autenticación de Azure Active Directory"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/22/2016
ms.author: rick.byham@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 5fc68a32ab11e80b547fd517524a7f87a31ebfba
ms.openlocfilehash: 911cf0c3204545091829adcb7dcbf6cbddc5ee9e


---
# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory
La autenticación de Azure Active Directory es un mecanismo de conexión a la Base de datos SQL de Microsoft Azure y [Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure AD puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece una ubicación única para administrar usuarios de base de datos y simplifica la administración de permisos. Entre las ventajas se incluyen las siguientes:

* Ofrece una alternativa a la autenticación de SQL Server.
* Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
* Permite la rotación de contraseñas en un solo lugar
* Los clientes pueden administrar los permisos de la base de datos con grupos externos (AAD).
* Puede eliminar el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
* La autenticación de Azure AD utiliza usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
* Azure AD admite la autenticación basada en token para las aplicaciones que se conectan a SQL Database.
* La autenticación de Azure AD es compatible con ADFS (federación de dominio) o la autenticación nativa de usuario y contraseña para una instancia de Azure Active Directory local sin sincronización de dominios.  
* Azure AD admite conexiones de SQL Server Management Studio que usan la autenticación universal de Active Directory, lo cual incluye Multi-Factor Authentication (MFA).  MFA incluye una sólida autenticación con una gama de sencillas opciones de comprobación: llamada de teléfono, mensaje de texto, tarjetas inteligentes con PIN o notificación de aplicación móvil. Para obtener más información, consulte [Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL](sql-database-ssms-mfa-authentication.md).

En los pasos de configuración se incluyen los siguientes procedimientos para configurar y usar la autenticación de Azure Active Directory.

1. Cree y rellene una instancia de Azure AD.
2. Asegúrese de que la base de datos está en Base de datos SQL de Azure V12. (No es necesario para Almacenamiento de datos SQL).
3. Opcional: asocie o cambie la instancia de Active Directory que está asociada a la suscripción de Azure.
4. Cree un administrador de Azure Active Directory para Azure SQL Server o para [Almacenamiento de datos SQL de Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configure los equipos cliente.
6. Cree usuarios de bases de datos independientes en la base de datos y asignados a identidades de Azure AD.
7. Conéctese a la base de datos mediante identidades de Azure AD.

## <a name="trust-architecture"></a>Arquitectura de confianza
En el siguiente diagrama de alto nivel se resume la arquitectura de la solución del uso de la autenticación de Azure AD con la Base de datos SQL de Azure. Los mismos conceptos se aplican a Almacenamiento de datos SQL. Para admitir la contraseña de usuario nativa de Azure AD, se considera solo la parte de la nube y SQL Database de Azure o Azure AD. Para admitir la autenticación federada (o el usuario o contraseña para las credenciales de Windows), se requiere la comunicación con el bloque ADFS. Las flechas indican las rutas de comunicación.

![diagrama de autenticación de aad][1]

En el diagrama siguiente se indica la federación, la confianza y las relaciones de hospedaje que permiten que un cliente se conecte a una base de datos mediante el envío de un token que una instancia de Azure AD autenticó y que es de confianza para la base de datos. El cliente 1 puede representar una instancia de Azure Active Directory con usuarios nativos o una instancia de AD con usuarios federados. El cliente 2 representa una posible solución, incluidos los usuarios importados; en este ejemplo proceden de una instancia de Azure Active Directory federada con ADFS sincronizado con Azure Active Directory. Es importante comprender que el acceso a una base de datos mediante la autenticación de Azure AD requiere que la suscripción de hospedaje esté asociada a Azure AD. Debe utilizarse la misma suscripción para crear el servidor SQL Server que hospeda la Base de datos SQL de Azure o el Almacenamiento de datos SQL.

![relación de suscripción][2]

## <a name="administrator-structure"></a>Estructura del administrador
Cuando se usa la autenticación de Azure AD, existen dos cuentas de administrador para el servidor de Base de datos SQL: el administrador del servidor SQL Server original y el administrador de Azure AD. Los mismos conceptos se aplican a Almacenamiento de datos SQL. Solo el administrador basado en una cuenta de Azure AD puede crear el primer usuario de base de datos independiente en Azure AD en una base de datos de usuario. El inicio de sesión del administrador de Azure AD puede ser un usuario de Azure AD o un grupo de Azure AD. Cuando el administrador es una cuenta de grupo, cualquier miembro del grupo lo puede usar, lo que permite varios administradores de Azure AD para la instancia de SQL Server. Mediante el uso de la cuenta de grupo como un administrador, se mejora la administración al permitir agregar y quitar miembros de grupo de forma centralizada en Azure AD sin cambiar los usuarios ni los permisos de Base de datos SQL. Solo un administrador de Azure AD (un usuario o grupo) se puede configurar en cualquier momento.

![estructura de administración][3]

## <a name="permissions"></a>Permisos
Para crear nuevos usuarios, debe tener el permiso `ALTER ANY USER` en la base de datos. El permiso `ALTER ANY USER` se puede conceder a cualquier usuario de la base de datos. Las cuentas de administrador del servidor también disponen del permiso `ALTER ANY USER`, así como los usuarios de la base de datos con los permisos `CONTROL ON DATABASE` o `ALTER ON DATABASE` para esa base de datos y los miembros del rol de la base de datos `db_owner`.

Para crear un usuario de base de datos independiente en la Base de datos SQL de Azure o en Almacenamiento de datos SQL, debe conectarse a la base de datos con una identidad de Azure AD. Para crear el primer usuario de base de datos independiente , debe conectarse a la base de datos con un administrador de Azure AD (que es el propietario de la base de datos). Esto se muestra a continuación, en los pasos 4 y 5. La autenticación de Azure AD solo es posible si se creó el administrador de Azure AD para Azure SQL Database o para el servidor de SQL Data Warehouse. Si se ha quitado el administrador de Azure Active Directory desde el servidor, los usuarios de Azure Active Directory creados previamente en SQL Server ya no podrán conectarse a la base de datos utilizando sus credenciales de Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Características y limitaciones de Azure AD
Los siguientes miembros de Azure AD se pueden aprovisionar en Azure SQL Server o en SQL Data Warehouse:

* Miembros nativos: un miembro creado en Azure AD en el dominio administrado o en un dominio personalizado. Para más información, consulte [Incorporación de su nombre de dominio personalizado a Azure Active Directory](../active-directory/active-directory-add-domain.md).
* Miembros de dominio federado: un miembro creado en Azure AD con un dominio federado. Para más información, consulte [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)(Microsoft Azure ya admite la federación con Windows Server Active Directory).
* Miembros importados de otros directorios de Azure AD que son miembros nativos o miembros de dominio federado.
* Grupos de Active Directory creados como grupos de seguridad.

No se admiten las cuentas de Microsoft (por ejemplo, outlook.com, hotmail.com, live.com) ni otras cuentas de invitado (por ejemplo, gmail.com, yahoo.com). Si puede iniciar sesión en [https://login.live.com](https://login.live.com) con la cuenta y la contraseña, está usando una cuenta Microsoft que no se admite para la autenticación de Azure AD para Base de datos SQL de Azure o Almacenamiento de datos SQL de Azure.

### <a name="additional-considerations"></a>Consideraciones adicionales

* Para mejorar la capacidad de administración, se recomienda que aprovisione un grupo dedicado de Azure AD como administrador.   
* Solo un administrador de Azure AD (un usuario o grupo) se puede configurar en un servidor de Azure SQL Server o Almacenamiento de datos SQL de Azure en cualquier momento.   
* Inicialmente, solo un administrador de Azure AD para SQL Server puede conectarse al servidor Azure SQL Server o Azure SQL Data Warehouse con una cuenta de Azure Active Directory. El administrador de Active Directory puede configurar los usuarios de la base de datos de Azure AD sucesivos.   
* Se recomienda establecer el tiempo de espera de conexión a 30 segundos.   
* SQL Server 2016 Management Studio y SQL Server Data Tools para Visual Studio 2015 (versión 14.0.60311.1 abril de 2016 o posterior) admiten la autenticación de Azure Active Directory. (La autenticación de Azure AD es compatible con el **proveedor de datos .NET Framework para SqlServer**; al menos la versión 4.6 de .NET Framework). Por lo tanto, las versiones más recientes de estas herramientas y aplicaciones de capa de datos (DAC y .bacpac) pueden usar la autenticación de Azure AD.   
* [La versión 13.1 de ODBC](https://www.microsoft.com/download/details.aspx?id=53339) es compatible con la autenticación de Azure Active Directory; sin embargo, `bcp.exe` no se puede conectar mediante la autenticación de Azure Active Directory porque usa un proveedor de ODBC anterior.   
* `sqlcmd` admite la autenticación de Azure Active Directory a partir de la versión 13.1, disponible en el [Centro de descarga](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools para Visual Studio 2015 requiere al menos la versión de abril de 2016 de Data Tools (versión 14.0.60311.1). Actualmente, los usuarios de Azure AD no se muestran en el explorador de objetos de SSDT. Como solución alternativa, vea los usuarios de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [Microsoft JDBC Driver 6.0 para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) es compatible con la autenticación de Azure AD. Consulte también [Configurar las propiedades de conexión](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase no se puede autenticar mediante la autenticación de Azure AD.   
* No se admiten algunas herramientas como BI y Excel.   
* La autenticación de Azure AD se admite para SQL Database con las hojas **Importar base de datos** y **Exportar base de datos de Azure Portal**. La importación y exportación mediante la autenticación de Azure AD también se admite desde el comando de PowerShell.   

## <a name="1-create-and-populate-an-azure-ad"></a>1. Crear y rellenar una instancia de Azure AD.
Cree una instancia de Azure AD y rellénela con usuarios y grupos. Azure AD puede ser el dominio administrado de Azure AD de dominio inicial. Azure AD también puede ser una instancia local de Active Directory Domain Services que se federa con Azure AD.

Para obtener más información, consulte [Integrating your on-premises identities with Azure Active Directory](../active-directory/active-directory-aadconnect.md) (Integración de sus identidades locales con Azure Active Directory), [Incorporación de su nombre de dominio personalizado a Azure Active Directory](../active-directory/active-directory-add-domain.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure admite ahora Windows Server Active Directory), [Administración de su directorio de Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Administrar Azure AD mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) y [Hybrid Identity Required Ports and Protocols](../active-directory/active-directory-aadconnect-ports.md) (Puertos y protocolos requeridos para la identidad híbrida).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. Asegurarse de que la versión de Base de datos SQL sea la versión 12.   
La última versión de SQL Database (V12) admite la autenticación de Azure AD. Para obtener más información sobre SQL Database V12 y descubrir si está disponible en su región, consulte [SQL Database features](sql-database-features.md) (Características de SQL Database). Este paso no es necesario para Almacenamiento de datos SQL de Azure porque Almacenamiento de datos SQL solo está disponible en V12.

Si tiene una base de datos existente, compruebe que está alojada en Base de datos SQL v12 conectándose a la base de datos (por ejemplo con SQL Server Management Studio) y ejecutando `SELECT @@VERSION;`. El resultado esperado para una base de datos en Base de datos SQL v12 es al menos **Microsoft SQL Azure (RTM) - 12.0**. Si la base de datos no está hospedada en Base de datos SQL V12, consulte [Planeación y preparación para actualizar a SQL Database V12](sql-database-v12-plan-prepare-upgrade.md)y después visite el Portal de Azure clásico para migrar la base de datos a Base de datos SQL V12.

También puede crear una base de datos en Base de datos SQL V12 siguiendo los pasos que se muestran en [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md). 

>  [!TIP]   
>  Lea el paso siguiente antes de seleccionar una suscripción para la nueva base de datos.   

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. Opcional: asociar o cambiar el Active Directory que está asociado actualmente a la suscripción de Azure.
Para asociar la base de datos con el directorio de Azure AD para su organización, cree el directorio como un directorio de confianza para la suscripción de Azure que hospeda la base de datos. Para obtener más información, consulte [Cómo se asocian las suscripciones a Azure con Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Información adicional:** cada suscripción de Azure tiene una relación de confianza con una instancia de Azure AD. Esto significa que confía en ese directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero una suscripción confía solo en un único directorio. Puede ver el directorio en que confía la suscripción en la pestaña **Configuración** , en [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.), que son más parecidos a los recursos secundarios de una suscripción. Si una suscripción expira, el acceso a esos otros recursos asociados a la suscripción también se detiene. Sin embargo, el directorio permanece en Azure y puede asociar otra suscripción a ese directorio y continuar con la administración de los usuarios del directorio. Para más información sobre recursos, consulte [Descripción de acceso a los recursos de Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

En los procedimientos siguientes se ofrecen instrucciones paso a paso sobre cómo cambiar el directorio asociado para una suscripción determinada.

1. Conéctese a su [Portal de Azure clásico](https://manage.windowsazure.com/) mediante un administrador de suscripciones de Azure.
2. En el área de la izquierda, seleccione **CONFIGURACIÓN**.
3. Las suscripciones aparecen en la pantalla de configuración. Si la suscripción deseada no aparece, haga clic en el apartado **Suscripciones** de la parte superior, despliegue la lista del cuadro **FILTRAR POR DIRECTORIO**, seleccione el directorio que contiene las suscripciones y después haga clic en **APLICAR**.
   
    ![seleccionar suscripción][4]
4. En el área **Configuración**, haga clic en la suscripción y, después, en **EDITAR DIRECTORIO** en la parte inferior de la página.
   
    ![portal de configuración de ad][5]
5. En el cuadro **EDITAR DIRECTORIO** , seleccione el directorio de Azure Active Directory asociado al servidor SQL Server o a la instancia de Almacenamiento de datos SQL y haga clic en la flecha para avanzar.
   
    ![selección de editar directorio][6]
6. En el cuadro de diálogo de la asociación de directorios **CONFIRMAR**, confirme el mensaje "**Se quitarán todos los coadministradores**".
   
    ![confirmación de editar directorio][7]
7. Haga clic en la marca de verificación para volver a cargar el portal.

> [!NOTE]
> Cuando se cambia el directorio, el acceso de todos los coadministradores, usuarios y grupos de Azure AD y usuarios de recursos con respaldo del directorio se quitan y dejan de tener acceso a la suscripción y sus recursos. Solo el administrador del servicio puede configurar el acceso para las entidades de seguridad según el nuevo directorio. Este cambio podría tardar una cantidad considerable de tiempo en propagarse a todos los recursos. Al cambiar el directorio también se modifica el administrador de Azure AD de Base de datos SQL y Almacenamiento de datos SQL, y se deshabilita el acceso a la base de datos de los usuarios de Azure AD existentes. El administrador de Azure AD debe restablecerse (como se describe a continuación) y se deben crear nuevos usuarios de Azure AD.
> 
> 

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. Crear un administrador de Azure AD para Azure SQL Server.
Cada servidor de Azure SQL Server (que hospeda una instancia de Base de datos SQL o Almacenamiento de datos SQL) se inicia con una única cuenta de administrador, que es el administrador de todo el servidor Azure SQL Server. Se debe crear un segundo administrador de SQL Server que sea una cuenta de Azure AD. Esta entidad de seguridad se crea como un usuario de base de datos independiente en la base de datos maestra. Como administradores, las cuentas de administrador del servidor son miembros del rol **db_owner** en todas las bases de datos de usuarios y se especifican en cada base de datos de usuarios como el usuario **dbo**. Para obtener más información sobre las cuentas de administrador del servidor, consulte el tema que versa sobre la [administración de bases de datos e inicios de sesión de Azure SQL Database](sql-database-manage-logins.md) y la sección **Inicios de sesión y usuarios** de [Instrucciones y limitaciones de seguridad de Azure SQL Database](sql-database-security-guidelines.md).

Cuando se usa Azure Active Directory con replicación geográfica, el administrador de Azure Active Directory debe configurarse para los servidores principal y secundarios. Si un servidor no tiene un administrador de Azure Active Directory, los usuarios y los inicios de sesión de Azure Active Directory reciben un error "No se puede conectar con el servidor".

> [!NOTE]
> Los usuarios que no se basen en una cuenta de Azure AD (incluida la cuenta de administrador del servidor Azure SQL Server) no pueden crear usuarios basados en Azure AD porque no tienen permisos para validar los usuarios de la base de datos propuesta con Azure AD.
> 
> 

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Aprovisionamiento de un administrador de Azure Active Directory para el servidor Azure SQL Server mediante el Portal de Azure
1. En el [Portal de Azure](https://portal.azure.com/), en la esquina superior derecha, haga clic en la conexión para desplegar una lista de posibles directorios de Active Directory. Elija el Active Directory correcto como el valor predeterminado de Azure AD. En este paso se vincula la asociación de la suscripción de Active Directory con Azure SQL Server, asegurándose de que la misma suscripción se use tanto para Azure AD como para SQL Server. (El servidor Azure SQL Server se puede hospedar en Base de datos SQL de Azure o en Almacenamiento de datos SQL de Azure).
   
    ![elegir ad][8]
2. En el área de la izquierda, seleccione **Servidores SQL Server** y su **servidor SQL Server** y, después, en la parte superior de la hoja **SQL Server**, haga clic en **Configuración**.
   
    ![configuración de ad][9]
3. En la hoja **Configuración**, haga clic en **Administrador de Active Directory**.
4. En la hoja **Administrador de Active Directory**, haga clic en **Administrador de Active Directory** y, después, en la parte superior, haga clic en **Establecer administrador**.
5. En la hoja **Agregar administrador**, busque un usuario, seleccione el usuario o grupo que convertirá en administrador y, después, haga clic en **Seleccionar**. (En la hoja del administrador de Active Directory se muestran todos los miembros y grupos del directorio de Active Directory). No se pueden seleccionar los usuarios o grupos que aparecen atenuados porque no se admiten como administradores de Azure AD. (Consulte la lista de administradores admitidos en la sección anterior **Características y limitaciones de Azure AD** ). El control de acceso basado en rol (RBAC) se aplica solo al portal y no se propaga a SQL Server.
6. En la parte superior de la hoja **Administrador de Active Directory**, haga clic en **GUARDAR**.
    ![elegir administrador][10]
   
    El proceso de cambio del el administrador puede tardar varios minutos. El nuevo administrador aparece en el cuadro **Administrador de Active Directory** .

> [!NOTE]
> Al configurar el nuevo administrador de Azure AD, el nuevo nombre del administrador (usuario o grupo) no puede estar en la base de datos maestra virtual como usuario de autenticación de SQL Server. Si estuviera presente, se producirá un error en el programa de instalación del administrador de Azure AD y, por lo tanto, deberá revertir su creación e indicar que ese administrador (nombre) ya existe. Como dicho usuario de autenticación de SQL Server no forma parte de Azure AD, se producirá un error cada vez que intente conectarse al servidor mediante la autenticación de Azure AD.
> 
> 

Si más adelante desea quitar un administrador, en la parte superior de la hoja **Administrador de Active Directory**, haga clic en **Quitar administrador** y, después, en **Guardar**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Aprovisionar un administrador de Azure AD para Azure SQL Server mediante PowerShell
Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Para aprovisionar un administrador de Azure AD, debe ejecutar los siguientes comandos de Azure PowerShell:

* Add-AzureRmAccount
* Select-AzureRmSubscription

Cmdlets que se usan para aprovisionar y administrar administradores de Azure AD:

| Nombre del cmdlet | Description |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) |Aprovisiona un administrador de Azure Active Directory para Azure SQL Server o para Almacenamiento de datos SQL de Azure. (Debe ser de la suscripción actual). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) |Quita un administrador de Azure Active Directory para Azure SQL Server o para Almacenamiento de datos SQL de Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) |Devuelve información sobre un administrador de Azure Active Directory configurado actualmente para el servidor de Azure SQL Server o para Almacenamiento de datos SQL. |

Use el comando de PowerShell get-help para ver más detalles de cada uno de estos comandos; por ejemplo, ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

El script siguiente aprovisiona un grupo de administradores de Azure AD denominado **DBA_Group** (id. de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para el servidor **demo_server**, en un grupo de recursos llamado **Group-23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

El parámetro de entrada **DisplayName** acepta tanto el nombre para mostrar de Azure AD, como el nombre principal de usuario. Por ejemplo, ``DisplayName="John Smith"`` y ``DisplayName="johns@contoso.com"``. En los grupos de Azure AD solo se admite el nombre para mostrar de Azure AD.

> [!NOTE]
> El comando ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` de Azure PowerShell no le impedirá aprovisionar administradores de Azure AD para usuarios no admitidos. Es posible aprovisionar un usuario no admitido, pero no podrá conectarse a una base de datos. (Consulte la lista de administradores admitidos en la sección anterior **Características y limitaciones de Azure AD** ).
> 
> 

En el ejemplo siguiente se usa el elemento opcional **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> El elemento **ObjectID** de Azure AD es necesario cuando el valor **DisplayName** no es único. Para recuperar los valores **ObjectID** y **DisplayName**, use la sección Active Directory del Portal de Azure clásico y visualice las propiedades de un usuario o grupo.
> 
> 

En el ejemplo siguiente se devuelve información sobre el administrador actual de Azure AD para el servidor de Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

En el ejemplo siguiente se quita un administrador de Azure AD:

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

También puede aprovisionar un administrador de Azure Active Directory mediante las API de REST. Para obtener más información, consulte [Service Management REST API Reference and Operations for Azure SQL Databases](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. Configurar los equipos cliente.
En todos los equipos cliente, desde el que las aplicaciones o los usuarios se conectan a la Base de datos SQL de Azure o a Almacenamiento de datos SQL mediante identidades de Azure AD, debe instalar el software siguiente:

* .NET Framework 4.6 o una versión posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* La Biblioteca de autenticación de Azure Active Directory para SQL Server (**ADALSQL.DLL**) está disponible en varios idiomas (x86 y amd64) en el centro de descarga en la sección [Biblioteca de autenticación de Microsoft Active Directory para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Herramientas
* La instalación de [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o de [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) cumple los requisitos de .NET Framework 4.6.
* SSMS instala la versión x86 de **ADALSQL.DLL**.
* SSMS instala la versión amd64 de **ADALSQL.DLL**.
* La versión más reciente de Visual Studio de la sección [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumple con los requisitos de .NET Framework 4.6, pero no instala la versión amd64 necesaria de **ADALSQL.DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. Crear usuarios de base de datos independiente  en la base de datos y asignados a identidades de Azure AD.
### <a name="about-contained-database-users"></a>Sobre los usuarios de bases de datos independientes
La autenticación de Azure Active Directory requiere que los usuarios de la base de datos se creen como usuarios de bases de datos independientes. Un usuario de base de datos independiente basado en una identidad de Azure AD es un usuario de base de datos que no tiene un inicio de sesión en la base de datos maestra y que se asigna a una identidad en el directorio de Azure AD que está asociado a la base de datos. La identidad de Azure AD puede ser una cuenta de usuario individual o un grupo. Para más información sobre los usuarios de bases de datos independientes, vea [Usuarios de bases de datos independientes: cómo hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Los usuarios de base de datos (a excepción de los administradores) no se pueden crear mediante el portal. Los roles de RBAC no se propagan a Almacenamiento de datos SQL, Base de datos SQL o SQL Server. Los roles de RBAC de Azure se utilizan para administrar los recursos de Azure y no se aplican a los permisos de base de datos. Por ejemplo, el rol **Colaborador de SQL Server** no concede acceso para conectarse a Base de datos SQL o Almacenamiento de datos SQL. El permiso de acceso tiene que concederse directamente en la base de datos mediante instrucciones de Transact-SQL.
 

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Conexión con la base de datos de usuario o el almacén de datos mediante SQL Server Management Studio o SQL Server Data Tools
Para confirmar que el administrador de Azure AD está correctamente configurado, conéctese a la base de datos **maestra** con la cuenta de administrador de Azure AD.
Para aprovisionar un usuario de base de datos independiente basada en un directorio de Azure AD (que no sea el administrador del servidor que es el propietario de la base de datos), conéctese a la base de datos con una identidad de Azure AD que tenga acceso a la base de datos.

> [!IMPORTANT]
> La autenticación de Azure Active Directory es compatible con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) en Visual Studio 2015. La versión de agosto de 2016 de SSMS también incluye compatibilidad con la autenticación universal de Active Directory, que permite a los administradores requerir Multi-Factor Authentication con una llamada de teléfono, un mensaje de texto, tarjetas inteligentes con PIN o una notificación de aplicación móvil.
 

#### <a name="connect-using-active-directory-integrated-authentication"></a>Conectarse mediante la autenticación integrada de Active Directory
Use este método si tiene la sesión iniciada en Windows con sus credenciales de Azure Active Directory desde un dominio federado.

1. Inicie Management Studio o Data Tools y, en el cuadro de diálogo **Conectar con el servidor** (o **Conectarse al motor de base de datos**), seleccione **Autenticación integrada de Active Directory** en el cuadro **Autenticación**. No se necesita ni se puede especificar una contraseña para la conexión, ya que aparecen las credenciales existentes.   

    ![Selección de autenticación integrada de Active Directory][11]
2. Haga clic en el botón **Opciones** y, en la página **Propiedades de conexión**, en el cuadro **Conectar con base de datos**, escriba el nombre de la base de datos de usuarios a la que quiere conectarse.   

    ![Selección del nombre de la base de datos][13]

#### <a name="connect-using-active-directory-password-authentication"></a>Conectarse mediante la autenticación de contraseña de Active Directory
Use este método al conectarse con un nombre de entidad de seguridad de Azure AD mediante el dominio administrado de Azure AD. También puede utilizarlo para la cuenta federada sin acceso al dominio, por ejemplo, cuando se trabaja de forma remota.

Use este método si tiene la sesión iniciada en Windows con las credenciales de un dominio que no está federado con Azure, o cuando utiliza la autenticación de Azure AD mediante Azure AD basado en el dominio inicial o del cliente.

1. Inicie Management Studio o Data Tools y, en el cuadro **Conectar con el servidor** (o **Conectarse al motor de base de datos**), en el cuadro** Autenticación**, seleccione **Autenticación de contraseña de Active Directory**.
2. En el cuadro **Nombre de usuario**, escriba el nombre de usuario de Azure Active Directory con el formato **username@domain.com**. Debe tratarse de una cuenta de Azure Active Directory o una cuenta de un dominio federado con el directorio de Azure Active Directory.
3. En el cuadro **Contraseña** , escriba la contraseña de usuario de la cuenta de Azure Active Directory o de la cuenta de dominio federado.
    ![Selección de autenticación de contraseña de Active Directory][12]
4. Haga clic en el botón **Opciones** y, en la página **Propiedades de conexión**, en el cuadro **Conectar con base de datos**, escriba el nombre de la base de datos de usuarios a la que quiere conectarse. (Consulte el gráfico que aparece en la opción anterior).

### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Crear un usuario de base de datos independiente en Azure AD en una base de datos de usuario
Para crear un usuario de base de datos independiente basada en Azure AD (que no sea el administrador del servidor que es el propietario de la base de datos), conéctese a la base de datos con una identidad de Azure AD, como un usuario con al menos el permiso **ALTER ANY USER** . Después, use la sintaxis de Transact-SQL siguiente:

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* puede ser el nombre principal de usuario de un usuario de Azure AD, o el nombre para mostrar de un grupo de Azure AD.

**Por ejemplo:** para crear un usuario de base de datos independiente que represente un usuario de dominio administrado o federado de Azure AD:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Para crear un usuario de base de datos contenido que represente un grupo de dominio federado o de Azure AD, proporcione el nombre para mostrar de un grupo de seguridad:

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Para crear un usuario de base de datos independiente que represente a una aplicación que se conecta mediante un token de Azure AD:

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

>  [!TIP]
>  No puede crear directamente un usuario a partir de una instancia de Azure Active Directory distinta a la que esté asociada a su suscripción de Azure. Sin embargo, se pueden agregar miembros de otras instancias de AD que sean usuarios importados en el Active Directory asociado (que se conocen como "usuarios externos") a un grupo de Active Directory del AD inquilino. Al crear un usuario de la base de datos independiente para ese grupo de AD, los usuarios del Active Directory externo pueden obtener acceso a SQL Database.   

Para más información sobre la creación de usuarios de bases de datos independientes basados en identidades de Azure Active Directory, vea [CREAR USUARIO (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Si se quita el administrador de Azure Active Directory de Azure SQL Server, se impide que cualquier usuario de autenticación de Azure AD pueda conectarse al servidor. Si es necesario, un administrador de Base de datos SQL puede quitar manualmente a usuarios de Azure AD no utilizados.   

>  [!NOTE]
>  Si recibe la notificación **Tiempo de espera de conexión agotado**, es posible que deba establecer el parámetro `TransparentNetworkIPResolution` de la cadena de conexión en False. Para obtener más información, consulte [Connection timeout issue with .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Problema de tiempo de espera de conexión agotado con Framework 4.6.1: TransparentNetworkIPResolution).   

   
Cuando se crea un usuario de base de datos, dicho usuario recibe el permiso **CONNECT** y puede conectarse a esa base de datos como un miembro con el rol **PUBLIC**. En un principio, los únicos permisos disponibles para el usuario son los permisos que se conceden al rol **PUBLIC** o cualquier otro permiso que se conceda a los grupos de Windows de los que sea miembro. Cuando se aprovisiona un usuario de base de datos de independiente basada en AD Azure, se pueden conceder permisos adicionales al usuario, del mismo modo que se conceden permisos a cualquier otro tipo de usuario. Normalmente, se conceden permisos a roles de base de datos y después se agregan usuarios a los roles. Para obtener más información, consulte [Conceptos básicos de los permisos de los motores de las bases de datos](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obtener más información sobre los roles especiales de Base de datos SQL, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md).
Un usuario de dominio federado que se importa en un dominio de administración debe usar la identidad de dominio administrado.

> [!NOTE]
> Los usuarios de Azure AD se marcan en los metadatos de la base de datos con el tipo E (EXTERNAL_USER) y en los grupos con el tipo X (EXTERNAL_GROUPS). Para obtener más información, consulte [sys.database_principals (Transact-SQL)](https://msdn.microsoft.com/library/ms187328.aspx). 


## <a name="7-connect-by-using-azure-ad-identities"></a>7. Conectarse con identidades de Azure AD.
La autenticación de Azure Active Directory admite los siguientes métodos de conexión a una base de datos mediante identidades de Azure AD:

* Mediante la autenticación integrada de Windows.
* Mediante el nombre y contraseña de una entidad de seguridad de Azure AD
* Mediante la autenticación de token de aplicación

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Conexión mediante autenticación integrada (Windows)
Para usar la autenticación integrada de Windows, la instancia de Active Directory del dominio se debe federar con Azure Active Directory. La aplicación cliente (o un servicio) que se conecte a la base de datos debe ejecutarse en un equipo unido a un dominio con las credenciales de dominio de un usuario.

Para conectarse a una base de datos mediante autenticación integrada y una identidad de Azure AD, la palabra clave Authentication de la cadena de conexión de la base de datos debe establecerse como Active Directory Integrated. En el siguiente ejemplo de código de C# se usa ADO.NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Tenga en cuenta que no se admite la palabra clave ``Integrated Security=True`` de la cadena de conexión para conectarse a la Base de datos SQL de Azure.
Tenga en cuenta que al crear una conexión ODBC tendrá que quitar espacios y establecer la autenticación en 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Conexión con el nombre y la contraseña de una entidad de seguridad de Azure AD
Para conectarse a una base de datos mediante autenticación integrada y una identidad de Azure AD, la palabra clave Authentication debe establecerse como Active Directory Password. La cadena de conexión debe contener las palabras clave y los valores User ID/UID y Password/PWD. En el siguiente ejemplo de código de C# se usa ADO.NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Puede encontrar más información sobre los métodos de autenticación de Azure AD con los ejemplos de código de demostración que se encuentran disponibles en la [demostración de la autenticación de Azure AD de GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 Conexión con un token de Azure AD
Este método de autenticación permite a los servicios de nivel intermedio conectarse a Base de datos SQL de Azure o Almacenamiento de datos SQL de Azure mediante la obtención de un token de Azure Active Directory (AAD). Permite escenarios sofisticados, incluida la autenticación basada en certificados. Para usar la autenticación de token de Azure AD tiene que realizar cuatro pasos básicos:

1. Registrar la aplicación con Azure Active Directory y obtener el identificador de cliente para el código. 
2. Crear un usuario de base de datos que represente a la aplicación. (Completado anteriormente en el paso 6).
3. Crear un certificado en el equipo cliente que va ejecuta la aplicación.
4. Agregar el certificado como una clave para la aplicación.

Cadena de conexión de ejemplo:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Para más información, consulte el [blog de seguridad de SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Conexión con sqlcmd
Las instrucciones siguientes permiten conectar con la versión 13.1 de sqlcmd, que está disponible en el [Centro de descarga](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Otras referencias
[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)

[Bases de datos independientes](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png




<!--HONumber=Nov16_HO4-->


