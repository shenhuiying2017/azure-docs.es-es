---
title: Actualización de Azure IoT Hub | Microsoft Docs
description: Cambie el nivel de precios y de escala para que IoT Hub obtenga más funcionalidades de administración de dispositivos y mensajería.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: d383d26b406c012b6b76225faf89f4b5dbd6bb9c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-upgrade-your-iot-hub"></a>Cómo actualizar IoT Hub

A medida que crece la solución de IoT, Azure IoT Hub está listo para ayudarle a escalar verticalmente. Azure IoT Hub ofrece dos niveles, básico (B) y estándar (S) para adaptarse a los clientes que desean usar diferentes características. Dentro de cada nivel hay tres tamaños (1,2 y 3) que determinan el número de mensajes que se pueden enviar cada día. 

Cuando tiene más dispositivos y necesita más funcionalidades, hay tres formas de ajustar IoT Hub para satisfacer sus necesidades:

* Agregue unidades dentro de IoT Hub. Por ejemplo, cada una de las unidades adicionales en IoT Hub B1 permite 400 000 mensajes diarios adicionales. 
* Cambie el tamaño de IoT Hub. Por ejemplo, migre del nivel B1 al nivel B2 para aumentar la cantidad de mensajes que puede admitir al día cada unidad.
* Actualice a un nivel superior. Por ejemplo, actualice del nivel B1 al nivel S1 para la misma funcionalidad de mensajería, pero con las características avanzadas que vienen en el nivel estándar.

Todos estos cambios pueden producirse sin interrumpir las operaciones existentes.

Si desea cambiar IoT Hub a una versión anterior, puede quitar unidades y reducir el tamaño de IoT Hub. Sin embargo, no puede cambiar a un nivel inferior. Por ejemplo, puede cambiar del nivel S2 al nivel S1, pero no puede cambiar del nivel S2 al nivel B1. 

Estos ejemplos están diseñados para ayudarle a entender cómo ajustar IoT Hub a medida que cambia su solución. Para obtener información específica sobre las funcionalidades de cada nivel, debe consultar siempre [Precios de Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/). 

## <a name="upgrade-your-existing-iot-hub"></a>Actualizar su IoT Hub existente 

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y vaya a IoT Hub. 
2. Seleccione **Precios y escala**. 

   ![Precios y escala](./media/iot-hub-upgrade/pricing-scale.png)

3. Para cambiar el nivel para su central, seleccione **Nivel de precios y de escala**. Elige el nuevo nivel y, a continuación, haga clic en **Seleccionar**.

   ![Precios y escala](./media/iot-hub-upgrade/select-tier.png)

4. Para cambiar el número de unidades en su central, escriba un nuevo valor en **Unidades de IoT Hub**. 
5. Haga clic en **Guardar** para guardar los cambios. 

IoT Hub se ajusta ahora y sus configuraciones permanecen sin cambios. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga más detalles sobre [cómo elegir el nivel de IoT Hub adecuado](iot-hub-scaling.md). 

