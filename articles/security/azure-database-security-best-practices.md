---
title: "Prácticas recomendadas de seguridad de las bases de datos de Azure | Microsoft Docs"
description: "En este artículo se proporciona un conjunto de prácticas recomendadas para la reforzar la seguridad de las bases de datos de Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: c8f49a778ab5e32108531cab05cab7f7eb092221
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-database-security-best-practices"></a>Procedimientos recomendados para la seguridad de las bases de datos de Azure

La seguridad es un aspecto importante a la hora de administrar las bases de datos y siempre ha sido una prioridad para Azure SQL Database. Las bases de datos pueden protegerse de forma estricta para ayudar a satisfacer la mayoría de los requisitos de seguridad o legales, como HIPAA, ISO 27001/27002 y PCI DSS nivel 1, entre otros. En el [sitio del Centro de confianza de Microsoft](http://azure.microsoft.com/support/trust-center/services/) hay disponible una lista actualizada de certificaciones de cumplimiento de seguridad. También puede colocar las bases de datos en centros de datos de Azure específicos en función de los requisitos normativos.

En este artículo veremos un conjunto de procedimientos recomendados de seguridad de las bases de datos de Azure. Estos procedimientos recomendados se derivan de nuestra experiencia con la seguridad de las bases de datos de Azure, y las experiencias de clientes como usted.

Para cada procedimiento recomendado, explicaremos:

-   Qué es el procedimiento recomendado
-   Por qué le conviene habilitar este procedimiento recomendado
-   Cuál podría ser el resultado si no habilita el procedimiento recomendado
-   Cómo aprender a habilitar el procedimiento recomendado

Este artículo de Procedimientos recomendados de seguridad de las bases de datos de Azure se basa en las funcionalidades y los conjuntos de características de la plataforma Azure existentes cuando se redactó. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

Los procedimientos recomendados de seguridad para las bases de datos de Azure descritos en este artículo incluyen:

-   Uso de reglas de firewall para restringir el acceso de las bases de datos
-   Habilitación de la autenticación de bases de datos
-   Protección de los datos con cifrado
-   Protección de los datos en tránsito
-   Habilitación de la auditoría de bases de datos
-   Habilitación de la detección de amenazas para las bases de datos


## <a name="use-firewall-rules-to-restrict-database-access"></a>Uso de reglas de firewall para restringir el acceso de las bases de datos

Microsoft Azure SQL Database ofrece un servicio de base de datos relacional para Azure y otras aplicaciones basadas en Internet. Para proporcionar seguridad en el acceso, SQL Database controla el acceso con reglas de firewall que limitan la conectividad por dirección IP, con mecanismos de autenticación que requieren a los usuarios que demuestren su identidad y con mecanismos de autorización que limitan a los usuarios el acceso a datos y acciones específicos. Los firewalls impiden todo acceso al servidor de bases de datos, excepto a aquellos equipos a los que haya concedido permiso. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.

![Reglas de firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

El servicio Azure SQL Database solo está disponible a través del puerto TCP 1433. Para tener acceso a una instancia de SQL Database desde el equipo, asegúrese de que el firewall de equipos cliente permite la comunicación TCP saliente en el puerto TCP 1433. Si no es necesario para otras aplicaciones, bloquee las conexiones entrantes en el puerto TCP 1433 mediante reglas de firewall.

Como parte del proceso de conexión, las conexiones de máquinas virtuales de Azure se redirigen a una dirección IP y un puerto diferente, único para cada rol de trabajo. El número de puerto está comprendido en el rango del 11000 al 11999. Para obtener más información sobre los puertos TCP, consulte [Puertos más allá del 1433 para ADO.NET 4.5 y SQL Database2](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Para más información general acerca de las reglas de firewall de SQL Database, consulte [Introducción a las reglas de firewall de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Habilitación de la autenticación de bases de datos
SQL Database admite dos tipos de autenticación: la de SQL y la de Azure Active Directory.

La **autenticación de SQL** se recomienda en los casos siguientes:

-   Permite a SQL Azure admitir entornos con sistemas operativos mixtos, donde todos los usuarios no son autenticados por un dominio de Windows.
-   Permite a SQL Azure admitir aplicaciones anteriores y otras proporcionadas por terceros que requieren la autenticación de SQL Server.
-   Permite a los usuarios conectarse desde dominios desconocidos o que no son de confianza. Por ejemplo, una aplicación en la que los clientes establecidos se conectan con los inicios de sesión de SQL Server asignados para recibir el estado de sus pedidos.
-   Permite que SQL Azure admita las aplicaciones basadas en web donde los usuarios crean sus propias identidades.
-   Permite a los programadores de software distribuir sus aplicaciones mediante el uso de una compleja jerarquía de permisos basada en inicios de sesión de SQL Server conocidos y preestablecidos.

> [!Note]
> Sin embargo, la autenticación de SQL Server no puede usar el protocolo de seguridad Kerberos.

Si usa la **autenticación de SQL**, debe:

-   Administrar las credenciales seguras usted mismo.
-   Proteger las credenciales en la cadena de conexión.
-   Proteger (potencialmente) las credenciales transmitidas a través de la red desde el servidor web a la base de datos. Para más información, consulte [Procedimiento para conectarse a SQL Server mediante la autenticación de SQL en ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx).

La **autenticación de Azure Active Directory** es un mecanismo de conexión a Microsoft Azure SQL Database y [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure AD puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración de identificadores central ofrece una ubicación única para administrar usuarios de base de datos y simplifica la administración de permisos. Entre las ventajas se incluyen las siguientes:

-   Ofrece una alternativa a la autenticación de SQL Server.
-   Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
-   Permite la rotación de contraseñas en un solo lugar.
-   Los clientes pueden administrar los permisos de la base de datos con grupos externos (AAD).
-   Puede eliminar el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
-   La autenticación de Azure AD utiliza usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
-   Azure AD admite la autenticación basada en token para las aplicaciones que se conectan a SQL Database.
-   La autenticación de Azure AD es compatible con ADFS (federación de dominio) o la autenticación nativa de usuario y contraseña para una instancia de Azure Active Directory local sin sincronización de dominios.
-   Azure AD admite conexiones de SQL Server Management Studio que usan la autenticación universal de Active Directory, lo cual incluye Multi-Factor Authentication (MFA). MFA incluye una sólida autenticación con una gama de sencillas opciones de comprobación: llamada de teléfono, mensaje de texto, tarjetas inteligentes con PIN o notificación de aplicación móvil. Para obtener más información, consulte [Compatibilidad de SSMS con Azure AD MFA con SQL Database y SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

En los pasos de configuración se incluyen los siguientes procedimientos para configurar y usar la autenticación de Azure Active Directory.

-   Cree y rellene una instancia de Azure AD.
-   Opcional: asocie o cambie la instancia de Active Directory que esté asociada a la suscripción de Azure.
-   Cree un administrador de Azure Active Directory para Azure SQL Server o para [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure los equipos cliente.
-   Cree usuarios de bases de datos independientes en la base de datos y asignados a identidades de Azure AD.
-   Conéctese a la base de datos mediante identidades de Azure AD.

Puede encontrar más detalles [aquí](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Protección de los datos con cifrado

El [Cifrado de datos transparente (TDE) de Azure SQL Database](https://msdn.microsoft.com/library/dn948096.aspx) facilita la protección contra la amenaza de actividades malintencionadas, ya que la base de datos se cifra y descifra en tiempo real, se realizan copias de seguridad asociadas y archivos de registro de transacciones en reposo sin necesitar cambios en la aplicación. TDE cifra el almacenamiento de una base de datos completa mediante el uso de una clave simétrica denominada clave de cifrado de base de datos.

Incluso cuando se cifra el almacenamiento completo, es muy importante cifrar también la base de datos. Se trata de una implementación del enfoque defensivo en profundidad para la protección de los datos. Si usa Azure SQL Database y quiere proteger datos confidenciales como números de tarjetas de crédito o del seguro social, puede cifrar las bases de datos con el cifrado AES de 256 bits validado por FIPS 140-2, que cumple los requisitos de muchas normas del sector (por ejemplo, HIPAA, PCI).

Es importante comprender que los archivos relacionados con la [extensión del grupo de búferes (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) (BPE) no se cifran cuando se cifra una base de datos mediante TDE. Debe usar herramientas de cifrado de nivel de sistema de archivos como [BitLocker](https://technet.microsoft.com/library/cc732774) o el [Sistema de cifrado de archivos (EFS)](https://technet.microsoft.com/library/cc700811.aspx) para los archivos relacionados con la BPE.

Ya que un usuario autorizado, como un administrador de seguridad o de base de datos, puede acceder a los datos incluso si la base de datos está cifrada con TDE, también se deben seguir estas recomendaciones:

-   Habilite la autenticación de SQL en el nivel de base de datos.
-   Use la autenticación de Azure AD mediante [roles RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Los usuarios y las aplicaciones deberían usar cuentas independientes para la autenticación. De este modo, puede limitar los permisos que se conceden a usuarios y aplicaciones, y reducir los riesgos causados por la actividad malintencionada.
-   Implemente la seguridad de nivel de base de datos con roles fijos de base de datos (como db_datareader o db_datawriter). También puede crear roles personalizados para su aplicación con el fin de conceder permisos explícitos a determinados objetos de la base de datos.

Si desea conocer otras formas de cifrar datos, considere:

-   [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
-   Usar el cifrado con [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx), que permite a los clientes cifrar información confidencial dentro de las aplicaciones cliente y no revelar las claves de cifrado al motor de base de datos (SQL Database o SQL Server). En consecuencia, Always Encrypted proporciona una separación entre quienes poseen los datos (y pueden verlos) y quienes administran los datos (pero no deben tener acceso a los mismos).
-   Usar la seguridad de nivel de fila, que permite a los clientes controlar el acceso a las filas de una tabla de base de datos en función de las características del usuario que ejecuta una consulta (por ejemplo, la pertenencia a un grupo o el contexto de ejecución). Para más información, consulte [Seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Protección de los datos en tránsito
Proteger los datos en tránsito debe ser una parte esencial de su estrategia de protección de datos. Puesto que los datos se desplazan entre muchas ubicaciones, la recomendación general es utilizar siempre los protocolos SSL/TLS para intercambiar datos entre diferentes ubicaciones. En algunas circunstancias, podría aislar todo el canal de comunicación entre su infraestructura local y la nube mediante una red privada virtual (VPN).

Para los datos que se desplazan entre la infraestructura local y Azure, debe plantearse usar medidas de seguridad apropiadas, como HTTPS o VPN.

Para las organizaciones que necesitan proteger el acceso a Azure desde varias estaciones de trabajo locales, use una [VPN de sitio a sitio de Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Para las organizaciones que necesitan proteger el acceso desde varias estaciones de trabajo individuales situadas en ubicaciones bien locales o bien externas a Azure, considere el uso de una [VPN de sitio a sitio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Los conjuntos de datos más grandes se pueden mover por medio de un vínculo WAN dedicado de alta velocidad como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si decide usar ExpressRoute, también puede cifrar los datos en el nivel de aplicación mediante [SSL/TLS](https://support.microsoft.com/kb/257591) u otros protocolos para una mayor protección.

Si interactúa con Azure Storage a través de Azure Portal, todas las transacciones se realizan a través de HTTPS. También se puede usar la [API de REST de almacenamiento](https://msdn.microsoft.com/library/azure/dd179355.aspx) a través de HTTPS para interactuar con [Azure Storage](https://azure.microsoft.com/services/storage/) y [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Las organizaciones que no protegen los datos en tránsito son más susceptibles a los [ataques del tipo "Man in the middle"](https://technet.microsoft.com/library/gg195821.aspx), a la [interceptación](https://technet.microsoft.com/library/gg195641.aspx) y al secuestro de sesión. Estos ataques pueden ser el primer paso para obtener acceso a datos confidenciales.

Para más información sobre la opción de VPN de Azure, lea el artículo [Planeamiento y diseño de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Habilitación de la auditoría de bases de datos
La auditoría de una instancia del motor de base de datos de SQL Server o una base de datos individual implica el seguimiento y el registro de los eventos que se producen en el motor de base de datos. La auditoría de SQL Server le permite crear auditorías de servidor, que pueden contener especificaciones de auditoría de servidor para los eventos del nivel de servidor así como especificaciones de auditoría de base de datos para los eventos del nivel de base de datos. Los eventos auditados se pueden escribir en los registros de eventos o en los archivos de auditoría.

Hay varios niveles de auditoría de SQL Server, según los requisitos legales o de estándares para la instalación. SQL Server Audit proporciona las herramientas y los procesos que debe tener para habilitar, almacenar y ver auditorías en varios objetos de servidor y base de datos.

La [auditoría de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) realiza un seguimiento de los eventos de bases de datos y los registra en un registro de auditoría de la cuenta de Azure Storage.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

La auditoría posibilita y facilita la observancia de estándares reguladores pero no garantiza el cumplimiento.

Para más información acerca de la auditoría de base de datos y de cómo habilitarla, lea el artículo [Habilitación de la auditoría y la detección de amenazas en servidores SQL Server en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Habilitación de la detección de amenazas para las bases de datos
La detección de amenazas de SQL le permite detectar posibles amenazas y responder a ellas si fuera necesario, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan. Se recibirá una alerta sobre las actividades sospechosas en las bases de datos, posibles vulnerabilidades y ataques de inyección de SQL, así como en patrones anómalos de acceso a bases de datos. Las alertas de Detección de amenazas de SQL proporcionan detalles de actividad sospechosa y recomiendan acciones sobre cómo investigar y mitigar la amenaza.

Por ejemplo, la inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

Para aprender a configurar la detección de amenazas para la base de datos en Azure Portal, vea [Detección de amenazas de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Además, la detección de amenazas de SQL integra las alertas con [Azure Security Center](https://azure.microsoft.com/services/security-center/). Le invitamos a probarlo gratis durante 60 días.

Para más información acerca de la detección de amenazas para las bases de datos y de cómo habilitarla, lea el artículo [Habilitación de la auditoría y la detección de amenazas en servidores SQL Server en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Conclusión
Azure Database es una sólida plataforma de base de datos, con una amplia gama de características de seguridad que satisfacen muchos de los requisitos de cumplimiento tanto normativos como organizativos. Puede ayudar a proteger los datos controlando el acceso físico a los mismos y con diversas opciones de seguridad en el archivo (nivel de fila, de columna o de archivo) con el cifrado de datos transparente, el cifrado de nivel de celda o la seguridad de nivel de fila. Always Encrypted también permite operaciones en los datos cifrados, simplificando el proceso de actualizaciones de las aplicaciones. A su vez, el acceso a los registros auditoría de la actividad de SQL Database le proporciona la información que necesita, lo que le permite saber cómo y cuándo se tiene acceso a datos.

## <a name="next-steps"></a>Pasos siguientes
- Para más información acerca de las reglas de firewall, consulte [Reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Para obtener información acerca de los usuarios e inicios de sesión, consulte [Administración de inicios de sesión](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Para ver un tutorial, consulte [Protección de Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
