---
title: "Introducción a la seguridad de Azure SQL Database | Microsoft Docs"
description: Aprenda sobre la seguridad de SQL Database y SQL Server, incluidas las diferencias entre la nube y SQL Server local.
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
editor: 
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: cf105dbc366b96dbb49484bffce9b81960cf41f4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="securing-your-sql-database"></a>Protección de SQL Database

Este artículo describe los fundamentos de la protección de la capa de datos de una aplicación que use Azure SQL Database. Con este artículo, podrá empezar a trabajar con recursos para proteger datos, controlar el acceso y realizar una supervisión proactiva. 

Para obtener una descripción completa de las características de seguridad disponibles en todas las versiones de SQL, consulte [Seguridad y protección (motor de base de datos)](https://msdn.microsoft.com/library/bb510589). Asimismo, tiene información adicional disponible en las [Notas del producto técnicas acerca de la seguridad y Azure SQL Database](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (en PDF).

## <a name="protect-data"></a>Protección de datos

### <a name="encryption"></a>Cifrado
Para proteger los datos, SQL Database cifra los datos en movimiento a través del protocolo de [Seguridad de la capa de transporte](https://support.microsoft.com/kb/3135244), los datos en reposo a través del [Cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) y los datos en uso a través de [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). 

> [!IMPORTANT]
>Todas las conexiones a Azure SQL database requieren cifrado (SSL/TLS) siempre que haya datos "en tránsito" hacia y desde la base de datos. En la cadena de conexión de la aplicación, debe especificar los parámetros necesarios para cifrar la conexión y *no* confiar en el certificado de servidor (esto se hace automáticamente si copia la cadena de conexión fuera de Azure Portal). De lo contrario, la conexión no comprueba la identidad del servidor y es vulnerable a ataques de tipo "Man in the middle". Por ejemplo, en el caso del controlador de ADO.NET, los parámetros de la cadena de conexión son **Encrypt=True** y **TrustServerCertificate=False**. 

Si desea conocer otras formas de cifrar datos, considere:

* [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
* Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar el [Azure Key Vault con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="data-discovery--classification"></a>Clasificación y detección de datos
La opción Clasificación y detección de datos (actualmente en su versión preliminar) proporciona funcionalidades avanzadas integradas en Azure SQL Database para detectar, clasificar, etiquetar y proteger la información confidencial de las bases de datos. Las funciones de detección y clasificación de la información confidencial más importante (empresarial, financiera, médica, personal, etc.) desempeñan un rol fundamental en el modo en que se protege la información de su organización. Puede servir como infraestructura para:

- Varios escenarios de seguridad, como la supervisión (auditoría) y las alertas relacionadas con accesos anómalos a información confidencial.
- Controlar el acceso y mejorar la seguridad de las bases de datos que contienen información altamente confidencial.
- Ayudar a cumplir los requisitos de cumplimiento de normas y los estándares relacionados con la privacidad de datos.

Para obtener más información, consulte [Azure SQL Database Data Discovery and Classification](sql-database-data-discovery-and-classification.md) (Detección y clasificación de datos de Azure SQL Database). 

## <a name="control-access"></a>Control de acceso
SQL Database protege los datos mediante la limitación del acceso a la base de datos a través de reglas de firewall, de mecanismos de autenticación que requieren que los usuarios prueben su identidad y de la autorización a través de pertenencias y permisos basados en roles, así como la seguridad de nivel de fila y el enmascaramiento dinámico de datos. Para obtener información acerca del uso de las características de control de acceso en SQL Database, consulte [Control de acceso](sql-database-control-access.md).

> [!IMPORTANT]
> La administración de bases de datos y servidores lógicos en Azure se controlan mediante las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este artículo, consulte [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md).
>

### <a name="firewall-and-firewall-rules"></a>Firewall y reglas de firewall
Para ayudarle a proteger los datos, los firewalls impiden todo acceso al servidor de bases de datos hasta que especifique los equipos que tienen permiso mediante [reglas de firewall](sql-database-firewall-configure.md). Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.

### <a name="authentication"></a>Autenticación
La autenticación de SQL Database indica cómo probar su identidad al conectarse a la base de datos. SQL Database admite dos tipos de autenticación:

* **Autenticación de SQL**, que usa un nombre de usuario y una contraseña. Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo". 
* **Autenticación de Azure Active Directory**, que usa las identidades administradas por Azure Active Directory y es compatible con dominios administrados e integrados. Use la autenticación de Active Directory (seguridad integrada) [siempre que sea posible](https://msdn.microsoft.com/library/ms144284.aspx). Si desea usar la autenticación de Azure Active Directory, debe crear otro administrador de servidor llamado "administrador de Azure AD" con permiso para administrar usuarios y grupos de Azure AD. Este administrador también puede realizar todas las operaciones de un administrador de servidor normal. Consulte el tutorial [Conectar a la SQL Database mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md) , para obtener información acerca de cómo crear un administrador de Azure AD y así habilitar la autenticación de Azure Active Directory.

### <a name="authorization"></a>Autorización
Autorización indica las acciones que pueden realizar los usuarios en Azure SQL Database, algo que controlan los permisos de nivel de objeto y las pertenencias a roles de bases de datos de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. La cuenta de administrador de servidor con la que se está conectando forma parte de db_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

### <a name="row-level-security"></a>Seguridad de nivel de fila
La seguridad de nivel de fila permite a los clientes controlar el acceso a las filas de una tabla de base de datos en función de las características del usuario que ejecuta una consulta (por ejemplo, la pertenencia a un grupo o el contexto de ejecución). Para más información, consulte [Seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131).

### <a name="dynamic-data-masking"></a>Enmascaramiento de datos dinámicos 
El enmascaramiento dinámico de datos de SQL Database limita la exposición de información confidencial mediante su enmascaramiento a los usuarios sin privilegios. La característica Enmascaramiento dinámico de datos detecta automáticamente información potencialmente confidencial en Azure SQL Database y proporciona recomendaciones accionables para enmascarar estos campos, con un impacto mínimo en el nivel de aplicación. Su funcionamiento consiste en ocultar los datos confidenciales del conjunto de resultados de una consulta en los campos designados de la base de datos, mientras que los datos de la base de datos no cambian. Para más información, vea [Introducción al Enmascaramiento dinámico de datos de SQL Database](sql-database-dynamic-data-masking-get-started.md), que se puede utilizar para limitar la exposición de la información confidencial.

## <a name="proactive-monitoring"></a>Supervisión proactiva
SQL Database protege los datos proporcionando funcionalidades de auditoría y detección de amenazas. 

### <a name="auditing"></a>Auditoría
SQL Database Auditing realiza un seguimiento de las actividades de la base de datos y ayuda a mantener el cumplimiento normativo, para lo que graba eventos de base de datos en un registro de auditoría de su cuenta de Azure Storage. La auditoría permite conocer las actividades en curso de la base de datos, así como analizar e investigar la actividad histórica para identificar posibles amenazas o supuestas infracciones de seguridad y abusos. Para más información, consulte [Introducción a SQL Database Auditing](sql-database-auditing.md).  

### <a name="threat-detection"></a>Detección de amenazas
Detección de amenazas complementa la auditoría, ya que proporciona una capa adicional de inteligencia de seguridad integrada en el servicio de Azure SQL Database que detecta intentos inusuales y potencialmente dañinos para obtener acceso a las bases de datos o vulnerarlas. Recibirá alertas de actividades sospechosas, vulnerabilidades potenciales y ataques por inyección de código SQL, así como patrones anómalos de acceso a bases de datos. Las alertas de Detección de amenazas pueden verse en [Azure Security Center](https://azure.microsoft.com/services/security-center/) y proporcionar detalles de actividad sospechosa y la acción recomendada sobre cómo investigar y mitigar la amenaza. Detección de amenazas cuesta 15 USD/servidor/mes. Pruébelo gratis durante los primeros 60 días. Para más información, consulte [Introducción a Detección de amenazas de SQL Database](sql-database-threat-detection.md).
 
## <a name="compliance"></a>Cumplimiento normativo
Además de las anteriores características y funcionalidades que pueden ayudar a la aplicación a cumplir distintos requisitos de seguridad, Azure SQL Database también participa en las auditorías regulares y ha obtenido la certificación de una serie de normas de cumplimiento. Para más información, consulte el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/), donde podrá encontrar la lista más reciente de [certificaciones de cumplimiento de SQL Database](https://azure.microsoft.com/support/trust-center/services/).

## <a name="next-steps"></a>pasos siguientes

- Para obtener información acerca del uso de las características de control de acceso en SQL Database, consulte [Control de acceso](sql-database-control-access.md).
- Para más información sobre la auditoría de bases de datos, consulte [SQL Database auditing](sql-database-auditing.md) (Auditoría de SQL Database).
- Para más información sobre la detección de amenaza, consulte [SQL Database threat detection](sql-database-threat-detection.md) (Detección de amenazas de SQL Database).
