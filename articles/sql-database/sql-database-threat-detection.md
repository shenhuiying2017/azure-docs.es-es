---
title: "Detección de amenazas de Azure SQL Database| Microsoft Docs"
description: "Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos."
services: sql-database
documentationcenter: 
author: rmatchoro
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 05/01/2017
ms.author: ronmat; ronitr
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 5c2742a1d8ed6df7496a14226a38e02ca993abf3
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="sql-database-threat-detection"></a>Detección de amenazas de SQL Database

Detección de amenazas de SQL detecta actividad anómala que indica intentos inusuales y potencialmente peligrosos de obtener acceso a las bases de datos o de vulnerar su seguridad.

## <a name="overview"></a>Información general

Detección de amenazas de SQL ofrece un nuevo nivel de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas a medida que se producen, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan.  Los usuarios recibirán una alerta en actividades sospechosas de bases de datos, posibles vulnerabilidades y ataques de inyección de SQL, así como patrones de acceso de base de datos anómalos. Las alertas de Detección de amenazas de SQL proporcionan detalles de actividad sospechosa y recomiendan acciones sobre cómo investigar y mitigar la amenaza. Los usuarios pueden explorar los eventos sospechosos mediante la [auditoría de SQL Database](sql-database-auditing.md) para determinar si proceden de un intento de acceder a los datos en la base de datos, infringir su seguridad o aprovecharlos. Detección de amenazas facilita la solución de las posibles amenazas a la base de datos sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de seguridad avanzada.

Por ejemplo, la inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

Detección de amenazas de SQL integra alertas con [Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/). Cada servidor de SQL Database protegido se factura al mismo precio que el nivel Estándar de Azure Security Center, a 15 USD/nodo/mes (cada servidor protegido cuenta como un nodo). Le invitamos a probarlo gratis durante 60 días. 

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configuración de la detección de amenazas en Azure Portal
1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la hoja de configuración de Base de datos SQL que desea supervisar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**. 
    ![Panel de navegación][1]
3. En la hoja de configuración **Auditoría y detección de amenazas**, **active** la auditoría; se mostrará la configuración de detección de amenazas.
  
    ![Panel de navegación][2]
4. **Active** Detección de amenazas.
5. Configure la lista de correos electrónicos que recibirán alertas de seguridad cuando se detecten actividades anómalas en la base de datos.
6. Haga clic en **Guardar** en la hoja **Auditoría y detección de amenazas** para guardar la configuración de auditoría y detección de amenazas nueva o actualizada.
       
    ![Panel de navegación][3]

## <a name="set-up-threat-detection-using-powershell"></a>Configuración de la detección de amenazas mediante PowerShell

Para ver un script de ejemplo, consulte [Configuración de la auditoría y detección de amenazas mediante PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Exploración de las actividades anómalas en la base de datos cuando se detecta un evento sospechoso
1. Recibirá una notificación por correo electrónico tras la detección de actividades anómalas en la base de datos. <br/>
   El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor, el nombre de la aplicación y la hora del evento. Además, el correo electrónico proporcionará información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos.<br/>
     
    ![Panel de navegación][4]
2. La alerta de correo electrónico incluye un vínculo directo al registro de auditoría de SQL. Al hacer clic en este vínculo inicia Azure Portal y abre los registros de auditoría de SQL aproximadamente a la hora del evento sospechoso. Haga clic en un registro de auditoría para ver más detalles sobre las actividades sospechosas de la base de datos, lo que facilita la detección de las instrucciones SQL que se ejecutaron (quién ha obtenido acceso, qué hizo y cuándo), y determinar si el evento ha sido legítimo o malintencionado (por ejemplo, ha aprovechado una vulnerabilidad de la aplicación a la inyección de SQL, alguien ha realizado la infracción datos confidenciales, etc.).<br/>
   ![Panel de navegación][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Exploración de alertas de detección de amenazas para la base de datos en Azure Portal

Detección de amenazas de SQL Database integra la alerta con [Azure Security Center](https://azure.microsoft.com/en-us/services/security-center/). Un icono de seguridad SQL dinámico dentro de la hoja de la base de datos en Azure Portal realiza un seguimiento del estado de amenazas activas. 

   ![Panel de navegación][6]
   
1. Al hacer clic en el icono de seguridad de SQL se inicia la hoja de alertas de Azure Security Center y proporciona una descripción general de las amenazas SQL activas detectadas en la base de datos. 

  ![Panel de navegación][7]

2. Al hacer clic en una alerta específica se proporcionan detalles y acciones adicionales para investigar esta amenaza y solucionar amenazas futuras.

  ![Panel de navegación][8]


## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre Detección de amenazas, visite el [Blog de Azure](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* Más información acerca de la [auditoría de Azure SQL Database](sql-database-auditing.md)
* Más información acerca de [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)
* Para obtener más detalles sobre los precios, vea la [página de precios de SQL Database](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
* Para ver un script de ejemplo de PowerShell, consulte [Configuración de la auditoría y detección de amenazas mediante PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png



