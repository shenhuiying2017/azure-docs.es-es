---
title: 'IoT Hub: filtro IP | Microsoft Docs'
description: "En este tutorial se muestra cómo crear una lista de direcciones IP permitidas y no permitidas para Azure IoT Hub."
services: iot-hub
documentationcenter: 
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 457d0d97601e18de2a19b83be100954d01f9bbf8
ms.openlocfilehash: 86622918a136da4c478c8d47a677a67e46eb093b


---

# <a name="ip-filter"></a>Filtro IP

La seguridad es un aspecto importante de cualquier solución de IoT basada en Azure IoT Hub. En ciertas ocasiones necesitará crear una lista de direcciones IP permitidas y no permitidas como parte de la configuración de seguridad. La característica de _filtro IP_ le permite configurar reglas para rechazar o aceptar tráfico de direcciones IPv4 específicas.

## <a name="when-to-use"></a>Cuándo se deben usar

Hay dos casos específicos cuando resulta útil bloquear los puntos de conexión de IoT hub para determinadas direcciones IP:

- Cuando el centro de IoT debe recibir tráfico solo de un intervalo concreto de direcciones IP y rechazar todo lo demás. Por ejemplo, cuando se usa el centro de IoT con [Azure ExpressRoute] para crear conexiones privadas entre un centro de IoT y la infraestructura local.
- Cuando necesite rechazar el tráfico de direcciones IP que el administrador del centro de IoT haya identificado como sospechosas.

## <a name="how-filter-rules-are-applied"></a>Cómo se aplican las reglas de filtro

Las reglas de filtro IP se aplican en el nivel de servicio de IoT Hub. Por lo tanto, las reglas de filtro IP se aplican a todas las conexiones de los dispositivos y aplicaciones de back-end mediante un protocolo admitido.

Cualquier intento de conexión desde una dirección IP que coincida con una regla IP de rechazo en su centro de IoT recibe un código de estado 401 no autorizado y la descripción. El mensaje de respuesta no menciona la regla IP.

## <a name="default-setting"></a>Configuración predeterminada
De forma predeterminada, la cuadrícula de **filtro IP** del portal para los centros de IoT está vacía. Esta configuración predeterminada significa que el centro de acepta la conexión de cualquier dirección IP. Esta configuración predeterminada es equivalente a una regla que acepta el intervalo de direcciones IP 0.0.0.0/0.

![][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Incorporación o edición de una regla de filtro IP

Al agregar una regla de filtro IP, le pediremos los siguientes valores:

- Un **nombre de regla de filtro IP** que debe ser una cadena única, que distinta mayúsculas de minúsculas y alfanumérica de hasta 128 caracteres. Solo se aceptan los caracteres alfanuméricos de 7 bits ASCII más `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.
- Seleccione un **Rechazar** o **Aceptar** como **acción** para la regla de filtro IP.
- Proporcione una única dirección IPv4 o un bloque de direcciones IP en la notación CIDR. Por ejemplo, en notación CIDR, 192.168.100.0/22 representa las direcciones IPv4 de 1024 de 192.168.100.0 a 192.168.103.255.

![][img-ip-filter-add-rule]

Tras guardar la regla, se mostrará una alerta que le informará de que la actualización está en curso.

![][img-ip-filter-save-new-rule]

La opción **Agregar** está deshabilitada cuando se alcanza el máximo de diez reglas de filtro IP.

Puede editar una regla existente al hacer doble clic en la fila que la contiene.

## <a name="delete-an-ip-filter-rule"></a>Eliminación de una regla de filtro IP

Para eliminar una regla de filtro IP, seleccione una o varias reglas en la cuadrícula y haga clic en **Eliminar**.

![][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Evaluación de las reglas de filtro IP

Las reglas de filtro IP se aplican en orden y la primera regla que coincida con la dirección IP determina la acción de aceptar o rechazar.

Por ejemplo, si desea aceptar las direcciones del intervalo 192.168.100.0/22 y rechazar todas las demás, la primera regla de la cuadrícula debe aceptar el intervalo de direcciones 192.168.100.0/22. La siguiente regla debe rechazar todas las direcciones mediante el intervalo 0.0.0.0/0. Si agrega una última regla que rechace el intervalo de 0.0.0.0/0, cambiará el comportamiento predeterminado para la creación de listas blancas.

Puede cambiar el orden de las reglas de filtro IP en la cuadrícula al hacer clic en los tres puntos verticales situados al principio de las filas y mediante el método arrastrar y colocar.

Para guardar el nuevo orden de reglas de filtro IP, haga clic en **Guardar**.

![][img-ip-filter-rule-order]

## <a name="next-steps"></a>Pasos siguientes

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Supervisión de operaciones][lnk-monitor]
* [Métricas de IoT Hub][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[Guía para desarrolladores del Centro de IoT]: iot-hub-devguide.md
[Azure ExpressRoute]:  https://azure.microsoft.com/en-us/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md


<!--HONumber=Nov16_HO3-->


