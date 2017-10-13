---
title: "Supervisión y seguimiento del uso de los servicios gratuitos: Azure | Microsoft Docs"
description: Aprenda a comprobar el uso de los servicios gratuitos. Uso de Azure Portal y CSV de uso.
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 27ff6c92904a0b32cd4a37c8b1930adc121a7231
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Comprobación del uso de los servicios gratuitos incluidos con la cuenta gratuita de Azure 

No se le cobrará por los servicios incluidos de forma gratuita con la cuenta gratuita de Azure, a menos que se superen los límites de estos servicios. Para no superar los límites, puede usar Azure Portal o el archivo de uso para realizar la supervisión y el seguimiento del uso de los servicios gratuitos. 

## <a name="check-usage-on-the-azure-portal"></a>Comprobación del uso de Azure Portal

1.  Inicie sesión en [Azure Portal]( http://portal.azure.com).

2.  En la parte inferior del panel de navegación izquierdo, seleccione **Más servicios**.

3.  Seleccione **Suscripciones**.

4.  Seleccione la suscripción que creó cuando se registró para la cuenta gratuita.

    ![Captura de pantalla que muestra todas las suscripciones](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  La sección de información general muestra información esencial sobre la suscripción, como el identificador de suscripción, el tipo de oferta y el nombre de la suscripción. También puede encontrar información sobre cuándo expirará el crédito de la cuenta gratuita.

    ![Captura de pantalla que muestra información esencial de suscripción](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Desplácese hacia abajo para buscar información sobre el costo actual y previsto. El costo incluye el uso de servicios no incluidos con su cuenta gratuita y el uso que supera los límites de los servicios gratuitos. 

    ![Captura de pantalla que muestra información de costos de suscripción](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  En la parte final de la sección de información general hay una tabla sobre el uso de los servicios gratuitos. 

    ![Captura de pantalla que muestra el uso de los servicios gratuitos](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    La tabla contiene las columnas siguientes:

* **Nombre del medidor:** Identifica la unidad de medida del medidor que se está utilizando. Para información sobre la asignación de servicios a medidores, consulte [Understand free service to meter mapping](billing-understand-free-service-meter-mapping.md) (Descripción de la asignación gratuita de servicios a medidores). 
* **Uso y límite:** Uso y límite del mes actual para el medidor. También puede encontrar esta información en la barra de estado.
* **Estado:** Estado de uso del medidor. Según el patrón de uso, puede tener uno de estos estados.
  * **Not in use (No está en uso):** No ha usado el medidor o el uso de este no llega al sistema de facturación.
  * **Exceeded on (Lo superó) el \<Fecha>:** Ha superado el límite del medidor el \<Fecha>.
  * **Superación improbable:** Es poco probable que supere el límite del medidor.
  * **Lo supera el \<Fecha>:** Es probable que supere el límite del medidor el \<Fecha >.


## <a name="check-usage-through-the-usage-file"></a>Comprobación del uso a través del archivo de uso

El archivo de uso proporciona información detallada para la suscripción de Azure. Puede descargar el archivo de uso mensual y diariamente del Centro de cuentas de Azure. Para información sobre cómo descargar el archivo de uso y comprender el acceso necesario, consulte [Procedimiento para descargar los datos de uso diario y de factura de Azure](billing-download-azure-invoice-daily-usage-date.md). Para información sobre las columnas del archivo de uso, consulte [Descripción de los términos de los cargos de uso detallados de Microsoft Azure](billing-understand-your-usage.md). 

El archivo de uso contiene información de uso de servicios gratuitos y de pago. Los medidores de servicio gratuito tendrían la palabra **Gratuito** anexada al final del nombre del medidor. Para buscar medidores gratuitos, abra el archivo en Excel y filtre la **columna Categoría del medidor** para obtener las celdas que contienen el texto **- Gratuito** (utilice el filtro Filtros de texto &rarr; Contiene)&nbsp;

![Captura de pantalla que muestra el uso de los servicios gratuitos](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
