---
title: Uso de los informes de Cost Management en Azure Cost Management | Microsoft Docs
description: "En este artículo se describe cómo usar diversos informes de Cost Management en el portal de Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/29/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 92bb4f2a6458057613bdbcb749026781111a778d
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="use-cost-management-reports"></a>Uso de los informes de Cost Management

En este artículo se describe cómo usar diversos informes de Cost Management en el portal de Cloudyn. La mayoría de los informes de Cloudyn son intuitivos y tienen un aspecto uniforme. Para obtener información general sobre los informes de Cloudyn, consulte [Descripción de los informes de costos](understading-cost-reports.md). En el artículo también se describen diversas opciones y campos usados en la mayoría de los informes.

## <a name="cost-analysis-reports"></a>Informes de análisis de costos

En los informes de análisis de costos se muestran datos de facturación de los proveedores de nube. Con los informes, puede agrupar y profundizar en diversos segmentos de datos detallados en el archivo de facturación. Los informes permiten la navegación de costos pormenorizados por los datos de facturación sin procesar de los proveedores de la nube.

Los informes de análisis de costos no agrupan los costos por etiquetas. La creación de informes basada en etiquetas solo está disponible en los informes de asignación de costos establecidos tras crear un modelo de costo mediante la asignación de costos 360.

### <a name="actual-cost-analysis"></a>Análisis del costo real

En el informe de análisis del costo real se muestran sus colaboradores de costos principales, incluidos los costos en curso y los cargos únicos.

 Use el informe de análisis del costo real para:

- Analice y supervise los costos reales gastados durante un período de tiempo especificado
- Programar una alerta de umbral
- Análisis de informes de visualización y contracargo de gastos

#### <a name="to-use-the-actual-cost-analysis-report"></a>Para usar el informe de análisis del costo real

Como mínimo, lleve a cabo los siguientes pasos. También puede usar otras opciones y campos.

1. Seleccione un intervalo de fechas.
2. Seleccione un filtro.

Puede hacer clic con el botón derecho en los resultados del informe para profundizar en ellos y ver información más detallada.

