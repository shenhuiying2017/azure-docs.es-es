---
title: "Límites de cuota de Azure Data Lake Analytics | Microsoft Docs"
description: "Obtenga información sobre cómo ajustar y aumentar los límites de cuota en una cuenta de Azure Data Lake Analytics (ADLA)."
services: data-lake-analytics
keywords: "Análisis con Azure Data Lake"
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Límites de cuota de Azure Data Lake Analytics
Obtenga información sobre cómo ajustar y aumentar los límites de cuota en una cuenta de Azure Data Lake Analytics (ADLA). Conocer estos límites puede ayudarle a comprender su comportamiento de trabajos de U-SQL. Todos estos límites son flexibles y siempre se puede poner en contacto con nosotros para aumentar los límites máximos.

## <a name="azure-subscriptions-limits"></a>Límites de las suscripciones de Azure

**Número máximo de cuentas de ADLA por suscripción:** 5. Este es el número máximo de cuentas de ADLA que puede crear por suscripción. Se muestra el error “Ha alcanzado el número máximo de cuentas de Data Lake Analytics permitido (5) en {región} con la suscripción {nombre}” al intentar crear la sexta cuenta de ADLA. Para solucionar este error fácilmente, elimine las cuentas de ADLA usadas en la suscripción o cree una incidencia de soporte técnico para ponerse en contacto con nosotros.

## <a name="adla-account-limits"></a>Límites de la cuenta de ADLA

**Número máximo de unidades de análisis por cuenta:** 250. 

Es el número máximo de unidades de análisis que se pueden ejecutar de forma simultánea en su cuenta. El total de unidades de análisis ejecutándose en todos los trabajos no puede superar este número. Si se supera este valor, los nuevos trabajos se agregarán automáticamente a la cola. Por ejemplo:

  * Si solo tiene un trabajo donde se ejecuten 250 unidades de análisis, al enviar el segundo trabajo, este permanecerá en la cola hasta que se complete el primero.
  * Si tiene 5 trabajos ejecutándose y cada uno se ha enviado con 50 unidades de análisis, al enviar el sexto trabajo con 20 unidades de análisis, esperará en la cola de trabajos y empezará a ejecutarse cuando haya 20 unidades de análisis disponibles.


**Número máximo de trabajos de U-SQL simultáneos por cuenta:** 20. 

Es el número máximo de trabajos que se pueden ejecutar de forma simultánea en su cuenta. Si se supera este valor, los nuevos trabajos se agregarán automáticamente a la cola.

## <a name="adjust-the-adla-quota-limits-per-account"></a>Ajuste de los límites de cuota de ADLA por cuenta

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Seleccione la cuenta de ADLA que ha creado.
3. Haga clic en **Propiedades**.
4. Ajuste los valores de **Paralelismo** y **Trabajos simultáneos** según sus necesidades.

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>Para aumentar los límites de cuota máximos

1. Cree una petición de soporte técnico en Azure Portal.

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Seleccione el tipo de problema como **Cuota**.
3. Seleccione su **Suscripción** (asegúrese de que no sea una suscripción de “prueba”).
4. Seleccione el tipo de cuota como **Data Lake Analytics**.

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. En la hoja del problema, explique el límite de aumento que quiere pedir y **Detalles** sobre por qué necesita esta capacidad adicional.

    ![Hoja del portal de Análisis de Azure Data Lake](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Compruebe su información de contacto y cree la petición de soporte técnico.

Microsoft revisa la solicitud y se intenta dar cabida a sus necesidades empresariales tan pronto como sea posible.

## <a name="next-steps"></a>Pasos siguientes
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Administración de Análisis de Azure Data Lake mediante Azure Powershell](data-lake-analytics-manage-use-powershell.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

