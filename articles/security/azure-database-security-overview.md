---
title: "Introducción a la seguridad de base de datos de Azure | Microsoft Docs"
description: "En este artículo se proporciona información general sobre las características de seguridad de base de datos de Azure."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 3c83645f61cd42c8c2b46f787c9e7531726d3fbb
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="azure-database-security-overview"></a>Introducción a la seguridad de base de datos de Azure

La seguridad es un aspecto importante a la hora de administrar bases de datos y siempre ha sido una prioridad para Azure SQL Database. Azure SQL Database admite la seguridad de conexión con las reglas de firewall y el cifrado de conexión. Es compatible con la autenticación mediante nombre de usuario y contraseña y con la autenticación de Azure Active Directory, que usa las identidades administradas por Azure Active Directory. La autorización usa el control de acceso basado en roles.

Azure SQL Database admite el cifrado mediante el cifrado y descifrado de la base de datos en tiempo real, copias de seguridad asociadas y archivos de registro de transacciones en reposo sin necesidad de efectuar cambios en la aplicación.

Microsoft proporciona otros métodos para cifrar los datos de la empresa:

-   Cifrado de nivel de celda para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
-   Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar Azure Key Vault con SQL Server en una máquina virtual de Azure.
-   Always Encripted (actualmente en versión preliminar) hace que el cifrado sea transparente para las aplicaciones y permite que los clientes cifren información confidencial dentro de aplicaciones cliente sin compartir las claves de cifrado con SQL Database.

