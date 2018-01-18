---
title: Conocimiento del uso detallado de Azure
description: "Aprenda a leer y entender las secciones del archivo CSV de uso detallado de la suscripción de Azure"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 999f24b5a5c1bd5d259b785f9107697d2928b4eb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Descripción de los términos de los cargos de uso detallados de Microsoft Azure 

El archivo CSV de cargos de uso detallados contiene los cargos por uso diario y por medidor del periodo de facturación actual. 

Para obtener el archivo de uso detallado, consulte [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md).
Está disponible en un formato de archivo de valores separados por comas (.csv) que se puede abrir en una aplicación de hoja de cálculo. Si ve dos versiones disponibles, descargue la versión 2. Este es el formato de archivo más reciente.

Los cargos de uso son los cargos **mensuales** totales de una suscripción. Los cargos de uso no tienen en cuenta los créditos o descuentos.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Términos y descripciones detallados del archivo de uso detallado

En las secciones siguientes se describen los términos importantes que se muestran en la versión 2 del archivo de uso detallado.

### <a name="statement"></a>Instrucción

La sección superior del archivo CSV de uso detallado muestra los servicios usados durante el ciclo de facturación del mes anterior. En la tabla siguiente se enumeran los términos y las descripciones que se muestran en esta sección.

| Término | DESCRIPCIÓN |
| --- | --- |
|Período de facturación |El período de facturación en el que se utilizaron los medidores |
|Categoría de medidor |Identifica el servicio de nivel superior para el uso. |
|Subcategoría de medidor |Define el tipo de servicio de Azure que puede afectar a la tarifa. |
|Nombre de medidor |Identifica la unidad de medida del medidor que se está utilizando. |
|Medidor de la región |Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos. |
|SKU |Identifica el identificador único de sistema de cada medidor de Azure. |
|Unidad |Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, 10.000 s. |
|Cantidad consumida |La cantidad del medidor usado durante el período de facturación. |
|Cantidad incluida |El importe del medidor incluido sin cargo en el período de facturación actual. |
|Cantidad de superávit |Muestra la diferencia entre la cantidad consumida y la cantidad incluida. Se factura este importe. En el caso de ofertas de pago por uso sin cantidad incluida en la oferta, este total será igual a la cantidad consumida. |
|Dentro del compromiso |Muestra los cargos por medidor que se restan del importe comprometido asociado a su oferta de 6 o 12 meses. Los cargos por medidor se restan en orden cronológico. |
|Moneda |Moneda usada en el período de facturación actual. |
|Superávit |Muestra los cargos por medidor que superan el importe comprometido asociado a su oferta de 6 o 12 meses. |
|Tarifa de compromiso |Muestra la tarifa comprometida basada en el importe comprometido total asociado a su oferta de 6 o 12 meses. |
|Tarifa |La tarifa que se le carga por unidad facturable. |
|Valor |Muestra el resultado de multiplicar las columnas Cantidad superior al límite y Tarifa. Si la cantidad consumida no supera la cantidad incluida, no hay ningún cargo en esta columna. |

### <a name="daily-usage"></a>Uso diario

La sección de uso diario del archivo CSV muestra los detalles de uso que afectan a las tarifas de facturación. En la tabla siguiente se enumeran los términos y las descripciones que se muestran en esta sección.

| Término | DESCRIPCIÓN |
| --- | --- |
|Fecha de uso |La fecha en que se usó el medidor. |
|Categoría de medidor |Identifica el servicio de nivel superior al que pertenece este uso. |
|Id. de medidor |Identificador del medidor de facturación empleado para valorar el uso de la facturación. |
|Subcategoría de medidor |Define el tipo de servicio de Azure que puede afectar a la tarifa. |
|Nombre de medidor |Identifica la unidad de medida del medidor que se está utilizando. |
|Medidor de la región |Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos. |
|Unidad |Identifica la unidad en que se cobra el medidor. Por ejemplo, GB, horas, 10.000 s. |
|Cantidad consumida |La cantidad del medidor consumida durante ese día. |
|Ubicación del recurso |Identifica el centro de datos donde se está ejecutando el medidor. |
|Servicio consumido |El servicio de la plataforma Azure que ha usado. |
|Grupo de recursos |El grupo de recursos en el que se ejecuta el medidor implementado. <br/><br/>Para más información, consulte [Información general de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|Id. de instancia | Identificador del medidor. <br/><br/> El identificador contiene el nombre especificado para el medidor cuando se creó. Es el nombre del recurso o el id. de recurso completo. Para más información, consulte [API de Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Etiquetas | Etiqueta asignada al medidor. Use etiquetas para agrupar los registros de facturación.<br/><br/>Por ejemplo, puede usar etiquetas para distribuir los costos por el departamento que utiliza el medidor. Los servicios que admiten la emisión de etiquetas son máquinas virtuales, almacenamiento y servicios de red aprovisionados mediante la [API de Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Para más información, consulte [Organize your Azure resources with tags](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organización de los recursos de Azure con etiquetas). |
|Información adicional |Metadatos específicos del servicio. Por ejemplo, un tipo de imagen de una máquina virtual. |
|Información de servicio 1 |El nombre del proyecto al que pertenece el servicio en la suscripción. |
|Información de servicio 2 |Campo heredado que captura los metadatos específicos del servicio opcional. |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>¿Cómo puedo asegurarme de que los cargos de mi archivo de uso detallado son correctos?
Si hay un cargo en el archivo de uso detallado del que quiera tener más detalles, consulte [Descripción de la factura de Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>¿Qué son los cargos de servicios externos?
Los servicios externos (también conocidos como pedidos de Marketplace) se proporcionan mediante proveedores de servicios independientes y se facturan por separado. Los cargos no se muestran en la factura de Azure. Para aprender más, consulte [Descripción de los gastos de servicios externos de Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?) para resolver el problema rápidamente.
