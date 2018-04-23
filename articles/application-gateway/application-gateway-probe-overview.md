---
title: Introducción al seguimiento de estado para Azure Application Gateway
description: Aprenda sobre las funciones de supervisión de la puerta de enlace de aplicaciones de Azure
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2018
ms.author: victorh
ms.openlocfilehash: 2f62f01c1178f9529eb46051f088affccc5279a7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="application-gateway-health-monitoring-overview"></a>Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones

La puerta de enlace de aplicaciones de Azure supervisa de forma predeterminada el estado de todos los recursos de su grupo de back-end y elimina automáticamente del grupo los recursos que se considera que están en mal estado. Además, continúa supervisando las instancias en mal estado y las agrega de nuevo al grupo de back-end en buen estado, una vez que están disponibles y responden a los sondeos de estado. Application Gateway envía los sondeos de mantenimiento con el mismo puerto que se define en la configuración de HTTP de back-end. Esta configuración garantiza que el sondeo prueba el mismo puerto que utilizarían los clientes para conectarse al back-end.

![ejemplo de sondeo de Application Gateway][1]

Aparte del uso de la supervisión del sondeo de estado, también puede personalizar el sondeo de estado para adaptarlo a las necesidades de su aplicación. En este artículo trataremos ambos sondeos de estado: el personalizado y el predeterminado.

> [!NOTE]
> Si hay un grupo de seguridad de red en la subred de Application Gateway, se deberían abrir los intervalos de puertos 65503-65534 en la subred de Application Gateway destinada al tráfico de entrada. Estos puertos son necesarios para que funcione correctamente la API de estado de back-end.

## <a name="default-health-probe"></a>Sondeo de estado predeterminado

Una puerta de enlace de aplicaciones configura automáticamente un sondeo de estado predeterminado cuando no hay ningún sondeo personalizado configurado. El comportamiento de supervisión consiste en realizar una solicitud HTTP a las direcciones IP configuradas para el grupo de back-end. En el caso de los sondeos predeterminados si las opciones de HTTP del back-end se configuran para HTTPS, el sondeo usa HTTPS también para comprobar el mantenimiento de los back-ends.