Gracias a la auditoría de Azure SQL Database, las empresas pueden registrar eventos en un almacenamiento de Azure Storage de registros de auditoría. La auditoría de SQL Database también se integra con Microsoft Power BI, con el fin de facilitar la generación de análisis e informes detallados.

 Las bases de datos SQL de Azure pueden estar firmemente protegidas para satisfacer la mayoría de los requisitos de seguridad o legales, como HIPAA, ISO 27001/27002 y PCI DSS nivel 1, entre otros. En el [sitio del Centro de confianza de Microsoft Azure](http://azure.microsoft.com/support/trust-center/services/) hay disponible una lista actualizada de certificaciones de cumplimiento de seguridad.

En este artículo se describen los conceptos básicos de la protección de las bases de datos de Microsoft Azure SQL Database para los datos estructurados, tabulares y relacionales. En concreto, este artículo le ayudará a empezar a trabajar con los recursos necesarios para proteger los datos, controlar el acceso y realizar una supervisión proactiva.

Este artículo de información general sobre la seguridad de base de datos de Azure se centra en las siguientes áreas:

-   Protección de datos
-   Control de acceso
-   Supervisión proactiva
-   Administración de seguridad centralizada
-   Azure Marketplace

## <a name="protect-data"></a>Protección de datos

SQL Database protege los datos mediante el cifrado de los datos en movimiento a través de [Seguridad de la capa de transporte](https://support.microsoft.com/kb/3135244), de los datos en reposo a través de [Cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242) y de los datos de uso a través de [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

En esta sección hablaremos de:

-   Cifrado en movimiento
-   Cifrado en reposo
-   Cifrado en uso (cliente)

Si desea conocer otras formas de cifrar datos, considere:

-   [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
-   Si necesita un módulo de seguridad de hardware o la administración central de la jerarquía de claves de cifrado, considere la posibilidad de usar [Azure Key Vault con SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Cifrado en movimiento

Un problema habitual de todas las aplicaciones cliente/servidor es la necesidad de privacidad cuando los datos se desplazan a través de redes públicas y privadas. Si los datos que se transfieren a través de una red no están cifrados, existe la posibilidad de que usuarios no autorizados los capturen y roben. Cuando trabaja con los servicios de base de datos, debe asegurarse de que se cifren los datos entre el cliente y el servidor de la base de datos, así como entre los servidores de base de datos que se comunican entre sí y las aplicaciones de nivel intermedio.

Uno de los problemas existentes a la hora de administrar una red es el de proteger los datos que se envían entre las aplicaciones a través de una red que no es de confianza. Puede usar [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores y clientes y usarlo para cifrar mensajes entre las partes autenticadas.

En el proceso de autenticación, un cliente TLS/SSL envía un mensaje a un servidor TLS/SSL y el servidor responde con la información que necesita para autenticarse a sí mismo. El cliente y el servidor efectúan un intercambio adicional de claves de sesión y se cierra el cuadro de diálogo de autenticación. Una vez concluida la autenticación, se puede iniciar la comunicación protegida por SSL entre el servidor y el cliente mediante las claves de cifrado simétrico que se establecen durante el proceso de autenticación.

Todas las conexiones a Azure SQL database requieren cifrado (SSL/TLS) siempre que haya datos "en tránsito" hacia y desde la base de datos. SQL Azure usa TLS/SSL para autenticar servidores y clientes y después lo usa para cifrar mensajes entre las partes autenticadas. En la cadena de conexión de su aplicación, debe especificar los parámetros para cifrar la conexión y no confiar en el certificado de servidor (esto se hace automáticamente si copia la cadena de conexión en Azure Portal). De lo contrario, la conexión no comprobará la identidad del servidor y será vulnerable a los ataques de tipo "Man in the middle". Por ejemplo, en el caso del controlador de ADO.NET, los parámetros de la cadena de conexión son Encrypt=True y TrustServerCertificate=False.

### <a name="encryption-at-rest"></a>Cifrado en reposo
Puede tomar varias precauciones para ayudar a proteger la base de datos, como diseñar un sistema seguro, cifrar los recursos confidenciales e instalar un firewall alrededor de los servidores de base de datos. Pero en un escenario en que se roban medios físicos (como unidades o cintas de copias de seguridad), un tercero malintencionado puede restaurar o asociar la base de datos y examinar los datos.

Una solución consiste en cifrar los datos confidenciales de la base de datos y proteger las claves que se usan para cifrar los datos con un certificado. Con ello se impide que cualquiera que no disponga de las claves use los datos, pero este tipo de protección debe planificarse.

Para solucionar este problema, SQL Server y SQL Azure admiten el [cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). El TDE cifra los archivos de datos de SQL Server y Azure SQL Database, conocidos como datos de cifrado en reposo.

El cifrado de datos transparente de Azure SQL Database facilita la protección contra la amenaza de actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo sin necesitar cambios en la aplicación.  

TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos. En SQL Database, la clave de cifrado de base de datos está protegida por un certificado de servidor integrado. El certificado de servidor integrado es único para cada servidor de SQL Database.

Si una base de datos está en una relación de GeoDR, está protegida por una clave diferente en cada servidor. Si hay dos bases de datos conectadas al mismo servidor, comparten el mismo certificado integrado. Microsoft alterna automáticamente estos certificados al menos cada 90 días. Para obtener una descripción general de TDE, vea [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Cifrado en uso (cliente)

La mayoría de las infracciones de datos implican el robo de datos críticos, como los números de tarjeta de crédito o información de identificación personal. Las bases de datos pueden representar tesoros repletos de información confidencial. Pueden contener datos personales de los clientes, información confidencial sobre la competencia y propiedad intelectual. Los datos perdidos o robados (sobre todo los datos de los clientes) pueden comportar daños de marca, desventajas competitivas e importantes sanciones, incluso demandas.

![Always Encrypted](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) es una característica creada para proteger la información confidencial, como números de tarjetas de crédito o números de identificación nacionales (por ejemplo, números de la seguridad social de EE. UU.), almacenados en bases de datos de Azure SQL Database o SQL Server. Always Encrypted permite a los clientes cifrar información confidencial dentro de aplicaciones cliente y no revelar las claves de cifrado al motor de base de datos (SQL Database o SQL Server).

Always Encrypted proporciona una separación entre quienes poseen los datos (y pueden verlos) y quienes administran los datos (pero no deben tener acceso a estos), garantizando que los administradores de bases de datos locales, los operadores de bases de datos en la nube y otros usuarios con privilegios elevados (pero no autorizados) no puedan obtener acceso a los datos cifrados.

Además, Always Encrypted hace que el cifrado resulte transparente para las aplicaciones. En el equipo cliente se instala un controlador habilitado para Always Encrypted para que pueda cifrar y descifrar los datos confidenciales en la aplicación cliente de forma automática. El controlador cifra los datos en columnas confidenciales antes de pasarlos al motor de base de datos y vuelve a escribir las consultas automáticamente para que se conserve la semántica de la aplicación. De forma similar, el controlador descifra los datos de forma transparente, almacenados en columnas de bases de datos cifradas, incluidas en los resultados de la consulta.

## <a name="access-control"></a>Control de acceso
Para proporcionar seguridad, SQL Database controla el acceso con reglas de firewall que limitan la conectividad por dirección IP, con mecanismos de autenticación que requieren a los usuarios que demuestren su identidad y con mecanismos de autorización que limitan a los usuarios el acceso a datos y acciones específicos.

### <a name="database-access"></a>Acceso a la base de datos

La protección de datos comienza con el control de acceso a los datos. El centro de datos que hospeda sus datos administra el acceso físico, aunque puede configurar un firewall para administrar la seguridad en el nivel de red. También puede controlar el acceso configurando inicios de sesión para la autenticación y definiendo permisos para los roles de servidor y de base de datos.

En esta sección hablaremos de:

-   Firewall y reglas de firewall
-   Autenticación
-   Autorización

#### <a name="firewall-and-firewall-rules"></a>Firewall y reglas de firewall

Microsoft Azure SQL Database ofrece un servicio de base de datos relacional para Azure y otras aplicaciones basadas en Internet. Para ayudar a proteger los datos, los firewalls impiden todo acceso al servidor de bases de datos, excepto a aquellos equipos a los que haya concedido permiso. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud. Para más información, consulte [Introducción a las reglas de firewall de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

El servicio [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) solo está disponible a través del puerto TCP 1433. Para tener acceso a una base de datos SQL desde el equipo, asegúrese de que el firewall de equipos cliente permite la comunicación TCP saliente en el puerto TCP 1433. Si no es necesario para otras aplicaciones, bloquee las conexiones entrantes en el puerto TCP 1433.

#### <a name="authentication"></a>Autenticación

La autenticación de SQL Database indica cómo probar su identidad al conectarse a la base de datos. SQL Database admite dos tipos de autenticación:

-   **Autenticación SQL:** cuando se crea una instancia lógica de SQL, se crea también una cuenta de inicio de sesión único, que se denomina Cuenta de suscriptor de SQL Database. Esta cuenta se conecta mediante la [autenticación de SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nombre de usuario y contraseña). Esta cuenta es un administrador en la instancia del servidor lógico y en todas las bases de datos de usuario asociadas a dicha instancia. Los permisos de la cuenta del suscriptor no se pueden restringir. Solo puede existir una de estas cuentas.
-   **Autenticación de Azure Active Directory:**[La autenticación de Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) es un mecanismo de conexión a Microsoft Azure SQL Database y a SQL Data Warehouse mediante identidades en Azure Active Directory (Azure AD). Esto le permite administrar de forma centralizada las identidades de los usuarios de base de datos.

![Autenticación](./media/azure-databse-security-overview/azure-database-fig2.png)

 Ventajas de la autenticación de Azure Active Directory:
  - Ofrece una alternativa a la autenticación de SQL Server.
  - También permite detener la proliferación de identidades de usuario en los servidores de base de datos y posibilita la rotación de contraseñas en un solo lugar.
  - Puede administrar los permisos de la base de datos usando grupos externos (Azure Active Directory).
  - Puede eliminar el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.

#### <a name="authorization"></a>Autorización
La [autorización](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) indica las acciones que pueden llevar a cabo los usuarios en Azure SQL Database, algo que controlan los [permisos de nivel de objeto](https://msdn.microsoft.com/library/ms189121) y las [pertenencias a roles](https://msdn.microsoft.com/library/ms191291.aspx) de bases de datos de la cuenta de usuario. La autorización es el proceso en el que se determinan los recursos protegibles a los que puede obtener acceso una entidad de seguridad y las operaciones permitidas para dichos recursos.

### <a name="application-access"></a>Acceso a las aplicaciones

En esta sección hablaremos de:

-   Enmascaramiento de datos dinámicos
-   Seguridad de nivel de fila

#### <a name="dynamic-data-masking"></a>Enmascaramiento de datos dinámicos
Un representante de servicio de un centro de llamadas podría identificar a los autores de las llamadas a partir de varios dígitos de su número de seguridad social o el número de tarjeta de crédito, pero esa es una información que no debería exponerse por completo al representante del servicio.

Se puede definir una regla de enmascaramiento que enmascare todo excepto los cuatro últimos dígitos de un número de seguridad social o un número de tarjeta de crédito en el conjunto de resultados de cualquier consulta.

![Enmascaramiento de datos dinámicos](./media/azure-databse-security-overview/azure-database-fig3.png)

Otro ejemplo, una máscara de datos apropiada se puede definir para proteger los datos de información de identificación personal, para que un desarrollador pueda consultar los entornos de producción para solucionar problemas sin infringir las reglamentaciones de cumplimiento.

El [enmascaramiento dinámico de datos de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de información confidencial mediante su enmascaramiento a los usuarios sin privilegios. El enmascaramiento de datos dinámicos se admite con la versión V12 de Azure SQL Database.

El [enmascaramiento dinámico de datos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ayuda a impedir el acceso no autorizado a datos confidenciales permitiéndole designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian.


> [!Note]
> El enmascaramiento de datos dinámicos puede configurarse mediante el administrador de Base de datos de Azure, el administrador del servidor o los roles de autoridad de seguridad.

#### <a name="row-level-security"></a>Seguridad de nivel de fila
Otro requisito de seguridad habitual de las bases de datos multiinquilino es la [seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131.aspx). Esta característica le permite controlar el acceso a las filas de una tabla de base de datos en función de las características del usuario que ejecuta una consulta (por ejemplo, la pertenencia a un grupo o el contexto de ejecución).

![Seguridad de nivel de fila](./media/azure-databse-security-overview/azure-database-fig4.png)

La lógica de restricción de acceso está en el nivel de la base de datos, en lugar de encontrarse en otro nivel de la aplicación lejos de los datos. El sistema de base de datos aplica las restricciones de acceso cada vez que se intenta acceder a los datos desde cualquier nivel. Esto hace que el sistema de seguridad resulte más sólido y confiable al reducir el área expuesta del sistema de seguridad.

La seguridad de nivel de fila introduce el control de acceso basado en predicados. Ofrece una evaluación flexible, centralizada y basada en predicados que puede tener en cuenta los metadatos o cualquier otro criterio que el administrador determine como adecuado. El predicado se usa como criterio para determinar si el usuario tiene el acceso adecuado a los datos en función de los atributos de usuario. El control de acceso basado en etiquetas se puede implementar mediante el control de acceso basado en predicados.

## <a name="proactive-monitoring"></a>Supervisión proactiva
SQL Database protege los datos proporcionando capacidades de **auditoría** y **detección de amenazas**.

### <a name="auditing"></a>Auditoría
La auditoría de SQL Database aumenta la capacidad de obtener información sobre los eventos y los cambios que se producen en la base de datos, incluidas las actualizaciones y las consultas efectuadas en los datos.

La [auditoría de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los registra en un registro de auditoría de la cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. La auditoría posibilita y facilita la observancia de estándares reguladores pero no garantiza el cumplimiento.

La auditoría de SQL Database le permite:

-   **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
-   **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
-   **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Existen dos métodos de auditoría:

-   **Auditoría de blobs**: los registros se escriben en Azure Blob Storage. Es el método de auditoría más reciente, que proporciona mayor rendimiento, admite auditoría de nivel de objeto de mayor granularidad y es más rentable.
-   **Auditoría de tablas**: los registros se escriben en Azure Table Storage.

### <a name="threat-detection"></a>Detección de amenazas
La [detección de amenazas de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) detecta actividades sospechosas que indican posibles amenazas de seguridad. La detección de amenazas le permite responder a eventos sospechosos en la base de datos (como las inserciones de SQL) en el momento en que se producen. Proporciona alertas y permite usar la auditoría de Azure SQL Database para explorar los eventos sospechosos.

![Detección de amenazas](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Por ejemplo, la inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

Los responsables de seguridad u otros administradores designados pueden recibir una notificación inmediata sobre las actividades sospechosas en las bases de datos cuando se producen. Cada notificación proporciona detalles de la actividad sospechosa y recomienda cómo investigar más y mitigar la amenaza.        

## <a name="centralized-security-management"></a>Administración de seguridad centralizada

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) ayuda a evita y a detectar las amenazas, además de a responder a ellas. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-database) ayuda a proteger los datos de SQL Database ofreciendo visibilidad sobre la seguridad en todos los servidores y bases de datos. Con Security Center puede realizar estas tareas:

-   Definir las directivas de auditoría y cifrado de SQL Database
-   Supervisar la seguridad de los recursos de SQL Database de todas sus suscripciones
-   Identificar y corregir problemas de seguridad rápidamente
-   Integrar las alertas de [detección de amenazas de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
-   El Centro de seguridad admite el acceso basado en rol.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace es una tienda de aplicaciones y servicios en línea que permite a las nuevas empresas y a los fabricantes de software independientes (ISV) ofrecer sus soluciones a clientes de Azure de todo el mundo.
Azure Marketplace combina los ecosistemas de asociados de Microsoft Azure en una sola plataforma unificada para atender mejor a nuestros clientes y asociados. Haga clic [aquí](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) para echar un vistazo a los productos de seguridad de base de datos disponibles en Azure Marketplace.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [protección de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Obtenga más información sobre [los servicios Azure Security Center y Azure SQL Database](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Para obtener más información sobre la detección de amenazas, consulte [Detección de amenazas de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Para obtener más información, consulte [Mejora del rendimiento de la base de datos de SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
