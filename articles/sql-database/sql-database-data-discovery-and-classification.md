---
title: "Clasificación y detección de datos de Azure SQL Database| Microsoft Docs"
description: "Clasificación y detección de datos de Azure SQL Database"
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 4538ad9c048a3e84bbe793572f569dd7126803da
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Clasificación y detección de datos de Azure SQL Database
La opción Clasificación y detección de datos (actualmente en su versión preliminar) proporciona funcionalidades avanzadas integradas en Azure SQL Database para detectar, clasificar, etiquetar y proteger la información confidencial de las bases de datos.
Las funciones de detección y clasificación de la información confidencial más importante (empresarial, financiera, médica, personal, etc.) desempeñan un rol fundamental en el modo en que se protege la información de su organización. Puede servir como infraestructura para:
* Varios escenarios de seguridad, como la supervisión (auditoría) y las alertas relacionadas con accesos anómalos a información confidencial.
* Controlar el acceso y mejorar la seguridad de las bases de datos que contienen información altamente confidencial.
* Cumplir los requisitos de cumplimiento de los estándares relacionados con la privacidad de datos y normas, como RGPD.

## <a id="subheading-1"></a>Información general
La función de clasificación y detección de datos incluye un conjunto de servicios avanzados y nuevas funcionalidades de SQL y forma un nuevo paradigma de Information Protection de SQL destinado a proteger los datos, no solo la base de datos:
* **Detección y recomendaciones**: el motor de clasificación examina la base de datos e identifica las columnas que contienen datos potencialmente confidenciales. A continuación, le proporciona una manera sencilla de revisar y aplicar las recomendaciones de clasificación adecuadas a través de Azure Portal.
* **Etiquetado**: las etiquetas de clasificación de la confidencialidad se pueden etiquetar de forma persistente en columnas con nuevos atributos de metadatos de clasificación introducidos en el motor de SQL. A continuación, estos metadatos se pueden utilizar para escenarios avanzados de auditoría y protección basados en la confidencialidad.
* **Confidencialidad del conjunto de resultados de consulta**: la confidencialidad del conjunto de resultados de consulta se calcula en tiempo real con fines de auditoría.
* **Visibilidad**: el estado de clasificación de la base de datos puede verse en un panel detallado en el portal. Además, puede descargar un informe (en formato de Excel) para usarlo con fines de auditoría y cumplimiento de normas, así como para otras necesidades.

## <a id="subheading-2"></a>Detección, clasificación y etiquetado de las columnas confidenciales
En la siguiente sección se describen los pasos para detectar, clasificar y etiquetar las columnas que contienen datos confidenciales de su base de datos, así como para ver el estado actual de clasificación de su base de datos y exportar informes.

La clasificación incluye dos atributos de metadatos:
* Etiquetas: atributos de clasificación principales, que se utilizan para definir el nivel de confidencialidad de los datos almacenados en la columna.  
* Tipos de información: proporcionan una granularidad adicional en el tipo de datos almacenados en la columna.

<br>
**Para clasificar su instancia de SQL Database:**

1. Vaya a [Azure Portal](https://portal.azure.com).

2. Vaya a la configuración **Clasificación y detección de datos (versión preliminar)** en SQL Database.

    ![Panel de navegación][1]

3. La pestaña **Introducción** incluye un resumen del estado actual de clasificación de la base de datos, incluida una lista detallada de todas las columnas clasificadas, que también puede filtrar para ver solo tipos de información, etiquetas y partes del esquema específicos. Si aún no ha clasificado ninguna columna, [vaya al paso 5](#step-5).

    ![Panel de navegación][2]

4. Para descargar un informe en formato de Excel, haga clic en la opción **Exportar** del menú superior de la ventana.

    ![Panel de navegación][3]

5.  <a id="step-5"></a>Para empezar a clasificar los datos, haga clic en la **pestaña Clasificación** en la parte superior de la ventana.

    ![Panel de navegación][4]

6. El motor de clasificación examina las columnas de su base de datos que contienen datos potencialmente confidenciales y proporciona una lista de **clasificaciones de columna recomendadas**. Para ver y aplicar las recomendaciones de clasificación:

    * Para ver la lista de clasificaciones de columnas recomendadas, haga clic en el panel de recomendaciones en la parte inferior de la ventana:

        ![Panel de navegación][5]

    * Revise la lista de recomendaciones: para aceptar una recomendación para una columna específica, active la casilla de la columna izquierda de la fila correspondiente. También puede aceptar *todas las recomendaciones*. Para ello, active la casilla del encabezado de la tabla de recomendaciones.

        ![Panel de navegación][6]

    * Para aplicar las recomendaciones seleccionadas, haga clic en el botón azul **Accept selected recommendations** (Aceptar recomendaciones seleccionadas).

        ![Panel de navegación][7]

7. Si quiere, también puede **clasificar manualmente** las columnas, o puede optar por hacerlo así además de realizar la clasificación basada en recomendaciones:

    * Haga clic en **Agregar clasificación** en el menú superior de la ventana.

        ![Panel de navegación][8]

    * En la ventana de contexto que se abre, seleccione el esquema > la tabla > la columna que quiera clasificar, y el tipo de información y la etiqueta de confidencialidad. A continuación, haga clic en el botón azul **Agregar clasificación** situado en la parte inferior de la ventana de contexto.

        ![Panel de navegación][9]

8. Para completar la clasificación y etiquetar de forma persistente las columnas de la base de datos con los nuevos metadatos de clasificación, haga clic en **Guardar** en el menú superior de la ventana.

    ![Panel de navegación][10]

## <a id="subheading-3"></a>Auditoría del acceso a datos confidenciales

La [auditoría de Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) se ha mejorado para incluir un nuevo campo en el registro de auditoría denominado *data_sensitivity_information*, que registra la clasificación de confidencialidad (etiquetas) de los datos reales que devuelve la consulta.

![Panel de navegación][11]

## <a id="subheading-4"></a>Pasos siguientes
Considere la posibilidad de configurar la [auditoría de Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) para supervisar y auditar el acceso a los datos confidenciales clasificados.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
