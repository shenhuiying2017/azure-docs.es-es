---
title: "Protección de aplicaciones web y móviles PaaS con Azure SQL Database y SQL Data Warehouse | Microsoft Docs"
description: " Obtenga información sobre los procedimientos recomendados de seguridad de Azure SQL Database y SQL Data Warehouse para proteger las aplicaciones web y móviles PaaS.. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b3db1281bbd37fcfbec54bdeb998e6338bb08369
ms.openlocfilehash: 39f841c0f3c8bed1bd640dfd644a58a764422aff


---
# <a name="securing-paas-web-and-mobile-applications-using-sql-database-and-sql-data-warehouse"></a>Protección de aplicaciones web y móviles PaaS con Azure SQL Database y SQL Data Warehouse

En este artículo se presenta una colección de procedimientos recomendados de seguridad de [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) y [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) para proteger las aplicaciones web y móviles PaaS. Estos procedimientos recomendados proceden de nuestra experiencia con Azure y las experiencias de clientes como usted.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL Database y SQL Data Warehouse
[Azure SQL Database](../sql-database/sql-database-technical-overview.md) y [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) proporcionan un servicio de base de datos relacional para aplicaciones basadas en Internet. Se van a considerar los servicios que ayudan a proteger las aplicaciones y los datos al utilizar Azure SQL Database y SQL Data Warehouse en una implementación de PaaS:

- Autenticación de Azure Active Directory (en lugar de la autenticación de SQL Server)
- Firewall de Azure SQL
- Cifrado de datos transparente (TDE) 

## <a name="best-practices"></a>Prácticas recomendadas

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Uso de un repositorio centralizado de identidades para autenticación y autorización

Las bases de datos Azure SQL pueden configurarse para usar uno de los dos tipos de autenticación:

- **Autenticación de SQL** usa un nombre de usuario y una contraseña. Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos.

- **Autenticación de Azure Active Directory** usa las identidades administradas por Azure Active Directory y es compatible con dominios administrados e integrados. Para usar la autenticación de Azure Active Directory, debe crear otro administrador de servidor llamado "administrador de Azure AD" con permiso para administrar usuarios y grupos de Azure AD. Este administrador también puede realizar todas las operaciones de un administrador de servidor normal.

La [autenticación de Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) es un mecanismo de conexión a Azure SQL Database y SQL Data Warehouse mediante identidades de Azure Active Directory (AD). Azure AD proporciona una alternativa a la autenticación de SQL Server, por lo que puede detener la proliferación de identidades de usuario entre los servidores de base de datos. La autenticación de Azure AD permite administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece una ubicación única para administrar usuarios de base de datos y simplifica la administración de permisos.  

Entre las ventajas de utilizar la autenticación de Azure AD en lugar de la autenticación de SQL se incluyen:

- Permite la rotación de contraseñas en un solo lugar.
- Administra los permisos de la base de datos con grupos externos de Azure AD.
- Elimina el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure AD.
- Usa usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
- Admite la autenticación basada en token para las aplicaciones que se conectan a SQL Database.
- Es compatible con ADFS (federación de dominios) o la autenticación nativa de usuario y contraseña para una instancia de Azure AD local sin sincronización de dominios.
- Admite conexiones de SQL Server Management Studio que usan la autenticación universal de Active Directory, lo cual incluye [Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md). MFA incluye una sólida autenticación con una gama de sencillas opciones de comprobación: llamada de teléfono, mensaje de texto, tarjetas inteligentes con PIN o notificación de aplicación móvil. Para obtener más información, consulte [Compatibilidad de SSMS con Azure AD MFA con Base de datos SQL y Almacenamiento de datos SQL](../sql-database/sql-database-ssms-mfa-authentication.md).

Para obtener más información sobre la autenticación de Azure AD, vea:

- [Conexión a Base de datos SQL o a Almacenamiento de datos SQL mediante autenticación de Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Autenticación a Almacenamiento de datos SQL de Azure](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Token-based authentication support for Azure SQL DB using Azure AD authentication](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/) (Compatibilidad de la autenticación basada en tokens para Azure SQL DB mediante la autenticación de Azure AD)

