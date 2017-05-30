---
title: "Supervisión del rendimiento de muchas bases de datos SQL de Azure en una aplicación SaaS multiinquilino | Microsoft Docs"
description: "Supervisión y administración del rendimiento de la aplicación SaaS de Wingtip de ejemplo de Azure SQL Database"
keywords: tutorial de base de datos sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 54f29cc816d356e22b425f3824ef89800c017e61
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017


---
# <a name="monitor-performance-of-the-wingtip-saas-application"></a>Supervisión del rendimiento de la aplicación SaaS de Wingtip

En este tutorial se ponen de manifiesto la supervisión y las características de alerta integradas de SQL Database y los grupos elásticos y se exploran varios escenarios clave de administración del rendimiento que se usan en las aplicaciones SaaS.

La aplicación SaaS de Wingtip utiliza un modelo de datos de inquilino único, donde cada lugar (inquilino) tiene su propia base de datos. Al igual que en muchas aplicaciones SaaS, el patrón de carga de trabajo de inquilino previsto es imprevisible y esporádico. En otras palabras, las ventas de entradas pueden producirse en cualquier momento. Para aprovechar las ventajas de este patrón de uso típico de base de datos, las bases de datos de inquilinos se implementan en grupos de bases de datos elásticas. Los grupos elásticos optimizan el costo de las soluciones mediante el uso compartido de los recursos entre distintas bases de datos. Con este tipo de patrón, es importante supervisar la base de datos y el uso de los recursos del grupo para asegurarse de que las cargas están razonablemente repartidas entre los grupos. También debe asegurarse de que las bases de datos individuales tienen los recursos suficientes y de que los grupos no están al límite de [unidades de transacción de bases de datos elásticas](sql-database-what-is-a-dtu.md). En este tutorial se exploran métodos para supervisar y administrar las bases de datos y los grupos, y se explica cómo tomar medidas correctivas en respuesta a las variaciones en la carga de trabajo.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Simular el uso de las bases de datos de inquilinos mediante la ejecución de un generador de carga proporcionado
> * Supervisar las bases de datos de inquilinos a medida que responden al aumento de carga
> * Escalar verticalmente el grupo elástico en respuesta al aumento de la carga en la base de datos
> * Aprovisionar un segundo grupo elástico para equilibrar la actividad de la base de datos


Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Se implementa la aplicación SaaS de Wingtip. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de Wingtip](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Introducción a los patrones de administración del rendimiento de SaaS

La administración del rendimiento de la base de datos consiste en compilar y analizar los datos de rendimiento y, a continuación, reaccionar a estos datos mediante el ajuste de parámetros para mantener un tiempo de respuesta aceptable de la aplicación. Cuando se hospedan a varios inquilinos, los grupos de bases de datos elásticas son una manera rentable de proporcionar y administrar los recursos para un grupo de bases de datos con cargas de trabajo impredecibles. Con ciertos patrones de carga de trabajo, se pueden beneficiar de la administración en grupo un mínimo de dos bases de datos S3. Un grupo no solo comparte el costo de los recursos, también puede eliminar la necesidad de supervisar y realizar el seguimiento de las bases de datos individuales constantemente.

![medios](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

Sigue siendo necesario supervisar los grupos y las bases de datos de estos para garantizar que permanecen dentro de los intervalos de rendimiento aceptables. Ajuste la configuración del grupo para satisfacer las necesidades de la carga de trabajo agregada, garantizando que las unidades de transacción de bases de datos elásticas del grupo son adecuadas para la carga de trabajo global. Ajuste los valores mínimo y máximo de unidades de transacción de bases de datos elásticas por base de datos adecuados para los requisitos de su aplicación.

### <a name="performance-management-strategies"></a>Estrategias de administración del rendimiento

* Para evitar tener que supervisar el rendimiento manualmente, resulta más eficaz **establecer alertas que se activan si las bases de datos o los grupos salen de los intervalos normales**.
* Para responder a las fluctuaciones de rendimiento global de un grupo a corto plazo, **se puede escalar o reducir verticalmente el nivel de eDTU del grupo**. Si esta fluctuación es habitual o previsible, **se puede programar el escalado automático del grupo**. Por ejemplo, cuando haya poca carga de trabajo (por la noche o durante el fin de semana), redúzcalo verticalmente.
* Para responder a las fluctuaciones a largo plazo o a cambios en el número de bases de datos, **las bases de datos se pueden mover a otros grupos**.
* Para responder a aumentos breves en la carga *individual* de una base de datos, **se puede sacar de un grupo para asignarle un nivel de rendimiento individual** durante cierto tiempo. Una vez reducida la carga, la base de datos se puede devolver al grupo. Cuando se sepa de antemano, las bases de datos se pueden mover con antelación para garantizar que siempre tienen los recursos necesarios y evitar que otras bases de datos del grupo resulten afectadas. Si es predecible, como para un establecimiento, una avalancha de ventas de entradas para un evento popular, este comportamiento de administración puede integrarse en la aplicación.

[Azure Portal](https://portal.azure.com) proporciona supervisión y alertas integradas en la mayoría de recursos. Para SQL Database, la supervisión y las alertas están disponibles para las bases de datos y los grupos. La supervisión y las alertas integradas son específicas de los recursos, por lo que es conveniente utilizarlas con un número reducido de recursos, no son muy prácticas cuando se trabaja con muchos recursos.

Para escenarios de gran volumen, puede utilizarse Log Analytics (también conocido como OMS). Se trata de un servicio de Azure independiente que proporciona análisis de los registros de diagnóstico emitidos y de telemetría recopilados en un área de trabajo de Log Analytics, que recopila la telemetría de muchos servicios y sirve para consultar y establecer alertas.

## <a name="get-the-wingtip-application-scripts"></a>Obtener scripts de la aplicación Wingtip

Los scripts SaaS de Wingtip y el código fuente de la aplicación están disponibles en el repositorio de GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Pasos para descargar los scripts SaaS de Wingtip](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).

## <a name="provision-additional-tenants"></a>Aprovisionamiento de inquilinos adicionales

Aunque los grupos pueden ser rentables con solo dos bases de datos S3, cuantas más bases de datos en el grupo, más rentable será la media. Para una buena comprensión de cómo funciona la administración y la supervisión del rendimiento a escala, para este tutorial se necesitan al menos 20 bases de datos implementadas.

Si ha proporcionado un lote de inquilinos en un tutorial anterior, puede ir directamente a la sección [Simulación de uso en todas las bases de datos de inquilinos](#simulate-usage-on-all-tenant-databases).

1. Abra …\\Learning Modules\\Provision and Catalog\\*Demo-PerformanceMonitoringAndManagement.ps1* en **PowerShell ISE**. Mantenga este script abierta, ya que se van a ejecutar varios escenarios en este tutorial.
1. Establezca **$DemoScenario** = **1**, **Aprovisionamiento de un lote de inquilinos**
1. Presione **F5** para ejecutar el script.

El script implementará 17 inquilinos en menos de cinco minutos.

El script *New-TenantBatch* utiliza un conjunto anidado o vinculado de plantillas de [Resource Manager](../azure-resource-manager/index.md) que crean un lote de inquilinos, que, de forma predeterminada, copia la base de datos **baseTenantDb** en el servidor de catálogo para crear las bases de datos de inquilinos y las registra en el catálogo; finalmente, las inicializa con el nombre de inquilino y el tipo de lugar. Esto es coherente con la manera en que la aplicación aprovisiona un nuevo inquilino. Los cambios realizados en *baseTenantDB* se aplican a los nuevos inquilinos que se aprovisionen a partir de ese momento. Consulte el [tutorial de administración de esquemas](sql-database-saas-tutorial-schema-management.md) para ver cómo realizar cambios de esquema en bases de datos de inquilinos *existentes* (incluida la base de datos *golden*).

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Simulación de diferentes patrones de uso mediante la generación de tipos de carga distintos

El script *Demo-PerformanceMonitoringAndManagement.ps1* inicia el generador de carga mediante uno de los *tipos de carga* disponibles:

| Demostración | Escenario |
|:--|:--|
| 2 | Generación de una carga de intensidad normal (aprox. 40 DTU) |
| 3 | Generación de una carga con ráfagas más prolongadas y frecuentes por base de datos|
| 4 | Generación de una carga con mayores ráfagas de DTU por base de datos (aprox. 80 DTU)|
| 5 | Generación de una carga normal y una carga elevada en un solo inquilino (aprox. 95 DTU)|
| 6 | Generación de una carga desequilibrada en varios grupos|

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulación de uso en todas las bases de datos de inquilinos

El generador de carga se aplica una carga *sintética* exclusiva de CPU para cada base de datos de inquilinos. El generador de inicia un trabajo para cada base de datos de inquilinos, que llama a un procedimiento almacenado periódicamente que genera la carga. Los niveles de carga (en eDTU), la duración y los intervalos varían en todas las bases de datos, lo cual simula una actividad de inquilinos imprevisible.

1. Establezca **$DemoScenario** = **2**, *Generación de una carga de intensidad normal*.
1. Presione **F5** para aplicar una carga de todas las bases de datos de inquilinos.

Dada la naturaleza esporádica de la carga, deje que el generador se ejecute durante 10 a 20 minutos para que la actividad alcance un estado estable y adopte un patrón correcto.

> [!IMPORTANT]
> El generador de carga se ejecuta como una serie de trabajos en la sesión local de PowerShell. Mantenga abierta la pestaña *Demo-PerformanceMonitoringAndManagement.ps1*. Si la cierra o suspende la máquina, el generador de carga se detendrá.

## <a name="monitor-resource-usage-using-the-portal"></a>Supervisión del uso de los recursos mediante el portal

Para supervisar el uso de los recursos derivado de la carga que se aplica, abra el portal al grupo que contiene las bases de datos de inquilinos.

1. Abra [Azure Portal](https://portal.azure.com) y vaya al servidor tenants1 -&lt;USER&gt;.
1. Debería ver la lista de bases de datos de inquilinos, incluido el nuevo lote de bases de datos.
1. Desplácese hacia abajo, busque los grupos elásticos y haga clic en **Pool1**. Este grupo contiene todas las bases de datos de inquilinos creadas hasta ahora.
1. Expanda la hoja del grupo que se abre y observe el gráfico de uso del grupo y el gráfico de las bases de datos más usadas.

El uso del grupo es, en esencia, la suma del uso de las bases de datos del grupo. El gráfico de uso de las base de datos muestra las 5 bases de datos principales:

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Como hay otras bases de datos en el grupo aparte de las 5 principales, el uso del grupo muestra la actividad que no se refleja en el gráfico de las cinco bases de datos principales. Para ver algunos detalles adicionales, haga clic en **Uso de recursos de base de datos**:

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Establecimiento de alertas de rendimiento en el grupo

Establezca una alerta en el grupo que se desencadene cuando el uso de \> 75 % se mantenga durante 5 minutos; siga estos pasos:

1. Abra *Pool1* (en el servidor *tenants1-\<user\>*) en [Azure Portal](https://portal.azure.com).
1. Haga clic en **Reglas de alerta** y en **+ Agregar alerta**:

   ![agregar alerta](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Proporcione un nombre, como **High DTU**, 
1. Establezca los valores siguientes:
   * **Métrica = eDTU percentage**
   * **Condición = greater than**.
   * **Umbral = 75**.
   * **Período = Over the last 30 minutes**.

   ![Establecer alerta](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

Puede hacer que las notificaciones se envíen al correo electrónico de la cuenta de Azure y, opcionalmente, otros mensajes de correo electrónico (se recomienda no establecer esto, a menos que la suscripción en uso sea la suya).

> [!NOTE]
> Como la alerta solo se activa si se supera el umbral durante los últimos 30 minutos, el generador de carga debe llevar en ejecución más de 30 minutos para probar la alerta.


## <a name="scale-up-a-busy-pool"></a>Escalado vertical de un grupo ocupado

Si aumenta el nivel de carga global en un grupo hasta el punto de llegar al máximo del grupo y alcanza el 100 % del uso de eDTU, el rendimiento de las bases de datos se verá afectado, lo cual podría ralentizar los tiempos de respuesta de consulta para todas las bases de datos del grupo.

A corto plazo, considere la posibilidad de escalar verticalmente el grupo para proporcionar recursos adicionales o de quitar bases de datos del grupo (muévalos a otros grupos o fuera del grupo a un nivel de servicio independiente).

A largo plazo, considere la posibilidad de optimizar las consultas o el uso de índices para mejorar el rendimiento de las bases de datos. En función de la sensibilidad de la aplicación a los problemas de rendimiento, es un procedimiento recomendado el escalado vertical de un grupo antes de que llegue al 100 % del uso de eDTU. Use una alerta para que le avise con antelación.

Puede simular un grupo ocupado si aumenta la carga que produce el generador. Hacer que las bases de datos generen ráfagas con más frecuencia y más prolongadas aumenta la carga global del grupo sin cambiar los requisitos de las bases de datos individualmente. El escalado vertical del grupo se realiza fácilmente en el portal o desde PowerShell. En este ejercicio se usa el portal.

1. Establezca *$DemoScenario* = **3**, _Generación de una carga con ráfagas más prolongadas y frecuentes por base de datos_ para aumentar la intensidad de la carga global del grupo sin cambiar la carga máxima necesaria para cada base de datos.
1. Presione **F5** para aplicar una carga de todas las bases de datos de inquilinos.


1. **Abra la hoja del grupo** **para tenants1/Pool1**.


1. Supervise el aumento del uso de DTU del grupo en el gráfico superior. La carga mayor tarda unos minutos en entrar en vigor, pero debería ver cómo rápidamente el grupo empieza a llegar al 100 % de uso, y, según la carga se va estabilizando en el nuevo patrón, sobrecarga rápidamente el grupo.


1. Para escalar verticalmente el grupo, haga clic en **Configurar grupo**.
1. Ajuste el control deslizante de **eDTU del grupo** en 100 (se recomienda no superar esto para limitar los costos). Tenga en cuenta cómo el almacenamiento global disponible para todas las bases de datos del grupo, indicado por **GB del grupo**, está vinculado a la configuración de eDTU y también aumenta. Cambiar la eDTU del grupo no cambia la configuración por base de datos (que sigue siendo 50 eDTU máx. por base de datos). Puede ver la configuración por base de datos en el lado derecho de la hoja **Configurar grupo**.
1. Haga clic en **Guardar** para enviar la solicitud. Normalmente, el cambio tardará de 3 a 5 minutos para un grupo Estándar.

Vuelva a **Pool1** > **Introducción** para ver los gráficos de supervisión. Supervise el efecto de proporcionar más recursos al grupo (aunque con pocas bases de datos y una carga aleatoria no siempre es fácil verlo bien). Mientras observe los gráficos, tenga en cuenta que el 100 % del gráfico superior representa ahora 100 eDTU, mientras que en el gráfico inferior, el 100 % sigue siendo 50 eDTU, ya que el máximo por base de datos sigue siendo 50 eDTU.

Las bases de datos permanecen en línea y están totalmente disponibles durante el proceso. En el último momento, como las bases de datos están preparadas para la habilitación con la nueva eDTU de grupo, todas las conexiones activas se interrumpen. El código de la aplicación debe escribirse para siempre reintente recuperar las conexiones interrumpidas, por lo tanto, se volverá a conectar a la base de datos del grupo escalado verticalmente.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Creación de un segundo grupo y equilibrio de la carga de las bases de datos para manejar el aumento de carga global

Como alternativa al escalado vertical del grupo, cree un segundo grupo y mueva bases de datos a este para equilibrar la carga entre los dos grupos. Para ello, el nuevo grupo debe crearse en el mismo servidor que el primero.

1. Abra la **hoja del servidor para el servidor customers1-&lt;USER&gt;**. Si se encuentra en una hoja de base de datos o de grupo, puede bajar el control de Información esencial y seleccionar el nombre del servidor como acceso directo.
1. Haga clic en **+ Grupo nuevo** para crear un grupo en el servidor actual
1. En la nueva plantilla de grupo de bases de datos elásticas:

    1. establezca **Nombre = Pool2**.
    1. Deje el plan de tarifa como **Grupo Estándar**.
    1. Haga clic en **Configurar grupo**.
    1. En la hoja Configurar grupo que se abre, establezca **eDTU del grupo = 50 DTU**.
    1. Haga clic en el comando **Agregar bases de datos** para ver una lista de las bases de datos de este servidor que no están en el grupo actual.
    1. En la lista, **marque** la mitad de las bases de datos (10 de 20) para moverlas al nuevo grupo y haga clic en **Seleccionar**.
    1. Haga clic en **Seleccionar** de nuevo para aceptar los cambios de configuración. Tenga en cuenta la estimación del costo de un mes de uso con las opciones seleccionadas.
    1. Seleccione **Aceptar** para crear el grupo con la configuración nueva y mover las bases de datos.

Crear el grupo y mover las bases de datos a este tarda unos minutos. Las bases de datos que se mueven permanecen en línea y totalmente accesibles hasta el último momento, cuando se cierran todas las conexiones abiertas. Cuando un cliente reintenta la conexión, se conectará a la base de datos en el nuevo grupo.

Una vez creado el grupo, aparecerá en la hoja de servidor customers1. Haga clic en el nombre del grupo para abrir la hoja del grupo y supervisar el rendimiento.

Debería ver disminuir el uso de los recursos en Pool1 y una carga similar en Pool2.

## <a name="manage-an-increased-load-on-a-single-database"></a>Administración de un aumento de carga en una sola base de datos

Si una base de datos de un grupo experimenta una carga elevada durante un tiempo prolongado, según la configuración de grupo, puede tender a dominar los recursos del grupo, lo cual puede afectar a otras bases de datos. Si es probable que la actividad continúe durante un tiempo, la base de datos puede sacar temporalmente del grupo. Esto permite asignar más recursos a la base de datos que a las demás bases de datos del grupo y la aísla de ellas. En este ejercicio se simula el efecto de una carga elevada en Contoso Concert Hall cuando se venden entradas para un concierto popular.

1. En el script …\\**Demo-PerformanceManagementAndMonitoring**.ps1.
1. Establezca **$DemoScenario = 5, Generación de una carga normal y una carga elevada en un solo inquilino (aprox. 95 DTU).**
1. Establezca **$SingleTenantDatabaseName = contosoconcerthall**
1. Ejecute el script con **F5**.
1. **Abra la hoja del grupo** **para Customers1/Pool1**.
1. Observe la presentación de la **supervisión del grupo elástico** en la parte superior de la hoja y busque el grupo con el aumento de uso de DTU. Pasado un minuto o dos, la carga mayor entrará en vigor rápidamente y verá que el grupo alcanza el 100 % de uso.
1. Observe también la presentación de la **supervisión de bases de datos elásticas** que muestra las bases de datos principales de la última hora. La base de datos de contosoconcerthall pronto debería aparecer como una de las 5 bases de datos principales.
1. **Haga clic en el **gráfico** de supervisión de bases de datos elásticas** y se abrirá una hoja de **Uso de recursos de base de datos** donde podrá elegir la supervisión de cualquier base de datos. Esto le permite aislar la presentación de la base de datos de contosoconcerthall.
1. En la lista de bases de datos, **haga clic en contosoconcerthall** y se abrirá la hoja de la base de datos.
1. Haga clic en **Plan de tarifa (escalar DTU)** en el menú contextual para abrir la hoja **Configurar rendimiento**, donde podrá establecer un nivel de rendimiento para de la base de datos.
1. Haga clic en la pestaña **Estándar** para abrir las opciones de escalado del nivel Estándar.
1. Deslice el **control deslizante de las DTU** a la derecha para seleccionar 100. Tenga en cuenta esto corresponde al objetivo de servicio, **S3**, que se muestra entre corchetes entre los medidores de tamaño de almacenamiento y DTU.
1. Haga clic en **Aplicar** para sacar la base de datos del grupo y convertirla en una base de datos S3 Estándar.
1. Una vez completada la implementación, supervise en las hojas de base de datos y grupos elásticos el efecto sobre la base de datos de contosoconcert hall y sobre el grupo elástico del que se quitó.

Una vez que desaparezca el exceso de carga de la base de datos de contosoconcerthall debe devolverla rápidamente al grupo para reducir el costo. Si no es evidente cuándo ocurrirá esto, podría establecer una alerta en la base de datos que se active cuando el uso de DTU descienda por debajo del máximo por base de datos establecido para el grupo. El movimiento de una base de datos a un grupo se describe en el ejercicio 5.

## <a name="other-performance-management-patterns"></a>Otros modelos de administración del rendimiento

**Escalado preventivo**. En el ejercicio 6 donde exploramos cómo escalar una base de datos aislada, sabía la base de datos que buscaba. Si la administración de Contoso Concert Hall había informado a Wingtip de la venta inminente de entradas, la base de datos podía haberse sacado del grupo de manera preventiva. De lo contrario, probablemente se hubiera necesitado una alerta en el grupo o la base de datos permite detectar lo que estaba pasando. No le gustaría que le informaran de esto los otros inquilinos del grupo con quejas sobre el rendimiento. Y si el inquilino puede predecir durante cuánto tiempo necesita recursos adicionales, podrá configurar un runbook de Azure Automation para sacar la base de datos del grupo y volverla a meter según un programa determinado.

**Autoservicio de escalado de inquilinos**. Como el escalado es una tarea que se llama fácilmente a través de la API de administración, puede crear fácilmente la capacidad de escalar las bases de datos de inquilinos en la aplicación de inquilino y ofrecerla como característica del servicio SaaS. Por ejemplo, puede permitir que los inquilinos se autoadministren el escalado y la reducción vertical y quizá vincularlo directamente a su facturación.

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Escalado y reducción vertical de un grupo según un programa para cumplir patrones de uso

Cuando el uso global de los inquilinos siga patrones previsibles, puede utilizar Azure Automation para escalar y reducir verticalmente un grupo según un programa. Por ejemplo, puede reducir un grupo verticalmente después de las 6 p.m. y escalarlo de nuevo antes de las 6 a.m. entre semana porque sabe que se necesitan menos recursos.



## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Simular el uso de las bases de datos de inquilinos mediante la ejecución de un generador de carga proporcionado
> * Supervisar las bases de datos de inquilinos a medida que responden al aumento de carga
> * Escalar verticalmente el grupo elástico en respuesta al aumento de la carga en la base de datos
> * Aprovisionar un segundo grupo elástico para equilibrar la actividad de la base de datos

[Tutorial sobre la restauración de un único inquilino](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionales

* Otros [tutoriales basados en la implementación de la aplicación SaaS de Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Grupos elásticos de SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md): tutorial de configuración y uso de Log Analytics
