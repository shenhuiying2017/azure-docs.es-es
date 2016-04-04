<properties
	pageTitle="Personalización de las soluciones preconfiguradas | Microsoft Azure"
	description="Proporciona directrices sobre la personalización de ñas soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# Personalización de soluciones preconfiguradas

Las soluciones preconfiguradas proporcionadas con el conjunto de aplicaciones de IoT de Azure muestran los servicios del conjunto de aplicaciones que funcionan juntos para proporcionar una solución completa. Desde este punto de partida, existen diversos lugares donde puede ampliar y personalizar la solución para cada situación específica. Las secciones siguientes describen estos puntos de personalización comunes.

## Búsqueda del código fuente

El código fuente de su solución preconfigurada está disponible en Github en los repositorios siguientes:

- Supervisión remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Mantenimiento predictivo: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

El código fuente de las soluciones preconfiguradas se proporciona para demostrar los patrones y los procedimientos que se usan para implementar la funcionalidad de extremo a extremo de una solución de IoT mediante el conjunto de aplicaciones de IoT de Azure. Puede encontrar más información sobre cómo crear e implementar las soluciones en los repositorios de GitHub.

## Administración de permisos en una solución preconfigurada
El portal de cada solución preconfigurada se crea como una nueva aplicación de Azure Active Directory. Puede administrar los permisos para el portal de su solución (aplicación de AAD) de la siguiente manera:

1. Abra el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la aplicación de AAD seleccionando **Aplicaciones que tiene mi compañía** y, después, haga clic en la marca de verificación.
3. Vaya a **Usuarios** y asigne los miembros de su inquilino de Azure Active Directory a un rol. 

De manera predeterminada, la aplicación se aprovisiona con los roles **Administrador**, **Solo lectura** e **Implícito de solo lectura**. **Implícito de solo lectura** se concede a los usuarios que son miembros del inquilino de Azure Active Directory, pero que no se les ha asignado a un rol. Puede modificar [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs) una vez bifurcado el repositorio de GitHub y, después, volver a implementar la solución.

## Cambio de las reglas previamente configuradas

La solución de supervisión remota incluye tres trabajos de [análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) para implementar la información del dispositivo, la telemetría y la lógica de reglas que se muestran para la solución.

Los tres trabajos de análisis de transmisiones y su sintaxis se describen detalladamente en el [Tutorial de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-sample-walkthrough.md).

Puede editar directamente estos trabajos y modificar la lógica, o agregar lógica específica a su escenario. Para encontrar los trabajos de Análisis de transmisiones, siga estos pasos:
 
1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Vaya al grupo de recursos con el mismo nombre que la solución de IoT. 
3. Seleccione el trabajo de Análisis de transmisiones de Azure que desea modificar. 
4. Detenga el trabajo seleccionando **Detener** en el conjunto de comandos. 
5. Edite las entradas, la consulta y las salidas.

    Una modificación sencilla consiste en cambiar la consulta para que el trabajo **Reglas** use **"<"** en lugar de **">"**. El portal de la solución seguirá mostrando **">"** al editar una regla, pero observará que el comportamiento varía debido al cambio en el trabajo subyacente.

6. Inicio del trabajo

> [AZURE.NOTE] El panel de supervisión remota depende de datos específicos, así que la modificación de los trabajos puede provocar errores del panel.

## Adición de reglas propias

Además de cambiar los trabajos de Análisis de transmisiones de Azure preconfigurados, puede usar el Portal de Azure para agregar nuevos trabajos o nuevas consultas a los trabajos existentes.

## Personalización de dispositivos

Una de las actividades de extensión más comunes es trabajar con dispositivos específicos de su escenario. Existen varios métodos para trabajar con dispositivos. Entre estos se incluye la modificación de un dispositivo simulado para adaptarlo a su situación, o bien el uso del [SDK de dispositivo de IoT][] para conectar el dispositivo físico a la solución.

Para obtener una guía paso a paso sobre cómo agregar dispositivos a la solución preconfigurada de supervisión remota, consulte [Dispositivos de conexión del conjunto de aplicaciones de IoT](iot-suite-connecting-devices.md).

### Creación de dispositivo simulado propio

El código fuente de la solución de supervisión remota (mencionado anteriormente) incluye un simulador. NET. Este simulador es el que se aprovisiona como parte de la solución y se puede modificar para enviar distintos metadatos, telemetría o responder a distintos comandos.

El simulador preconfigurado en la solución preconfigurada de supervisión remota es un dispositivo de refrigeración que emite la telemetría de temperatura y humedad. Puede modificar el simulador en el proyecto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) después de bifurcar el repositorio de GitHub.

Además, IoT de Azure proporciona un [SDK de C de ejemplo](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) que está diseñado para funcionar con la solución preconfigurada de supervisión remota.

### Creación y uso de dispositivos propios (físicos)

Los [SDK de IoT de Azure](https://github.com/Azure/azure-iot-sdks) proporcionan bibliotecas que permiten conectar distintos tipos de dispositivos (lenguajes y sistemas operativos) a soluciones IoT.

## Comentarios

¿Tiene una personalización que le gustaría que se tratara en este documento? Agregue las sugerencias de características al [foro de IoT de Azure](https://feedback.azure.com/forums/321918-azure-iot) o comente este artículo a continuación.

## Pasos siguientes

Para obtener más información sobre los dispositivos de IoT, consulte el [sitio para desarrolladores de IoT de Azure](https://azure.microsoft.com/develop/iot/), donde encontrará documentación y vínculos.

[SDK de dispositivo de IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0323_2016-->