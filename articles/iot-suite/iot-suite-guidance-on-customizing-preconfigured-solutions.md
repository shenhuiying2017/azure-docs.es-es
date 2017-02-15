---
title: "Personalizar una solución preconfigurada | Microsoft Docs"
description: "Proporciona directrices sobre la personalización de ñas soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure."
services: 
suite: iot-suite
documentationcenter: .net
author: aguilaaj
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 45fd461defc00c5dc018496b85b8bf85614f03dd
ms.openlocfilehash: 0037b9e28b20c9a85f810cba45aa5b4cbcf6ab6b


---
# <a name="customize-a-preconfigured-solution"></a>Personalizar una solución preconfigurada
Las soluciones preconfiguradas proporcionadas con el conjunto de aplicaciones de IoT de Azure muestran los servicios del conjunto de aplicaciones que funcionan juntos para proporcionar una solución completa. Desde este punto de partida, existen diversos lugares donde puede ampliar y personalizar la solución para cada situación específica. Las secciones siguientes describen estos puntos de personalización comunes.

## <a name="finding-the-source-code"></a>Búsqueda del código fuente
El código fuente de su solución preconfigurada está disponible en Github en los repositorios siguientes:

* Supervisión remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Mantenimiento predictivo: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

El código fuente de las soluciones preconfiguradas se proporciona para demostrar los patrones y los procedimientos que se usan para implementar la funcionalidad de extremo a extremo de una solución de IoT mediante el conjunto de aplicaciones de IoT de Azure. Puede encontrar más información sobre cómo crear e implementar las soluciones en los repositorios de GitHub.

## <a name="changing-the-preconfigured-rules"></a>Cambio de las reglas previamente configuradas
La solución de supervisión remota incluye tres trabajos de [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para implementar la información del dispositivo, la telemetría y la lógica de reglas que se muestran para la solución.

Los tres trabajos de análisis de transmisiones y su sintaxis se describen detalladamente en el [Tutorial de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-sample-walkthrough.md). 

Puede editar directamente estos trabajos y modificar la lógica, o agregar lógica específica a su escenario. Para encontrar los trabajos de Análisis de transmisiones, siga estos pasos:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Vaya al grupo de recursos con el mismo nombre que la solución de IoT. 
3. Seleccione el trabajo de Análisis de transmisiones de Azure que desea modificar. 
4. Detenga el trabajo seleccionando **Detener**en el conjunto de comandos. 
5. Edite las entradas, la consulta y las salidas.
   
    Una modificación sencilla consiste en cambiar la consulta para que el trabajo **Reglas** use **"<"** en lugar de **">"**. El portal de la solución seguirá mostrando **">"** al editar una regla, pero observará que el comportamiento varía debido al cambio en el trabajo subyacente.
6. Inicio del trabajo

> [!NOTE]
> El panel de supervisión remota depende de datos específicos, así que la modificación de los trabajos puede provocar errores del panel.
> 
> 

## <a name="adding-your-own-rules"></a>Adición de reglas propias
Además de cambiar los trabajos de Análisis de transmisiones de Azure preconfigurados, puede usar el Portal de Azure para agregar nuevos trabajos o nuevas consultas a los trabajos existentes.

## <a name="customizing-devices"></a>Personalización de dispositivos
Una de las actividades de extensión más comunes es trabajar con dispositivos específicos de su escenario. Existen varios métodos para trabajar con dispositivos. Entre estos se incluye la modificación de un dispositivo simulado para adaptarlo a su situación, o bien el uso del [SDK de dispositivo de IoT][IoT Device SDK] para conectar el dispositivo físico a la solución.

Para obtener una guía paso a paso para agregar dispositivos a la solución preconfigurada de supervisión remota, consulte los [dispositivos de conexión de Conjunto de aplicaciones de IoT](iot-suite-connecting-devices.md) y el [ejemplo del SDK de C de supervisión remota](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring) que está diseñado para trabajar con la solución preconfigurada de supervisión remota.

### <a name="creating-your-own-simulated-device"></a>Creación de dispositivo simulado propio
El código fuente de la solución de supervisión remota (mencionado anteriormente) incluye un simulador. NET. Este simulador es el que se aprovisiona como parte de la solución y se puede modificar para enviar distintos metadatos, telemetría o responder a distintos comandos.

El simulador preconfigurado en la solución preconfigurada de supervisión remota es un dispositivo de refrigeración que emite la telemetría de temperatura y humedad. Puede modificar el simulador en el proyecto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) después de bifurcar el repositorio de GitHub.

