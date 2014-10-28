<properties title="Monitor a DocumentDB Account" pageTitle="Monitor a DocumentDB account | Azure" description="Learn how to monitor your DocumentDB account for performance metrics (such as requests and server errors) and usage metrics (such as storage consumption)." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, monitor, accounts" services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="brradsev"></tags>

# Supervisión de una cuenta de Base de datos de documentos

Puede supervisar sus cuentas de Base de datos de documentos en el [Portal de vista previa de Azure][Portal de vista previa de Azure]. Para cada cuenta de Base de datos de documentos, existen métricas de rendimiento (como solicitudes y errores de servidor) y métricas de uso (como consumo de almacenamiento).

## En este artículo

-   [Visualización de métricas de rendimiento para una cuenta de Base de datos de documentos][Visualización de métricas de rendimiento para una cuenta de Base de datos de documentos]
-   [Personalización de las vistas de métricas de rendimiento para una cuenta de Base de datos de documentos][Personalización de las vistas de métricas de rendimiento para una cuenta de Base de datos de documentos]
-   [Creación de gráficos de métricas de rendimiento paralelos][Creación de gráficos de métricas de rendimiento paralelos]
-   [Visualización de métricas de uso para una cuenta de Base de datos de documentos][Visualización de métricas de uso para una cuenta de Base de datos de documentos]
-   [Configuración de alertas de métricas de rendimiento para una cuenta de Base de datos de documentos][Configuración de alertas de métricas de rendimiento para una cuenta de Base de datos de documentos]
-   [Pasos siguientes][Pasos siguientes]

## <span id="metrics"></span></a>Visualización de métricas de rendimiento para una cuenta de Base de datos de documentos

1.  En el [Portal de vista previa de Azure][Portal de vista previa de Azure], haga clic en **Examinar**, luego en **Cuentas de Base de datos de documentos** y, después, haga clic en el nombre de la cuenta cuyas métricas de rendimiento desea ver.
2.  En el modo **Supervisión** puede ver de forma predeterminada:

    -   El total de solicitudes del día actual.
    -   La media de solicitudes por segundo del día actual.

    ![][]

3.  Al hacer clic en la parte **Total o promedio de solicitudes por segundo** se abre un cuadro de **Métrica** detallado.
4.  El cuadro Métrica muestra los detalles de las métricas seleccionadas. En la parte superior del cuadro hay un gráfico y, debajo, una tabla que muestra valores de agregación de las métricas seleccionadas, como el promedio, el valor máximo y el mínimo. El cuadro Métrica muestra también la lista de alertas que se han definido, filtrada por las métricas que aparecen en el cuadro actual (de esta forma, si tiene un número de alertas, solo verá aquí las pertinentes).

    ![][1]

## <span id="custom"></span></a>Personalización de las vistas de métricas de rendimiento para una cuenta de Base de datos de documentos

1.  Para personalizar las métricas que se muestran en una determinada parte, haga clic con el botón secundario en el gráfico de métricas y luego seleccione **Editar gráfico**.
    ![][2]

2.  En el cuadro **Editar gráfico**, hay opciones para modificar las métricas que se muestran en la parte, junto con su intervalo de tiempo.

    ![][3]

3.  Para cambiar las métricas que se muestran en la parte, solo tiene que marcar o desmarcar las métricas de rendimiento disponibles y luego hacer clic en **Guardar** en la parte inferior del cuadro.
4.  Para cambiar el intervalo de tiempo, elija un intervalo diferente (por ejemplo, **Hora pasada**) y luego haga clic en **Guardar** en la parte inferior del cuadro.

    ![][4]

5.  El intervalo de tiempo personalizado le permite elegir cualquier período de tiempo de las dos últimas semanas.
6.  Cuando haga clic en **Guardar**, los cambios continuarán hasta que salga del cuadro Cuenta de Base de datos de documentos. Cuando regrese en otro momento, verá la métrica y el intervalo de tiempo originales.

## <span id="create"></span></a>Creación de gráficos paralelos

El Portal de vista previa de Azure le permite crear gráficos de métricas paralelos.

1.  En primer lugar, haga clic con el botón secundario en el gráfico que desea iniciar y seleccione **Personalizar**.
    ![][5]

