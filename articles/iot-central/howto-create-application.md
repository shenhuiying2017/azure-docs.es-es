---
title: Creación de una aplicación de Azure IoT Central | Microsoft Docs
description: Como administrador, ¿cómo crea una aplicación de Azure IoT Central?
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 83879c6b81985f61b9fcff665e9f764c1346592e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200816"
---
# <a name="create-your-azure-iot-central-application"></a>Creación de una aplicación de Azure IoT Central

La aplicación de Microsoft Azure IoT Central se crea desde la página [Crear aplicación](https://apps.microsoftiotcentral.com/create). Para crear una aplicación de Azure IoT Central, debe completar todos los campos de esta página y luego eligir **Crear**. Este artículo tiene más información sobre cada uno de los campos.

![Página Crear aplicación](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plan de pago

Puede crear una aplicación de evaluación o de pago. Obtenga más información acerca de las aplicaciones de evaluación y de pago en esta página.

## <a name="application-name"></a>Nombre de la aplicación

El nombre de la aplicación se muestra en la página **Administrador de aplicaciones** y dentro de cada aplicación de Azure IoT Central. Puede elegir cualquier nombre para la aplicación de Azure IoT Central. Elija un nombre que tenga sentido para usted y para otros usuarios de la organización.

## <a name="application-url"></a>Dirección URL de la aplicación

La dirección URL de la aplicación es el vínculo a la aplicación. Puede guardar un marcador para ella en el explorador o compartirla con otros usuarios.

Cuando escriba el nombre de la aplicación, la dirección URL de la misma se genera automáticamente. Si lo prefiere, puede elegir una dirección URL diferente para la aplicación. Cada dirección URL de Azure IoT Central debe ser única. Verá un mensaje de error si la dirección URL que elige ya se está utilizando.

## <a name="directory"></a>Directorio

Solo en las aplicaciones de pago.

Elija un inquilino de Azure Active Directory para crear una aplicación de Azure IoT Central. Un inquilino de Azure Active Directory contiene identidades de usuario, credenciales y otra información de la organización. Se pueden asociar varias suscripciones de Azure a un solo inquilino de Azure Active Directory.

Si no tiene un inquilino de Azure Active Directory, se crea uno automáticamente cuando se crea una suscripción de Azure.

Para más información, consulte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Suscripción de Azure

Una suscripción de Azure permite crear instancias de los servicios de Azure. Azure IoT Central busca automáticamente todas las suscripciones de Azure a las que usted tiene acceso y las muestra en una lista desplegable en la página **Crear aplicación**. Elija una nueva suscripción de Azure para crear una nueva aplicación de Azure IoT Central.

Si no tiene una suscripción de Azure, puede crear una en esta página. Después de crear la suscripción de Azure, vuelva a la página **Crear aplicación**. La nueva suscripción aparecerá en el cuadro de lista desplegable **Suscripción de Azure**.

Para más información, consulte [Suscripciones de Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Solo en las aplicaciones de pago.

Elija la región donde desea crear la aplicación de Azure IoT Central. Normalmente, debe elegir la región más cercana físicamente a los dispositivos a fin de obtener un rendimiento óptimo.

Para más información, consulte [Regiones de Azure](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions).

Puede ver las regiones en las que Azure IoT Central está disponible en la página [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

> [!Note]
> Una vez que elija una región, ya no podrá mover la aplicación a otra región más adelante.

## <a name="application-template"></a>Plantilla de la aplicación

Puede elegir una de las plantillas de aplicación disponibles para la nueva aplicación de Azure IoT Central. Una plantilla de aplicación puede contener elementos predefinidos, como plantillas de dispositivos y paneles que le ayudarán a empezar a trabajar:

| Plantilla de la aplicación | DESCRIPCIÓN |
| -------------------- | ----------- |
| Aplicación personalizada   | Permite crear una aplicación vacía para que pueda rellenarla con sus propias plantillas de dispositivo y dispositivos. |
| Ejemplo Contoso       | Permite crear una aplicación que incluye una plantilla de dispositivo para un dispositivo conectado simple. Utilice esta plantilla para empezar a explorar Azure IoT Central. |
| Ejemplo Devkits       | Permite crear una aplicación con plantillas de dispositivo preparadas para que se conecte a un dispositivo MXChip o Raspberry Pi. Utilice esta plantilla si es un desarrollador de dispositivos que experimenta con código en uno de estos dispositivos. |

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear una aplicación de Azure IoT Central, este es el paso siguiente sugerido:

> [!div class="nextstepaction"]
> [Administrar su aplicación](howto-administer.md)