---
title: "Información de rendimiento de consultas para Azure SQL Database | Microsoft Docs"
description: "La supervisión del rendimiento de las consultas identifica las consultas que más CPU consumen en una base de datos SQL de Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: c2f580b2-3835-453f-89f5-140e02dd2ea7
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 1925d4ff8f5b16a0df56de987f8653cfd8441c52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-query-performance-insight"></a>Query Performance Insight de Base de datos SQL de Azure
La administración y ajuste del rendimiento de las bases de datos relacionales son tareas difíciles que requieren una gran inversión de tiempo y muchos conocimientos. Información de rendimiento de consultas permite dedicar menos tiempo a la solución de problemas de rendimiento de bases de datos, ya que proporciona:

* Información más detallada sobre el consumo de recursos (DTU) de las bases de datos. 
* Las consultas principales por CPU, duración y recuento de ejecuciones, que se pueden ajustar para mejorar el rendimiento.
* La capacidad de profundizar en los detalles de una consulta, ver su texto e historial de utilización de recursos. 
* Anotaciones de ajuste del rendimiento que muestran las acciones realizadas por el [Asesor de Base de datos SQL de Azure](sql-database-advisor.md)  



## <a name="prerequisites"></a>Requisitos previos
* Información de rendimiento de consultas requiere que el [Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx) esté activo en la base de datos. Si Almacén de consultas no está en ejecución, el portal le pedirá que lo active.

## <a name="permissions"></a>Permisos
Los siguientes permisos de [control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) se requieren para usar Query Performance Insight: 

* Se requieren permisos de **lector**, **propietario**, **colaborador**, **colaborador de base de datos SQL** o **colaborador de SQL Server** para ver las consultas y los gráficos que más recursos consumen. 
* Se requieren permisos de **propietario**, **colaborador**, **colaborador de base de datos SQL** o **colaborador de SQL Server** para ver el texto de la consulta.

## <a name="using-query-performance-insight"></a>Uso de Query Performance Insight
Query Performance Insight es fácil de usar:

