---
title: Integración de datos de la SIM en la solución de supervisión remota (Azure) | Microsoft Azure
description: En este artículo se describe cómo integrar los datos de la SIM de Telefónica en la solución de supervisión remota.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integración de datos de la SIM en la solución de supervisión remota

## <a name="overview"></a>Información general
A menudo, los dispositivos IoT se conectan a la nube con una tarjeta SIM que les permite enviar flujos de datos desde cualquier lugar. La solución de supervisión remota de Azure IoT permite la integración de los datos de administración de la SIM, para que los operadores también puedan realizar un seguimiento del estado del dispositivo mediante los datos proporcionados por la SIM. La supervisión remota proporciona integración lista para usar con IoT de Telefónica, lo que permite a los clientes usar su plataforma de conectividad de IoT que sincroniza sus datos de conectividad de las tarjetas SIM de los dispositivos con las soluciones. Esta solución puede ampliarse para ofrecer compatibilidad con otros proveedores de telefonía mediante el repositorio de GitHub.
En este tutorial, aprenderá a:
* Integración de datos de la SIM en la solución de supervisión remota
* Vista de la telemetría en tiempo real
* Visualización de datos de la SIM 

## <a name="telefonica-iot-integration-setup"></a>Configuración de la integración de IoT de Telefónica

### <a name="prerequisites"></a>requisitos previos
Para sincronizar los datos de conectividad en la solución de supervisión remota de Azure, siga estos pasos:

1.  Rellene una solicitud en el [sitio de Telefónica](https://iot.telefonica.com/contact), seleccione la opción **Supervisión remota de Azure** e incluya sus datos de contacto.
2.  Telefónica activará su cuenta. 
3.  Si aún no es cliente de Telefónica y desea disfrutar de esta ventaja o de otros servicios preparados para la nube con conectividad para IoT, visite el [sitio de Telefónica](https://iot.telefonica.com/contact) y seleccione la opción **Conectividad**.

### <a name="telefonica-sim-setup"></a>Configuración de la SIM de Telefónica
La asociación entre la SIM de Telefónica y el identificador del dispositivo gemelo de Azure se basará en la propiedad del "alias" de la SIM de IoT de Telefónica. 

Vaya al [Portal de la plataforma de conectividad de IoT de Telefónica](https://m2m-movistar-es.telefonica.com/) > Inventario de SIM > Seleccione su SIM y actualice el "alias" de cada SIM con el identificador del dispositivo gemelo deseado. 

Esta tarea también puede realizarse de modo masivo (consulte los manuales de usuario de la plataforma de conectividad de IoT de Telefónica).

![Actualización de Telefónica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Para conectar el dispositivo a la solución de supervisión remota, puede seguir estos tutoriales con [C](iot-suite-connecting-devices-linux.md) o [Nodo](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visualización de las propiedades de la SIM y de la telemetría del dispositivo
Una vez que la cuenta de Telefónica está correctamente configurada y el dispositivo está conectado, puede visualizar los detalles del dispositivo y los datos de la SIM.
Se pueden publicar los siguientes parámetros de conectividad:
* ICCID
* IP
* Presencia de red
* Estado de SIM
* Ubicación basada en la red
* Tráfico de datos consumido

![panel](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Pasos siguientes

Ahora que dispone de información general sobre cómo integrar los datos de la SIM en la solución de supervisión remota de Azure IoT, a continuación se recomiendan los pasos siguientes para los aceleradores de soluciones:

* [Operar con la solución de supervisión remota de Azure IoT](iot-suite-remote-monitoring-explore.md)
* [Supervisión avanzada](iot-suite-remote-monitoring-monitor.md)
* [Administración de dispositivos](iot-suite-remote-monitoring-manage.md)
* [Solución de problemas de dispositivo](iot-suite-remote-monitoring-maintain.md)

