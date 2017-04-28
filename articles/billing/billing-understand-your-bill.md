---
title: "Descripción de la factura de Azure | Microsoft Docs"
description: "Aprenda a leer y entender el uso y la factura de la suscripción de Azure"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 6db1fedad08ee33f3468fb98f59071a1521921db
ms.lasthandoff: 04/11/2017


---
# <a name="understand-your-bill-for-microsoft-azure"></a>Comprender la factura de Microsoft Azure
Para entender la factura de Azure, revise la factura con el resumen de cargos y el archivo independiente de uso diario detallado. En este artículo se describen la mayoría de los términos que se muestran en la factura y en el archivo de uso diario. Para obtener estos archivos, consulte el artículo sobre [cómo obtener los datos de uso diario y de factura de facturación de Azure](billing-download-azure-invoice-daily-usage-date.md). Si va a usar una suscripción de prueba gratuita, puede obtener la información de uso diario pero no tendrá una factura.

Los cargos de las suscripciones de Microsoft Azure varían según el plan de tarifas. Algunos planes de tarifas, como el de los suscriptores de Visual Studio Enterprise (MPN), incluyen créditos mensuales que puede usar en cualquier servicio de Azure según sus necesidades.

En la factura actual pueden aparecer hasta 24 horas de uso al final del período de facturación anterior. Además, los cargos enumerados en los extractos de facturación para clientes internacionales tienen únicamente carácter estimativo. Los bancos tienen costos diferentes para los tipos de conversión.

