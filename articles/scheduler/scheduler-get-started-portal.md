<properties
 pageTitle="Introducción al uso de Programador en el Portal de administración"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article" 
 ms.date="08/04/2015"
 ms.author="krisragh"/>

# Introducción al uso de Programador en el Portal de administración

## Configuración de Programador de Azure mediante el Portal de administración de Azure para crear trabajos de manera rápida y fácil

Para completar este tutorial, necesita una cuenta de Azure que tenga habilitada la característica Programador de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://msdn.microsoft.com/library/)

## Introducción

Es fácil crear trabajos y colecciones de trabajos en Programador de Azure con el Portal de administración de Azure. Este tutorial le guiará por el proceso de creación de la colección de trabajos que se va a usar para almacenar trabajos, la creación de un trabajo en una colección de trabajos y una información general de las tareas de administración y supervisión de los trabajos disponibles mediante el Portal de administración. No es necesario que tenga experiencia previa con Azure para usar este tutorial.

La primera vez que abra el Portal de administración de Azure, entrará automáticamente en la pestaña **TODOS LOS ELEMENTOS**. Las columnas de la pestaña **TODOS LOS ELEMENTOS** se pueden ordenar. Para ver los trabajos de Programador y las colecciones de trabajos, haga clic en la pestaña **PROGRAMADOR**.

![][1]

## Creación de una colección de trabajos y de un trabajo

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/).  

2.  Haga clic en **Servicios de aplicaciones**, a continuación, haga clic en **Crear nuevo**, seleccione **Programador**, y, a continuación, haga clic en **Creación personalizada**. <br /><br /> ![][2]

3.  En **Colección de trabajos**, seleccione una colección de trabajos existente si ya ha creado una y desea agregar este trabajo a esa colección de trabajos, mediante la selección del nombre de la colección de trabajos existentes en el menú desplegable **Colección de trabajos**. Si no dispone de una colección de trabajos existentes a la que le gustaría agregar el trabajo, seleccione **Crear nuevo** y escriba un nombre para identificar la nueva colección de trabajos.<br /><br /> ![][3]

4.  En **Región**, seleccione la región geográfica de la colección de trabajos.

5.  Haga clic en la tecla de flecha para crear la colección de trabajos y pasar a la siguiente fase: creación de un trabajo.

6.  Vamos a crear un trabajo que simplemente selecciona http://www.microsoft.com/ con una solicitud GET. En la pantalla de la acción del trabajo, defina los siguientes valores para los campos de formulario solicitados:

    1.  **Nombre:** ` getmicrosoft`  

    2.  **Tipo de acción:** ` HTTP`

    3.  **Método:** ` GET`

    4.  **URI:** ` http://www.microsoft.com`

   	![][4]

7.  En el paso siguiente, defina una programación. El trabajo se puede definir como un trabajo único, pero vamos a seleccionar una programación de periodicidad. Algunas capturas de pantalla de este tutorial muestran una periodicidad de 1 minuto con fines ilustrativos únicamente, pero seleccione una periodicidad de 12 horas.

    1.  **Repetir cada:** ` 12 Hours`  

    2.  **Inicio:** ` Now`

    3.  **Finalización:** ` Select date 2 days after current day and any time`

   	![][5]

8.  Por último, haga clic en Aceptar.

9.  Puede tardar algo de tiempo la creación de la colección de trabajos y del trabajo. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal.

   	![][6]

   	Una vez creado el trabajo y la colección de trabajos, un mensaje le indicará que el trabajo o la colección de trabajos se han creado correctamente. El trabajo se mostrará en la sección Trabajos de la sección Programador y la colección de trabajos se mostrará en la sección Colecciones de trabajos. Para configurar opciones avanzadas adicionales en el trabajo, consulte la sección "Configuración de un trabajo" a continuación.

   	![][7]

## Administración y supervisión de colecciones de trabajos y trabajos

Cuando se crea una colección de trabajos, se muestra en la pantalla principal de administración de Programador.

![][8]

Si se hace clic en una colección de trabajos, se abre una nueva ventana con las opciones siguientes:

1.  Panel  

2.  Escala

3.  Historial

4.  Trabajos

Los temas siguientes describen estas pestañas con mayor detalle.

### Panel

Al hacer clic en el nombre de la colección de trabajos, aparece la pestaña Panel. El Panel muestra la siguiente información:

![][9]

#### Información general del uso de trabajo e información general del uso de ejecución

Una tabla y una serie de gráficos que muestran una lista fija de métricas. Estas métricas proporcionan valores en tiempo real relacionados con el estado de la colección de trabajos, como:

1.  Trabajos actuales  

2.  Trabajos completados

