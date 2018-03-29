---
title: Límites de cuota de Azure Data Lake Analytics
description: Obtenga información sobre cómo ajustar y aumentar los límites de cuota en cuentas de Azure Data Lake Analytics (ADLA).
services: data-lake-analytics
keywords: Análisis con Azure Data Lake
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 22774511720173915207da80a6ca33d5dbc83e19
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Límites de cuota de Azure Data Lake Analytics

Obtenga información sobre cómo ajustar y aumentar los límites de cuota en cuentas de Azure Data Lake Analytics (ADLA). Conocer estos límites puede ayudarle a comprender su comportamiento de trabajos de U-SQL. Todos los límites de cuota son flexibles, y siempre se puede poner en contacto con el servicio de soporte técnico de Azure para aumentar los límites máximos.

## <a name="azure-subscriptions-limits"></a>Límites de las suscripciones de Azure

**Número máximo de cuentas de ADLA por suscripción:** 5

Este es el número máximo de cuentas de ADLA que puede crear por cada suscripción y región. Al intentar crear la sexta cuenta de ADLA, se muestra el error "Ha alcanzado el número máximo de cuentas de Data Lake Analytics permitido (5) en {región} con la suscripción {nombre}". En esta situación, puede elegir otra región si es adecuado, eliminar las cuentas de ADLA no utilizadas en la misma región o ponerse en contacto con el servicio de soporte técnico de Azure mediante la [apertura de una incidencia de soporte técnico](#increase-maximum-quota-limits) para solicitar un aumento de la cuota.

## <a name="adla-account-limits"></a>Límites de la cuenta de ADLA

**Número máximo de unidades de análisis por cuenta:** 250

Es el número máximo de unidades de análisis que se pueden ejecutar de forma simultánea en su cuenta. Si el total de unidades de análisis en ejecución entre todos los trabajos supera este límite, los trabajos nuevos se ponen en cola automáticamente. Por ejemplo: 

* Si solo tiene un trabajo en ejecución con doscientas cincuenta unidades de análisis, cuando envíe un segundo trabajo, este permanecerá en la cola de trabajos hasta que el primero se complete.
* Si ya tiene cinco trabajos en ejecución y cada uno usa cincuenta unidades de análisis, al enviar un sexto trabajo que necesita veinte unidades de análisis, este permanecerá en la cola de trabajos hasta que haya veinte unidades de análisis disponibles.

**Número máximo de trabajos de U-SQL simultáneos por cuenta:** 20

Es el número máximo de trabajos que se pueden ejecutar de forma simultánea en su cuenta. Por encima de este valor, los últimos trabajos permanecen en la cola automáticamente.

## <a name="adjust-adla-quota-limits-per-account"></a>Ajuste de los límites de cuota de ADLA por cuenta

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Elija una cuenta de ADLA existente.
3. Haga clic en **Propiedades**.
4. Ajuste los valores de **Paralelismo** y **Trabajos simultáneos** según sus necesidades.

    ![Página del portal de Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Aumento de los límites de cuota máximos

1. Cree una solicitud de soporte técnico en Azure Portal.

    ![Página del portal de Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Página del portal de Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Seleccione el tipo de problema **Cuota**.
3. Seleccione su **Suscripción** (asegúrese de que no sea una suscripción de "prueba").
4. Seleccione el tipo de cuota **Data Lake Analytics**.

    ![Página del portal de Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. En la página del problema, explique el límite de aumento que quiere pedir y los **detalles** de por qué necesita esta capacidad adicional.

    ![Página del portal de Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifique su información de contacto y cree la petición de soporte técnico.

Microsoft revisa la solicitud y se intenta dar cabida a sus necesidades empresariales tan pronto como sea posible.

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-manage-use-powershell.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
