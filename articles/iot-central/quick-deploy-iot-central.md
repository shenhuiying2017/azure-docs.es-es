---
title: Implementación de una aplicación de Azure IoT Central | Microsoft Docs
description: Cree una aplicación de Azure IoT Central para administrar máquinas expendedoras refrigeradas. Visualice los datos de telemetría generados a partir de los dispositivos simulados.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: hero-article
ms.prod: microsoft-iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 54219246f073f48c2a7e1b04dce7653bd2da0b40
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202380"
---
# <a name="create-an-azure-iot-central-application"></a>Creación de una aplicación de Azure IoT Central

Como _generador_, usará la interfaz de usuario de Azure IoT Central para definir la aplicación de Azure IoT Central. Esta guía de inicio rápido le muestra cómo:

- Crear una aplicación de Azure IoT Central que contenga un ejemplo de _plantilla de dispositivo_ y _dispositivos_ simulados.
- Ver las características de la plantilla de dispositivo **Refrigerated Vending Machine** en la aplicación.
- Ver la telemetría y los análisis de los dispositivos simulados **Refrigerator**.

En esta guía de inicio rápido se crea un dispositivo simulado **Refrigerator** a partir de una plantilla de dispositivo. El dispositivo simulado:

* Envía telemetría a la aplicación, como la temperatura y la presión.
* Informa a la aplicación de los valores de propiedad del dispositivo, por ejemplo, las alertas de movimiento.
* Tiene una configuración de dispositivo que se puede establecer en la aplicación, como la velocidad del ventilador.

Con un dispositivo simulado creado a partir de una plantilla de dispositivo en la aplicación de Azure IoT Central se puede probar la aplicación antes de conectar un dispositivo real.

## <a name="create-the-application"></a>Creación de la aplicación

Para completar esta guía de inicio rápido debe crear una aplicación de Azure IoT Central a partir de la plantilla de aplicación **Sample Contoso**.

