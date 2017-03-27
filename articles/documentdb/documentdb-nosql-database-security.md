---
title: 'Seguridad de bases de datos NoSQL: Azure DocumentDB | Microsoft Docs'
description: "Obtenga información sobre cómo Azure DocumentDB proporciona protección de bases de datos y seguridad de datos NoSQL."
keywords: "seguridad de bases de datos NoSQL, información de seguridad, seguridad de datos, el cifrado de bases de datos, protección de bases de datos, directivas de seguridad, pruebas de seguridad"
services: documentdb
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ca5f5696e9478d5087a09fff16742f65ea3e5589
ms.lasthandoff: 03/10/2017


---

# <a name="documentdb-nosql-database-security"></a>Seguridad de bases de datos de DocumentDB NoSQL

 En este artículo se describen los procedimientos recomendados de seguridad de bases de datos SQL y las principales características que ofrece Azure DocumentDB para ayudarlo a evitar y detectar infracciones en bases de datos, así como a responder a estos incidentes.

## <a name="how-do-i-secure-my-nosql-database"></a>¿Cómo puedo proteger mi base de datos NoSQL? 

La seguridad de los datos constituye una responsabilidad compartida entre el cliente y el proveedor de base de datos. Dependiendo del proveedor de base de datos que elija, puede variar el nivel de responsabilidad que asumir. Si elige una solución local, debe proporcionar todos los elementos: desde la protección de extremo a extremo hasta la seguridad física del hardware, lo que no es tarea fácil. Si elige un proveedor de bases de datos en la nube PaaS como Azure DocumentDB, se reduce considerablemente el área de responsabilidad. En la siguiente imagen, que se tomó prestada de las notas del producto [Shared Responsibilities for Cloud Computing](https://aka.ms/sharedresponsibility) (Responsabilidades compartidas de la informática en la nube), se muestra cómo se reduce la responsabilidad con un proveedor de PaaS como Azure DocumentDB.

![Responsabilidades del cliente y del proveedor de bases de datos](./media/documentdb-nosql-database-security/nosql-database-security-responsibilities.png)

En el diagrama anterior se muestran los componentes de seguridad en la nube de alto nivel, pero, en una solución de base de datos NoSQL, ¿por qué debe preocuparse exactamente? ¿Y cómo puede comparar soluciones? 

Recomendamos la siguiente lista de comprobación de requisitos para comparar sistemas de bases de datos NoSQL:

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
- Capacidad de aplicar límites geográficos a datos para cumplir las restricciones de gobernanza de datos
- Protección física de los servidores en centros de datos protegidos

Aunque puede parecer obvio, algunas [infracciones recientes de bases de datos de gran envergadura](http://thehackernews.com/2017/01/mongodb-database-security.html) nos recuerdan lo importantes que son los siguientes requisitos (que, además, son muy fáciles de cumplir):
- Servidores revisados que se mantengan actualizados
- Protocolo HTTPS de forma predeterminada/cifrado SSL
- Cuentas administrativas con contraseñas seguras

## <a name="how-does-azure-documentdb-secure-my-database"></a>¿Cómo puede proteger DocumentDB Azure mi base de datos?

Echemos un vistazo volver a la lista anterior, ¿cuántos de esos requisitos de seguridad proporciona Azure DocumentDB? Todos.

Analicemos cada uno de ellas en detalle.

|Requisito de seguridad|Enfoque de seguridad de DocumentDB|
|---|---|---|
|Seguridad de las redes|Usar un firewall IP es la primera línea de defensa para proteger una base de datos NoSQL. DocumentDB admite controles de acceso basado en IP orientado a directivas para agregar compatibilidad con el firewall de entrada. Los controles de acceso basado en IP son similares a las reglas de firewall que emplean los sistemas de base de datos tradicionales, pero se han ampliado para que una cuenta de bases de datos DocumentDB sea accesible desde un conjunto aprobado de máquinas o servicios en la nube. <br><br>DocumentDB permite habilitar una dirección IP específica (168.61.48.0), un intervalo IP (168.61.48.0/8) y combinaciones de direcciones IP e intervalos. <br><br>Todas las solicitudes procedentes de máquinas que no pertenezcan a esta lista permitida las bloqueará DocumentDB. Las solicitudes de máquinas aprobadas y servicios en la nube deben completar el proceso de autenticación para poder controlar el acceso a los recursos.<br><br>Obtenga más información en [Compatibilidad con el firewall de DocumentDB](documentdb-firewall-support.md).|
|Autorización|DocumentDB utiliza el código de autenticación de mensajes basado en hash (HMAC) para la autorización. <br><br>Cada solicitud se cifra mediante la clave de cuenta secreta, y el hash codificado base64 subsiguiente se envía con cada llamada a DocumentDB. Para validar la solicitud, el servicio DocumentDB utiliza la clave secreta y las propiedades correctas para generar un valor hash y, luego, compara el valor con el que muestra la solicitud. Si los dos coinciden, significa que la operación está autorizada correctamente y se procesa la solicitud, en caso contrario, se produce un error de autorización y se rechaza la solicitud.<br><br>Puede usar una [clave maestra](documentdb-secure-access-to-data.md#master-keys) o un [token de recurso](documentdb-secure-access-to-data.md#resource-tokens) permitir acceso muy específico a un recurso, por ejemplo, un documento.<br><br>Obtenga más información en [Protección del acceso a los datos de DocumentDB](documentdb-secure-access-to-data.md).|
|Usuarios y permisos|Mediante la [clave maestra](#master-key) de la cuenta, puede crear recursos de usuario y de permiso por base de datos. Un [token de recurso](#resource-token) está asociado con un permiso en una base de datos y determina si el usuario tiene acceso (lectura y escritura, de solo lectura, o ninguno) aun recurso de aplicación en la base de datos. Los recursos de aplicación incluyen colecciones, documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF. El token de recurso se usa luego durante la autenticación para proporcionar o denegar el acceso al recurso.<br><br>Obtenga más información en [Protección del acceso a los datos de DocumentDB](documentdb-secure-access-to-data.md).|
|Integración de Active Directory (RBAC)| También puede proporcionar acceso a la cuenta de base de datos con el control de acceso (IAM) en Azure Portal. IAM proporciona una funcionalidad de control de acceso basado en roles y se integra con Active Directory. Puede usar roles integrados o personalizado para usuarios y grupos, tal y como se muestra en la siguiente imagen.<br><br>![Control de acceso (IAM) en Azure Portal: demostración de la seguridad de bases de datos NoSQL](./media/documentdb-nosql-database-security/nosql-database-security-identity-access-management-iam-rbac.png)|
|Replicación global|DocumentDB ofrece una distribución global llave en mano, lo que permite replicar los datos en cualquiera de los centros de datos de todo el mundo de Azure con solo hacer clic en un botón. Gracias a la replicación global, podrá realizar un escalado a nivel internacional y proporcionar un acceso de latencia baja a datos de todo el mundo.<br><br>En el contexto de seguridad, la replicación global garantiza la protección de los datos frente a errores regionales.<br><br>Obtenga más información en [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md).|
|Conmutaciones por error regionales|Si se han replicado los datos en más de un centro de datos, DocumentDB sustituirá automáticamente las operaciones en caso de que se desconecte un centro de datos regional. Puede crear una lista de prioridades de regiones de conmutación por error con las regiones en las que se replicarán los datos. <br><br>Obtenga más información en [Conmutaciones por error regionales de Azure DocumentDB](documentdb-regional-failovers.md).|
|Replicación local|Incluso dentro de un solo centro de datos, DocumentDB replica automáticamente los datos para lograr una alta disponibilidad, lo que posibilita diversos [niveles de coherencia](documentdb-consistency-levels.md). Esto asegura que haya un  [Acuerdo de Nivel de Servicio de disponibilidad del&99;,99 % del tiempo de actividad](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/), que incluye una garantía financiera (algo que no puede proporcionar ningún otro servicio de bases de datos NoSQL).|
|Copias de seguridad en línea automatizadas|Las copias de seguridad de las bases de datos de DocumentDB se realizan de manera periódica y se guardan en un almacén con redundancia geográfica. <br><br>Obtenga más información en [Copias de seguridad y restauración automáticas en línea con DocumentDB](documentdb-online-backup-and-restore.md).|
|Restauración de los datos eliminados|Las copias de seguridad automatizadas en línea se pueden utilizar para recuperar los datos que puede que haya eliminado accidentalmente hasta unos&30; días después del suceso. <br><br>Obtenga más información en [Copias de seguridad y restauración automáticas en línea con DocumentDB](documentdb-online-backup-and-restore.md).|
|Protección y aislamiento de datos confidenciales|La DCP y otros datos confidenciales pueden aislarse en colecciones específicas. Además, se puede limitar el acceso de escritura-lectura o de solo lectura a usuarios concretos.|
|Supervisión de los ataques|Mediante el uso de registros de actividad y de auditoría, puede supervisar su cuenta para detectar actividad normal y anómala. Puede ver qué operaciones se realizaron en los recursos, quién inició la operación, cuando se produjo, el estado y mucha más información.<br><br>![Registros de actividad de Azure DocumentDB](./media/documentdb-nosql-database-security/nosql-database-security-application-logging.png)|
|Respuesta a ataques|Una vez que se ha puesto en contacto con el equipo de asistencia técnica de Azure para informar de un posible ataque, se inicia un proceso de respuesta a incidentes de 5 pasos. El objetivo de dicho proceso es restaurar las operaciones y la seguridad de los servicios a su estado normal lo antes posible después de que se detecte un problema y se inicie una investigación.<br><br>Obtenga más información en [Microsoft Azure Security Response in the Cloud](https://aka.ms/securityresponsepaper) (Respuesta de seguridad de Microsoft Azure en la nube).|
|Geovalla|DocumentDB garantiza el cumplimiento y la gobernanza de datos para regiones soberana (por ejemplo, Alemania y US Gov).|
|Instalaciones protegidas|DocumentDB se almacena en los SSD de los centros de datos protegidos de Azure.<br><br>Obtenga información sobre los [centros de datos globales de Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters).|
|HTTPS, SSL y cifrado TLS|Todas las interacciones de DocumentDB de cliente a servicio exigen los protocolos SSL o TLS 1.2. Además, para todas las replicaciones dentro de centro de datos y entre ellos se exigen estos protocolos.|
|Servidores revisados|Como una base de datos NoSQL administrada, DocumentDB elimina la necesidad de administrar y aplicar revisiones a servidores, que se hace automáticamente.|
|Cuentas administrativas con contraseñas seguras|Es difícil creer que tengamos que hacer mención a este requisito, pero a diferencia de algunos de nuestros competidores, no se puede tener una cuenta administrativa sin contraseña en DocumentDB.<br><br> La seguridad a través de SSL y autenticación basada en secreto HMAC está incorporada de forma predeterminada.|
|Certificaciones de protección de datos y seguridad|DocumentDB tiene las certificaciones [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [las cláusulas modelo de la Comisión Europea (EUMC)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) e [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA). Hay pendientes más certificaciones.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las claves maestras y los tokens de recursos, consulte [Protección del acceso a los datos de DocumentDB](documentdb-secure-access-to-data.md).

Si quiere saber más sobre las certificaciones de Microsoft, visite el [Centro de confianza de Azure](https://azure.microsoft.com/en-us/support/trust-center/).
