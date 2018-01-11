---
title: "Actualización y escalado de una instancia de Azure API Management | Microsoft Docs"
description: "En este tema, se explica cómo se actualiza y se escala una instancia de Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6ae977344101c02222fd9930e26a083bf5e3f800
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Actualización y escalado de una instancia de API Management 

Los clientes pueden escalar una instancia de API Management (APIM) agregando o quitando unidades. Una **unidad** se compone de recursos de Azure dedicados y tiene cierta capacidad de carga, que se expresa mediante el número de llamadas API que se realizan cada mes. Dicho número no representa un límite de llamadas, sino un valor de rendimiento máximo que permite el planeamiento de la capacidad aproximada. El rendimiento y la latencia reales varían considerablemente en función de factores como el número y la tasa de conexiones concurrentes, el tipo y número de directivas configuradas, los tamaños de las solicitudes y respuestas y la latencia del back-end.

La capacidad y el precio de cada unidad dependen del **nivel** en que se encuentra la unidad. Puede elegir entre cuatro niveles: **Desarrollador**, **Básico**, **Estándar**, **Premium**. Si necesita más capacidad para un servicio de un nivel, debe agregar una unidad. Si el nivel que está seleccionado actualmente en la instancia de APIM no permite agregar más unidades, deberá actualizar a un nivel superior. 

El precio de cada unidad y las características disponibles (por ejemplo, la implementación en varias regiones) dependen del nivel elegido para la instancia de APIM. En el artículo sobre los [precios](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), se explican el precio por unidad y las características que se obtienen en cada nivel. 

>[!NOTE]
>En este artículo sobre los [precios](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), se indica el número aproximado de unidades que puede tener cada nivel. Para obtener unos datos más exactos, deberá consultar un escenario que se ajuste a la realidad de sus API. Consulte a continuación la sección "Planeación de la capacidad".

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos que se describen en este artículo, debe tener:

+ Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Una instancia de APIM. Para más información, consulte [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Planeación de la capacidad

Para determinar si dispone de unidades suficientes para administrar el tráfico, debe realizar pruebas con las cargas de trabajo previstas. 

Tal como se ha mencionado, el número de solicitudes por segundo que una unidad de APIM puede procesar depende de muchas variables. por ejemplo, el patrón de conexión, el tamaño de la solicitud y la respuesta, las directivas configuradas en cada API y el número de clientes que están enviando solicitudes.

Utilice **Métricas** (donde se utilizan las funcionalidades de Azure Monitor) para saber cuánta capacidad se está consumiendo en un momento dado.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Uso de Azure Portal para examinar las métricas 

1. Acceda a la instancia de APIM de [Azure Portal](https://portal.azure.com/).
2. Seleccione **Métricas**.
3. En **Métricas disponibles**, seleccione la métrica **Capacidad**. 

    Esta métrica le da una idea de la cantidad de capacidad de proceso disponible que se usa en el inquilino. Su valor se deriva de los recursos de proceso que utiliza el inquilino como la memoria, la CPU y las longitudes de cola de la red. No es una medida directa del número de solicitudes que se procesan. Puede probar a aumentar la carga de solicitudes en el inquilino y supervisar el valor de la métrica de capacidad que corresponde a la carga máxima. Puede establecer una alerta de métrica que le avise cuando ocurra algo inesperado. Por ejemplo, la instancia de APIM ha superado su capacidad máxima esperada durante más de 10 minutos.

    >[!TIP]
    > Puede configurar alertas que le avisen cuando el servicio se esté quedando sin baja capacidad o llamar a una aplicación lógica que realizar el escalado automático mediante la incorporación de una unidad.

## <a name="upgrade-and-scale"></a>Actualización y escalado 

Como ya se ha mencionado, puede elegir entre cuatro niveles: **Desarrollador**, **Básico**, **Estándar** y **Premium**. El nivel **Desarrollador** debe utilizarse para evaluar el servicio; no debe emplearse para producción. El nivel **Desarrollador** no dispone de un Acuerdo de Nivel de Servicio y no se puede escalar (no se pueden agregar o quitar unidades). 

Los niveles de producción **Básico**, **Estándar** y **Premium** cuentan con un Acuerdo de Nivel de Servicio y se pueden escalar. El nivel **Básico** es el más económico que cuenta con un Acuerdo de Nivel de Servicio y puede escalarse hasta dos unidades. El nivel **Estándar** puede escalarse hasta cuatro unidades. Puede agregar cualquier número de unidades en el nivel **Premium**.

El nivel **Premium** le permite distribuir una instancia de API Management entre cualquier número de regiones de Azure. Inicialmente, cuando se crea un servicio de API Management, la instancia contiene una sola unidad y reside en una única región de Azure. La región inicial se designa como región **primaria**. Pueden agregarse otras regiones fácilmente. Cuando agregue una región, debe especificar el número de unidades que desea asignar. Por ejemplo, puede tener una unidad en la región **primaria** y cinco unidades en otra región. Puede adaptar el número de unidades al tráfico que tiene en cada región. Para más información, consulte [Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md).

Puede cambiar un nivel por otro superior o inferior. Tenga en cuenta que, si cambia a un nivel superior o inferior, es posible que algunas características dejen de estar disponibles, como ocurre con las VNET o las implementaciones en varias regiones cuando se pasa del nivel Premium al nivel Estándar o Básico.

>[!NOTE]
>El proceso de actualización o escalado puede tardar entre 15 y 45 minutos en aplicarse. Recibirá una notificación cuando se haya completado.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Uso de Azure Portal para actualizar y escalar

1. Acceda a la instancia de APIM de [Azure Portal](https://portal.azure.com/).
2. Seleccione **Escalado y precios**.
3. Elija el nivel que desee.
4. Especifique el número de **unidades** que desea agregar. Puede utilizar el control deslizante o escribir directamente el número.<br/>
    Si selecciona el nivel **Premium**, primero tendrá que seleccionar una región.
5. Pulse **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md)

