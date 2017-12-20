---
title: "Introducción a Detección de amenazas de SQL Data Warehouse"
description: "Cómo empezar a trabajar con la detección de amenazas"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 7f5dab6936e8cac10ac7a4a7dc4c3be116de5ad5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-threat-detection"></a>Introducción a la detección de amenazas
> [!div class="op_single_selector"]
> * [Auditoría](sql-data-warehouse-auditing-overview.md)
> * [Detección de amenazas](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Información general
Detección de amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad a la base de datos. Detección de amenazas está en vista previa y es compatible con SQL Data Warehouse.

Detección de amenazas ofrece un nuevo nivel de seguridad, que permite a los clientes detectar amenazas potenciales y responder a ellas a medida que se producen, gracias a las alertas de seguridad sobre actividades anómalas que se proporcionan. Los usuarios pueden explorar los eventos sospechosos mediante la [auditoría de Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) para determinar si proceden de un intento de acceder a los datos del almacenamiento de datos, infringir su seguridad o aprovecharlos.
Detección de amenazas facilita la solución de las posibles amenazas al almacenamiento sin necesidad de ser un experto en seguridad ni administrar sistemas de supervisión de seguridad avanzada.

Por ejemplo, Detección de amenazas detecta determinadas actividades anómalas en la base de datos que sugieren posibles intentos de inyección de código SQL. La inyección de código SQL es uno de los problemas de seguridad habituales entre las aplicaciones web en Internet y se usa para atacar aplicaciones controladas por datos. Los atacantes aprovechan las vulnerabilidades de la aplicación para inyectar instrucciones SQL malintencionadas en los campos de entrada de la aplicación, con el fin de infringir la seguridad o modificar datos en la base de datos.

## <a name="set-up-threat-detection-for-your-database"></a>Configuración de la detección de amenazas para la base de datos
1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la hoja de configuración de SQL Data Warehouse que desea supervisar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.
   
    ![Panel de navegación][1]
3. En la hoja de configuración de **Auditoría y detección de amenazas**, **active** la auditoría; se mostrará la configuración de Detección de amenazas.
   
    ![Panel de navegación][2]
4. **Active** Detección de amenazas.
5. Configure la lista de correos electrónicos que recibirán alertas de seguridad cuando se detecten actividades anómalas en el almacenamiento.
6. Haga clic en **Guardar** en la hoja de configuración de **Auditoría y detección de amenazas** para guardar la directiva de auditoría y detección de amenazas nueva o actualizada.
   
    ![Panel de navegación][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Exploración de las actividades anómalas en el almacenamiento cuando se detecta un evento sospechoso
1. Recibirá una notificación por correo electrónico tras la detección de actividades anómalas en la base de datos. <br/>
   El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor y la hora del evento. Además, se proporcionará información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos.<br/>
   
    ![Panel de navegación][4]
2. En el correo electrónico, haga clic en el vínculo **Registro de auditoría SQL de Azure** , lo que iniciará el Portal de Azure y mostrará los registros de auditoría pertinentes en torno a la hora del evento sospechoso.
   
    ![Panel de navegación][5]
3. Haga clic en los registros de auditoría para ver más detalles sobre las actividades sospechosas en la base de datos, como instrucción SQL, motivo del error e IP de cliente.
   
    ![Panel de navegación][6]
4. En la hoja Registros de auditoría, haga clic en **Abrir en Excel** para abrir una plantilla de Excel ya configurada para importar y ejecutar un análisis más profundo del registro de auditoría en torno a la hora del evento sospechoso.<br/>
   **Nota:** en Excel 2010 o posterior, se necesitan Power Query y la opción **Combinación rápida**
   
    ![Panel de navegación][7]
5. Para definir la configuración **Combinación rápida**: en la pestaña **POWER QUERY** de la cinta, seleccione **Opciones** para mostrar el cuadro de diálogo Opciones. Seleccione la sección Privacidad y elija la segunda opción, "Omitir los niveles de privacidad para mejorar el rendimiento":
   
    ![Panel de navegación][8]
6. Para cargar registros de auditoría SQL, asegúrese de que los parámetros de la pestaña Configuración sean correctos y después seleccione "Datos" en la cinta y haga clic en el botón "Actualizar todo".
   
    ![Panel de navegación][9]
7. Los resultados aparecen en la hoja **Registros de auditoría SQL** , que permite ejecutar un análisis más profundo de las actividades anómalas que se detectaron y mitigar el impacto del evento de seguridad en la aplicación.

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
