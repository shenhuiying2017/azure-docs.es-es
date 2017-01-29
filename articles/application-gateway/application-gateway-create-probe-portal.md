---
title: "Creación de un sondeo personalizado para Application Gateway mediante el portal | Microsoft Docs"
description: "Más información acerca de la creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: aaf13418331f29287399621cb911e4b9f5b33dc0
ms.openlocfilehash: 0fafaff220dfb5d224bc5b57b630f8b2912155f7


---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal
> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-create-probe-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Escenario

El siguiente escenario pasa por la creación de un sondeo del estado personalizado en una puerta de enlace de aplicaciones existente.
En este escenario se asume que ya ha seguido los pasos para [crear una puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md).

## <a name="a-namecreateprobeacreate-the-probe"></a><a name="createprobe"></a>Creación del sondeo

Los sondeos se configuran en un proceso de dos pasos a través del portal. El primer paso consiste en crear el sondeo. Después, agregue el sondeo a la configuración HTTP de back-end de Application Gateway.

### <a name="step-1"></a>Paso 1

Vaya a [Azure Portal](http://portal.azure.com) y seleccione una Application Gateway existente.

![Introducción a Puerta de enlace de aplicaciones][1]

### <a name="step-2"></a>Paso 2

Haga clic en **Sondeos** y, después, en el botón **Agregar** para agregar un sondeo.

![Agregar hoja Sondeo con la información rellena][2]

### <a name="step-3"></a>Paso 3

Rellene la información necesaria para el sondeo y cuando finalice, haga clic en **Aceptar**.

* **Nombre**: este valor es un nombre descriptivo para el sondeo al que se puede acceder en el portal.
* **Host**: este valor es el nombre de host que se utiliza para el sondeo. Solo se puede aplicar cuando se ha configurado un entorno multisitio en Application Gateway; de lo contrario hay que usar '127.0.0.1'. Este valor es distinto del nombre de host de máquina virtual.
* **Ruta** : el resto de la dirección URL completa del sondeo personalizado. Las rutas de acceso válidas comienzan por '/'
* **Intervalo (s)** : la frecuencia con que se ejecuta el sondeo para comprobar el estado. No se recomienda establecer un valor inferior a 30 segundos.
* **Tiempo de espera (s)** : el periodo que espera el sondeo antes de agotarse el tiempo de espera. El intervalo de tiempo de espera debe ser lo suficientemente alto como para que se pueda realizar una llamada http para asegurarse de que la página de mantenimiento de back-end está disponible.
* **Umbral incorrecto** : número de intentos con error para que se considere incorrecto. Un umbral de 0 significa que si el resultado de una comprobación de mantenimiento no es satisfactorio, se determinará de inmediato que el back-end es incorrecto.

> [!IMPORTANT]
> El nombre de host no es el mismo que el del servidor. Este valor es el nombre del host virtual que se ejecuta en el servidor de aplicaciones. El sondeo se envía a http://(nombre de host):(puerto de httpsetting)/urlPath

![valores de configuración de sondeo][3]

## <a name="add-probe-to-the-gateway"></a>Adición de un sondeo a la puerta de enlace

Una vez creado el sondeo, es el momento de agregarlo a la puerta de enlace. La configuración del sondeo se establece en la configuración de http del back-end de la puerta de enlace de aplicaciones.

### <a name="step-1"></a>Paso 1

Haga clic en la **configuración de HTTP** de la instancia de Application Gateway para abrir la hoja Configuración y haga clic en la configuración de HTTP del back-end actual en la ventana.

![ventana de configuración de https][4]

### <a name="step-2"></a>Paso 2

En la hoja de configuración de **appGatewayBackEndHttp**, haga clic en **Use custom probe** (Usar sondeo personalizado) y elija el sondeo creado en la sección [Creación del sondeo](#createprobe).
Cuando haya terminado, haga clic en **Aceptar** y se aplicará la configuración.

![hoja de configuración de appgatewaybackend][5]

El sondeo predeterminado comprueba el acceso predeterminado a la aplicación web. Una vez que se ha creado el sondeo personalizado, la puerta de enlace de aplicaciones usa la ruta de acceso personalizada definida para supervisar el estado del backend seleccionado. En función de los criterios definidos, la puerta de enlace de aplicaciones comprueba el archivo especificado en el sondeo. Si la llamada a host:Port/path no devuelve una respuesta de estado Http 200 OK, el servidor se saca de la rotación cuando se alcanza el umbral de estado incorrecto. El sondeo continúa en la instancia incorrecta para determinar cuándo vuelve a ser correcta. Una vez que la instancia se vuelve a agregar al grupo de servidores correcto, el tráfico comienza a fluir hacia ella de nuevo y el sondeo de la instancia continúa en el intervalo especificado por el usuario de forma normal.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar la descarga de SSL con Azure Application Gateway, consulte [Configuración de la descarga SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png



<!--HONumber=Dec16_HO3-->