Vaya a la página [Application Manager](https://aka.ms/iotcentral) (Administrador de aplicaciones) de Azure IoT Central. A continuación, escriba la dirección de correo electrónico y la contraseña que usa para acceder a la suscripción de Azure:

![Incorporación de la cuenta de la organización](media/quick-deploy-iot-central/sign-in.png)

Para empezar a crear una nueva aplicación de Azure IoT Central, elija **New Application** (Nueva aplicación):

![Página Application Manager (Administrador de aplicaciones) de Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Para crear una nueva aplicación de Azure IoT Central:

1. Elija el plan de pago **Free Trial Application** (Evaluación gratuita de la aplicación).
1. Elija un nombre de aplicación descriptivo, como **Contoso IoT**. Azure IoT Central genera un prefijo de dirección URL único. Puede cambiar este prefijo de dirección URL por algo más fácil de recordar.
1. Elija la plantilla de aplicación **Sample Contoso**.
1. Luego elija **Crear**.

![Página de creación de una aplicación de Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="navigate-to-the-application"></a>Dirección a la aplicación

Cuando la aplicación esté preparada, aparecerá la **página principal** de esta. Se puede activar _Design Mode_ (Modo de diseño) de la esquina superior derecha para editar la página principal. La dirección URL de la aplicación es la que se especificó en el paso anterior:

![Página Application Builder](media/quick-deploy-iot-central/apphome.png)

Use el _menú de navegación izquierdo_ para acceder a las diferentes áreas de la nueva aplicación de Azure IoT Central:

![Menú de navegación izquierdo](media/quick-deploy-iot-central/navbar.png)

Para ver las plantillas de dispositivo y los dispositivos en la aplicación, elija **Device Explorer** (Explorador de dispositivos) en el menú de navegación izquierdo. La aplicación de ejemplo incluye la plantilla de dispositivo **Refrigerated Vending Machine**. Hay tres dispositivos simulados ya creados a partir de esta plantilla de dispositivo:

![Explorador de dispositivos](media/quick-deploy-iot-central/deviceexplorer.png)

## <a name="view-the-device-template-and-devices"></a>Visualización de la plantilla de dispositivo y los dispositivos

Utilice los pasos siguientes para ver un refrigerador creado a partir de la plantilla de dispositivo **Refrigerated Vending Machine**. Una plantilla de dispositivo define:

* Las _medidas_, como la telemetría de temperatura, que se envía desde un dispositivo.
* La _configuración_, como la velocidad del ventilador, que permite controlar el dispositivo.
* Las _propiedades_, como el número de serie, que almacena información sobre el dispositivo.
* Las [reglas](howto-create-telemetry-rules.md), que permiten automatizar acciones en función del comportamiento del dispositivo.
* Un _panel_ personalizable, que muestra información sobre el dispositivo.

A partir de una plantilla de dispositivo, puede crear dispositivos tanto simulados como reales.

### <a name="measurements"></a>Medidas

Se muestra la página **Measurements** (Medidas) del dispositivo **Refrigerator 1**. Puede ver la lista de medidas enviadas desde el dispositivo simulado. La página también muestra un gráfico personalizable de las medidas visibles:

![Página Measurements (Medidas)](media/quick-deploy-iot-central/measurements.png)

Puede alternar la visibilidad de los distintos elementos y personalizar el gráfico. El gráfico actual muestra la telemetría de un dispositivo simulado. Con los permisos adecuados, puede agregar nuevas medidas a la plantilla de dispositivo.

> [!NOTE]
> Debe esperar unos instantes para que los datos simulados aparezcan en el gráfico.

### <a name="settings"></a>Settings

Elija **Settings** (Configuración). El dispositivo se controla desde la página **Settings** (Configuración). Por ejemplo, puede actualizar la velocidad del ventilador del frigorífico:

![Settings](media/quick-deploy-iot-central/settings.png)

Los valores de configuración se muestran **sincronizados** cuando un dispositivo confirma el cambio.

### <a name="properties"></a>Properties (Propiedades)

Elija **Properties** (Propiedades). En la página **Properties** (Propiedades), puede:

* Mantener la información sobre el dispositivo, como el nombre del cliente.
* Ver los valores de propiedad notificados por el dispositivo, por ejemplo, una alerta de movimiento.

![Properties (Propiedades)](media/quick-deploy-iot-central/properties.png)

### <a name="dashboard"></a>panel

Elija **Dashboard** (Panel). El panel es una vista personalizable de información sobre el dispositivo, como las medidas, las propiedades y los indicadores clave de rendimiento:

![panel](media/quick-deploy-iot-central/dashboard.png)

## <a name="view-analytics"></a>Visualización de análisis

La sección anterior mostraba cómo ver información de un dispositivo individual. Puede usar [conjuntos de dispositivos](howto-use-device-sets.md) y [análisis](howto-create-analytics.md) para ver información consolidada de varios dispositivos.

Un conjunto de dispositivos usa una consulta para seleccionar dinámicamente un conjunto de dispositivos que coinciden con determinados criterios. Por ejemplo, el conjunto de dispositivos **Machines in Seattle** selecciona los frigoríficos cuya ubicación es Seattle. Para ver el conjunto de dispositivos **Machines in Seattle**, elija **Device Sets** (Conjuntos de dispositivos) en el menú de navegación izquierdo y **Machines in Seattle**:

![Conjunto de dispositivos Machines in Seattle](media/quick-deploy-iot-central/deviceset.png)

Puede ver los datos de análisis de los dispositivos del conjunto en la página **Analytics** (Análisis):

![Análisis de las máquinas de Seattle](media/quick-deploy-iot-central/analytics.png)

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido se ha creado una aplicación de Azure IoT Central previamente rellenada que contiene una plantilla de dispositivo **Refrigerated Vending Machine** y dispositivos simulados. Consulte el artículo de [definición de una nueva plantilla de dispositivo en la aplicación](tutorial-define-device-type.md) para más información sobre cómo definir sus propias plantillas de dispositivo como generador.
