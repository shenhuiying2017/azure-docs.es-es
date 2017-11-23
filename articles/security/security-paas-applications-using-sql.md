---
title: "Protección de bases de datos de PaaS en Azure | Microsoft Docs"
description: " Obtenga información sobre los procedimientos recomendados de seguridad de Azure SQL Database y SQL Data Warehouse para proteger las aplicaciones web y móviles PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: ed15ff750e73179f6979d13e45ab27aeee5c82ab
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-databases-in-azure"></a>Protección de bases de datos de PaaS en Azure

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
- Admite conexiones de SQL Server Management Studio que usan la autenticación universal de Active Directory, lo cual incluye [Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md). MFA incluye una sólida autenticación con una gama de sencillas opciones de comprobación: llamada de teléfono, mensaje de texto, tarjetas inteligentes con PIN o notificación de aplicación móvil. Para obtener más información, consulte [Compatibilidad de SSMS con Azure AD MFA con SQL Database y SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Para obtener más información sobre la autenticación de Azure AD, vea:

- [Conexión a SQL Database o a SQL Data Warehouse mediante autenticación de Azure Active Directory](../sql-database/sql-database-aad-authentication.md)
- [Autenticación a Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
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
[Cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/azure/bb934049) está habilitado de forma predeterminada. TDE cifra de forma transparente los archivos de datos y de registro de SQL Server, Azure SQL Database y Azure SQL Data Warehouse. TDE protege contra un posible peligro de acceso directo a los archivos o las copias de seguridad. Esto le permite cifrar los datos en reposo sin cambiar las aplicaciones existentes. TDE siempre debería permanecer habilitado, si bien esto no impedirá que un atacante use la ruta de acceso normal. TDE ofrece la posibilidad de cumplir muchas leyes, normativas y directrices establecidas en diversos sectores.

Azure SQL administra los problemas relacionados con las claves para TDE. Al igual que con TDE, se debe tener especial cuidado en el entorno local para garantizar la capacidad de recuperación y al mover las bases de datos. En escenarios más complejos, las claves se pueden administrar explícitamente en Azure Key Vault mediante la administración extensible de claves (consulte [Habilitar TDE en SQL Server con EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). Esto también permite la funcionalidad Bring Your Own Key (BYOK), que incorpora Azure Key Vault.

Azure SQL ofrece cifrado de columnas mediante [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Esta funcionalidad permite que solo las aplicaciones autorizadas accedan a las columnas confidenciales. Mediante este tipo de cifrado, se limitan las consultas SQL en las columnas cifradas a valores basados en la igualdad.

También debe usarse el cifrado a nivel de aplicación para datos selectivos. Las cuestiones sobre la soberanía de los datos se pueden mitigar en ocasiones mediante el cifrado de datos con una clave que se mantiene en el país correcto. Esto impide incluso que las transferencias de datos accidentales causen algún error, ya que resultará imposible descifrar los datos sin la clave, suponiendo que se emplee un algoritmo seguro (como AES 256).

Puede tomar precauciones adicionales para ayudar a proteger la base de datos, como diseñar un sistema seguro, cifrar los recursos confidenciales e instalar un firewall alrededor de los servidores de base de datos.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se presenta una colección de procedimientos recomendados de seguridad de SQL Database y SQL Data Warehouse para proteger las aplicaciones web y móviles PaaS. Para obtener más información sobre cómo proteger las implementaciones de PaaS, vea:

- [Protección de implementaciones de PaaS](security-paas-deployments.md)
- [Protección de aplicaciones web y móviles PaaS con Azure App Services](security-paas-applications-using-app-services.md)
