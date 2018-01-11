---
title: Uso de Azure Stream Analytics con SQL Data Warehouse | Microsoft Docs
description: Sugerencias para usar Azure Stream Analytics con Azure SQL Data Warehouse para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c5c0450cba541a9346f023057345c5fc9b147903
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Uso de Azure Stream Analytics con SQL Data Warehouse
Azure Stream Analytics es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través el streaming de datos en la nube. Para aprender los conceptos básicos, lea la [introducción a Azure Stream Analytics][Introduction to Azure Stream Analytics]. Después puede aprender a crear una solución de un extremo a otro con Stream Analytics siguiendo el tutorial de [introducción al uso de Azure Stream Analytics][Get started using Azure Stream Analytics].

En este artículo, aprenderá a usar la base de datos de Azure SQL Data Warehouse como receptor de salida para los trabajos de Análisis de transmisiones.

## <a name="prerequisites"></a>Requisitos previos
En primer lugar, ejecute los pasos siguientes del tutorial de [introducción al uso de Azure Stream Analytics][Get started using Azure Stream Analytics].  

1. Creación de una entrada de Centro de eventos
2. Configuración e inicio de la aplicación del generador de eventos
3. Aprovisionamiento de un trabajo de Stream Analytics
4. Especificación de una consulta y entrada de trabajo

Luego cree una base de datos de Azure SQL Data Warehouse.

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique la salida de trabajo: base de datos de Azure SQL Data Warehouse.
### <a name="step-1"></a>Paso 1
En el trabajo de Stream Analytics, haga clic en **SALIDA** en la parte superior de la página y luego en **AGREGAR SALIDA**.

### <a name="step-2"></a>Paso 2
Seleccione SQL Database y haga clic en Siguiente.

![][add-output]

### <a name="step-3"></a>Paso 3
Escriba estos valores en la página siguiente:

* *Alias de salida*: escriba un nombre descriptivo para esta salida de trabajo.
* *Suscripción*:
  * Si la base de datos de SQL Data Warehouse está en la misma suscripción que el trabajo de Stream Analytics, seleccione Usar SQL Database de la suscripción actual".
  * Si la base de datos está en una suscripción diferente, seleccione Usar SQL Database de otra suscripción.
* *Base de datos*: especifique el nombre de una base de datos de destino.
* *Nombre del servidor*: especifique el nombre del servidor para la base de datos especificada. Para encontrarlo, puede usar Azure Portal.

![][server-name]

* *Nombre de usuario*: especifique el nombre de usuario de una cuenta que tenga permisos de escritura para la base de datos.
* *Contraseña*: proporcione la contraseña de la cuenta de usuario especificada.
* *Tabla*: especifique el nombre de la tabla de destino en la base de datos.

![][add-database]

### <a name="step-4"></a>Paso 4
Haga clic en el botón de comprobación para agregar esta salida de trabajo y comprobar que Stream Analytics puede conectarse correctamente a la base de datos.

![][test-connection]

Cuando la conexión a la base de datos se realice correctamente, verá una notificación en la parte inferior del portal. Puede hacer clic en Probar conexión en la parte inferior para probar la conexión con la base de datos.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general sobre la integración, consulte la [información general de la integración de SQL Data Warehouse][SQL Data Warehouse integration overview].

Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