### <a name="available-locations-for-simulated-devices"></a>Ubicaciones disponibles para dispositivos simulados
El conjunto predeterminado de ubicaciones es Seattle/Redmond, Washington (Estados Unidos de América). Puede cambiar estas ubicaciones en [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="building-and-using-your-own-physical-device"></a>Creación y uso de dispositivos propios (físicos)
Los [SDK de IoT de Azure](https://github.com/Azure/azure-iot-sdks) proporcionan bibliotecas que permiten conectar distintos tipos de dispositivos (lenguajes y sistemas operativos) a soluciones IoT.

## <a name="modifying-dashboard-limits"></a>Modificación de los límites de panel
### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos que se muestran en la lista desplegable del panel
El valor predeterminado es 200. Puede cambiar este número en [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de chinchetas que se muestran en el control de Bing Maps
El valor predeterminado es 200. Puede cambiar este número en [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Periodo del gráfico de telemetría
El valor predeterminado es 10 minutos. Puede cambiarlo en [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configuración manual de los roles de aplicación
El siguiente procedimiento describe cómo agregar los roles de aplicación **Admin** y **ReadOnly** a una solución preconfigurada. Tenga en cuenta que las soluciones preconfiguradas aprovisionadas desde el sitio azureiotsuite.com ya incluyen los roles **Admin** y **ReadOnly**.

Los miembros del rol **ReadOnly** pueden ver el panel y la lista de dispositivos, pero no tienen permiso para agregar dispositivos, cambiar atributos de dispositivo ni enviar comandos.  Los miembros del rol **Admin** tienen acceso total a todas las funciones de la solución.

1. Vaya al [Portal de Azure clásico][lnk-classic-portal].
2. Seleccione **Active Directory**.
3. Haga clic en el nombre del inquilino de AAD que usó al aprovisionar la solución.
4. Haga clic en **Aplicaciones**.
5. Haga clic en el nombre de la aplicación que coincida con el nombre de la solución preconfigurada. Si no ve la aplicación en la lista, seleccione **Aplicaciones que tiene mi compañía** en la lista desplegable **Mostrar** y haga clic en la marca de verificación.
6. En la parte inferior de la página, haga clic en **Administrar manifiesto** y, luego, en **Descargar manifiesto**.
7. Se descargará un archivo .json en la máquina local.  Abra este archivo para editarlo en el editor de texto que quiera.
8. En la tercera línea del archivo .json, encontrará:
   
   ```
   "appRoles" : [],
   ```
   Reemplácelo por lo siguiente:
   
   ```
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```
9. Guarde el archivo .json actualizado (puede sobrescribir el archivo existente).
10. En el Portal de administración de Azure, en la parte inferior de la página, seleccione **Administrar manifiesto** y, después, **Cargar manifiesto** para descargar el archivo .json que guardó en el paso anterior.
11. Ahora, los roles **Admin** y **ReadOnly** ya están agregados a la aplicación.
12. Para asignar uno de estos roles a un usuario del directorio, consulte [Permisos en el sitio azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Comentarios
¿Tiene una personalización que le gustaría que se tratara en este documento? Agregue las sugerencias de características a [User Voice](https://feedback.azure.com/forums/321918-azure-iot)o comente este artículo a continuación. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las opciones para personalizar las soluciones preconfiguradas, consulte estos artículos:

* [Tutorial: Conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT de Azure][lnk-logicapp]
* [Uso de telemetría dinámica con la solución de supervisión remota preconfigurada][lnk-dynamic]
* [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com



<!--HONumber=Dec16_HO3-->


