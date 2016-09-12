<properties
 pageTitle="Administración de dispositivos mediante la IU desde el Centro de IoT | Microsoft Azure"
 description="Tutorial de uso de la IU para la administración de dispositivos desde el Centro de IoT de Azure"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo

Interactuar con la interfaz de usuario de administración de dispositivos de ejemplo lo ayudará a consolidar los conceptos y las funcionalidades que se tratan en la [introducción][lnk-dm-overview] y los artículos de [introducción][lnk-get-started] a la administración de dispositivos desde Centro de IoT de Azure. Este artículo le guiará a través de cada uno de los tres conceptos de administración de dispositivos principales, *dispositivo gemelo*, *consultas de dispositivo*, y *trabajos del dispositivo*, tal y como se representan en la interfaz de usuario web de administración del dispositivo de ejemplo.

Los desarrolladores que buscan para crear su propia experiencia interactiva de administración de dispositivos pueden bifurcar la base de código de la interfaz de usuario de ejemplo para usarla en un proyecto personalizado. Puede revisar todos los documentos de código y el archivo Léame del proyecto que detallan funcionalidades adicionales para los programadores en el repositorio GitHub de la [interfaz de usuario de administración de dispositivos de Azure IoT][lnk-dm-github].

## Requisitos previos

Antes de comenzar este tutorial, debe completar los pasos del artículo [Introducción a la administración de dispositivos de Centro de IoT de Azure con C# (versión preliminar)][lnk-get-started]. Si no lo ha hecho, vuelva y realice todos los pasos de este artículo para continuar.

Cuando haya completado el tutorial de introducción, en su sistema de prueba se ejecutará la siguiente:

- Seis dispositivos simulados **iotdm\_simple\_sample** que se ejecutan en ventanas de consola/terminal, cada uno con un mensaje de "REGISTRADO".

- La aplicación de interfaz de usuario de ejemplo de administración de dispositivos integrada y en ejecución localmente en <http://127.0.0.1:3003>.

## Vista de dispositivos predeterminada

La pantalla principal predeterminada de la interfaz de usuario de ejemplo de administración de dispositivos es la vista **Dispositivos**, que incluye los siguientes 5 componentes:

![][1]

1.  *Botones de navegación*: vista **Dispositivos** (seleccionada), vista **Historial de trabajos** y vista **Add a Device** (Agregar un dispositivo.

2. *Búsqueda de dispositivos*: busque y edite dispositivos por el identificador.

3.  *Acciones de dispositivo*: **Editar**, **Eliminar** y **Exportar**.

4.  *Device jobs* (Trabajos del dispositivo): **Reiniciar**, **Firmware Update** (Actualización de Firmware) y **Restablecimiento de fábrica**.

5.  *Filtro de la cuadrícula de dispositivos*: editor de filtros para crear y guardar vistas personalizadas de la cuadrícula de dispositivos.

6.  *Cuadrícula de dispositivos*: vista de todos los dispositivos registrados con la instancia de Centro de IoT y las propiedades predeterminadas (**Id. de dispositivo**, **Estado** y **Etiquetas**).

La [introducción a la administración de dispositivos][lnk-dm-overview] presentó el concepto de *dispositivo gemelo*, que representa un dispositivo físico (o simulado) en el Centro de IoT de Azure. En la cuadrícula de dispositivos puede seleccionar cualquier dispositivo registrado de la lista para ver y editar su gemelo.

Para acceder a esta vista detallada en el primer dispositivo simulado (**Device11 7ce4a850**), seleccione la fila de dispositivo correspondiente. A continuación, haga clic en el botón **Editar** (también puede haga doble clic en la fila o escribir el identificador de dispositivo en el cuadro de búsqueda).

Ahora verá la representación integral de los componentes del dispositivo gemelo donde podrá actualizar las propiedades editables y realizar otras operaciones de dispositivo, como se detalla a continuación:

![][2]

1.  **Edit a Device Twin** (Editar un dispositivo gemelo): esta opción incluye la opción **Habilitado/Deshabilitado** para el dispositivo.

2.  **Propiedades del servicio**: esta opción incluye **Etiquetas** para el dispositivo.

3.  **Device Properties** (Propiedades del dispositivo): haga clic para expandir esta sección.

4.  Botón **Actualizar**: recupera los valores y propiedades más recientes del dispositivo gemelo.

Para continuar y volver a la página con la lista de dispositivos predeterminada, haga clic en **Cancelar** en la esquina inferior derecha de esta vista.

## Uso de consultas de dispositivo para filtrar la vista

Las consultas de dispositivo son una forma eficaz de localizar rápidamente un dispositivo o un grupo de dispositivos con una propiedad determinada (por ejemplo, una etiqueta en particular). El ejemplo de interfaz de usuario usa consultas de dispositivo para rellenar la lista de la página predeterminada. El ejemplo de interfaz de usuario le permite agregar y quitar propiedades del servicio de la tabla y filtrar por algunas de estas propiedades.

Realice los pasos siguientes para crear un filtro de cliente en la etiqueta de propiedad de servicio "bacon":

1.  Haga clic en el icono de embudo para abrir la vista de edición de la consulta de dispositivo:

    ![][3]

2.  Haga clic en **+ Agregar un filtro** para expandir el editor. Seleccione **Etiquetas** en la lista desplegable de propiedades, escriba **bacon** en el campo de texto y haga clic en **Aplicar**. La lista de dispositivos ahora muestra solo los tres dispositivos con la etiqueta "bacon":

    ![][4]

3.  En el campo de título de la consulta (junto al icono de embudo), asigne el nombre de la consulta **Only Bacon** (Solo bacon) y haga clic en **Guardar**:

    ![][5]

4.  Haga clic en el icono de embudo para salir del editor consultas de dispositivo.

## Simulación de reinicios con un trabajo de dispositivo 

Como ha aprendido en la introducción de la administración de dispositivos, los trabajos de dispositivo permiten organizar acciones simples o complejas en uno o más dispositivos físicos. En esta sección, se creará un trabajo de dispositivo en el ejemplo de interfaz de usuario para realizar una operación de reinicio en todos los dispositivos de simulación con la etiqueta "bacon":

1.  En la lista de consultas de dispositivo **Only Bacon** (Solo bacon), haga clic en la fila de cada dispositivo para el reinicio:

    ![][6]

2.  Haga clic en el botón **Reiniciar** de la barra de herramientas de trabajos de dispositivo para crear el trabajo de reinicio. Después de confirmar con **Sí**, haga clic en hipervínculo **Historial de trabajos** en el cuadro de diálogo **Job for Device has started** (Trabajo de dispositivo iniciado) resultante para desplazarse a la vista de trabajos del dispositivo.

    ![][7]

Así habrá creado un solo trabajo primario que genera tres trabajos secundarios, cada uno de los cuales realiza la operación de reinicio en uno de los tres dispositivos etiquetados como "bacon":

![][8]

Actualice esta pantalla después de unos minutos para ver que el estado del trabajo primario y los tres secundarios se cambia a **Completado**. Los nuevos valores de estado muestran que las operaciones de reinicio se realizaron correctamente y se confirmaron mediante los dispositivos simulados. Con la columna **Id. de dispositivo** se determina qué trabajos están asociados a los dispositivos.


> [AZURE.NOTE] Si los trabajos secundarios devuelven el estado "erróneo", compruebe que los dispositivos simulados se estén ejecutando en el sistema de prueba. De lo contrario, vuelva a ejecutar el script simulate.bat o simulate.sh y repita los pasos del trabajo de reinicio del dispositivo de la sección anterior.

## Pasos siguientes

Ha completado una exploración interactiva por los conceptos de administración de dispositivos, como el ejemplo de uso de la interfaz de usuario para administrar dispositivos. Si desea una descripción más avanzada de las API de administración de dispositivos y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

- [How to use the device twin (Uso de dispositivos gemelos)][lnk-tutorial-twin]
- [How to find device twins using queries (Búsqueda de dispositivos gemelos mediante consultas)][lnk-tutorial-queries]
- [How to use device jobs to update device firmware (Uso de trabajos de dispositivos para actualizar el firmware del dispositivo)][lnk-tutorial-jobs]
- [Habilitación de los dispositivos administrados detrás de una puerta de enlace de IoT][lnk-dm-gateway]
- [Introducción a la biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure][lnk-library-c]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Diseño de la solución][lnk-design]
- [Guía del desarrollador][lnk-devguide]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del Portal de Azure][lnk-portal]
- [Protección total de la solución de IoT][lnk-securing]

[1]: media/iot-hub-device-management-ui-sample/image1.png
[2]: media/iot-hub-device-management-ui-sample/image2.png
[3]: media/iot-hub-device-management-ui-sample/image3.png
[4]: media/iot-hub-device-management-ui-sample/image4.png
[5]: media/iot-hub-device-management-ui-sample/image5.png
[6]: media/iot-hub-device-management-ui-sample/image6.png
[7]: media/iot-hub-device-management-ui-sample/image7.png
[8]: media/iot-hub-device-management-ui-sample/image8.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management/

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0831_2016-->