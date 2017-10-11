---
title: "Creación de soluciones integradas con SQL Data Warehouse | Microsoft Docs"
description: 'Herramientas y asociados con soluciones que se integran con Almacenamiento de datos SQL. '
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: d407c29f99fd7537590ec787febd84a9e3f4f353
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>Aprovechamiento de otros servicios con Almacenamiento de datos SQL
Además de su funcionalidad básica, Almacenamiento de datos SQL permite a los usuarios aprovechar muchos de los otros servicios de Azure.  En concreto, hemos tomado medidas para que realizar una estrecha integración con lo siguiente:

* Power BI
* Factoría de datos de Azure
* Aprendizaje automático de Azure
* Análisis de transmisiones de Azure

Estamos trabajando para conectar con más servicios en el ecosistema de Azure.

## <a name="power-bi"></a>Power BI
La integración de Power BI le permite aprovechar en gran medida la capacidad de procesamiento de Almacenamiento de datos SQL con los informes dinámicos y la visualización de Power BI. La integración de Power BI actualmente incluye:

* **Conexión directa**: una conexión más avanzada con aplicación de lógica en Almacenamiento de datos SQL.  Esto proporciona un análisis más rápido a mayor escala.
* **Open in Power BI**: el botón ‘Open in Power BI’ (Abrir en Power BI) pasa la información de la instancia a Power BI para lograr una conexión más fluida.

Consulte [Integración con Power BI](sql-data-warehouse-integrate-power-bi.md) o la [documentación de Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) para más información.

## <a name="azure-data-factory"></a>Factoría de datos de Azure
Factoría de datos de Azure ofrece a los usuarios una plataforma administrada para crear canalizaciones complejas de extracción y carga.  La integración de Almacenamiento de datos SQL con Factoría de datos de Azure incluye lo siguiente:

* **Procedimientos almacenados**: coordinar la ejecución de procedimientos almacenados en Almacenamiento de datos SQL.
* **Copia**: use ADF para mover datos a Almacenamiento de datos SQL.  Esta operación puede utilizar el mecanismo estándar de movimiento de datos de ADF o PolyBase en segundo plano. 

Consulte [Integración con Azure Data Factory](sql-data-warehouse-integrate-azure-data-factory.md) o la [documentación de Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) para más información.

## <a name="azure-machine-learning"></a>Aprendizaje automático de Azure
Aprendizaje automático de Azure es un servicio de análisis totalmente administrado que permite a los usuarios crear modelos complejos aprovechando un amplio conjunto de herramientas de predicción.  Almacenamiento de datos SQL se admite como origen y destino para estos modelos con la siguiente funcionalidad:

* **Lectura de datos:** producir modelos a escala usando T-SQL en Almacenamiento de datos SQL.
* **Escritura de datos:** confirmar los cambios de cualquier modelo en Almacenamiento de datos SQL.

Consulte [Integración con Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) o la[ documentación de Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) para más información.

## <a name="azure-stream-analytics"></a>Análisis de transmisiones de Azure
Análisis de transmisores de Azure es una infraestructura compleja y totalmente administrada para el procesamiento y consumo de datos de eventos generados por el Centro de eventos de Azure.  La integración con Almacenamiento de datos SQL permite que los datos de transmisión se procesen y almacenen junto con los datos relacionales para permitir un análisis más profundo y avanzado de los datos.  

* **Resultado del trabajo:** enviar los resultados de los trabajos de Análisis de transmisiones directamente al Almacenamiento de datos SQL.

Consulte [Integración con Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md) o la [documentación de Azure Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) para más información.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
