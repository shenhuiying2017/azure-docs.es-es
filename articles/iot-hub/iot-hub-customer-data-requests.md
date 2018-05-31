---
title: Características de solicitud de datos de cliente
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 3af76fe22e93b6c5d502733196994bda61b9a93e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361522"
---
# <a name="summary-of-customer-data-request-features"></a>Resumen de las características de solicitud de datos de cliente

Azure IoT Hub es un servicio en la nube basado en la API de REST destinado a los clientes de empresas que permite una comunicación segura y bidireccional entre millones de dispositivos y un servicio de Azure con particiones.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Un administrador de inquilinos asigna un número de identificador (id. de dispositivo) a cada dispositivo. Los datos del dispositivo se basan en el id. de dispositivo asignado. Microsoft no retiene ninguna información y no tiene acceso a datos que permitan la correlación entre el id. de dispositivo y el usuario.

Muchos de los dispositivos administrados en Azure IoT Hub no son dispositivos personales; por ejemplo, el termostato de una oficina o el robot de una fábrica. Sin embargo, los clientes pueden considerar que algunos dispositivos se pueden identificar a nivel personal y, a su entera discreción, pueden mantener sus propios métodos de seguimiento de activos o de inventario que vinculan dispositivos a personas. Azure IoT Hub administra y almacena todos los datos asociados con dispositivos como si fueran datos personales.

Los administradores de inquilinos pueden usar Azure Portal o las API de REST del servicio para atender las solicitudes de información mediante la exportación o eliminación de los datos asociados con un id. de dispositivo.

Si usa la característica de enrutamiento del servicio Azure IoT Hub para reenviar mensajes de dispositivo a otros servicios, entonces, el administrador de inquilinos debe realizar solicitudes de datos para cada punto de conexión de enrutamiento con el fin de completar una solicitud completa para un determinado dispositivo. Consulte la documentación de referencia sobre puntos de conexión para obtener más detalles. Si quiere obtener más información sobre los puntos de conexión compatibles, consulte [Referencia: puntos de conexión de IoT Hub](iot-hub-devguide-endpoints.md).

Si usa la característica de integración Azure Event Grid del servicio de Azure IoT Hub, entonces, el administrador de inquilinos debe realizar solicitudes de datos para cada suscriptor de estos eventos. Para obtener más información, consulte la sección sobre cómo [responder a eventos de IoT Hub mediante el uso de Event Grid](iot-hub-event-grid.md).

Si usa la característica de integración Azure Monitor del servicio de Azure IoT Hub para crear registros de diagnóstico, entonces, el administrador de inquilinos debe realizar solicitudes de datos para cada registro almacenado. Para obtener más información, consulte [Supervisión del mantenimiento de Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Eliminar datos de cliente

Los administradores de inquilinos pueden usar la hoja de dispositivos de IoT de la extensión de Azure IoT Hub de Azure Portal para eliminar un dispositivo. De ese modo, se eliminarán los datos asociados con ese dispositivo.

También se pueden realizar operaciones de eliminación para dispositivos mediante las API de REST. Para obtener más información, consulte [Device Api - Delete Device](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) (API de dispositivo: eliminar dispositivo).

## <a name="exporting-customer-data"></a>Exportación de los datos de cliente

Los administradores de inquilinos pueden copiar y pegar en la hoja de dispositivos de IoT de la extensión de Azure IoT Hub en Azure Portal para exportar los datos asociados con un dispositivo.

También se pueden realizar operaciones de exportación de dispositivos mediante las API de REST. Para obtener más información, consulte [Device Api - Get Device](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) (API de dispositivo: obtener dispositivo).

> [!NOTE]
> Cuando se usan servicios para empresas de Microsoft, Microsoft genera cierta información, conocida como registros generados por el sistema. Los administradores de inquilinos no pueden acceder a ciertos registros generados por el sistema de Azure IoT Hub ni exportarlos. Estos registros constituyen acciones objetivas que se realizan en el servicio y datos de diagnóstico relacionados con dispositivos individuales.

## <a name="links-to-additional-documentation"></a>Vínculos a documentación adicional

Se puede encontrar documentación completa sobre las API de dispositivos para Azure IoT Hub en [https://docs.microsoft.com/rest/api/iothub/deviceapi](https://docs.microsoft.com/rest/api/iothub/deviceapi).