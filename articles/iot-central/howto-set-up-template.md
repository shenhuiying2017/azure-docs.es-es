---
title: Configuración de una plantilla de dispositivo en una aplicación de Azure IoT Central | Microsoft Docs
description: Aprenda a configurar una plantilla de dispositivo con medidas, configuración, propiedades, reglas y panel.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 52c6c8fe4375354d650f92b73bffc288c9a2ccfe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201516"
---
# <a name="set-up-a-device-template"></a>Configuración de una plantilla de dispositivo

Una plantilla de dispositivo es un plano técnico que define las características y los comportamientos de un tipo de dispositivo que se conecta a una aplicación de Microsoft Azure IoT Central.

Por ejemplo, un generador puede crear una plantilla de dispositivo para un ventilador conectado IoT que tenga:

- Medida de telemetría de temperatura

- Medida de eventos de error del motor del ventilador

- Medida del estado de funcionamiento del ventilador

- Configuración de la velocidad del ventilador

- Propiedad de ubicación

- Reglas que envían alertas

- Panel que ofrece una vista completa del dispositivo

A partir de esta plantilla de dispositivo, un operador puede crear y conectar dispositivos de ventilador reales con nombres, como **ventilador-1** y **ventilador-2**. Todos estos ventiladores tienen medidas, configuraciones, propiedades, reglas y un panel que los usuarios de la aplicación pueden supervisar y administrar.

> [!NOTE]
Solo los generadores y administradores pueden crear, editar y eliminar plantillas de dispositivo. Cualquier usuario puede crear dispositivos en la página **Device Explorer** a partir de plantillas de dispositivo existentes.

## <a name="create-a-new-device-template"></a>Creación de una nueva plantilla de dispositivo

1. Navegue a la página **Application Builder** (Generador de aplicaciones).

1. Para crear una plantilla en blanco, elija **Create Device Template** (Crear plantilla de dispositivo) y luego elija **Personalizado**.

1. Escriba un nombre para la nueva plantilla de dispositivo y elija **Crear**.

    ![Página Detalles del dispositivo](./media/howto-set-up-template/devicedetailspage.png)

1. Ahora está en la página **Detalles del dispositivo** de un nuevo dispositivo simulado. Un dispositivo simulado se crea automáticamente cuando crea una nueva plantilla de dispositivo. Informa de los datos y se puede controlar como un dispositivo real.

Ahora examinemos cada una de las pestañas de la página **Detalles del dispositivo**.

## <a name="measurements"></a>Medidas

Las medidas son los datos procedentes del dispositivo. Puede agregar varias medidas a la plantilla de dispositivo para que coincidan con las funcionalidades de dicho dispositivo. Actualmente, telemetría y evento son los tipos de medidas admitidas.

- Las medidas de tipo **Telemetría** son los puntos de datos numéricos que el dispositivo recopila a lo largo del tiempo y se representan con una transmisión continua. Por ejemplo, la temperatura.
- Las medidas de tipo **Evento** son los datos en un momento dado que representan algo significativo en el dispositivo. Los eventos tienen una gravedad asociada que representa la importancia del evento. Por ejemplo, error del motor del ventilador.
- Las medidas de tipo **Estado** representan el estado del dispositivo o sus componentes durante un período de tiempo. Por ejemplo, el modo de ventilador, que se puede definir en funcionamiento y detenido como los dos estados posibles.

### <a name="create-a-telemetry-measurement"></a>Creación de una medida de tipo Telemetría
Para agregar una nueva medida de tipo Telemetría, haga clic en el botón **+New Measurement** (+Nueva medida) que abre un formulario con opciones para seleccionar el tipo de medida. Seleccione **Telemetry** (Telemetría) y escriba los detalles en el formulario **Create Telemetry** (Crear telemetría).

> [!NOTE]
> Cuando se conecte un dispositivo real, preste atención al nombre de la medida que dicho dispositivo notifica. El nombre debe coincidir exactamente con el **Nombre de campo** de una medida.