* Abra [Portal de Azure](https://portal.azure.com/) y busque la base de datos que desee examinar. 
  * En el menú de la izquierda, bajo Soporte y solución de problemas, seleccione "Información de rendimiento de consultas".
* En la primera pestaña, revise la lista de consultas que más recursos consumen.
* Seleccione una consulta individual para ver sus detalles.
* Abra el [Asesor de Base de datos SQL de Azure](sql-database-advisor.md) y compruebe si existen recomendaciones disponibles.
* Use controles deslizantes o iconos de zoom para cambiar el intervalo observado.
  
    ![panel de rendimiento](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Para proporcionar información de rendimiento de consultas, es preciso que el Almacén de consultas para Base de datos SQL capture un par de horas de datos. Si la base de datos no tiene actividad o Almacén de consultas no estuvo activo durante un período determinado, los gráficos estará vacíos al mostrar dicho período. Puede habilitar el almacén de consulta en cualquier momento si no se está ejecutando.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Revisión de las consultas que más CPU consumen
En el [portal](http://portal.azure.com) , realice estas acciones:

1. Vaya a una base de datos SQL y haga clic en **Toda la configuración** > **Soporte y solución de problemas** > **Información de rendimiento de consultas**. 
   
    ![Información de rendimiento de consultas][1]
   
    Se abre la vista de las principales consultas y aparece la lista de las consultas principales que más CPU consumen.
2. Para más detalles, haga clic alrededor del gráfico.<br>La línea superior muestra el porcentaje general de DTU de la base de datos, mientras que las barras muestran el porcentaje de CPU consumido por las consultas seleccionadas durante el intervalo seleccionado (por ejemplo, si se selecciona **Semana anterior** , cada barra representa un día).
   
    ![principales consultas][2]
   
    La cuadrícula inferior representa información agregada para las consultas visibles.
   
   * Id. de consulta: identificador único de la consulta en la base de datos.
   * CPU por consulta durante el intervalo observable (depende de la función de agregación).
   * Duración por consulta (depende de la función de agregación).
   * Número total de ejecuciones para una consulta determinada.
     
     Seleccione o anule la selección de las consultas individuales para incluirlas o excluirlas del gráfico mediante casillas.
3. Si los datos se quedan desusados, haga clic en el botón **Actualizar** .
4. Puede usar controles deslizantes y botones de zoom para cambiar el intervalo de observación e investigar los picos: ![configuración](./media/sql-database-query-performance/zoom.png).
5. Opcionalmente, si desea una vista diferente, puede seleccionar la pestaña **Personalizado** y establecer:
   
   * Métrica (CPU, duración, recuento de ejecuciones)
   * Intervalo de tiempo (últimas 24 horas, semana anterior, mes anterior). 
   * Número de consultas.
   * Función de agregación.
     
     ![Configuración](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Visualización de los detalles de las consultas individuales
Para ver los detalles de una consulta:

1. Haga clic en cualquiera de las consultas de la lista de las principales consultas.
   
    ![detalles](./media/sql-database-query-performance/details.png)
2. Se abre la vista de detalles y el consumo de CPU, la duración y el recuento de ejecuciones de las consultas se desglosan por tiempo.
3. Para más detalles, haga clic alrededor del gráfico.
   
   * El gráfico superior muestra la línea con el porcentaje general de DTU de la base de datos, mientras que las barras son el porcentaje de CPU consumido por la consulta seleccionada.
   * El segundo gráfico muestra la duración total por la consulta seleccionada.
   * El gráfico inferior muestra el número total de ejecuciones por la consulta seleccionada.
     
     ![detalles de consulta][3]
4. Opcionalmente, use controles deslizantes, botones de zoom o haga clic en **Configuración** para personalizar la forma en que se muestran los datos de consulta o para seleccionar otro período.

## <a name="review-top-queries-per-duration"></a>Revisión de las principales consultas por duración
En la reciente actualización de Información de rendimiento de consultas, presentamos dos nuevas métricas que pueden ayudarle a identificar posibles cuellos de botella: duración y recuento de ejecuciones.<br>

Las consultas de larga ejecución tienen el máximo potencial para bloquear recursos durante más tiempo, bloquear otros usuarios y limitar la escalabilidad. También son las mejores candidatas para la optimización.<br>

Para identificar consultas de larga ejecución:

1. Abra la pestaña **Personalizado** en Información de rendimiento de consultas para la base de datos seleccionada
2. Cambie las métricas por **duración**
3. Seleccione el número de consultas y el intervalo de observación
4. Seleccione la función de agregación
   
   * **Sum** aumenta el tiempo de ejecución de todas las consultas durante todo el intervalo de observación.
   * **Max** busca consultas cuyo tiempo de ejecución era máximo en todo el intervalo de observación.
   * **Avg** busca el tiempo medio de ejecución de todas las ejecuciones de consulta y le muestra el límite de estos promedios. 
     
     ![duración de la consulta][4]

## <a name="review-top-queries-per-execution-count"></a>Revisión de las principales consultas por recuento de ejecuciones
Es posible que el elevado número de ejecuciones no afecte a la propia base de datos y el uso de recursos puede ser bajo, pero la aplicación general podría ralentizarse.

En algunos casos, un recuento de ejecuciones muy alto puede dar lugar al aumento de los ciclos de ida y vuelta de red. Los ciclos de ida y vuelta afectan significativamente al rendimiento. Están sujetos a la latencia de red y a la latencia del servidor auxiliar. 

Por ejemplo, muchos sitios web controlados por datos tienen acceso en gran medida a la base de datos para cada solicitud de usuario. Mientras la agrupación de conexiones ayuda, el mayor tráfico de red y la carga de procesamiento del servidor de bases de datos pueden afectar de forma negativa al rendimiento.  La recomendación general es reducir al mínimo los ciclos de ida y vuelta.

Para identificar consultas ejecutadas de forma habitual (“consultas fragmentadas”):

1. Abra la pestaña **Personalizado** en Información de rendimiento de consultas para la base de datos seleccionada
2. Cambie las métricas por **recuento de ejecuciones**
3. Seleccione el número de consultas y el intervalo de observación
   
    ![recuento de ejecuciones de la consulta][5]

## <a name="understanding-performance-tuning-annotations"></a>Descripción de las anotaciones de ajuste del rendimiento
Al explorar su carga de trabajo en Información de rendimiento de consultas, puede que observe iconos con línea vertical en la parte superior del gráfico.<br>

Estos iconos son anotaciones; representan el rendimiento que afecta a las acciones realizadas por el [Asesor de Base de datos SQL de Azure](sql-database-advisor.md). Al desplazar el mouse sobre la anotación, obtiene información básica relativa a la acción:

![anotación de la consulta][6]

Si desea obtener más información o aplicar las recomendaciones del asesor, haga clic en el icono. Se abrirán los detalles de la acción. Si se trata de una recomendación activa, puede aplicarla inmediatamente mediante el comando.

![detalles de la anotación de la consulta][7]

### <a name="multiple-annotations"></a>Varias anotaciones.
Es posible que, debido al nivel de zoom, las anotaciones que están cerca unas de otras se contraigan en una. Un icono especial representará este suceso y, al hacer clic en él, se abrirá una nueva hoja donde se mostrará una lista de las anotaciones agrupadas.
La correlación de consultas y acciones de ajuste del rendimiento puede ayudar a comprender mejor la carga de trabajo. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimización de la configuración del almacén de consultas para obtener información de rendimiento de consultas
Durante el uso de información de rendimiento de consultas, puede que encuentre los siguientes mensajes del almacén de consultas:

* "El Almacén de consultas no está configurado correctamente en esta base de datos. Haga clic aquí para más información".
* "El Almacén de consultas no está configurado correctamente en esta base de datos. Haga clic aquí para cambiar la configuración". 

Estos mensajes suelen aparecer cuando el almacén de consultas no puede recopilar datos nuevos. 

El primer caso sucede si el Almacén de consultas está en estado de solo lectura y los parámetros están óptimamente establecidos. Para solucionarlo, puede aumentar el tamaño del Almacén de consultas o borrar el Almacén de consultas.

![botón qds][8]

El segundo caso sucede si el Almacén de consultas está desactivado o los parámetros no están óptimamente establecidos. <br>Puede cambiar la directiva de retención y captura y habilitar el Almacén de consultas ejecutando los comandos que aparecen a continuación o directamente desde el portal:

![botón qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Directiva de retención y captura recomendada
Hay dos tipos de directivas de retención:

* Basada en tamaño: si se establece en AUTO, los datos se borrarán automáticamente cuando el tamaño casi haya alcanzado el tamaño máximo.
* Basada en tiempo: de forma predeterminada, estableceremos esta opción en 30 días, lo que significa que, si el Almacén de consultas se queda sin espacio, eliminará la información de consulta anterior a los 30 días.

La directiva de capturas se podría establecer como:

* **Todas**: captura todas las consultas.
* **Automática**: se ignoran las consultas poco frecuentes y las consultas con una duración de ejecución y compilación insignificantes. Los umbrales para la duración del tiempo de ejecución y de compilación y para el recuento de ejecuciones se determinan internamente. Esta es la opción predeterminada.
* **Ninguna**: el Almacén de consultas deja de capturar nuevas consultas, pero las estadísticas en tiempo de ejecución de las consultas ya capturadas siguen recopilándose.

Se recomienda establecer todas las directivas en AUTO y la directiva de limpieza en 30 días:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumentar el tamaño del almacén de consultas. Esto puede realizarse mediante la conexión a una base de datos y la emisión de la consulta siguiente:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

La aplicación de estos valores hará que finalmente el Almacén de consultas recopile nuevas consultas, pero, si no quiere esperar, puede borrar el Almacén de consultas. 

> [!NOTE]
> Al ejecutar la consulta siguiente, se eliminará toda la información actual del Almacén de consultas. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Resumen
Query Performance Insight le ayuda a comprender el impacto de la carga de trabajo de las consultas y su relación con el consumo de recursos de base de datos. Con esta característica, conocerá las consultas que más consumen e identificará fácilmente las que deben corregirse antes de que conviertan en un problema.

## <a name="next-steps"></a>Pasos siguientes
Para recomendaciones adicionales sobre cómo mejorar el rendimiento de la base de datos SQL, haga clic en [Recomendaciones](sql-database-advisor.md) en la hoja **Información de rendimiento de consultas** .

![Asesor de rendimiento](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

