---
title: "Configuración de una puerta de enlace de aplicaciones para la descarga de SSL mediante el portal | Microsoft Docs"
description: "En esta página se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante el portal"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0105c00b40143981c4f5dba621f91425b80f31cf
ms.openlocfilehash: 2463136502ae37347e0d621902f2b7f93e239af5


---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
> * [Azure Classic PowerShell](application-gateway-ssl.md)
> 
> 

Puerta de enlace de aplicaciones de Azure puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="scenario"></a>Escenario

El siguiente escenario pasa por la configuración de la descarga SSL en una puerta de enlace de aplicaciones existente. El escenario supone que ya ha seguido los pasos para [crear una Puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de empezar

Para configurar la descarga SSL con una puerta de enlace de aplicaciones, se requiere un certificado. Este certificado se carga en la puerta de enlace de aplicaciones y se usa para cifrar y descifrar el tráfico enviado a través de SSL. El certificado debe estar en formato Intercambio de información personal (.pfx). Este formato de archivo permite la exportación de la clave privada, necesaria para que la puerta de enlace de aplicaciones realice el cifrado y descifrado del tráfico.

## <a name="add-an-https-listener"></a>Incorporación de un agente de escucha HTTPS

El agente de escucha HTTPS busca tráfico en función de su configuración y ayuda a enrutar el tráfico a los grupos de back-end.

### <a name="step-1"></a>Paso 1

Vaya a Azure Portal y seleccione una puerta de enlace de aplicaciones existente.

### <a name="step-2"></a>Paso 2

Haga clic en Agentes de escucha y en el botón Agregar para agregar un agente de escucha.

![hoja de información general de puerta de enlace de aplicaciones][1]

### <a name="step-3"></a>Paso 3

Rellene la información necesaria para el agente de escucha y cargue el certificado .pfx. Cuando haya terminado, haga clic en Aceptar.

**Nombre** : este valor es un nombre descriptivo del agente de escucha.

**Configuración de direcciones IP de front-end** : este valor es la configuración de direcciones IP de front-end usada para el agente de escucha.

**Puerto front-end (nombre/puerto)** : un nombre descriptivo para el puerto usado en el front-end de la puerta de enlace de aplicaciones y el puerto real usado.

**Protocolo** : un modificador para determinar si se usa https o http para el front-end.

**Certificado (nombre/contraseña)** : si se usa la descarga SSL, un certificado .pfx se requiere para esta configuración, al igual que un nombre y contraseña descriptivos.

![agregar hoja del agente de escucha][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Creación de una regla y asociación de la misma al agente de escucha

Ahora se ha creado el agente de escucha. Es hora de crear una regla para controlar el tráfico desde el agente de escucha.

### <a name="step-1"></a>Paso 1

Haga clic en las **reglas** de la puerta de enlace de aplicaciones y, a continuación, en Agregar.

![hoja de reglas de puerta de enlace de aplicaciones][3]

### <a name="step-2"></a>Paso 2

En la hoja **Agregar regla básica** , escriba el nombre descriptivo para la regla y elija el agente de escucha creado en el paso anterior. Elija el grupo de back-end y la configuración HTTP adecuados y haga clic en **Aceptar**

![ventana de configuración de https][4]

Ahora, la configuración está guardada en la puerta de enlace de aplicaciones. El proceso de guardar para esta configuración puede tardar un rato antes de que pueda verse a través del portal o de PowerShell. Una vez guardada, la puerta de enlace de aplicaciones controla el cifrado y descifrado del tráfico. Todo el tráfico entre la puerta de enlace de aplicaciones y los servidores web de back-end se controlará mediante http. Cualquier comunicación dirigida de nuevo al cliente iniciada mediante https se devolverá al cliente cifrado.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar un sondeo de estado personalizado con Puerta de enlace de aplicaciones de Azure, consulte la sección sobre cómo [crear un sondeo de estado personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png



<!--HONumber=Nov16_HO3-->


