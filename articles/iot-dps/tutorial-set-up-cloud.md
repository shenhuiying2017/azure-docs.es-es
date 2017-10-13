---
title: "Configuración de la nube del servicio Azure IoT Hub Device Provisioning en el portal | Microsoft Docs"
description: "Aprovisionamiento automático de dispositivos de IoT Hub en Azure Portal"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 088d127521ce89d3a82e30ad8797fe5746ae7e03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Configurar los recursos de la nube para el aprovisionamiento de dispositivos con el servicio IoT Hub Device Provisioning

Este tutorial muestra cómo configurar la nube para el aprovisionamiento automático de dispositivos mediante el servicio IoT Hub Device Provisioning. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar Azure Portal para crear un servicio IoT Hub Device Provisioning y obtener el ámbito del identificador
> * Crear un centro de IoT
> * Vincular la instancia de IoT Hub al servicio Device Provisioning
> * Establecer la directiva de asignación del servicio Device Provisioning

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Creación de una instancia del servicio Device Provisioning y obtención del ámbito del identificador

Siga estos pasos para crear una nueva instancia del servicio Device Provisioning.

1. Haga clic en **Nuevo**, en la esquina superior izquierda de Azure Portal.
2. En el cuadro de búsqueda, escriba **aprovisionamiento de dispositivos**. 
3. Haga clic en el **servicio Azure IoT Hub Device Provisioning**.
4. Rellene el formulario del **servicio IoT Hub Device Provisioning** con la siguiente información:
    
   | Configuración       | Valor sugerido | Descripción | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name** | Cualquier nombre único | -- | 
   | **Suscripción** | Su suscripción  | Para más información acerca de sus suscripciones, consulte [Suscripciones](https://account.windowsazure.com/Subscriptions). |
   | **Grupos de recursos** | myResourceGroup | Para conocer cuáles son los nombres de grupo de recursos válidos, consulte el artículo [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Convenciones de nomenclatura). |
   | **Ubicación** | Cualquier ubicación válida | Para obtener información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/). |   

   ![Especificar la información básica de DPS en el portal](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Haga clic en **Crear**.
6. El *ámbito del identificador* se usa para diferenciar los identificadores de registro y, además, garantiza que el identificador de registro es único. Para obtener este valor, haga clic en **Introducción** para que se abra la página **Información esencial** para el servicio Device Provisioning. Copie el valor del **ámbito del identificador** en una ubicación temporal para su uso posterior.
7. También tome nota del valor del **punto de conexión del servicio** o cópielo en una ubicación temporal para su uso posterior. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ya se creó IoT Hub y ya tiene el nombre de host y la cadena de conexión de IoT Hub que necesita para completar el resto del tutorial.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Vinculación del servicio Device Provisioning a una instancia de IoT Hub

El paso siguiente es vincular el servicio Device Provisioning a una instancia de IoT Hub para que este servicio pueda registrar dispositivos en ese concentrador. Este servicio solo puede aprovisionar los dispositivos para las instancias de IoT Hub que se hayan vinculado a él. Siga estos pasos.

1. En la página **Todos los recursos**, haga clic en la instancia del servicio Device Provisioning que creó anteriormente.
2. En la página del servicio Device Provisioning, haga clic en **Linked IoT hubs** (Centros de IoT vinculados).
3. Haga clic en **Agregar**.
4. En la página **Add link to IoT hub** (Agregar vínculo a centro de IoT), utilice los botones de radio para especificar si el centro de IoT vinculado se encuentra en la suscripción actual o en una suscripción diferente. A continuación, elija el nombre del centro de IoT en el cuadro **Centro de IoT**.
5. Haga clic en **Guardar**.

   ![Vincular el nombre del centro a la instancia de DPS del portal](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Establecer la directiva de asignación del servicio Device Provisioning

La directiva de asignación es una configuración del servicio IoT Hub Device Provisioning que determina cómo se asignan los dispositivos a una instancia de IoT Hub. Hay tres directivas de asignación admitidas: 

1. **Latencia más baja**: los dispositivos se aprovisionan en una instancia de IoT Hub en función del centro con la latencia más baja en el dispositivo.
2. **Distribución uniformemente ponderada** (predeterminada): las instancias de IoT Hub vinculadas tienen la misma probabilidad de tener dispositivos aprovisionados para ellos. Esta es la configuración predeterminada. Si va a aprovisionar dispositivos para un único centro de IoT Hub, puede mantener esta configuración. 
3. **Configuración estática a través de la lista de inscripción**: la especificación de la instancia de IoT deseada en la lista de inscripción tiene prioridad sobre la directiva de asignación del nivel del servicio Device Provisioning.

Para establecer la directiva de asignación, en la página del servicio Device Provisioning haga clic en **Manage allocation policy** (Administrar directiva de asignación). Asegúrese de que la directiva de asignación está establecida en **Distribución uniformemente ponderada** (la opción predeterminada). Si realiza cambios, haga clic en **Guardar** cuando haya terminado.

![Administrar directiva de asignación](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Otros tutoriales de esta colección se basan en los valores de este. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido o tutoriales, no elimine los recursos creados en este tutorial. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en este tutorial en Azure Portal.

1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione la instancia del servicio IoT Hub Device Provisioning. En la parte superior de la página **Todos los recursos**, haga clic en **Eliminar**.  
2. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. En la parte superior de la página **Todos los recursos**, haga clic en **Eliminar**.
 
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Usar Azure Portal para crear un servicio IoT Hub Device Provisioning y obtener el ámbito del identificador
> * Crear un centro de IoT
> * Vincular la instancia de IoT Hub al servicio Device Provisioning
> * Establecer la directiva de asignación del servicio Device Provisioning

Avance al siguiente tutorial para aprender a configurar el dispositivo para el aprovisionamiento.

> [!div class="nextstepaction"]
> [Configurar el dispositivo para el aprovisionamiento](tutorial-set-up-device.md)
