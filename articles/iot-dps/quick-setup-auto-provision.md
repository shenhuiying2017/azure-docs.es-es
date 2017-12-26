---
title: "Configuración del aprovisionamiento de dispositivos en Azure Portal | Microsoft Docs"
description: "Guía de inicio rápido de Azure: configuración del servicio Azure IoT Hub Device Provisioning en Azure Portal"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 00e0f169c7de2521b15b89c2d935967e51b38960
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Configuración de Azure IoT Hub Device Provisioning Service con Azure Portal

Estos pasos le enseñan a configurar los recursos de nube de Azure en el portal para el aprovisionamiento de los dispositivos. Esto incluye la creación de un centro de IoT, de un nuevo servicio Azure IoT Hub Device Provisioning, y la vinculación de los dos servicios. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Seleccione **Internet de las cosas**, seleccione **IoT Hub** y haga clic en el botón **Crear**. 

3. **Asigne un nombre** al centro de IoT. Seleccione una de las opciones disponibles para los precios, escriba las [unidades de IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/), seleccione el número de particiones para los mensajes del dispositivo a la nube y la suscripción que se utilizaría para este recurso. Escriba el nombre de un grupo de recursos nuevo o existente, y seleccione la ubicación. Cuando haya terminado, haga clic en **Cerrar**.

    ![Especificación de la información básica del centro de IoT en la hoja del portal](./media/quick-setup-auto-provision/create-iot-hub-portal.png)  

4. Una vez que el centro de IoT se implementa de forma correcta, se abre automáticamente la hoja de resumen del centro.


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Creación de una nueva instancia para el servicio Azure IoT Hub Device Provisioning

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. *Busque en Marketplace* el **servicio Device Provisioning**. Seleccione **IoT Device Provisioning Service** y haga clic en el botón **Crear**. 

3. **Asigne un nombre** a la instancia del servicio Device Provisioning. Seleccione la suscripción que se utilizaría para esta instancia y el nombre de un grupo de recursos nuevo o existente. Seleccione la ubicación. Cuando haya terminado, haga clic en **Cerrar**.

    ![Especificación de la información básica de la instancia de DPS en la hoja del portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Una vez que el servicio se implementa de forma correcta, se abre automáticamente la hoja de resumen.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Vínculo del centro de IoT y el servicio Device Provisioning

1. Haga clic en el botón **Todos los recursos** en el menú izquierdo de Azure Portal. Seleccione la instancia del servicio Device Provisioning que creó en la sección anterior.  

2. En la hoja de resumen del servicio Device Provisioning, seleccione **Linked IoT hubs** (Centros de IoT vinculados). Haga clic en el botón **+ Agregar** que aparece en la parte superior. 

3. En la hoja del portal **Agregar vínculo a IoT Hub**, seleccione la suscripción actual o escriba el nombre y la cadena de conexión de otra suscripción. Seleccione el nombre del centro en la lista desplegable. Cuando haya terminado, haga clic en **Guardar**. 

    ![Vínculo del nombre del centro a la instancia de DPS en la hoja del portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Ahora debería ver el centro seleccionado en la hoja **Linked IoT hubs** (Centros de IoT vinculados). Puede que deba hacer clic en **Actualizar** para mostrar **Instancias de IoT Hub vinculadas**.



## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido o tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido en Azure Portal.

1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  
2. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. En la parte superior de la hoja **Todos los recursos**, haga clic en **Eliminar**.  

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un centro de IoT y una instancia del servicio Device Provisioning, y ha vinculado los dos recursos. Para aprender a usar esta configuración para aprovisionar un dispositivo simulado, siga la guía de inicio rápido que permite crear un dispositivo simulado.

> [!div class="nextstepaction"]
> [Guía de inicio rápido para crear el dispositivo simulado](./quick-create-simulated-device.md)
