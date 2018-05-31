---
title: Detección de amenazas de Azure SQL Database| Microsoft Docs
description: Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364155"
---
# <a name="azure-sql-database-threat-detection"></a>Detección de amenazas de Azure SQL Database

La detección de amenazas de Azure SQL Database detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de acceder a las bases de datos o de vulnerar su seguridad.

La detección de amenazas forma parte de la oferta de [protección contra amenazas avanzada de SQL](sql-advanced-threat-protection.md) (ATP), que es un paquete unificado para capacidades avanzadas de seguridad de SQL. Puede acceder a la característica de detección de amenazas y administrarla a través del portal central de SQL ATP.

## <a name="what-is-threat-detection"></a>¿Qué es la detección de amenazas?

Detección de amenazas de SQL ofrece un nuevo nivel de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas a medida que se producen, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan. Los usuarios reciben una alerta sobre actividades sospechosas en las bases de datos, posibles vulnerabilidades y ataques por inyección de código SQL, así como sobre los patrones de acceso y consultas a las bases de datos anómalos. La detección de amenazas de SQL integra las alertas con [Azure Security Center](https://azure.microsoft.com/services/security-center/), que incluye detalles de actividades sospechosas y acciones recomendadas sobre cómo investigar y mitigar la amenaza. La detección de amenazas de SQL facilita la solución de posibles amenazas a la base de datos sin necesidad de ser un experto en seguridad ni tener que administrar sistemas de supervisión de seguridad avanzada. 

Para obtener una experiencia de investigación, se recomienda habilitar la [auditoría de SQL Database](sql-database-auditing.md), que escribe los eventos de las bases de datos en un registro de auditoría en su cuenta de Azure Storage.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configuración de la detección de amenazas en Azure Portal
1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la página de configuración del servidor de Azure SQL Database que quiere proteger. En la configuración de seguridad, seleccione **Protección contra amenazas avanzada**.
3. En la página de configuración **Protección contra amenazas avanzada**:

   - Habilite Protección contra amenazas avanzada en el servidor.
   - En **Configuración de detección de amenazas**, en el cuadro de texto **Send alerts to** (Enviar alertas a), proporcione la lista de correos electrónicos para recibir alertas de seguridad cuando se detecten actividades anómalas en la base de datos.
  
   ![Configurar la detección de amenazas](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Configuración de la detección de amenazas mediante PowerShell

Para ver un script de ejemplo, consulte [Configuración de la auditoría y detección de amenazas mediante PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Exploración de las actividades anómalas en la base de datos cuando se detecta un evento sospechoso

Cuando se detecten actividades anómalas en las bases de datos, recibirá una notificación por correo electrónico. El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor, el nombre de la aplicación y la hora del evento. Además, el correo electrónico proporciona información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos.

![Informes de actividades anómalas](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. Haga clic en el vínculo **View recent SQL alerts** (Ver las alertas recientes de SQL) del correo electrónico para iniciar Azure Portal y mostrar la página de alertas de Azure Security Center, que proporciona información general sobre amenazas activas detectadas en la base de datos de SQL.

   ![Amenazas de actividades](./media/sql-database-threat-detection/active_threats.png)

2. Haga clic en una alerta específica para obtener detalles y acciones adicionales para investigar esta amenaza y solucionar amenazas futuras.

   Por ejemplo, la inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos. Para las alertas de inyección de código SQL, los detalles de la alerta incluyen la instrucción SQL vulnerable que se ha aprovechado.

   ![Alerta específica](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Exploración de alertas de detección de amenazas para la base de datos en Azure Portal

Detección de amenazas de SQL Database integra la alerta con [Azure Security Center](https://azure.microsoft.com/services/security-center/). Un icono de detección de amenazas de SQL dinámico dentro de las hojas de SQL ATP y de la base de datos en Azure Portal realiza un seguimiento del estado de las amenazas activas.

Haga clic en la **alerta de detección de amenazas** para iniciar la página de alertas de Azure Security Center y obtener información general de las amenazas de SQL activas detectadas en la base de datos.

   ![Alerta de detección de amenazas](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![Alerta 2 de detección de amenazas](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Alertas de detección de amenazas de Azure SQL Database 
La capacidad de detección de amenazas para Azure SQL Database detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de acceder a bases de datos, o de vulnerar su seguridad, y puede desencadenar las siguientes alertas:
- **Vulnerabilidad ante ataques por inyección de código SQL**: esta alerta se desencadena cuando una aplicación genera una instrucción SQL errónea en la base de datos. Esto puede indicar una posible vulnerabilidad ante ataques por inyección de código SQL. Hay dos posibles razones para la generación de una instrucción errónea:
   - Existe un defecto en el código de la aplicación que crea la instrucción SQL errónea
   - El código de la aplicación o los procedimientos almacenados no corrigen los datos que proporciona el usuario al construir la instrucción SQL errónea, lo que se puede aprovechar para ataques por inyección de código SQL
- **Potencial inyección de código SQL**: esta alerta se desencadena cuando se produce una vulnerabilidad de seguridad activa contra una vulnerabilidad de la aplicación identificada ante inyección de código SQL. Esto significa que el atacante intentan inyectar instrucciones SQL malintencionadas mediante el código de la aplicación vulnerable o procedimientos almacenados.
- **Acceso desde una ubicación inusual**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a SQL Server, donde alguien ha iniciado sesión en el servidor SQL Server desde una ubicación geográfica inusual. En algunos casos, la alerta detecta una acción legítima (una nueva aplicación o el mantenimiento de un desarrollador). En otros casos, la alerta detecta una acción malintencionada (por ejemplo, un antiguo empleado o un atacante externo).
- **Acceso desde un centro de datos de Azure inusual**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a SQL Server, donde alguien ha iniciado sesión en el servidor SQL Server desde un centro de datos de Azure inusual que se vio en el servidor recientemente. En algunos casos, la alerta detecta una acción legítima (una aplicación nueva en Azure, Power BI o Azure SQL Query Editor). En otros casos, la alerta detecta una acción malintencionada procedente de un recurso o servicio de Azure (por ejemplo, un antiguo empleado o un atacante externo).
- **Acceso desde una entidad de seguridad desconocida**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a SQL Server, donde alguien ha iniciado sesión en el servidor SQL Server mediante una entidad de seguridad inusual (usuario de SQL). En algunos casos, la alerta detecta una acción legítima (una nueva aplicación o el mantenimiento de un desarrollador). En otros casos, la alerta detecta una acción malintencionada (por ejemplo, un antiguo empleado o un atacante externo).
- **Acceso desde una aplicación potencialmente dañina**: esta alerta se desencadena cuando una aplicación potencialmente dañina se utiliza para acceder a la base de datos. En algunos casos, la alerta detecta la realización de pruebas de seguridad. En otros casos, la alerta detecta un ataque que se realiza con herramientas de ataque comunes.
- **Credenciales de SQL por fuerza bruta**: esta alerta se desencadena cuando hay un número anormalmente elevado de inicios de sesión infructuosos con distintas credenciales. En algunos casos, la alerta detecta la realización de pruebas de seguridad. En otros casos, la alerta detecta ataques por fuerza bruta.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre la [protección contra amenazas avanzada de SQL](sql-advanced-threat-protection.md). 
* Más información acerca de la [auditoría de Azure SQL Database](sql-database-auditing.md)
* Más información acerca de [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para más información sobre los precios, consulte la [página Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
