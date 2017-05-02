---
title: "Detección de amenazas de Azure SQL Database| Microsoft Docs"
description: "Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security-protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/10/2016
ms.author: ronmat; ronitr
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c6f580a35115ed93e5575f51956e55dc7b5b8d0a
ms.lasthandoff: 04/15/2017


---
# <a name="sql-database-threat-detection"></a>Detección de amenazas de SQL Database

Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos.  La detección de amenazas se encuentra actualmente en versión preliminar.

## <a name="overview"></a>Información general

Detección de amenazas ofrece un nuevo nivel de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas a medida que se producen, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan.  Los usuarios pueden explorar los eventos sospechosos mediante la [auditoría de SQL Database](sql-database-auditing.md) para determinar si proceden de un intento de acceder a los datos en la base de datos, infringir su seguridad o aprovecharlos.
Detección de amenazas facilita la solución de las posibles amenazas a la base de datos sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de seguridad avanzada.

Por ejemplo, Detección de amenazas detecta determinadas actividades anómalas en la base de datos que sugieren posibles intentos de inyección de código SQL. La inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configuración de la detección de amenazas en Azure Portal
1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la hoja de configuración de Base de datos SQL que desea supervisar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.
   
    ![Panel de navegación][1]
3. En la hoja de configuración **Auditoría y detección de amenazas**, **active** la auditoría; se mostrará la configuración de detección de amenazas.
   
    ![Panel de navegación][2]
4. **Active** la detección de amenazas.
5. Configure la lista de correos electrónicos que recibirán alertas de seguridad cuando se detecten actividades anómalas en la base de datos.
6. Haga clic en **Guardar** en la hoja **Auditoría y detección de amenazas** para guardar la directiva de auditoría y detección de amenazas nueva o actualizada.
   
    ![Panel de navegación][3]

## <a name="set-up-threat-detection-using-powershell"></a>Configuración de la detección de amenazas mediante PowerShell

Para ver un script de ejemplo, consulte [Configuración de la auditoría y detección de amenazas mediante PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Exploración de las actividades anómalas en la base de datos cuando se detecta un evento sospechoso
1. Recibirá una notificación por correo electrónico tras la detección de actividades anómalas en la base de datos. <br/>
   El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor y la hora del evento. Además, se proporcionará información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos.<br/>
   
    ![Panel de navegación][4]
2. En el correo electrónico, haga clic en el vínculo **Registro de auditoría SQL de Azure** , lo que iniciará el Portal de Azure y mostrará los registros de auditoría pertinentes en torno a la hora del evento sospechoso.
   
    ![Panel de navegación][5]
3. Haga clic en los registros de auditoría para ver más detalles sobre las actividades sospechosas en la base de datos, como instrucción SQL, motivo del error e IP de cliente.
   
    ![Panel de navegación][6]
4. En la hoja Registros de auditoría, haga clic en **Abrir en Excel** para abrir una plantilla de Excel ya configurada para importar y ejecutar un análisis más profundo del registro de auditoría en torno a la hora del evento sospechoso.<br/>
   **Nota**: en Excel 2010 o posterior, se necesitan Power Query y la opción **Combinación rápida**.
   
    ![Panel de navegación][7]
5. Para definir la configuración **Combinación rápida**: en la pestaña **POWER QUERY** de la cinta, seleccione **Opciones** para mostrar el cuadro de diálogo Opciones. Seleccione la sección Privacidad y elija la segunda opción, "Omitir los niveles de privacidad para mejorar el rendimiento":
   
    ![Panel de navegación][8]
6. Para cargar registros de auditoría SQL, asegúrese de que los parámetros de la pestaña Configuración sean correctos y después seleccione "Datos" en la cinta y haga clic en el botón "Actualizar todo".
   
    ![Panel de navegación][9]
7. Los resultados aparecen en la hoja **Registros de auditoría SQL** , que permite ejecutar un análisis más profundo de las actividades anómalas que se detectaron y mitigar el impacto del evento de seguridad en la aplicación.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre la auditoría de SQL Database, consulte [este artículo sobre la realización de auditorías en las bases de datos](sql-database-auditing.md).
* Para ver un script de ejemplo de PowerShell, consulte [Configuración de la auditoría y detección de amenazas mediante PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

<!--Image references-->
[1]: ./media/sql-database-threat-detection-get-started/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection-get-started/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection-get-started/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection-get-started/4_td_email.png
[5]: ./media/sql-database-threat-detection-get-started/5_td_audit_records.png
[6]: ./media/sql-database-threat-detection-get-started/6_td_audit_record_details.png
[7]: ./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png
[8]: ./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png
[9]: ./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png