2.  Haga clic en **Clonar** en el menú para copiar la parte.

    ![][6]

3.  Finalmente, haga clic en **Done** en la barra de herramientas, en la parte superior de la pantalla. Ahora puede tratar esta parte como otra parte de métricas y personalizar las métricas y el intervalo de tiempo que se muestra en la parte. De esta forma, puede ver dos gráficos de métricas diferentes en paralelo al mismo tiempo.

    ![][7]

> Tenga en cuenta que el intervalo de tiempo del gráfico y las métricas seleccionadas se restablecen a los valores predeterminados de la parte cuando sale del Portal de vista previa de Azure.

## <span id="view"></span></a>Visualización de las métricas de uso para una cuenta de Base de datos de documentos

1.  En el [Portal de vista previa de Azure][Portal de vista previa de Azure], haga clic en **Examinar**, luego en **Cuentas de Base de datos de documentos** y, después, haga clic en el nombre de la cuenta cuyas métricas de uso desea ver.
2.  En el modo **Uso** puede ver de forma predeterminada:

    -   El almacenamiento consumido en la cuenta
    -   El almacenamiento máximo disponible de la cuenta
    -   El uso de datos adjuntos
    -   El uso de usuarios y permisos
    -   La asignación de unidades de capacidad
    -   El uso de datos adjuntos dentro de la cuenta.
        ![][8]

## <span id="setup"></span></a>Configuración de alertas de métricas de rendimiento para una cuenta de Base de datos de documentos

1.  En el [Portal de vista previa de Azure][Portal de vista previa de Azure], haga clic en **Examinar**, luego en **Cuentas de Base de datos de documentos** y, después, haga clic en el nombre de la cuenta cuyas alertas de métricas de rendimiento desea configurar.
2.  En el modo **Operaciones**, haga clic en la parte **Reglas de alerta**.

    ![][9]

3.  En el cuadro Reglas de alerta, haga clic en **Agregar alerta**.

    ![][10]

4.  En el cuadro **Agregar una regla de alerta**, especifique:

    -   El nombre de la regla de alerta que va a configurar.
    -   Una descripción de la nueva regla de alerta.
    -   La métrica de la regla de alerta.
    -   La condición, el umbral y el período que determinan cuándo se activa la alerta. Por ejemplo, un número de errores de servidor mayor que cinco durante los últimos 15 minutos.
    -   Si se envía un correo electrónico al administrador del servicio y a los coadministradores cuando la alerta de dispara.
    -   Direcciones de correo electrónico adicionales para las notificaciones de alerta.
        ![][11]

## <span id="next"></span></a>Pasos siguientes

-   Para obtener más información sobre Base de datos de documentos, consulte la documentación correspondiente en [azure.com][azure.com]

<!--Anchors-->

  [Portal de vista previa de Azure]: https://portal.azure.com/
  [Visualización de métricas de rendimiento para una cuenta de Base de datos de documentos]: #metrics
  [Personalización de las vistas de métricas de rendimiento para una cuenta de Base de datos de documentos]: #custom
  [Creación de gráficos de métricas de rendimiento paralelos]: #create
  [Visualización de métricas de uso para una cuenta de Base de datos de documentos]: #view
  [Configuración de alertas de métricas de rendimiento para una cuenta de Base de datos de documentos]: #setup
  [Pasos siguientes]: #next
  []: http://i.imgur.com/y7pigTT.png
  [1]: http://i.imgur.com/6rBNPBL.png
  [2]: http://i.imgur.com/tRZEHk1.png
  [3]: http://i.imgur.com/G4UTi5U.png
  [4]: ./media/documentdb-monitor-accounts/madocdb5.png
  [5]: http://i.imgur.com/vLXWftF.png
  [6]: ./media/documentdb-monitor-accounts/madocdb7.png
  [7]: ./media/documentdb-monitor-accounts/madocdb8.png
  [8]: http://i.imgur.com/sL5inOu.png
  [9]: ./media/documentdb-monitor-accounts/madocdb10.png
  [10]: ./media/documentdb-monitor-accounts/madocdb11.png
  [11]: http://i.imgur.com/Inra4Po.png
  [azure.com]: http://go.microsoft.com/fwlink/p/?LinkID=402319
