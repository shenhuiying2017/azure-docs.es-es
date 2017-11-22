---
title: 'Seguridad de bases de datos: Azure Cosmos DB | Microsoft Docs'
description: "Obtenga información sobre cómo Azure Cosmos DB proporciona protección de bases de datos y seguridad para los datos."
keywords: "seguridad de bases de datos NoSQL, información de seguridad, seguridad de datos, el cifrado de bases de datos, protección de bases de datos, directivas de seguridad, pruebas de seguridad"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2f0f6578b14b2fdd3807303eb94df077df92ba77
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-database-security"></a>Seguridad de base de datos de Azure Cosmos DB

En este artículo se describen los procedimientos recomendados de seguridad de bases de datos y las principales características que ofrece Azure Cosmos DB para ayudarle a evitar y detectar infracciones en bases de datos, así como a responder a estos incidentes.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Novedades en la seguridad de Azure Cosmos DB

El cifrado en reposo ahora está disponible para los documentos y copias de seguridad almacenados en Azure Cosmos DB en todas las regiones de Azure. El cifrado en reposo se aplica automáticamente a los clientes nuevos y existentes de estas regiones. No es necesario configurar nada; obtenga la misma latencia, rendimiento, disponibilidad y funcionalidad excelentes que antes con la ventaja de saber que los datos son seguros y de protegerlos con el cifrado en reposo.

## <a name="how-do-i-secure-my-database"></a>¿Cómo puedo proteger mi base de datos? 

La seguridad de los datos constituye una responsabilidad compartida entre el cliente y el proveedor de base de datos. Dependiendo del proveedor de base de datos que elija, puede variar el nivel de responsabilidad que asumir. Si elige una solución local, debe proporcionar todos los elementos: desde la protección de extremo a extremo hasta la seguridad física del hardware, lo que no es tarea fácil. Si elige un proveedor de bases de datos en la nube PaaS como Azure Cosmos DB, se reduce considerablemente el área de responsabilidad. En la siguiente imagen, que se tomó prestada de las notas del producto [Shared Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility) (Responsabilidades compartidas de la informática en la nube), se muestra cómo se reduce la responsabilidad con un proveedor de PaaS como Azure Cosmos DB.

![Responsabilidades del cliente y del proveedor de bases de datos](./media/database-security/nosql-database-security-responsibilities.png)

En el diagrama anterior se muestran los componentes de seguridad en la nube de alto nivel, pero, en una solución de base de datos, ¿por qué debe preocuparse exactamente? ¿Y cómo puede comparar soluciones? 

Recomendamos la siguiente lista de comprobación de requisitos para comparar sistemas de bases de datos:

- Seguridad de red y la configuración de firewall
- Autenticación de usuarios y controles de usuario muy específicos
- Capacidad de replicar datos globalmente en caso de errores regionales
- Capacidad de realizar conmutaciones por error de un centro de datos a otro
- Replicación de datos local dentro de un centro de datos
- Copias de seguridad de datos automáticas
- Restauración de los datos eliminados de las copias de seguridad
- Protección y aislamiento de datos confidenciales
- Supervisión de ataques
- Respuesta a alertas
- Capacidad de aplicar límites geografos a datos para cumplir las restricciones de gobernanza de datos
- Protección física de los servidores en centros de datos protegidos