![Ejemplo de informe de análisis del costo real](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Costo real a lo largo del tiempo

El informe de costo real a lo largo del tiempo es un informe de análisis del costo estándar que distribuye el costo en una resolución temporal definida. En el informe se muestra el gasto a lo largo del tiempo, lo que le permite observar tendencias y detectar irregularidades relativas al gasto. En este informe se muestran sus colaboradores de costos principales, incluidos los costos en curso y los cargos únicos de las instancias reservadas que se gastan durante un período de tiempo seleccionado.

Use el informe de costo real a lo largo del tiempo para:

- Ver las tendencias de costos a lo largo del tiempo.
- Buscar irregularidades en el costo.
- Buscar todas las preguntas relacionadas con el costo relativas a Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Para usar el informe de costo real a lo largo del tiempo:

Como mínimo, lleve a cabo los siguientes pasos. También puede usar otras opciones y campos.

- Seleccione un intervalo de fechas.

Por ejemplo, puede seleccionar grupos para ver su costo a lo largo del tiempo y, a continuación, agregar filtros para acotar sus resultados.

![Ejemplo de informe de costo real a lo largo del tiempo](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Informes de costo amortizado

En este conjunto de informes de costo amortizado se muestran los cargos por servicios no basados en el uso linealizados o los costos pagaderos una sola vez y se reparte su costo a lo largo del tiempo de manera uniforme durante su vida útil.

Por ejemplo, los cargos únicos podrían incluir:

- Cargos anuales por soporte técnico
- Cargos anuales por componentes de seguridad
- Cargos de compra de instancias reservadas
- Algunos elementos de Azure Marketplace

En el archivo de facturación, los cargos únicos se aplican en los casos en que la marca de tiempo o las fechas de inicio y finalización del consumo de servicios tienen los mismos valores. Cloudyn los reconoce entonces como cargos únicos que se pueden amortizar. Otros servicios basados en el consumo con costos de uso a petición no se pueden amortizar.

Para ilustrar los costos amortizados, revise la siguiente imagen de ejemplo de un informe de costo real a lo largo del tiempo. En el ejemplo, muestra un incremento del costo el 23 de agosto. Podría parecer una anomalía en comparación con la tendencia de costos diaria habitual. El análisis de causa raíz y la navegación de datos identificaron este costo como reserva de APN del servicio de AWS anual, que es un cargo único adquirido y facturado en ese día. Puede ver cómo se amortiza este costo en la siguiente sección.

![Ejemplo de informe de costo real a lo largo del tiempo en el que se muestra un costo único](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Para usar el informe de costo amortizado a lo largo del tiempo:

Como mínimo, lleve a cabo los siguientes pasos. También puede usar otras opciones y campos.

1. Seleccione un intervalo de fechas.
2. Seleccione un servicio y un proveedor.

Si sigue adelante con el ejemplo anterior, puede ver que el costo único se amortiza ahora en la siguiente imagen:

![Ejemplo de informe de costo amortizado a lo largo del tiempo](./media/use-reports/amort-cost-over-time.png)

En la imagen anterior se muestra el costo amortizado para el costo de reserva de APN a lo largo del tiempo. En este informe se muestra la amortización de los cargos únicos y el costo APN como adquisición de reserva anual. El costo APN se distribuye de manera uniforme cada día como 1/365 del gasto inicial de reserva.

## <a name="cost-allocation-analysis-reports"></a>Informes de análisis de asignación de costos

Los informes de análisis de asignación de costos están disponibles tras crear un modelo de costos mediante la asignación de costos 360. Cloudyn procesa datos de costos o facturación y hace coincidir los datos con los datos de uso y etiqueta datos de sus cuentas en la nube. Para hacer coincidir los datos, Cloudyn requiere acceso a sus datos de uso. Las cuentas que carecen de credenciales se etiquetan como recursos no categorizados.

### <a name="cost-analysis-report"></a>Informe de análisis de costos

En el informe de análisis de costos se proporciona información sobre el consumo y el gasto en la nube durante un período de tiempo seleccionado. Las directivas establecidas en el administrador de asignación de costos se usan en el informe de análisis de costos.

¿Cómo calcula Cloudyn este informe?

Cloudyn garantiza que la asignación retenga la integridad de cada cuenta vinculada aplicando afinidad de la cuenta. La afinidad garantiza que una cuenta que no usa un servicio específico no tenga ningún costo de este servicio asignado a ella. Los costos acumulados en esa cuenta permanecen allí y las directivas de asignación no los calculan. Por ejemplo, podría tener cinco cuentas vinculadas. Si solo tres de ellas usan servicios de almacenamiento, el costo de los servicios de almacenamiento solo se asigna entre etiquetas en las tres cuentas.

 Use el informe de análisis de costos para:

- Mostrar una vista agregada de toda su implementación durante un período de tiempo específico.
- Consultar los costos por categorías de etiquetas en función de las directivas creadas en el modelo de costos.

#### <a name="to-use-the-cost-analysis-report"></a>Para usar el informe de análisis de costos:

1. Seleccione un intervalo de fechas.
2. Agregue etiquetas, según sea necesario.
3. Agregue grupos.
4. Elija un modelo de costos creado anteriormente.

En la siguiente imagen se muestra un informe de análisis de costos de ejemplo en formato de proyección solar. Los anillos muestran grupos. En el anillo externo se muestra Servicio y en el círculo interno se muestra Unidad.

![Ejemplo de informe de análisis de costos](./media/use-reports/cost-analysis01.png)



A continuación se muestra un ejemplo de la misma información en una vista de tabla.

![Ejemplo de informe de análisis de costos](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Informe de costo a lo largo del tiempo

En el informe de costo a lo largo del tiempo se muestra el gasto a lo largo del tiempo, por lo que puede observar tendencias y detectar irregularidades en su implementación. En el se muestran básicamente los costos distribuidos a lo largo de un período definido. En el informe se muestran sus colaboradores de costos principales, incluidos los costos en curso y los cargos únicos de las instancias reservadas que se gastan durante un período de tiempo seleccionado. Las directivas establecidas en el administrador de costos 360° se pueden usar en este informe.

Use el informe de costo a lo largo del tiempo para:

- Consultar los cambios a lo largo del tiempo y qué influencias cambian de un día (o intervalo de fechas) para otro.
- Analizar los costos a lo largo del tiempo para una instancia específica.
- Comprender por qué hubo un aumento del costo para una instancia específica.

#### <a name="to-use-the-cost-over-time-report"></a>Para usar el informe de costo a lo largo del tiempo:

1. Seleccione un intervalo de fechas.
2. Agregue etiquetas, según sea necesario.
3. Agregue grupos.
4. Elija un modelo de costos creado anteriormente.
5. Seleccione los costos reales o amortizados.
6. Elija si desea aplicar reglas de asignación para consultar la vista de datos de facturación sin formato o recalcular el costo mediante la vista de Cloudyn.

Este es un ejemplo del informe.

![Ejemplo de costo a lo largo del tiempo](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Pasos siguientes

- Si aún no ha completado el primer tutorial de Cost Management, léalo en [Revisión del uso y los costos](tutorial-review-usage.md).