Por ejemplo: configura la puerta de enlace de aplicaciones para usar los servidores back-end A, B y C para recibir el tráfico de red HTTP en el puerto 80. La supervisión de estado predeterminada comprueba los tres servidores cada 30 segundos para ver que la respuesta de HTTP es correcta. Una respuesta HTTP correcta tiene un [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 y 399.

Si la comprobación de sondeo predeterminado da error en el servidor A, la puerta de enlace de aplicaciones lo elimina de su grupo de back-end y el tráfico de red deja de fluir a este servidor. El sondeo predeterminado sigue comprobando el servidor A cada 30 segundos. Cuando el servidor A responde correctamente a una solicitud de un sondeo de estado predeterminado, se considera que está en buen estado y se agrega de nuevo al grupo de back-end; el tráfico comienza a fluir de nuevo a él.

### <a name="probe-matching"></a>Sondeo de búsqueda de coincidencia

De forma predeterminada, una respuesta HTTP (S) con el código de estado 200 se considera correcta. Los sondeos de estado personalizados admiten además dos criterios de coincidencia. Los criterios de coincidencia pueden usarse para modificar opcionalmente la interpretación predeterminada de lo que constituye una respuesta correcta.

Estos son los criterios de coincidencia: 

- **Coincidencia de código de estado de respuesta HTTP**: criterio de coincidencia de sondeo para aceptar el código de respuesta, o los intervalos de códigos de respuesta, HTTP especificados por el usuario. Se admiten códigos de estado de respuesta individuales, o un intervalo de códigos de estado de respuesta, separados por coma.
- **Coincidencia de cuerpo de respuesta HTTP**: criterio de coincidencia de sondeo que examina el cuerpo de la respuesta HTTP y busca la coincidencia con una cadena especificada por el usuario. Tenga en cuenta que la coincidencia solo busca la existencia de la cadena especificada por el usuario en el cuerpo de la respuesta, no es una coincidencia de expresión regular completa.

Los criterios de coincidencia se pueden especificar mediante el cmdlet `New-AzureRmApplicationGatewayProbeHealthResponseMatch`.

Por ejemplo: 

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Una vez que se especifican los criterios de coincidencia, se pueden asociar a la configuración de sondeo con un parámetro `-Match` en PowerShell.

### <a name="default-health-probe-settings"></a>Configuración de sondeo de estado predeterminado

| Propiedad de sondeo | Valor | DESCRIPCIÓN |
| --- | --- | --- |
| Dirección URL de sondeo |http://127.0.0.1:\<port\>/ |Ruta de acceso URL |
| Intervalo |30 |Intervalo de sondeo en segundos |
| Tiempo de espera |30 |Tiempo de espera del sondeo en segundos |
| Umbral incorrecto |3 |Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |

> [!NOTE]
> El puerto es el mismo que la configuración de HTTP del back-end.

El sondeo predeterminado solo examina la propiedadhttp://127.0.0.1:\<port\> para determinar el estado de mantenimiento. Si necesita configurar el sondeo de estado para ir a una dirección URL personalizada o modificar alguna otra configuración, debe usar sondeos personalizada tal como se describe en los siguientes pasos:

## <a name="custom-health-probe"></a>Sondeo de estado personalizado

Los sondeos personalizados permiten un control más específico sobre la supervisión de estado. Cuando se usan sondeos personalizados, puede configurar el intervalo de sondeo, la dirección URL y la ruta de acceso a la comprobación y cuántas respuestas erróneas se aceptan antes de marcar la instancia del grupo de back-end como en mal estado.

### <a name="custom-health-probe-settings"></a>Configuración de sondeo de estado personalizado

La siguiente tabla proporciona definiciones de las propiedades de un sondeo de mantenimiento personalizado.

| Propiedad de sondeo | DESCRIPCIÓN |
| --- | --- |
| NOMBRE |Nombre del sondeo. Este nombre se usa para hacer referencia al sondeo en la configuración de HTTP de back-end. |
| Protocolo |Protocolo usado para enviar el sondeo. El sondeo utiliza el protocolo definido en la configuración de HTTP del back-end. |
| Host |Nombre de host para enviar el sondeo. Solo se puede aplicar cuando se ha configurado un entorno multisitio en Application Gateway; de lo contrario hay que usar '127.0.0.1'. Este valor es distinto del nombre de host de máquina virtual. |
| Ruta de acceso |Ruta de acceso relativa del sondeo. La ruta de acceso válida se inicia desde '/'. |
| Intervalo |Intervalo de sondeo en segundos. Este valor es el intervalo de tiempo entre dos sondeos consecutivos. |
| Tiempo de espera |Tiempo de espera del sondeo en segundos. Si no se recibe una respuesta válida dentro del período de tiempo de espera, el sondeo se marca como error.  |
| Umbral incorrecto |Número de reintentos de sondeo. El servidor back-end se marca como inactivo después de que el número de errores de sondeo consecutivos alcanza el umbral incorrecto. |

> [!IMPORTANT]
> Si la instancia de Puerta de enlace de aplicaciones está configurada para un único sitio, de forma predeterminada, el nombre de host debe especificarse como 127.0.0.1, salvo que se configure de otra manera en la sonda personalizada.
> A modo de referencia se envía un sondeo personalizado a \<protocolo\>://\<host\>:\<puerto\>\<ruta de acceso\>. El puerto usado será el definido en la configuración de HTTP del back-end.

## <a name="next-steps"></a>Pasos siguientes
Tras conocer todo lo referente a la supervisión del mantenimiento de Application Gateway, puede configurar un [sondeo de mantenimiento personalizado](application-gateway-create-probe-portal.md) en Azure Portal o un [sondeo de mantenimiento personalizado](application-gateway-create-probe-ps.md) mediante PowerShell y el modelo de implementación con Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