3.  Trabajos con errores

4.  Trabajos habilitados

5.  Trabajos deshabilitados

6.  Ejecuciones de trabajos

#### Vista rápida

Una tabla que muestra una lista fija de métricas de configuración y de estado. Estas métricas proporcionan valores en tiempo real relacionados con el estado y la configuración asociados a la colección de trabajos, como:

1.  Estado  

2.  Region

3.  Número de errores

4.  Número de errores de repetición

5.  URI

### Escala

En la pestaña Escala, puede cambiar la configuración y el nivel de servicio utilizado por Programador.

![][10]

#### General

Muestra si se encuentra en un plan **Gratis** o **Estándar**.

#### Cuotas

Programador de Azure implementa las cuotas en función de varias condiciones. En esta sección se enumeran los umbrales de cuota y puede cambiarlos. De forma predeterminada, hay un conjunto de cuotas configuradas. Los límites de esta configuración de cuota están restringidos por el plan y el cambio del plan puede afectar al precio. Las cuotas se pueden cambiar para escalar el Programador. Las opciones incluyen:

1.  Nº máximo de trabajos  

2.  Frecuencia máxima

3.  Intervalo máximo

### Historial

La pestaña Historial muestra la información siguiente para el trabajo seleccionado:

![][11]

#### Tabla de historial

Una tabla que muestra las métricas seleccionadas para cada ejecución del trabajo en el sistema para el trabajo seleccionado. Estas métricas proporcionan valores en tiempo real relacionados con el estado del Programador.

#### Métricas disponibles

Están disponibles las siguientes métricas y contadores de rendimiento:

1.  Estado  

2.  Detalles

3.  Reintentos

4.  Número de ejecuciones (1ª, 2ª, 3ª, etc.)

5.  Marca de tiempo de ejecución

Puede hacer clic en **Ver detalles del historial** para consultar la respuesta completa a cada ejecución. Este cuadro de diálogo también le permitirá copiar la respuesta en el Portapapeles.

![][12]

### Trabajos

La pestaña Trabajos muestra la siguiente información para supervisar el historial de ejecución de los trabajos:

![][13]

#### Tabla de trabajos

Una tabla que muestra las métricas seleccionadas para cada ejecución del trabajo en el sistema. Estas métricas proporcionan valores en tiempo real relacionados con el estado del Programador.

#### Deshabilitar, habilitar o eliminar un trabajo

Al hacer clic en un nombre de trabajo, le ofrece la opción de habilitar, deshabilitar o eliminar el trabajo. Los trabajos eliminados no pueden recuperarse.

#### Métricas disponibles

Están disponibles las siguientes métricas y contadores:

1.  Nombre  

2.  Última ejecución

3.  Siguiente ejecución

4.  Estado

5.  Frecuencia

6.  Errores

7.  Errores

8.  Ejecuciones

9.  Tipo de acción

### Configurar un trabajo

Al hacer clic en un trabajo en la pantalla "Trabajos", podrá configurar ese trabajo. Esto le permite configurar opciones avanzadas adicionales más allá de lo que está disponible en el Asistente de creación rápida. Para configurar un trabajo, haga clic en la flecha derecha situada junto al nombre del trabajo en la pantalla "Trabajos".

La página de configuración de trabajo permite actualizar la configuración del trabajo. A continuación se muestra la página de configuración del trabajo para los trabajos HTTP y HTTPS. Para los tipos de acción de los trabajos HTTP y HTTPS, puede cambiar el método a cualquier verbo HTTP permitido. También puede agregar, eliminar o cambiar los encabezados y la información de autenticación básica.

![][14]

La página de configuración del trabajo aparece como se muestra a continuación para los trabajos de la cola de almacenamiento. Para los tipos de acciones de la cola de almacenamiento, puede cambiar la cuenta de almacenamiento, el nombre de la cola, el token SAS y el cuerpo. La sección "Programar" (no se muestra a continuación) es idéntica a la sección "Programar" de los tipos de acción del trabajo HTTP/HTTPS.

![][15]

Por último, para todos los tipos de acción, puede cambiar la misma programación así como su comportamiento de repetición. Puede cambiar la fecha y hora de inicio, la programación de periodicidad y la fecha y hora de finalización (si el trabajo es periódico). Después de realizar cambios, puede guardarlos haciendo clic en 'Guardar' o descartarlos haciendo clic en 'Descartar'.

## Otras referencias

 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador](scheduler-concepts-terms.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de Programador](https://msdn.microsoft.com/library/dn528946)

 [Referencia de cmdlets de PowerShell de Programador](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador](scheduler-outbound-authentication.md)



[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
 

<!---HONumber=August15_HO6-->