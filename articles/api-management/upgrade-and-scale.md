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
ms.openlocfilehash: 22cc917eb6f296724bf535e48b0dd6ba8927e5d3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Actualización y escalado de una instancia de API Management 

Los clientes pueden escalar una instancia de API Management (APIM) agregando o quitando unidades. Una **unidad** se compone de recursos de Azure dedicados y tiene cierta capacidad de carga, que se expresa mediante el número de llamadas API que se realizan cada mes. El rendimiento y la latencia real variarán considerablemente dependiendo de diferentes factores, como el número y la velocidad de las conexiones simultáneas; el tipo y el número de directivas configuradas; el tamaño de la solicitud y la respuesta, y la latencia de back-end. 

La capacidad y el precio de cada unidad varían en función del **nivel** en que se encuentra la unidad. Puede elegir entre tres niveles: **Desarrollador**, **Estándar**, **Premium**. Si necesita más capacidad para un servicio de un nivel, debe agregar una unidad. Si el nivel que está seleccionado actualmente en la instancia de APIM no permite agregar más unidades, deberá actualizar a un nivel superior. 

El precio de cada unidad, la capacidad para agregar y quitar unidades, la presencia o ausencia de ciertas características (por ejemplo, las implementaciones para varias regiones) dependen del nivel elegido para la instancia de APIM. En este artículo sobre los [precios](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), se explica en detalle el precio de cada unidad y las características disponibles en cada nivel. 

>[!NOTE]
>En este artículo sobre los [precios](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), se indica el número aproximado de unidades que puede tener cada nivel. Para obtener unos datos más exactos, deberá consultar un escenario que se ajuste a la realidad de sus API. Consulte a continuación la sección "Planeación de la capacidad".

## <a name="prerequisites"></a>Requisitos previos

Para seguir los pasos que se describen en este artículo, debe tener:

+ Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Una instancia de APIM. Para más información, consulte [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

## <a name="how-to-plan-for-capacity"></a>Planeación de la capacidad

Para determinar si dispone de unidades suficientes para administrar el tráfico, debe realizar pruebas con las cargas de trabajo previstas. 

Tal y como se mencionó anteriormente, el número de solicitudes por segundo que APIM puede procesar depende de muchas variables; por ejemplo, el patrón de conexión, el tamaño de la solicitud y la respuesta, las directivas configuradas en cada API y el número de clientes que están enviando solicitudes.

Utilice **Métricas** (donde se utilizan las funcionalidades de Azure Monitor) para saber cuánta capacidad se está consumiendo en un momento dado.

### <a name="use-the-azure-portal-to-examine-metrics"></a>Uso de Azure Portal para examinar las métricas 

1. Acceda a la instancia de APIM de [Azure Portal](https://portal.azure.com/).
2. Seleccione **Métricas**.
3. En **Métricas disponibles**, seleccione la métrica **Capacidad**. 

    Esta métrica le da una idea de la capacidad que se está consumiendo en el inquilino. Puede poner a prueba la capacidad y aumentar más y más la carga para determinar cuál es la carga máxima. Puede configurar una alerta de la métrica que le avise cuando ocurra algún suceso inesperado; por ejemplo, si una instancia de APIM supera la capacidad durante más de 5 minutos. 

    >[!TIP]
    > Puede configurar una alerta que le avise cuando el servicio se esté ejecutando con una baja capacidad o que llame a una aplicación lógica que escalará automáticamente el servicio incorporando una unidad.

## <a name="upgrade-and-scale"></a>Actualización y escalado 

Tal y como se mencionó anteriormente, puede elegir entre tres niveles: **Desarrollador**, **Estándar** y **Premium**. El nivel **Desarrollador** debe utilizarse para evaluar el servicio; no debe emplearse para producción. El nivel **Desarrollador** no dispone de un Acuerdo de Nivel de Servicio y no se puede escalar (no se pueden agregar o quitar unidades). 

Los niveles de producción **Estándar** y **Premium** cuentan con un Acuerdo de Nivel de Servicio y se pueden escalar. El nivel **Estándar** puede escalarse con hasta cuatro unidades. Puede agregar cualquier número de unidades en el nivel **Premium**. 

El nivel **Premium** le permite distribuir una instancia de API Management entre cualquier número de regiones de Azure. Inicialmente, cuando se crea un servicio de API Management, la instancia contiene una sola unidad y reside en una única región de Azure. La región inicial se designa como región **primaria**. Pueden agregarse otras regiones fácilmente. Cuando agregue una región, debe especificar el número de unidades que desea asignar. Por ejemplo, puede tener una unidad en la región **primaria** y cinco unidades en otra región. Puede adaptarlo en función del tráfico que haya en cada región. Para más información, consulte [Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md).

Puede cambiar un nivel por otro superior o inferior. Tenga en cuenta que, si cambia a un nivel inferior, es posible que algunas características dejen de estar disponibles, como ocurre con las VNET o las implementaciones en varias regiones cuando se pasa del nivel Premium al nivel Estándar.

>[!NOTE]
>El proceso de actualización o escalado puede tardar entre 15 y 30 minutos en aplicarse. Recibirá una notificación cuando se haya completado.

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Uso de Azure Portal para actualizar y escalar

1. Acceda a la instancia de APIM de [Azure Portal](https://portal.azure.com/).
2. Seleccione **Escalado y precios**.
3. Elija el nivel que desee.
4. Especifique el número de **unidades** que desea agregar. Puede utilizar el control deslizante o escribir directamente el número.<br/>
    Si selecciona el nivel **Premium**, primero tendrá que seleccionar una región.
5. Pulse **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de una instancia del servicio Azure API Management en varias regiones de Azure](api-management-howto-deploy-multi-region.md)

