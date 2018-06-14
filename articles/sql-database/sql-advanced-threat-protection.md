---
title: 'Protección avanzada contra amenazas: Azure SQL Database | Microsoft Docs'
description: Obtenga información acerca de la funcionalidad para detectar y clasificar datos confidenciales, administrar los puntos vulnerables de una bases de datos y detectar actividades anómalas que puedan indicar una amenaza para una base de datos SQL de Azure.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 5/17/2018
ms.author: ronitr
ms.reviewer: carlrab
ms.openlocfilehash: da21a0b66d86b4cc3e2dc59bdd972d4e24d7e5ec
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305722"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Protección avanzada contra amenazas en Azure SQL Database

SQL Advanced Threat Protection es un paquete unificado de funcionalidades avanzadas de seguridad de SQL. Incluye una funcionalidad para detectar y clasificar datos confidenciales, administrar los puntos vulnerables de una base de datos y detectar actividades anómalas que puedan indicar una amenaza para dicha base de datos. Proporciona una ubicación única para habilitar y administrar estas funcionalidades. 

## <a name="overview"></a>Información general

SQL Advanced Threat Protection (ATP) proporciona un conjunto de funcionalidades avanzadas de seguridad de SQL, entre las que se incluyen Clasificación y detección de datos, Evaluación de vulnerabilidad y Detección de amenazas. 

- [Clasificación y detección de datos](sql-database-data-discovery-and-classification.md) (actualmente en versión preliminar) proporciona funcionalidades integradas en Azure SQL Database para detectar, clasificar, etiquetar y proteger la información confidencial de las bases de datos. Se puede utilizar para proporcionar visibilidad del estado de clasificación de una base de datos y para realizar un seguimiento del acceso a información confidencial dentro de la base de datos y más allá de sus límites.
- [Evaluación de vulnerabilidad](sql-vulnerability-assessment.md) es un servicio fácil de configurar que puede detectar, realizar un seguimiento y ayudarle a corregir posibles puntos vulnerables de una base de datos. Permite ver el estado de la seguridad e incluye los pasos necesarios para resolver problemas de seguridad y mejorar las defensas de cualquier base de datos.
- [Detección de amenazas](sql-database-threat-detection.md) detecta actividades anómalas que indiquen intentos inusuales y potencialmente perjudiciales de acceder a una base de datos o de aprovechar sus vulnerabilidades. Supervisa constantemente una base de datos para detectar actividades sospechosas y proporciona de forma inmediata alertas de seguridad de posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a las bases de datos. Las alertas de Detección de amenazas proporcionan detalles de la actividad sospechosa y recomiendan acciones para investigar y mitigar la amenaza.

Habilite SQL ATP una vez para habilitar todas estas características incluidas. Con un solo clic puede habilitar ATP en todo el servidor de bases de datos, con lo que se aplica a todas las bases de datos del servidor. 

Los precios de ATP se alinean con el nivel estándar de Azure Security Center, 15 USD por nodo al mes, donde cada servidor de SQL Database protegido se cuenta como un nodo. Los primeros 60 días después de la habilitación se consideran un período de evaluación gratuita, por lo que no se cobran. Para más información, consulte la [página de precios de Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Introducción a ATP 
Estos pasos le ayudarán a empezar a usar ATP. 

## <a name="1-enable-atp"></a>1. Habilitación de ATP

Para habilitar ATP, vaya a la opción **Protección contra amenazas avanzada** del título **Seguridad** del panel de Azure SQL Database. Para habilitar ATP en todas las bases de datos en el servidor, haga clic en **Enable Advanced Threat Protection on the server** (Habilitar protección avanzada contra amenazas en el servidor).

![Habilitación de ATP](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> El costo de ATP es 15 USD por nodo al mes, donde un nodo es todo el servidor lógico de SQL. Por consiguiente, se paga una sola vez por proteger todas las bases de datos del servidor con ATP. Los primeros 60 días se consideran de evaluación, por lo que son gratis.

## <a name="2-configure-vulnerability-assessment"></a>2. Configuración de Evaluación de vulnerabilidad

Para empezar a usar Evaluación de vulnerabilidad, es preciso configurar una cuenta de almacenamiento en la que se guardan los resultados de los exámenes. Para ello, haga clic en la tarjeta Evaluación de vulnerabilidad.

![Configurar Evaluación de vulnerabilidad](./media/sql-advanced-protection/configure_va.png) 

Seleccione una cuenta de almacenamiento, o créela, para guardar los resultados de los exámenes. También puede activar exámenes periódicos recurrentes para configurar que Evaluación de vulnerabilidad ejecute exámenes automáticos una vez por semana. Un resumen de los resultados de los exámenes se envía a las direcciones de correo electrónico que especifique.

![Configuración de Evaluación de vulnerabilidad](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Comienzo de la clasificación de datos, el seguimiento de puntos vulnerables y la investigación de alertas de amenazas

Haga clic en la tarjeta **Clasificación y detección de datos** para ver las columnas confidenciales que se recomienda clasificar y para clasificar los datos con etiquetas de confidencialidad persistentes. Haga clic en la tarjeta **Evaluación de vulnerabilidad** tarjeta no solo para ver y administrar informes y exámenes de vulnerabilidad, sino también realizar un seguimiento del estado de la seguridad. Si se han recibido alertas de seguridad, haga clic en la tarjeta **Detección de amenazas** para ver los detalles de las alertas y un informe consolidado de todas las alertas de su suscripción de Azure en la página de alertas de seguridad de Azure Security Center.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Administración de los valores de ATP en un servidor con SQL Server

Para ver y administrar la configuración de Protección contra amenazas avanzada, vaya a la opción **Protección contra amenazas avanzada** del título **Seguridad** del panel servidor con SQL Server. En esta página, se puede habilitar o deshabilitar ATP, así como modificar la configuración de Detección de amenazas para todo el servidor con SQL Server.

![Configuración del servidor](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. Administración de la configuración de ATP para una base de datos SQL

Para reemplazar la configuración de Detección de amenazas de ATP de una base de datos concreta, seleccione la casilla **Enable Advanced Threat Protection at the database level** (Habilitar protección avanzada contra amenazas en el nivel de base de datos). Utilice esta opción solo si tiene un requisito concreto para recibir las alertas de detección de amenazas independientes de la base de datos individual, en lugar o además de las alertas que recibe de todas las bases de datos del servidor. 

Una vez seleccionada la casilla, haga clic en **Threat Detection settings for this database** (Configuración de detección de amenazas en esta base de datos) y configure los valores relevantes para la base de datos.

![Configuración de base de datos y de detección de amenazas](./media/sql-advanced-protection/database_threat_detection_settings.png) 

A la configuración de Protección contra amenazas avanzada del servidor también se puede acceder desde el panel de la base de datos de ATP. Haga clic en **Settings** (Configuración) en el panel principal de ATP y, después, haga clic en **View Advanced Threat Protection server settings** (Ver configuración del servidor de Protección contra amenazas avanzada). 

![Configuración de base de datos](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Pasos siguientes 

- Más información acerca de [Clasificación y detección de datos](sql-database-data-discovery-and-classification.md) 
- Más información acerca de [Evaluación de vulnerabilidad](sql-vulnerability-assessment.md) 
- Más información acerca de [Detección de amenazas](sql-database-threat-detection.md)
- Más información acerca de [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
