---
title: Un paseo por la interfaz de usuario de Azure IoT Central | Microsoft Docs
description: Como compilador, familiarícese con las áreas principales de la interfaz de usuario de Azure IoT Central, que se utiliza para crear una solución de IoT.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 4/13/2018
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: aa0d496b073600bb976a639ea0039d2a5bbdbbcf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201482"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Un paseo por la interfaz de usuario de Azure IoT Central

Este artículo presenta la interfaz de usuario de Microsoft Azure IoT Central. Puede usar la interfaz de usuario para crear, administrar y usar una solución de Azure IoT Central y sus dispositivos conectados.

Como _compilador_, usará la interfaz de usuario de Azure IoT Central para definir la solución de Azure IoT Central. Puede usar la interfaz de usuario para:

- Definir los tipos de dispositivos que se conectan a la solución.
- Configurar las reglas y las acciones para sus dispositivos.
- Personalizar la interfaz de usuario para un _operador_ que utiliza la solución.

Como _operador_, usará la interfaz de usuario de Azure IoT Central para administrar la solución de Azure IoT Central. Puede usar la interfaz de usuario para:

- Supervisar los dispositivos.
- Configurar los dispositivos.
- Solucionar los problemas y errores de los dispositivos.
- Aprovisionar dispositivos nuevos.

## <a name="use-the-left-navigation-menu"></a>Uso del menú de navegación izquierdo

Use el menú de navegación izquierdo para acceder a las diferentes áreas de la aplicación:

| Menú | DESCRIPCIÓN |
| ---- | ----------- |
| ![Menú de navegación izquierdo](media/overview-iot-central-tour/navigationbar.png) | <ul><li>El botón **Home** (Inicio) muestra la página principal de la aplicación. Como compilador, puede personalizar esta página de inicio para los operadores.</li><li>El botón **Device Explorer** (Explorador de dispositivos) muestra tanto las plantillas de dispositivo definidas en la aplicación como los dispositivos simulados y reales asociados a cada plantilla de dispositivo. Como operador, usará **Device Explorer** (Explorador de dispositivos) para administrar los dispositivos conectados.</li><li>El botón **Device Sets** (Conjuntos de dispositivos) permite ver y crear conjuntos de dispositivos. Como operador, puede crear conjuntos de dispositivos como una colección lógica de los dispositivos especificados por una consulta.</li><li>El botón **Analytics** (Análisis) muestra los análisis derivados de los datos de telemetría de los dispositivos y conjuntos de dispositivos. Como operador, puede crear vistas personalizadas a partir de los datos de los dispositivos para obtener información detallada de la aplicación.</li><li>El botón **Application Builder** (Compilador de aplicaciones) muestra las herramientas que un compilador utiliza, como la herramienta **Create Device Template** (Crear plantilla de dispositivo).</li><li>El botón **Administration** (Administración) muestra las páginas de administración de aplicaciones donde un administrador puede administrar la configuración de la aplicación, los usuarios y los roles.</li></ul> |

## <a name="search-help-and-support"></a>Búsqueda, ayuda y soporte técnico

El menú superior aparece en todas las páginas:

![Barra de herramientas](media/overview-iot-central-tour/toolbar.png)

- Para buscar plantillas de dispositivos y dispositivos, elija el icono **Buscar**.
- Para obtener ayuda y soporte técnico, elija el menú desplegable **Ayuda** para obtener una lista de recursos.
- Para controlar los tutoriales, cambiar el tema de la interfaz de usuario o cerrar sesión en la aplicación, elija el icono **Account** (Cuenta).

Puede elegir entre un tema claro o un tema oscuro para la interfaz de usuario:

![Elija un tema para la interfaz de usuario](media/overview-iot-central-tour/themes.png)

## <a name="home-page"></a>Página de inicio

![Página de inicio](media/overview-iot-central-tour/homepage.png)

La página principal es la primera página que verá cuando inicie sesión en la aplicación de Azure IoT Central. Como compilador, puede agregar iconos para personalizar la página principal de otros usuarios de la aplicación. Para más información, consulte el tutorial [Customize the Azure IoT Central operator's view](tutorial-customize-operator.md) (Personalización de la vista del operador de Azure IoT Central).

## <a name="device-explorer"></a>Explorador de dispositivos

![Página Explorer](media/overview-iot-central-tour/explorer.png)

La página del explorador muestra las _plantillas de dispositivo_ y los _dispositivos_ que hay en la aplicación de Azure IoT Central.

* Una plantilla de dispositivo define un tipo de dispositivo que se puede conectar a la aplicación. Para más información, consulte [Define a new device type in your Azure IoT Central application](tutorial-define-device-type.md) (Definición de un nuevo tipo de dispositivo en la aplicación de Azure IoT Central).
* Un dispositivo representa un dispositivo real o simulado en la aplicación. Para más información, consulte [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Adición de un nuevo dispositivo a la aplicación de Azure IoT Central).

## <a name="device-sets"></a>Conjuntos de dispositivos

![Página Device Sets](media/overview-iot-central-tour/devicesets.png)

La página _Device Sets_ (Conjuntos de dispositivos) muestra los conjuntos de dispositivos creados por el compilador. Un conjunto de dispositivos es una colección de dispositivos relacionados. Un compilador define una consulta para identificar los dispositivos que se incluyen en un conjunto de dispositivos. Los conjuntos de dispositivos se utilizan para personalizar el análisis en la aplicación. Para más información, consulte el artículo [Use device sets in your Azure IoT Central application](howto-use-device-sets.md) (Uso de conjuntos de dispositivos en la aplicación de Azure IoT Central).

## <a name="analytics"></a>Análisis

![Página Analytics](media/overview-iot-central-tour/analytics.png)

La página de análisis muestra gráficos que le ayudarán a entender cómo se comportan los dispositivos conectados a la aplicación. Un operador usará esta página para supervisar e investigar los problemas de los dispositivos conectados. El compilador puede definir los gráficos que se muestran en esta página. Para más información, consulte el artículo [Create custom analytics for your Azure IoT Central application](howto-create-analytics.md) (Creación de análisis personalizados para la aplicación de Azure IoT Central).

## <a name="application-builder"></a>Compilador de aplicaciones

![Página Application Builder](media/overview-iot-central-tour/applicationbuilder.png)

La página Application Builder contiene vínculos a las herramientas que un compilador utiliza para crear una aplicación de Azure IoT Central, por ejemplo, para crear plantillas de dispositivos y configurar la página principal. Para más información, consulte el tutorial [Define a new device type in your Azure IoT Central application](tutorial-define-device-type.md) (Definición de un nuevo tipo de dispositivo en la aplicación de Azure IoT Central).

## <a name="administration"></a>Administración

![Página Administration](media/overview-iot-central-tour/administration.png)

La página Administration contiene vínculos a las herramientas de administrador que se utiliza como definir usuarios y roles en la aplicación. Para más información, consulte el artículo [Administer your Azure IoT Central application](howto-administer.md) (Administración de la aplicación de Azure IoT Central).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene información general sobre Azure IoT Central y está familiarizado con el diseño de la interfaz de usuario, el siguiente paso sugerido es completar la guía de inicio rápido [Create an Azure IoT Central application](quick-deploy-iot-central.md) (Creación de una aplicación de Azure IoT Central).