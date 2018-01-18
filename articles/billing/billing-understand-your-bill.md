---
title: "Descripción de la factura de Azure"
description: "Aprenda a leer y entender el uso y la factura de la suscripción de Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: be15c74456b0cec64455f03dd72b8b64eef2bd5d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprender la factura de Microsoft Azure
Para entender la factura de Azure, compare la factura con el archivo de uso diario detallado y los informes de administración de costos en Azure Portal.

>[!NOTE]
>Este artículo no es aplicable para clientes del Contrato Enterprise (EA). Si usted es un cliente EA, [puede encontrar la documentación de las facturas en Enterprise Portal](https://ea.azure.com/helpdocs/understandingYourInvoice).  

Para obtener la factura en PDF y una copia de la descarga del archivo CSV de uso diario detallado, consulte [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md). 

Para términos y descripciones detallados de su factura y el archivo de uso diario detallado, vea [Descripción de los términos en su factura de Microsoft Azure](billing-understand-your-invoice.md) y [Understand terms on your Microsoft Azure detailed usage](billing-understand-your-usage.md) (Descripción de los términos sobre el uso detallado de Microsoft Azure). 

Para obtener información sobre los informes de administración de costes, vea [Azure portal cost management](https://docs.microsoft.com/azure/billing/billing-getting-started) (Administración de costos de Azure Portal).

## <a name="charges"></a>¿Cómo puedo asegurarme de que los cargos de mi factura son correctos?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Si hay un cargo en la factura del que quiera tener más detalles, hay un par de opciones.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Opción 1: Revisión de la factura y comparación del uso y los costos con el archivo CSV de uso detallado

El archivo CSV de uso detallado proporciona los cargos por período de facturación y el uso diario. Para obtener el archivo CSV de uso detallado, consulte [Obtención de los datos de uso diario y la factura de Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Los cargos de uso se muestran en el nivel de medidor. Los siguientes términos significan lo mismo en la factura y en el archivo de uso detallado. Por ejemplo, el ciclo de facturación en la factura es equivalente al período de facturación que se muestra en el archivo de uso detallado.

 | Factura (PDF) | Uso detallado (CSV)|
 | --- | --- |
|Ciclo de facturación | Período de facturación |
 |NOMBRE |Categoría de medidor |
 |type |Subcategoría de medidor |
 |Recurso |Nombre de medidor |
 |Region |Medidor de la región |
 |Consumida |Cantidad consumida |
 |Se incluye |Cantidad incluida |
 |Facturable |Cantidad de superávit |

La sección de **cargos de uso** de la factura tiene el valor total de cada medidor que se consumió durante el período de facturación. Por ejemplo, la captura de pantalla siguiente muestra un cargo de uso para el servicio del programador de Azure.

![Cargos de uso de la factura](./media/billing-understand-your-bill/1.png)

Se muestra el mismo cargo en la sección de **resumen de cuenta** del archivo CSV de uso detallado. Tanto la cantidad *consumida* como el *valor* coinciden con los de la factura.

![Cargos de uso del archivo CSV](./media/billing-understand-your-bill/2.png)

Para ver un desglose de este cargo a diario, vaya a la sección **Uso diario** del archivo CSV. Filtre por "Scheduler" en *Categoría de medidor* y podrá ver qué días se usó el medidor y cuánto se consumió. La información de *recursos* y de *grupo de recursos* también se incluye para poder realizar una comparación. Los valores *consumidos* deben corresponderse con los que aparecen en la factura.

![Sección de uso diario en el archivo CSV](./media/billing-understand-your-bill/3.png)

Para obtener el costo por día, multiplique las cantidades *consumidas* por el valor de la *tarifa* en la sección **Resumen de cuenta**.

Para más información acerca de la factura, consulte [Conocimiento de la factura de Azure](billing-understand-your-invoice.md).

Para más información sobre cada una de las columnas del archivo CSV, consulte [Conocimiento del uso detallado de Azure](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Opción 2: Revisión de la factura y comparación con el uso y los costos en Azure Portal

Azure Portal también puede ayudar a comprobar los costos, ya que proporciona gráficos de administración de costos para una visualización rápida del uso y de los cargos de la factura.

Para continuar con el ejemplo anterior, visite la [página de suscripciones](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), seleccione su suscripción y, a continuación, elija **Análisis de costos**. Desde allí, puede especificar el intervalo de tiempo y ver los cargos por uso para el servicio Azure Scheduler.

![Vista de análisis de costos en Azure Portal](./media/billing-understand-your-bill/4.png)

Para ver el desglose de costo diario en **Historial de costos**, haga clic en la fila.

![Visualización del historial de costos en Azure Portal](./media/billing-understand-your-bill/5.png)

Para más información, vea [Prevención de costes inesperados con la administración de costes y facturación de Azure](billing-getting-started.md#costs).

## <a name="external"></a>¿Qué son los cargos de servicios externos?
Los servicios externos (también conocidos como pedidos de Azure Marketplace) se proporcionan mediante proveedores de servicios independientes y se facturan por separado. Los cargos no se muestran en la factura de Azure. Para aprender más, consulte [Descripción de los gastos de servicios externos de Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>¿Cómo puedo realizar un pago?

Si ha configurado una tarjeta de crédito o de débito como forma de pago, el pago se cargará automáticamente en un plazo de diez días después de finalizar el período de facturación. En el extracto de la tarjeta de crédito, el elemento de línea diría **MSFT Azure**.

Si [paga mediante factura](billing-how-to-pay-by-invoice.md), envíe el pago a la ubicación indicada en la parte inferior de la factura. Para obtener ayuda, [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>¿Cómo puedo comprobar el estado de un pago realizado con tarjeta de crédito?

[Cree una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para solicitar el estado de su pago. 

## <a name="tips-for-cost-management"></a>Sugerencias de administración de costes
- Realice una estimación de los costos mediante la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) y la [calculadora de costo total de propiedad](https://aka.ms/azure-tco-calculator) y obtenga la [información detallada sobre los precios de cada servicio](https://azure.microsoft.com/en-us/pricing/).
- [Configure las alertas de facturación](billing-set-up-alerts.md).
- [Revise el uso y los costos con regularidad en Azure Portal](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
