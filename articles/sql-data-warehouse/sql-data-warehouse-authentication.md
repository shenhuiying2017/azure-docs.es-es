<properties
   pageTitle="Autenticación a Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Autenticación de Azure Active Directory (AAD) y SQL Server a Almacenamiento de datos SQL de Azure"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>


# <a name="authentication-to-azure-sql-data-warehouse"></a>Autenticación a Almacenamiento de datos SQL de Azure

> [AZURE.SELECTOR]
- [Información general sobre seguridad](sql-data-warehouse-overview-manage-security.md)
- [Autenticación](sql-data-warehouse-authentication.md)
- [Cifrado (Portal)](sql-data-warehouse-encryption-tde.md)
- [Cifrado (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Para conectarse a Almacenamiento de datos SQL, debe transmitir las credenciales de seguridad para realizar la autenticación. Después de establecer una conexión, determinados valores de conexión se configuran como parte del establecimiento de la sesión de la consulta.  

Para obtener más información sobre la seguridad y cómo habilitar las conexiones a su almacenamiento de datos, consulte [Proteger una base de datos en Almacenamiento de datos SQL][].

## <a name="sql-authentication"></a>Autenticación de SQL
Para conectarse a Almacenamiento de datos SQL, debe proporcionar la información siguiente:

- Nombre de servidor completo
- Especificar la autenticación de SQL
- Nombre de usuario
- Password
- Base de datos predeterminada (opcional)

De forma predeterminada, su conexión se realiza a la base de datos *maestra* y no a su base de datos de usuario. Para conectarse a la base de datos de usuario puede hacer dos cosas:

- Especificar la base de datos predeterminada al registrar el servidor con el Explorador de objetos de SQL Server en SSDT, SSMS o en la cadena de conexión de la aplicación. Por ejemplo, incluya el parámetro InitialCatalog para una conexión ODBC.
- Resalte la base de datos de usuario antes de crear una sesión en SSDT.

> [AZURE.NOTE] La instrucción **USE MyDatabase;** de Transact-SQL no se admite para cambiar la base de datos de una conexión. Para instrucciones sobre cómo conectarse a Almacenamiento de datos SQL con SSDT, consulte el artículo [Query with Visual Studio][] (Realización de consultas con Visual Studio).

## <a name="azure-active-directory-(aad)-authentication"></a>Autenticación de Azure Active Directory (AAD)

La autenticación de [Azure Active Directory][¿Qué es Azure Active Directory?] es un mecanismo de conexión a Microsoft Azure SQL Data Warehouse mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure Active Directory puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece un lugar único para administrar usuarios de Almacenamiento de datos SQL y simplifica la administración de permisos. 

### <a name="benefits"></a>Ventajas

Entre las ventajas de Azure Active Directory, se incluyen:

- Proporciona una alternativa a la autenticación de SQL Server.
- Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
- Permite la rotación de contraseñas en un solo lugar
- Permite administrar los permisos de la base de datos con grupos externos (AAD).
- Elimina el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
- Usa usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
- Admite la autenticación basada en token para las aplicaciones que se conectan a SQL Data Warehouse.
- Admite Multi-Factor Authentication mediante autenticación universal de Active Directory para SQL Server Management Studio. Para una descripción de Multi-Factor Authentication, consulte [Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory todavía es relativamente nuevo y tiene algunas limitaciones. Para asegurarse de que Azure Active Directory sea una buena elección para su entorno, consulte [Características y limitaciones de Azure AD][], específicamente las consideraciones adicionales.

### <a name="configuration-steps"></a>Pasos de configuración

Siga estos pasos para configurar la autenticación de Azure Active Directory.

1. Crear y rellenar un Azure Active Directory.
2. Opcional: asociar o cambiar el Active Directory que está asociado actualmente a la suscripción de Azure.
3. Crear un administrador de Azure Active Directory para Almacenamiento de datos SQL de Azure.
4. Configurar los equipos cliente.
5. Crear usuarios de base de datos independiente  en la base de datos y asignados a identidades de Azure AD.
6. Conectarse a su almacenamiento de datos mediante identidades de Azure AD.

Actualmente los usuarios de Azure Active Directory no se muestran en el Explorador de objetos de SSDT. Como solución alternativa, vea los usuarios de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Búsqueda de los detalles
- Siga los pasos detallados. Los pasos detallados para configurar y usar la autenticación de Azure Active Directory son casi idénticos para Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure. Siga los pasos detallados del tema [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
- Cree roles de base de datos personalizados y agrégueles usuarios. A continuación, conceda permisos específicos a los roles. Para obtener más información, consulte [Introducción a los permisos de los motores de bases de datos](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Pasos siguientes

Para empezar a realizar consultas en el almacenamiento de datos con Visual Studio y otras aplicaciones, consulte [Query with Visual Studio][](Realización de consultas con Visual Studio).

<!-- Article references -->
[Proteger una base de datos en Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[¿Qué es Azure Active Directory?]: ../active-directory/active-directory-whatis.md
[Características y limitaciones de Azure AD]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations



<!--HONumber=Oct16_HO2-->


