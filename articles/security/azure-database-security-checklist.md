---
title: "Lista de comprobación de la seguridad de Azure Database | Microsoft Docs"
description: "En este artículo se proporciona un conjunto de comprobaciones de la seguridad de Azure Database."
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
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: 5047635555a6b4592c0714677c2b942e50bad344
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-database-security-checklist"></a>Lista de comprobación de la seguridad de Azure Database

Para ayudar a mejorar la seguridad, Azure Database incluye una serie de controles de seguridad integrados que puede usar para limitar y controlar el acceso.

Entre ellos se incluyen los siguientes:

-   Un firewall que permite crear [reglas de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) que limitan la conectividad en función de su dirección IP
-   Firewall de nivel de servidor accesible desde Azure Portal
-   Reglas de firewall de nivel de base de datos accesibles desde SSMS
-   Conectividad segura a una base de datos mediante cadenas de conexión seguras
-   Uso de la administración del acceso
-   Cifrado de datos
-   Auditoría de SQL Database
-   Detección de amenazas de SQL Database

## <a name="introduction"></a>Introducción
La informática en la nube requiere nuevos paradigmas de seguridad que son poco conocidos para muchos usuarios de aplicaciones, administradores de bases de datos y programadores. Como resultado, algunas organizaciones dudan de si implementar una infraestructura en la nube para la administración de datos debido a los riesgos de seguridad percibidos. Sin embargo, se puede mitigar gran parte de este problema mediante una mejor comprensión de las características de seguridad integradas en Microsoft Azure y Microsoft Azure SQL Database.

## <a name="checklist"></a>Lista de comprobación
Le recomendamos que lea el artículo [Prácticas recomendadas de seguridad de Azure Database](https://docs.microsoft.com/en-us/azure/security/azure-database-security-best-practices) antes de revisar esta lista. Una vez que conozca las prácticas recomendadas, podrá obtener el máximo partido de esta lista de comprobación. A continuación, puede usarla para asegurarse de que ha abordado las cuestiones importantes de la seguridad para las bases de datos de Azure.


|Categoría de la lista de comprobación| Descripción|
| ------------ | -------- |
|**Protección de datos**||
| <br> Cifrado en movimiento o tránsito| <ul><li>[Seguridad de la capa de transporte](https://docs.microsoft.com/en-us/windows-server/security/tls/transport-layer-security-protocol), para el cifrado de datos cuando los datos se pasan a las redes.</li><li>Las bases de datos requieren una comunicación segura desde los clientes basados en el protocolo [TDS (Tabular Data Stream)](https://msdn.microsoft.com/en-in/library/dd357628.aspx) a través de TLS (Seguridad de la capa de transporte).</li></ul> |
|<br>Cifrado en reposo| <ul><li>[Cifrado de datos transparente](http://go.microsoft.com/fwlink/?LinkId=526242), cuando los datos inactivos se almacenan físicamente en cualquier formato digital.</li></ul>|
|**Control de acceso**||  
|<br> Acceso a la base de datos | <ul><li>[Autenticación](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access) (Autenticación de Azure Active Directory). La autenticación de AD usa las identidades administradas por Azure Active Directory.</li><li>[Autorización](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-control-access). Conceda a los usuarios los privilegios mínimos necesarios.</li></ul> |
|<br>Acceso a las aplicaciones| <ul><li>[Seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131). Se usa la directiva de seguridad, al mismo tiempo que se restringe el acceso de nivel de fila según el contexto de ejecución, rol o identidad de un usuario).</li><li>[Enmascaramiento dinámico de datos](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started). El uso de directivas y permisos limita la exposición de información confidencial al enmascararla para los usuarios sin privilegios</li></ul>|
|**Supervisión proactiva**||  
| <br>Seguimiento y detección| <ul><li>[La auditoría](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) realiza un seguimiento de los eventos de bases de datos y los escribe en un registro de auditoría y de actividad en su [cuenta de Azure Storage](https://docs.microsoft.com/en-us/azure/storage/storage-create-storage-account).</li><li>Seguimiento del estado de Azure Database mediante [registros de actividad de Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).</li><li>[Detección de amenazas](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection) detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad. </li></ul> |
|<br>Azure Security Center| <ul><li>[Supervisión de datos](https://docs.microsoft.com/en-us/azure/security-center/security-center-enable-auditing-on-sql-databases) Use Azure Security Center como solución de supervisión de seguridad centralizada para SQL y otros servicios de Azure.</li></ul>|     

## <a name="conclusion"></a>Conclusión
Azure Database es una sólida plataforma de base de datos, con una amplia gama de características de seguridad que satisfacen muchos de los requisitos de cumplimiento tanto normativos como organizativos. Puede proteger fácilmente los datos controlando el acceso físico a los mismos y con diversas opciones de seguridad en el archivo (nivel de fila, de columna o de archivo) con el cifrado de datos transparente, el cifrado de nivel de celda o la seguridad de nivel de fila. Always Encrypted también permite operaciones en los datos cifrados, simplificando el proceso de actualizaciones de las aplicaciones. A su vez, el acceso a los registros auditoría de la actividad de SQL Database le proporciona la información que necesita, lo que le permite saber cómo y cuándo se tiene acceso a datos.

## <a name="next-steps"></a>Pasos siguientes
Con unos pocos pasos sencillos puede mejorar la protección de su base de datos contra usuarios malintencionados o acceso no autorizado. En este tutorial, aprenderá a:

- Configurar [reglas de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) para un servidor o una base de datos.
- Proteger los datos con [cifrado](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/sql-server-encryption).
- Habilitar la [auditoría de SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing).

