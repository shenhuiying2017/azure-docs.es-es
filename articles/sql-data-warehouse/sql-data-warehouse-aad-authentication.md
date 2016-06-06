<properties
   pageTitle="Conexión a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory | Microsoft Azure"
   description="Aprenda a conectarse a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/11/2016"
   ms.author="rick.byham@microsoft.com"/>

# Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication (Conexión a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory)


La autenticación de Azure Active Directory es un mecanismo de conexión a Almacenamiento de datos de Microsoft Azure SQL mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure Active Directory puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece un lugar único para administrar usuarios de Almacenamiento de datos SQL y simplifica la administración de permisos.

## Ventajas

Entre las ventajas se incluyen las siguientes:

- Ofrece una alternativa a la autenticación de SQL Server.
- Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
- Permite la rotación de contraseñas en un solo lugar
- Los clientes pueden administrar los permisos de la base de datos con grupos externos (AAD).
- Puede eliminar el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
- La autenticación de Azure Active Directory utiliza usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
- Azure Active Directory admite la autenticación basada en token para las aplicaciones que se conectan a Almacenamiento de datos SQL.

> [AZURE.IMPORTANT] Azure Active Directory es una característica de vista previa y está sujeta a los términos de la versión de vista previa incluidos en el contrato de licencia (por ejemplo, el Contrato Enterprise, el Contrato de Microsoft Azure o el contrato Microsoft Online Subscription), así como cualquier otro [Términos de Uso Complementarios para Vistas Previas de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicable.

## Pasos de configuración

En los pasos de configuración se incluyen los siguientes procedimientos para configurar y usar la autenticación de Azure Active Directory.

1. Crear y rellenar un Azure Active Directory.
2. Opcional: asociar o cambiar el Active Directory que está asociado actualmente a la suscripción de Azure.
3. Crear un administrador de Azure Active Directory para Almacenamiento de datos SQL de Azure.
4. Configurar los equipos cliente.
5. Crear usuarios de base de datos independiente en la base de datos y asignados a identidades de Azure AD.
6. Conectarse a su almacenamiento de datos mediante identidades de Azure AD.

Las principales diferencias entre el uso de la autenticación de Azure Active Directory con Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure es que debe utilizar SQL Server Data Tools en lugar de SQL Server Management Studio para conectarse a Almacenamiento de datos SQL. Almacenamiento de datos SQL requiere al menos la versión de abril de 2016 (14.0.60311.1) de SQL Server Data Tools para Visual Studio 2015. Actualmente los usuarios de Azure Active Directory no se muestran en el Explorador de objetos de SSDT. Como solución alternativa, consulte los usuarios de [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
## Pasos siguientes 
- Siga los pasos detallados. Los pasos detallados para configurar y usar la autenticación de Azure Active Directory son casi idénticos para Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure. Siga los pasos detallados en el tema [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
- Cree roles de base de datos personalizados y agrégueles usuarios. A continuación, conceda permisos específicos a los roles. Para obtener información, consulte [Getting Started with Database Engine Permissions](https://msdn.microsoft.com/library/mt667986.aspx) (Introducción a los permisos de motor de la base de datos).

<!---HONumber=AcomDC_0525_2016-->