Por ejemplo, puede agregar una nueva medida de telemetría de temperatura:

![Formulario de medidas](./media/howto-set-up-template/measurementsform.png)

Después de elegir **Guardar**, la medida **Temperatura** aparece en la lista de medidas y un operador puede ver la visualización de los datos de temperatura que el dispositivo está recopilando.

![Gráfico de medidas](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Creación de una medida de tipo Evento
Para agregar una nueva medida de tipo Evento, haga clic en el botón **+New Measurement** (+Nueva medida) que abre un formulario con opciones para seleccionar el tipo de medida. Seleccione **Evento** y escriba los detalles en el formulario **Create Event** (Crear evento).

En este formulario, rellene los siguientes campos para el evento: **Nombre para mostrar**, **Nombre de campo** y **Gravedad**. Puede elegir entre los tres niveles disponibles de gravedad: **Error**, **Advertencia** e **Información**.  

Por ejemplo, puede agregar un nuevo evento denominado “Error del motor del ventilador”.

![Formulario de medidas de tipo Evento](./media/howto-set-up-template/eventmeasurementsform.png)

Después de elegir **Guardar**, la medida **Error del motor del ventilador** aparece en la lista de medidas y un operador puede ver la visualización de los datos de evento que el dispositivo está enviando.

![Gráfico de medidas de tipo Evento](./media/howto-set-up-template/eventmeasurementschart.png)

Para ver detalles adicionales sobre el evento, haga clic en el icono de evento en el gráfico:

![Detalle de medidas de tipo Evento](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Creación de una medida de tipo Estado
Para agregar una nueva medida de tipo Estado, haga clic en el botón **+New Measurement** (+Nueva medida) que abre un formulario con opciones para seleccionar el tipo de medida. Seleccione **Estado** y escriba los detalles en el formulario **Create State** (Crear estado).

En este formulario, rellene los siguientes campos para el evento: **Nombre para mostrar**, **Nombre de campo** y los **valores** posibles del estado. Cada **valor** también puede tener un nombre para mostrar que se usará al mostrar el valor en los gráficos y las tablas.

Por ejemplo, puede agregar un nuevo estado denominado "Modo de ventilador", que tenga dos valores posibles que el dispositivo puede enviar: **Operativo** y **Detenido**.

![Formulario de medidas de tipo Estado](./media/howto-set-up-template/statemeasurementsform.png)

Después de elegir **Guardar**, la medida de estado **Modo de ventilador** aparece en la lista de medidas y el operador puede ver la visualización de los datos de evento que el dispositivo está enviando.

![Gráfico de medidas de tipo Estado](./media/howto-set-up-template/statemeasurementschart.png)

En caso de que haya demasiados puntos de datos enviados por el dispositivo en un intervalo pequeño, la medida de estado se muestra con un objeto visual diferente, tal y como se muestra a continuación. Si hace clic en el gráfico, se muestran todos los puntos de datos dentro de ese período de tiempo en un orden cronológico. También puede reducir el intervalo de tiempo para ver la medida trazada en el gráfico.

![Detalle de medidas de tipo Estado](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Settings

La configuración controla un dispositivo. Permite a los operadores de la aplicación proporcionar entradas al dispositivo. Puede agregar varias configuraciones a la plantilla de dispositivo que aparecen como iconos en la pestaña **Configuración** para que los operadores los utilicen. Existen seis tipos de configuración que se pueden agregar: número, texto, fecha, alternar, lista de selección y etiqueta de sección.

> [!NOTE]
> Cuando se conecte un dispositivo real, preste atención al nombre de la configuración que dicho dispositivo notifica. El nombre debe coincidir exactamente con la opción **Field Name** (Nombre de campo) de una configuración.

La configuración puede tener uno de estos tres estados. El dispositivo informa de estos estados.

- **Sincronizado** (Sincronizado): el dispositivo ha cambiado para reflejar el valor de configuración.

- **Pending** (Pendiente): el dispositivo está actualmente cambiando al valor de configuración.

- **Error**: el dispositivo ha devuelto un error.

Por ejemplo, puede agregar una nueva configuración de velocidad del ventilador:

![Formulario de configuración](./media/howto-set-up-template/settingsform.png)

Después de elegir **Guardar**, la configuración **Fan speed** (Velocidad del ventilador) aparece como un icono y está lista para usarse para cambiar la velocidad del ventilador del dispositivo.

> [!NOTE]
> Después de crear un nuevo icono, puede probar la nueva configuración. En primer lugar, desactive el modo de diseño en la parte superior derecha de la pantalla:

![Icono de configuración](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Properties (Propiedades)

Las propiedades son los metadatos de dispositivo asociados al dispositivo, como la ubicación y el número de serie de dicho dispositivo. Puede agregar varias propiedades a la plantilla de dispositivo, que aparecen como iconos en la pestaña **Propiedades**. Un operador puede especificar los valores de las propiedades cuando crea un nuevo dispositivo y puede editar estos valores en cualquier momento. Existen seis tipos de propiedades que puede agregar: número, texto, fecha, alternar, propiedad de dispositivo y etiqueta.

Existen dos tipos de propiedades:

- Las **propiedades de dispositivo** son propiedades de las que informa el dispositivo.
- Las **propiedades de aplicación** son propiedades almacenadas exclusivamente en la aplicación. El dispositivo no tiene conocimiento de las propiedades de aplicación.

> [!NOTE]
> En lo que a las propiedades de dispositivo se refiere, cuando haya conectado un dispositivo real, preste atención al nombre de la propiedad que dicho dispositivo notifica. El nombre debe coincidir exactamente con la opción **Field Name** (Nombre de campo) de la propiedad. En lo que a las propiedades de aplicación se refiere, el nombre del campo puede ser cualquier cosa que desee, siempre que el nombre sea único en la plantilla de dispositivo.

Por ejemplo, puede agregar la ubicación del dispositivo como una nueva propiedad:

![Formulario Propiedades](./media/howto-set-up-template/propertiesform.png)

Después de elegir **Guardar**, la ubicación del dispositivo aparece como un icono:

![Icono Propiedades](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Después de crear un nuevo icono, puede cambiar el valor de propiedad. En primer lugar, desactive el modo de diseño que se encuentra en la parte superior derecha de la pantalla.

## <a name="rules"></a>Reglas

Las reglas permiten a los operadores supervisar los dispositivos prácticamente en tiempo real. Las reglas invocan automáticamente **acciones**, como el envío de un correo electrónico cuando la regla se desencadena. Actualmente hay un tipo de regla disponible:

- **Regla de telemetría**: una regla de telemetría se desencadena cuando la telemetría del dispositivo seleccionado supera un umbral especificado. Más información sobre las [reglas de telemetría](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>panel

El panel es donde puede ir un operador para ver información sobre un dispositivo. Como generador, puede agregar iconos a esta página que ayuden a los operadores a entender cómo se comporta el dispositivo. Puede agregar varios iconos de panel a la plantilla de dispositivo. Existen seis tipos de iconos de panel que puede agregar: imagen, gráfico de líneas, gráfico de barras, KPI, configuración y propiedades y etiqueta.

Por ejemplo, puede agregar un icono **Settings and Properties** (Configuración y propiedades) para mostrar una selección de los valores actuales de la configuración y las propiedades:

![Formulario de detalles de los dispositivos en el panel](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Ahora, cuando un operador vea el panel, podrá ver este icono que muestra las propiedades y la configuración del dispositivo:

![Icono del panel](./media/howto-set-up-template/dashboardtile.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a configurar una plantilla de dispositivo en la aplicación de Azure IoT Central, este es el paso siguiente sugerido:

> [!div class="nextstepaction"]
> [Creación de una nueva versión de plantilla de dispositivo](howto-version-devicetemplate.md)