## <a name="pdf"></a> Descripción de la factura (.pdf)
La factura proporciona un resumen de los cargos. Puede descargarla en el formato de documento portátil (.pdf) desde [Azure Portal](https://portal.azure.com). Para obtener más información, consulte el artículo sobre [cómo obtener los datos de uso diario y de factura de facturación de Azure](billing-download-azure-invoice-daily-usage-date.md). 

Las siguientes secciones enumeran la mayoría de los términos que ve en la factura y las descripciones de cada uno.

### <a name="account-information"></a>Información de cuenta
La sección de información de cuenta muestra información sobre el uso y el perfil.

![encabezado](./media/billing-understand-your-bill/Header.png)

| Término | Description |
| --- | --- |
| Número de factura |Identificador único de factura con fines de seguimiento |
| Ciclo de facturación |Intervalo de fechas que cubre esta factura |
| Fecha de la factura |Fecha en que se ha generado la factura |
| Método de pago |Tipo de pago que se usa en la cuenta (factura o tarjeta de crédito). Si necesita actualizar su tarjeta de crédito, consulte [Cambio de la tarjeta de crédito usada para pagar una suscripción de Azure](billing-how-to-change-credit-card.md). Si representa una empresa, puede pagar su suscripción de Azure con métodos de pago de factura como cheques, cheques urgentes o transferencias electrónicas. Consulte [Facturación de Azure: cómo facturar](https://azure.microsoft.com/pricing/invoicing/). |
| Dirección de facturación |Dirección de pagos de Microsoft Azure. Obtenga información sobre cómo actualizar la información de facturación en [Cambio de la tarjeta de crédito usada para pagar una suscripción de Azure](billing-how-to-change-credit-card.md). |
| Oferta de suscripción |Tipo de oferta de suscripción que se adquirió (pago por uso, BizSpark Plus, Pase para Azure, etc.) Si desea cambiar el pago por uso por otra oferta de suscripción, consulte [Cambio de la suscripción de Azure a otra oferta](billing-how-to-switch-azure-offer.md). |
| Correo electrónico del propietario de cuenta |La dirección de correo electrónico de la cuenta con que está registrada la cuenta de Microsoft Azure. Para cambiar la dirección de correo electrónico, consulte [Cómo cambiar la información de perfil de la cuenta de Azure, como el correo electrónico, la dirección y el número de teléfono de contacto](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Comprender el resumen de factura
La sección **Resumen de factura** de la factura resume las transacciones desde la última factura y los cargos de uso actuales.

![Resumen de factura](./media/billing-understand-your-bill/InvoiceSummary.png)

Las secciones Saldo anterior, Pagos y Saldo pendiente de la factura resumen las transacciones desde la última factura.

| Término | Description |
| --- | --- |
| Saldo anterior |El importe total debido de la última factura |
| Pagos |Los pagos totales aplicados a la última factura |
| Saldo pendiente (desde el ciclo de facturación anterior) |Todos los ajustes de factura (créditos o saldos) aplicados a la cuenta desde la última factura |

### <a name="understand-the-current-charges"></a>Comprender los cargos actuales
La sección Cargos actuales de la factura muestra detalles sobre los cargos mensuales. 

| Término | Description |
| --- | --- |
| Cargos de uso |Los cargos de uso son los cargos mensuales totales de una suscripción. Se le facturará según el uso que haya realizado el mes anterior. |
| Descuentos |Descuentos de servicio aplicados a la factura actual. |
| Ajustes |Créditos diversos o cargos pendientes aplicados a la factura actual. Por ejemplo, si tiene la oferta de Visual Studio Enterprise con MSDN, verá un crédito mensual. Si cancela su suscripción, verá los cargos por uso mensual que superan el crédito mensual que obtiene con su oferta de suscripción. Los cargos son desde el principio de su período de facturación actual hasta la fecha de cancelación de suscripción. |


### <a name="sold-to-and-payment-instructions"></a>Dirección de venta e instrucciones de pago
En la tabla siguiente se describen la dirección de venta y las instrucciones de pago que se muestran en su factura.

| Término | Description |
| --- | --- |
| Dirección de venta | Dirección de perfil que figura en la cuenta. Si tiene que modificar la dirección, consulte [Cómo cambiar la información de perfil de la cuenta de Azure, como el correo electrónico, la dirección y el número de teléfono de contacto](billing-how-to-change-azure-account-profile.md). |
| Instrucciones de pago | Si paga mediante factura, estas instrucciones indican dónde enviar los cheques, las transferencias bancarias o los cheques cobraderos al siguiente día. Para más información, consulte [Facturación de Azure: cómo realizar la facturación](https://azure.microsoft.com/pricing/invoicing/). |

## <a name="csv"></a> Descripción de los cargos por uso detallado (.csv)
El archivo de uso muestra la cantidad de cada recurso que se utiliza dentro del período de facturación actual. Está disponible en un formato de archivo de valores separados por comas (.csv) que se puede abrir en una aplicación de hoja de cálculo. Si ve dos versiones disponibles, descargue la versión 2. Este es el formato de archivo más reciente. Para obtener más información, consulte el artículo sobre [cómo obtener los datos de uso diario y de factura de facturación de Azure](billing-download-azure-invoice-daily-usage-date.md).

Los cargos de uso son los cargos totales **mensuales** de una suscripción, menos cualquier crédito o descuento. Se le facturará según el uso que haya realizado el mes anterior.  

En las secciones siguientes se describe la mayoría de los términos que se muestran en la versión 2 del archivo de uso detallado.

### <a name="statement"></a>Instrucción 
La sección superior del archivo muestra los servicios usados durante el ciclo de facturación del mes anterior. En la tabla siguiente se enumeran los términos y las descripciones que se muestran en esta sección.

| Término | Descripción |
| --- | --- |
|Período de facturación |El período de facturación de cuando se usa el recurso o servicio. |
|Categoría de medidor |Identifica el servicio de nivel superior al que pertenece este uso. |
|Subcategoría de medidor |Define el tipo de servicio de Azure y puede afectar a la tarifa. |
|Nombre de medidor |Identifica la unidad de medida del recurso que se está utilizando. |
|Medidor de la región |Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos. |
|SKU |Identifica el identificador único de sistema de cada recurso de Azure. |
|Unidad |Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, 10.000 s. |
|Cantidad consumida |La cantidad del recurso usado durante el período de facturación. |
|Cantidad incluida |El importe del recurso incluido sin cargo en el período de facturación actual. |
|Cantidad de superávit |Muestra la diferencia entre la cantidad consumida y la cantidad incluida. Se factura este importe. En el caso de ofertas de pago por uso que no incluyen el importe en la oferta, este total será igual a la cantidad consumida. |
|Dentro del compromiso |Muestra los cargos por recursos que se restan del importe comprometido asociado a su oferta de 6 o 12 meses. Los cargos por los recursos se restan en orden cronológico. |
|Moneda |Moneda usada en el período de facturación actual. |
|Superávit |Muestra los cargos por recursos que superan el importe comprometido asociado a su oferta de 6 o 12 meses. |
|Tarifa de compromiso |Muestra la tarifa comprometida basada en el importe comprometido total asociado a su oferta de 6 o 12 meses. |
|Tarifa |La tarifa que se le carga por unidad facturable. |
|Valor |Muestra el resultado de multiplicar las columnas Cantidad superior al límite y Tarifa. Si la cantidad consumida no supera la cantidad incluida, no hay ningún cargo en esta columna. |

### <a name="daily-usage"></a>Uso diario 

La sección de uso diario del archivo muestra los detalles de uso que afectan a las tarifas de facturación. En la tabla siguiente se enumeran los términos y las descripciones que se muestran en esta sección. 

| Término| Descripción |
| --- | --- |
|Fecha de uso |La fecha en que se usó el recurso. |
|Categoría de medidor |Identifica el servicio de nivel superior al que pertenece este uso. |
|Id. de medidor |Identificador del medidor de facturación empleado para valorar el uso de la facturación. |
|Subcategoría de medidor |Define el tipo de servicio de Azure que puede afectar a la tarifa. |
|Nombre de medidor |Identifica la unidad de medida del recurso que se está utilizando. |
|Medidor de la región|Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos. |
|Unidad |Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, 10.000 s. |
|Cantidad consumida |La cantidad del recurso consumida durante ese día. |
|Ubicación del recurso |Identifica el centro de datos donde se está ejecutando el recurso. |
|Servicio consumido |El servicio de la plataforma Azure que ha usado. |
|Grupo de recursos |El grupo de recursos en el que se ejecuta el recurso implementado. Para más información, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). |
|Id. de instancia |Identificador del recurso. El identificador contiene el nombre especificado para el recurso cuando se creó Es el nombre del recurso o el id. de recurso completo. Para más información, consulte [API de Azure Resource Manager](/rest/api/resources/resources). |
|Etiquetas |Etiqueta asignada al recurso. Use etiquetas para agrupar los registros de facturación. Por ejemplo, puede usar etiquetas para distribuir los costos por el departamento que utiliza el recurso. Los servicios que admiten la emisión de etiquetas son máquinas virtuales, almacenamiento y servicios de red aprovisionados mediante la [API de Azure Resource Manager](/rest/api/resources/resources). Para más información, consulte [Organize your Azure resources with tags](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organización de los recursos de Azure con etiquetas). |
|Información adicional |Metadatos específicos del servicio. Por ejemplo, un tipo de imagen de una máquina virtual. |
|Información de servicio 1 |El nombre del proyecto al que pertenece el servicio en la suscripción. |
|Información de servicio 2 |Campo heredado que captura los metadatos específicos del servicio opcional. |

## <a name="tips-for-cost-management"></a>Sugerencias de administración de costes
- Calcule los costes mediante la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/), la [calculadora de coste total de propiedad](https://aka.ms/azure-tco-calculator) y cuando agregue un servicio
- [Configurar alertas de facturación](billing-set-up-alerts.md)
- [Revise el uso y los costes con regularidad en Azure Portal](billing-getting-started.md#costs)

Para más información, consulte [Introducción a la administración de costes y facturación de Azure](billing-getting-started.md).

## <a name="how-do-i-make-a-payment"></a>¿Cómo se puede realizar un pago?
Si ha configurado una tarjeta de crédito o de débito como forma de pago, el pago se realizará automáticamente. Si usa un [método de pago con factura](https://azure.microsoft.com/pricing/invoicing/), envíe el pago a la ubicación indicada en la parte inferior de la factura. Para obtener ayuda, [póngase en contacto con el equipo de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>¿Cómo puedo comprobar el estado de un pago realizado con tarjeta de crédito?
[Cree una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para solicitar el estado de su pago. 

## <a name="what-about-marketplace-orders-or-external-service-charges"></a>¿Qué hay acerca de los pedidos de Marketplace o los cargos por servicios externos?
Los servicios externos se solían llamar pedidos de Marketplace. Los servicios externos los proporcionan proveedores de servicios independientes, pero están integrados en Azure. Para aprender más, consulte [Descripción de los gastos de servicios externos de Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico. 
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
 




