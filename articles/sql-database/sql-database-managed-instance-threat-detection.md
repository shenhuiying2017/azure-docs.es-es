---
title: 'Detección de amenazas: Instancia administrada de Azure SQL Database| Microsoft Docs'
description: Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 2112a0a3997af478de6b8c80abcf7924a66302f0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Detección de amenazas de Instancia administrada de Azure SQL Database

Detección de amenazas de SQL detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de acceder a las bases de datos o de vulnerar su seguridad en una instancia de Instancia administrada de Azure SQL Database (versión preliminar).

## <a name="overview"></a>Información general

Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a Instancia administrada. Detección de amenazas está en versión preliminar para Instancia administrada.

Detección de amenazas proporciona una nueva capa de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas cuando se producen, gracias a las alertas de seguridad sobre actividades anómalas en la base de datos que se proporcionan. Detección de amenazas facilita la solución de las posibles amenazas a la instancia administrada sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de seguridad avanzada. Para obtener una experiencia completa de investigación, se recomienda habilitar la auditoría de Instancia administrada de Azure, que escribe los eventos de base de datos en un registro de auditoría en su cuenta de Azure Storage. 

Detección de amenazas de SQL integra alertas con [Azure Security Center](https://azure.microsoft.com/services/security-center/). Cada instancia administrada protegida se factura al mismo precio que el nivel Estándar de Azure Security Center, a 15 USD/nodo/mes (cada instancia administrada protegida cuenta como un nodo).  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configuración de la detección de amenazas para instancias administradas en Azure Portal
1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la página de configuración de la instancia administrada que desea proteger. En la página **Configuración**, seleccione **Detección de amenazas**. 
3. En la página de configuración Detección de amenazas 
   - **Active** Detección de amenazas.
   - Configure la **lista de direcciones de correo electrónico** que recibirán alertas de seguridad cuando se detecten actividades anómalas en las bases de datos.
   - Seleccione la **cuenta de Azure Storage** donde se guardan los registros de anomalías de la auditoría de amenazas. 
4.  Haga clic en **Guardar** para guardar la directiva de detección de amenazas nueva o actualizada.

   ![detección de amenazas](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Exploración de las actividades anómalas de la instancia administrada cuando se detecta un evento sospechoso

1. Cuando se detecten actividades anómalas en las bases de datos, recibirá una notificación por correo electrónico. 

   El correo electrónico proporciona información acerca del evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor y la hora del evento. Proporciona información también sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la instancia administrada.

   ![correo electrónico de detección de amenazas](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Haga clic en el vínculo **View recent SQL alerts** (Ver las alertas recientes de SQL) del correo electrónico para iniciar Azure Portal y mostrar la página de alertas de Azure Security Center, que proporciona información general sobre amenazas activas de SQL detectadas en la base de datos de la instancia administrada.

   ![amenazas activas](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Haga clic en una alerta específica para obtener detalles y acciones adicionales para investigar esta amenaza y solucionar amenazas futuras.

   Por ejemplo, la inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet. La inyección de código SQL se utiliza para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos. Para las alertas de inyección de código SQL, los detalles de la alerta incluyen la instrucción SQL vulnerable que se ha aprovechado.

   ![Inyección de código SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Alertas de detección de amenazas de Instancia administrada 

Detección de amenazas para Instancia administrada detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de acceder a bases de datos, o de vulnerar su seguridad y puede desencadenar las siguientes alertas:
- **Vulnerabilidad ante ataques por inyección de código SQL**: esta alerta se desencadena cuando una aplicación genera una instrucción SQL errónea en la base de datos. Esto puede indicar una posible vulnerabilidad ante ataques por inyección de código SQL. Hay dos posibles razones para la generación de una instrucción errónea:
 - Existe un defecto en el código de la aplicación que crea la instrucción SQL errónea
 - El código de la aplicación o los procedimientos almacenados no corrigen los datos que proporciona el usuario al construir la instrucción SQL errónea, lo que se puede aprovechar para ataques por inyección de código SQL
- **Potencial inyección de código SQL**: esta alerta se desencadena cuando se produce una vulnerabilidad de seguridad activa contra una vulnerabilidad de la aplicación identificada ante inyección de código SQL. Esto significa que el atacante está intentando inyectar instrucciones SQL malintencionadas mediante el código de la aplicación vulnerable o procedimientos almacenados.
- **Acceso desde una ubicación inusual**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a una instancia administrada, en el que alguien ha iniciado sesión en la instancia administrada desde una ubicación geográfica inusual. En algunos casos, la alerta detecta una acción legítima (una nueva aplicación o una operación de mantenimiento de un desarrollador). En otros casos, la alerta detecta una acción malintencionada (por ejemplo, un antiguo empleado, un atacante externo, etc.)
- **Acceso desde un centro de datos de Azure inusual**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a la instancia administrada, en el que alguien ha iniciado sesión en la instancia administrada desde un centro de datos de Azure que no había accedido a la instancia administrada recientemente. En algunos casos, la alerta detecta una acción legítima (una aplicación nueva en Azure, Power BI, Azure SQL Query Editor, etc.) En otros casos, la alerta detecta una acción malintencionada procedente de un recurso o servicio de Azure (por ejemplo, un antiguo empleado o un atacante externo).
- **Acceso desde una entidad de seguridad desconocida**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso al servidor de Instancia administrada, en el cual alguien ha iniciado sesión en la instancia administrada mediante una entidad de seguridad inusual (usuario de SQL). En algunos casos, la alerta detecta una acción legítima (una nueva aplicación o una operación de mantenimiento de un desarrollador). En otros casos, la alerta detecta una acción malintencionada (por ejemplo, un antiguo empleado o un atacante externo).
- **Acceso desde una aplicación potencialmente dañina**: esta alerta se desencadena cuando una aplicación potencialmente dañina se utiliza para acceder a la base de datos. En algunos casos, la alerta detecta la realización de pruebas de seguridad. En otros casos, la alerta detecta un ataque que se realiza con herramientas de ataque comunes.
- **Credenciales de SQL por fuerza bruta**: esta alerta se desencadena cuando hay un número anormalmente elevado de inicios de sesión infructuosos con distintas credenciales. En algunos casos, la alerta detecta la realización de pruebas de seguridad. En otros casos, la alerta detecta ataques por fuerza bruta.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de Instancia administrada, consulte [¿Qué es Instancia administrada de SQL Database (versión preliminar)?](sql-database-managed-instance.md)
- Más información acerca de la [auditoría de Instancia administrada](https://go.microsoft.com/fwlink/?linkid=869430) 
- Más información acerca de [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