Aunque puede parecer obvio, algunas [infracciones recientes de bases de datos de gran envergadura](http://thehackernews.com/2017/01/mongodb-database-security.html) nos recuerdan lo importantes que son los siguientes requisitos (que, además, son muy fáciles de cumplir):
- Servidores revisados que se mantengan actualizados
- Protocolo HTTPS de forma predeterminada/cifrado SSL
- Cuentas administrativas con contraseñas seguras

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>¿Cómo puede proteger Azure Cosmos DB mi base de datos?

Echemos un vistazo volver a la lista anterior, ¿cuántos de esos requisitos de seguridad proporciona Azure Cosmos DB? Todos.

Analicemos cada uno de ellas en detalle.

|Requisito de seguridad|Enfoque de seguridad de Azure Cosmos DB|
|---|---|---|
|Seguridad de las redes|Usar un firewall IP es la primera línea de defensa para proteger una base de datos. Azure Cosmos DB admite controles de acceso basado en IP orientado a directivas para agregar compatibilidad con el firewall de entrada. Los controles de acceso basado en IP son similares a las reglas de firewall que emplean los sistemas de base de datos tradicionales, pero se han ampliado para que una cuenta de bases de datos de Azure Cosmos DB sea accesible desde un conjunto aprobado de máquinas o servicios en la nube. <br><br>Azure Cosmos DB permite habilitar una dirección IP específica (168.61.48.0), un intervalo IP (168.61.48.0/8) y combinaciones de direcciones IP e intervalos. <br><br>Todas las solicitudes procedentes de máquinas que no pertenezcan a esta lista permitida las bloqueará Azure Cosmos DB. Las solicitudes de máquinas aprobadas y servicios en la nube deben completar el proceso de autenticación para poder controlar el acceso a los recursos.<br><br>Más información en [Compatibilidad con un firewall de Azure Cosmos DB](firewall-support.md).|
|Autorización|Azure Cosmos DB utiliza el código de autenticación de mensajes basado en hash (HMAC) para la autorización. <br><br>Cada solicitud se cifra mediante la clave de cuenta secreta, y el hash codificado base64 subsiguiente se envía con cada llamada a Azure Cosmos DB. Para validar la solicitud, el servicio Azure Cosmos DB utiliza la clave secreta y las propiedades correctas para generar un valor hash y, luego, compara el valor con el que muestra la solicitud. Si los dos coinciden, significa que la operación está autorizada correctamente y se procesa la solicitud, en caso contrario, se produce un error de autorización y se rechaza la solicitud.<br><br>Puede usar una [clave maestra](secure-access-to-data.md#master-keys) o un [token de recurso](secure-access-to-data.md#resource-tokens) permitir acceso muy específico a un recurso, por ejemplo, un documento.<br><br>Obtenga más información en [Protección del acceso a los recursos de Cosmos DB](secure-access-to-data.md).|
|Usuarios y permisos|Mediante la [clave maestra](#master-key) de la cuenta, puede crear recursos de usuario y de permiso por base de datos. Un [token de recurso](#resource-token) está asociado con un permiso en una base de datos y determina si el usuario tiene acceso (lectura y escritura, de solo lectura, o ninguno) aun recurso de aplicación en la base de datos. Los recursos de aplicación incluyen colecciones, documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF. El token de recurso se usa luego durante la autenticación para proporcionar o denegar el acceso al recurso.<br><br>Obtenga más información en [Protección del acceso a los recursos de Cosmos DB](secure-access-to-data.md).|
|Integración de Active Directory (RBAC)| También puede proporcionar acceso a la cuenta de base de datos con el control de acceso (IAM) en Azure Portal, como se muestra en la captura de pantalla que sigue a esta tabla. IAM proporciona una funcionalidad de control de acceso basado en roles y se integra con Active Directory. Puede usar roles integrados o personalizado para usuarios y grupos, tal y como se muestra en la siguiente imagen.|
|Replicación global|Azure Cosmos DB ofrece una distribución global llave en mano, lo que permite replicar los datos en cualquiera de los centros de datos de todo el mundo de Azure con solo hacer clic en un botón. Gracias a la replicación global, podrá realizar un escalado a nivel internacional y proporcionar un acceso de latencia baja a datos de todo el mundo.<br><br>En el contexto de seguridad, la replicación global garantiza la protección de los datos frente a errores regionales.<br><br>Obtenga más información en [Distribución de datos global con DocumentDB](distribute-data-globally.md).|
|Conmutaciones por error regionales|Si se han replicado los datos en más de un centro de datos, Azure Cosmos DB sustituirá automáticamente las operaciones en caso de que se desconecte un centro de datos regional. Puede crear una lista de prioridades de regiones de conmutación por error con las regiones en las que se replicarán los datos. <br><br>Obtenga más información en [Conmutaciones por error regionales de Azure Cosmos DB](regional-failover.md).|
|Replicación local|Incluso dentro de un solo centro de datos, Azure Cosmos DB replica automáticamente los datos para lograr una alta disponibilidad, lo que posibilita diversos [niveles de coherencia](consistency-levels.md). Esto garantiza un [Acuerdo de Nivel de Servicio con disponibilidad](https://azure.microsoft.com/support/legal/sla/cosmos-db) del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada y, disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones.|
|Copias de seguridad en línea automatizadas|Las copias de seguridad de las bases de datos de Azure Cosmos DB se realizan de manera periódica y se guardan en un almacén con redundancia geográfica. <br><br>Obtenga más información en [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](online-backup-and-restore.md).|
|Restauración de los datos eliminados|Las copias de seguridad automatizadas en línea se pueden utilizar para recuperar los datos que puede que haya eliminado accidentalmente hasta unos 30 días después del suceso. <br><br>Obtenga más información en [Copias de seguridad y restauración automáticas en línea con Azure Cosmos DB](online-backup-and-restore.md).|
|Protección y aislamiento de datos confidenciales|Todos los datos de las regiones incluidos en [Novedades](#whats-new) ahora se cifran en reposo.<br><br>La DCP y otros datos confidenciales pueden aislarse en colecciones específicas. Además, se puede limitar el acceso de escritura-lectura o de solo lectura a usuarios concretos.|
|Supervisión de los ataques|Use los [registros de auditoría y los registros de actividad](logging.md) para supervisar la actividad normal y la anómala de su cuenta. Puede ver qué operaciones se realizaron en los recursos, quién inició la operación, cuando se produjo, el estado y mucha más información, como se muestra en la captura de pantalla que sigue a esta tabla.|
|Respuesta a ataques|Una vez que se ha puesto en contacto con el equipo de asistencia técnica de Azure para informar de un posible ataque, se inicia un proceso de respuesta a incidentes de 5 pasos. El objetivo de dicho proceso es restaurar las operaciones y la seguridad de los servicios a su estado normal lo antes posible después de que se detecte un problema y se inicie una investigación.<br><br>Obtenga más información en [Microsoft Azure Security Response in the Cloud](https://aka.ms/securityresponsepaper) (Respuesta de seguridad de Microsoft Azure en la nube).|
|Geovalla|Azure Cosmos DB garantiza el cumplimiento y la gobernanza de datos para regiones soberana (por ejemplo, Alemania y US Gov).|
|Instalaciones protegidas|Los datos de Azure Cosmos DB se almacenan en los SSD de los centros de datos protegidos de Azure.<br><br>Obtenga información sobre los [centros de datos globales de Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters).|
|HTTPS, SSL y cifrado TLS|Todas las interacciones de Azure Cosmos DB de cliente a servicio exigen los protocolos SSL o TLS 1.2. Además, para todas las replicaciones dentro de centro de datos y entre ellos se exigen estos protocolos.|
|Cifrado en reposo|Todos los datos almacenados en Azure Cosmos DB se cifran en reposo. Más información en [Cifrado de Azure Cosmos DB en reposo](.\database-encryption-at-rest.md)|
|Servidores revisados|Como una base de datos administrada, Azure Cosmos DB elimina la necesidad de administrar y aplicar revisiones a servidores, que se hace automáticamente.|
|Cuentas administrativas con contraseñas seguras|Es difícil creer que tengamos que hacer mención a este requisito, pero a diferencia de algunos de nuestros competidores, no se puede tener una cuenta administrativa sin contraseña en Azure Cosmos DB.<br><br> La seguridad a través de SSL y autenticación basada en secreto HMAC está incorporada de forma predeterminada.|
|Certificaciones de protección de datos y seguridad|Azure Cosmos DB tiene las certificaciones [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [las cláusulas modelo de la Comisión Europea (EUMC)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) e [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA). Hay pendientes más certificaciones.|

En la captura de pantalla siguiente se muestra la integración de Active Directory (RBAC) con control de acceso (IAM) en Azure Portal: ![control de acceso (IAM) en Azure Portal: demostración de la seguridad de base de datos](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

En la captura de pantalla siguiente se muestra cómo puede usar los registros de actividad y los registro de auditoría para supervisar su cuenta: ![registros de actividad de Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las claves maestras y los tokens de recursos, consulte [Protección del acceso a los datos de Azure Cosmos DB](secure-access-to-data.md).

Para saber más sobre el registro de auditoría, vea [Azure Cosmos DB diagnostic logging](logging.md) (Registro de diagnóstico de Microsoft Azure Cosmos DB).

Si quiere saber más sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/).
