---
title: "Descripción de la factura de Azure | Microsoft Docs"
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
ms.date: 06/29/2017
ms.author: tonguyen
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 264078587b81d7840fe5976498a91ae4f535b6aa
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprender la factura de Microsoft Azure
Para entender la factura de Azure, compare la factura con el archivo de uso diario detallado y los informes de administración de costes en Azure Portal.

Para la factura en PDF y la descarga del archivo CSV de uso diario detallado, vea [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md). 

Para términos y descripciones detallados de su factura y el archivo de uso diario detallado, vea [Descripción de los términos en su factura de Microsoft Azure](billing-understand-your-invoice.md) y [Understand terms on your Microsoft Azure detailed usage](billing-understand-your-usage.md) (Descripción de los términos sobre el uso detallado de Microsoft Azure).

Para obtener información sobre los informes de administración de costes, vea [Azure portal cost management](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started) (Administración de costes de Azure Portal).

## <a name="reconcile"></a>¿Cómo puedo asegurarme de que los cargos de mi factura son correctos?
Si hay un cargo en la factura del que quiera tener más detalles, hay un par de opciones:

### <a name="review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Revisión de la factura y comparación del uso y los costes con el archivo CSV de uso detallado

El archivo CSV de uso detallado proporciona los cargos por período de facturación y uso diario. Para obtener el archivo CSV de uso detallado, vea [Obtención de los datos de uso diario y la factura de Azure](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date).

Los cargos de uso se muestran en el nivel de medidor. La siguiente lista de términos significa lo mismo entre la factura y el archivo de uso detallado. Por ejemplo, el ciclo de facturación en la factura es equivalente al período de facturación que se muestra en el archivo de uso detallado.

 | Factura (PDF) | Uso detallado (CSV)|
 | --- | --- |
 |Ciclo de facturación | Período de facturación |
 |Nombre |Categoría de medidor |
 |Tipo |Subcategoría de medidor |
 |Recurso |Nombre de medidor |
 |Region |Medidor de la región |
 |Consumida |Cantidad consumida |
 |Se incluye |Cantidad incluida |
 |Facturable |Cantidad de superávit |

La sección de cargos de uso de la factura tiene el valor total de cada medidor que se consumió durante el período de facturación. Por ejemplo, la captura de pantalla siguiente muestra un cargo de uso para el servicio del programador de Azure.

![Cargos de uso de la factura](./media/billing-understand-your-bill/1.png)

Se muestra la misma carga en la sección de instrucción del archivo CSV de uso detallado.

![Cargos de uso del archivo CSV](./media/billing-understand-your-bill/2.png)

Además, hay una sección de uso diario en el archivo CSV de uso detallado. La sección incluye elementos de línea para cada día que se consumió el medidor. Estos elementos de línea agregan hasta la cantidad consumida para el medidor durante el período de facturación.

Para secciones, términos y descripciones detallados de la factura. Consulte [aquí](billing-understand-your-invoice.md).

Para secciones, términos y descripciones detallados del archivo de uso diario detallado, vea [aquí](billing-understand-your-usage.md).

### <a name="review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Revisión de la factura y comparación con el uso y los costes en Azure Portal

Azure Portal también puede ayudarle a comprobar los cargos. Azure Portal proporciona gráficos de administración de costes para información general rápida del uso y los cargos en su factura.

Algunos ejemplos de herramientas de administración de costes disponibles en Azure Portal:

![Coste por recurso en Azure Portal](./media/billing-understand-your-bill/3.png)

Este gráfico proporciona costes actuales por recurso.

![Tasa de cargo y previsión en Azure Portal](./media/billing-understand-your-bill/4.png)

Este gráfico muestra los costes actuales en esta suscripción y los costes de las previsiones para el resto del período de facturación.

![Vista de análisis de costes en Azure Portal](./media/billing-understand-your-bill/5.png)

El análisis de costes proporciona un uso de nivel de medidor y recursos para distintos períodos de facturación.

Para más información, vea [Prevención de costes inesperados con la administración de costes y facturación de Azure](billing-getting-started.md#costs).

## <a name="external"></a>¿Qué son los cargos de servicios externos?
Los servicios externos (también conocidos como pedidos de Marketplace) se proporcionan mediante proveedores de servicios independientes y se facturan por separado. Los cargos no se muestran en la factura de Azure. Para aprender más, consulte [Descripción de los gastos de servicios externos de Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>¿Cómo puedo realizar un pago?
Si ha configurado una tarjeta de crédito o de débito como forma de pago, el pago se realizará automáticamente. En el extracto de la tarjeta de crédito, el elemento de línea diría **MSFT Azure**.

Si usa un [método de pago con factura](https://azure.microsoft.com/pricing/invoicing/), envíe el pago a la ubicación indicada en la parte inferior de la factura. Para obtener ayuda, [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>¿Cómo puedo comprobar el estado de un pago realizado con tarjeta de crédito?
[Cree una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para solicitar el estado de su pago. 

## <a name="tips-for-cost-management"></a>Sugerencias de administración de costes
- Calcule los costes mediante la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/), la [calculadora de coste total de propiedad](https://aka.ms/azure-tco-calculator) y cuando agregue un servicio
- [Configurar alertas de facturación](billing-set-up-alerts.md)
- [Revise el uso y los costes con regularidad en Azure Portal](billing-getting-started.md#costs)

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico. 
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

