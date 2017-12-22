---
title: "Creación un sondeo personalizado para Azure Application Gateway mediante Azure Portal | Microsoft Docs"
description: "Más información acerca de la creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-probe-portal.md)
> * [PowerShell de Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

En este artículo, agregará un sondeo personalizado a una puerta de enlace de aplicaciones existente a través de Azure Portal. Los sondeos personalizados son útiles para aplicaciones que tienen una página de comprobación del estado o para aplicaciones que no proporcionan una respuesta correcta en la aplicación web predeterminada.

## <a name="before-you-begin"></a>Antes de empezar

Si no dispone aún de una puerta de enlace de aplicaciones, visite [Creación de una puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md) para crear una puerta de enlace de aplicaciones con la que trabajar.

## <a name="createprobe"></a>Creación del sondeo

Los sondeos se configuran en un proceso de dos pasos a través del portal. El primer paso consiste en crear el sondeo. En el segundo paso, agregue el sondeo a la configuración HTTP de back-end de la puerta de enlace de aplicaciones.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free).

1. En el panel Favoritos de Azure Portal, haga clic en Todos los recursos. Haga clic en la puerta de enlace de aplicaciones en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir partners.contoso.net en el cuadro Filtrar por nombre… para acceder fácilmente a la puerta de enlace de la aplicación.

1. Haga clic en **Sondeos** y, después, en el botón **Agregar** para agregar un sondeo.

  ![Agregar hoja Sondeo con la información rellena][1]

1. En la hoja **Agregar sondeo de estado**, rellene la información necesaria para el sondeo y cuando finalice, haga clic en **Aceptar**.

  |**Configuración** | **Valor** | **Detalles**|
  |---|---|---|
  |**Name**|customProbe|Este valor es un nombre descriptivo para el sondeo al que se puede acceder en el portal.|
  |**Protocolo**|HTTP o HTTPS | El protocolo que usa el sondeo de estado.|
  |**Host**|es decir, contoso.com|Este valor es el nombre de host que se utiliza para el sondeo. Solo se puede aplicar cuando se ha configurado un entorno multisitio en Application Gateway; de lo contrario hay que usar '127.0.0.1'. Este valor es distinto del nombre de host de máquina virtual.|
  |**Ruta de acceso**|/ u otra ruta de acceso|El resto de la dirección URL completa del sondeo personalizado. Las rutas de acceso válidas comienzan por '/'. Para la ruta de acceso predeterminada de http://contoso.com, use solo '/'. |
  |**Intervalo (segundos)**|30|La frecuencia con que se ejecuta el sondeo para comprobar el estado. No se recomienda establecer un valor inferior a 30 segundos.|
  |**Tiempo de espera (segundos)**|30|El período que espera el sondeo antes de agotarse el tiempo de espera. El intervalo de tiempo de espera debe ser lo suficientemente alto como para que se pueda realizar una llamada http para asegurarse de que la página de mantenimiento de back-end está disponible.|
  |**Umbral incorrecto**|3|Número de intentos con error para que se considere incorrecto. Un umbral de 0 significa que si el resultado de una comprobación de mantenimiento no es satisfactorio, se determinará de inmediato que el back-end es incorrecto.|

  > [!IMPORTANT]
  > El nombre de host no es el mismo que el del servidor. Este valor es el nombre del host virtual que se ejecuta en el servidor de aplicaciones. El sondeo se envía a http://(nombre de host):(puerto de httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Adición de un sondeo a la puerta de enlace

Una vez creado el sondeo, es el momento de agregarlo a la puerta de enlace. La configuración del sondeo se establece en la configuración de http del back-end de la puerta de enlace de aplicaciones.

1. Haga clic en la **configuración de HTTP** de la instancia de Application Gateway para abrir la hoja Configuración y haga clic en la configuración de HTTP del back-end actual que aparece en la ventana.

  ![ventana de configuración de https][2]

1. En la hoja de configuración de **appGatewayBackEndHttpSettings**, active la casilla **Usar sondeo personalizado** y elija el sondeo creado en la sección [Creación del sondeo](#createprobe) en el menú desplegable **Sondeo personalizado**.
Cuando haya terminado, haga clic en **Guardar** y se aplicará la configuración.

El sondeo predeterminado comprueba el acceso predeterminado a la aplicación web. Una vez que se ha creado el sondeo personalizado, la puerta de enlace de aplicaciones usa la ruta de acceso personalizada definida para supervisar el estado de los servidores backend. En función de los criterios definidos, la puerta de enlace de aplicaciones comprueba la ruta de acceso especificada en el sondeo. Si la llamada a host:Port/path no devuelve una respuesta de estado HTTP 200-399, el servidor se saca de la rotación cuando se alcanza el umbral de estado incorrecto. El sondeo continúa en la instancia incorrecta para determinar cuándo vuelve a ser correcta. Una vez que la instancia se vuelve a agregar al grupo de servidores correcto, el tráfico comienza a fluir hacia ella de nuevo y el sondeo de la instancia continúa en el intervalo especificado por el usuario de forma normal.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar la descarga de SSL con Azure Application Gateway, consulte [Configuración de la descarga SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