> [!NOTE]
> Para asegurarse de que Azure Active Directory sea una buena elección para su entorno, vea [Características y limitaciones de Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), específicamente las consideraciones adicionales.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Restricción de acceso según la dirección IP
Puede crear reglas de firewall que especifiquen intervalos de direcciones IP aceptables. Estas reglas pueden orientarse a los niveles de servidor y de base de datos. Se recomienda usar reglas de firewall de nivel de base de datos siempre que sea posible a fin de mejorar la seguridad y aumentar la portabilidad de la base de datos. Las reglas de firewall de nivel de servidor son más eficaces para administradores y cuando tenga muchas bases de datos con los mismos requisitos de acceso y no quiera dedicar tiempo a configurar cada una de ellas por separado.

Las restricciones de direcciones IP de origen predeterminadas de SQL Database permiten acceder desde cualquier dirección de Azure (incluidos otras suscripciones y otros inquilinos). Puede restringir esto para permitir que solo sus direcciones IP accedan a la instancia. A pesar de las restricciones de direcciones IP y del firewall de SQL, también se necesita una autenticación sólida. Vea las recomendaciones que se han realizado anteriormente en este artículo.

Para obtener más información sobre las restricciones de IP y el firewall de Azure SQL, vea:

- [Control de acceso a Azure SQL Database](../sql-database/sql-database-control-access.md)
- [Introducción a la configuración de reglas de firewall de Azure SQL Database](../sql-database/sql-database-firewall-configure.md)
- [Configuración de un firewall de nivel de servidor en Azure SQL Database mediante Azure Portal](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Cifrado de datos en reposo
[Cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/azure/bb934049) cifra los archivos de datos de SQL Server, Azure SQL Database y Azure SQL Data Warehouse, lo que se conoce como cifrado de datos en reposo. Puede tomar varias precauciones para ayudar a proteger la base de datos, como diseñar un sistema seguro, cifrar los recursos confidenciales e instalar un firewall alrededor de los servidores de base de datos. Sin embargo, en un escenario en que se roban medios físicos (como unidades o cintas de copias de seguridad), un tercero malintencionado puede restaurar o asociar la base de datos y examinar los datos. Una solución consiste en cifrar los datos confidenciales en la base de datos y proteger las claves que se usan para cifrar los datos con un certificado. Impide que cualquiera que no disponga de las claves use los datos, pero este tipo de protección debe planificarse por adelantado.

TDE protege los datos en reposo, es decir, los datos y los archivos de registro. Proporciona la capacidad de cumplir muchas leyes, normativas y directrices establecidas en diversos sectores. Esto permite a los desarrolladores de software cifrar los datos mediante el uso de algoritmos de cifrado estándar del sector sin cambiar las aplicaciones existentes.

TDE debe usarse si la normativa especifica explícitamente este tipo de cifrado. Tenga en cuenta, sin embargo, que esto no detendrá a un atacante que usa la ruta de acceso normal. Se usa TDE para ofrecer protección frente a un caso muy poco probable de que necesite usar cifrado adicional a nivel de aplicación, ya sea mediante Azure SQL con cifrado de filas y columnas o con el cifrado a nivel de aplicación.

También debe usarse el cifrado a nivel de aplicación para datos selectivos. Las cuestiones sobre la soberanía de los datos se pueden mitigar mediante el cifrado de datos con una clave que se mantiene en el país correcto. Esto impide incluso que las transferencias de datos accidentales causen algún error, ya que resultará imposible descifrar los datos sin la clave, suponiendo que se emplee un algoritmo seguro (como AES 256).

Azure SQL permite cifrar filas y columnas para permitir solo el acceso a usuarios autorizados ([RBAC](../active-directory/role-based-access-built-in-roles.md)) e impide que los usuarios con menos privilegios vean las columnas o filas.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se presenta una colección de procedimientos recomendados de seguridad de SQL Database y SQL Data Warehouse para proteger las aplicaciones web y móviles PaaS. Para obtener más información sobre cómo proteger las implementaciones de PaaS, vea:

- [Protección de implementaciones de PaaS](security-paas-deployments.md)
- [Protección de aplicaciones web y móviles PaaS con Azure App Services](security-paas-applications-using-app-services.md)



<!--HONumber=Feb17_HO1-->


