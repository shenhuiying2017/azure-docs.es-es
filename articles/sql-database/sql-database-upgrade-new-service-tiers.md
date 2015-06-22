<properties 
	pageTitle="Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos" 
	description="Actualice la base de datos SQL de Azure Web o Business a los nuevos niveles de rendimiento o de servicio de Base de datos SQL de Azure. Actualizar la Base de datos SQL, Cambiar los niveles de servicio, Cambiar el nivel de rendimiento." 
	services="sql-database" 
	documentationCenter="" 
	authors="jenniehubbard" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
ms.devlang="NA"
	ms.date="02/23/2015" 
	ms.author="jhubbard; sstein" 
	ms.workload="data-services" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos

Las bases de datos SQL Web o Business de Azure se están quedando obsoletas y [se retirarán en septiembre de 2015](http://msdn.microsoft.com/library/azure/dn741330.aspx) por lo que es el momento de comenzar a planear la actualización de las bases de datos Web o Business existentes a los niveles de servicio Basic, Standard o Premium.

## Información general

<p> La web Azure y las bases de datos Business SQL se ejecutan en un entorno compartido, multiempresa sin ninguna capacidad para reservar recursos para la base de datos. La actividad de otras bases de datos en este entorno de recursos compartidos puede afectar al rendimiento. La disponibilidad de los recursos en un momento dado depende mucho de otras cargas de trabajo simultáneas en el sistema. Esto puede dar lugar a un rendimiento de la aplicación de base de datos impredecible y muy variable. Los comentarios de los clientes indican que es difícil administrar este rendimiento impredecible y que es deseable un rendimiento más predecible. 

Para atender estos comentarios, el servicio de base de datos SQL de Azure introdujo nuevos niveles de servicio de bases de datos [(Basic, Standard y Premium)](http://msdn.microsoft.com/library/dn741340.aspx), que ofrecen un rendimiento predecible y una gran cantidad de nuevas características para la continuidad y seguridad del negocio. Estos nuevos niveles de servicio están diseñados para proporcionar un nivel específico de recursos para una carga de trabajo de base de datos, independientemente de otras cargas de trabajo del cliente que se ejecuten en ese entorno. Esto resulta en un comportamiento muy predecible del rendimiento. 

Estos cambios vienen acompañados de preguntas acerca de cómo evaluar y decidir qué nivel de servicio nuevo es la mejor elección para las bases de datos Web y Business (W/B) actuales y acerca del proceso de actualización real.

En última instancia, la mejor opción es la combinación de nivel de rendimiento y nivel de servicio que le proporcione el equilibrio óptimo entre características, rendimiento y coste, y que, además, satisfaga completamente los requisitos y las necesidades empresariales de la aplicación.

Este documento proporciona una metodología guiada para actualizar una base de datos Web o Business a uno de los nuevos niveles de servicio o de rendimiento.

Es importante tener en cuenta que las bases de datos SQL de Azure no se bloquean en ningún nivel de rendimiento o de servicio específico, por lo que si cambian los requisitos de la base de datos, podrá cambiar fácilmente entre los niveles de rendimiento y servicio disponibles. De hecho, las bases de datos SQL de los niveles Basic, Standard y Premium se facturan por hora, y es posible escalar o reducir verticalmente cada base de datos 4 veces en un período de 24 horas (mediante el [Portal de Azure o mediante programación](http://msdn.microsoft.com/library/azure/ff394116.aspx)). Esto significa que puede ajustar el nivel de servicio y de rendimiento para maximizar las necesidades de rendimiento de la base de datos, el conjunto de características y el coste, según los requisitos y las distintas cargas de trabajo de la aplicación. Esto también significa que evaluar y cambiar el nivel de servicio y de rendimiento de la base de datos (escalado y reducción verticales) es un proceso continuo que debe formar parte de la rutina de optimización del rendimiento y de mantenimiento programado. 


## Actualización de las bases de datos Web y Business

Al actualizar bases de datos Web o Business a un nuevo nivel de rendimiento o servicio, no hay que desconectar la base de datos. La base de datos seguirá funcionando durante la operación de actualización. En el momento de la transición real al nuevo nivel de rendimiento, puede producirse una caída temporal de las conexiones a la base de datos de muy poca duración (se suele medir en segundos). Si una aplicación tiene gestión de errores temporal para terminaciones de conexiones, es suficiente para protegerse frente a las conexiones interrumpidas al final de la actualización. 

La actualización de una base de datos Web o Business a un nuevo nivel de servicio implica los pasos siguientes:


1. Determinar el nivel de servicio basándose en la funcionalidad de características
2. Determinar el nivel de rendimiento aceptable basándose en el uso histórico de recursos
3. ¿Por qué rendimiento existente de la base de datos Web o Business coincide con los niveles más altos de Premium?
4. Optimizar la carga de trabajo para ajustarla a un nivel de rendimiento inferior
5. *Actualizar al nuevo nivel de servicio o de rendimiento*
6. Supervisar la actualización al nuevo nivel de servicio o de rendimiento
7. Supervisar la base de datos después de la actualización



## 1. Determinar el nivel de servicio basándose en la funcionalidad de características

Los niveles de servicio Basic, Standard y Premium ofrecen diferentes conjuntos de características, por lo que el primer paso a la hora de seleccionar un nivel apropiado es determinar el nivel de servicio que proporciona el nivel mínimo de características necesarias para su aplicación y su empresa. 

Por ejemplo, plantéese cuestiones como durante cuánto tiempo deben conservarse las copias de seguridad, si se necesitan características de [replicación geográfica activa o estándar](http://msdn.microsoft.com/library/azure/dn783447.aspx), qué tamaño máximo de base de datos general se necesita, etc. Estos requisitos determinan la elección del nivel de servicio mínimo.

El nivel "Basic" se usa principalmente para las bases de datos muy pequeñas y de baja actividad. Por lo tanto, para realizar una actualización, normalmente se debe comenzar con el nivel Standard o Premium, según el nivel mínimo de características necesarias.

Los niveles de rendimiento y características del nuevo nivel de servicio se resumen y se comparan en la tabla siguiente:

![Service Tier feature comparison][1]


**Recursos adicionales para comparar los niveles de servicio y de rendimiento:**

| Artículo | Descripción |
|:--|:--|
|[Niveles de servicio de base de datos SQL de Azure (ediciones)](http://msdn.microsoft.com/library/azure/dn741340.aspx)| Información general sobre los niveles de servicio Basic, Standard y Premium.|
|[Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](http://msdn.microsoft.com/library/dn741336.aspx)| Métricas y capacidades para cada nivel de servicio (y cómo supervisar el uso de la base de datos en el portal de administración y mediante las DMV). |
|[¿Qué diferencia hay en los niveles de servicio?](http://msdn.microsoft.com/library/dn369873.aspx#Different)| Más información acerca de los niveles de servicio, incluidos algunos motivos de por qué seleccionaría un nivel sobre otro. |
|[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)|Detalles de las características de recuperación ante desastres y continuidad de negocio (restauración a un momento dado, restauración geográfica, replicación geográfica) disponibles para los diferentes niveles de servicio.|
|[Precios de base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/)|Información detallada sobre precios de los diferentes niveles de servicio y niveles de rendimiento.|

<br>

Después de seleccionar un nivel de servicio adecuado según los requisitos de su base de datos, el siguiente paso es seleccionar un nivel de rendimiento aceptable para las cargas de trabajo de la base de datos real. 



## 2. Determinar el nivel de rendimiento aceptable basándose en el uso histórico de recursos

El servicio de base de datos SQL de Azure expone la información en el portal de administración y en las vistas del sistema para mostrar el nuevo nivel de rendimiento y servicio comparable que se sugiere para la base de datos Web o Business existente.

Puesto que las bases de datos Web o Business no tienen ningún límite de DTU ni recursos garantizados asociados, nosotros normalizamos los valores de porcentaje en función de la cantidad de recursos disponibles para una base de datos de nivel de rendimiento S2. El consumo medio de porcentaje de DTU de una base de datos en cualquier intervalo específico se puede calcular como el valor de porcentaje más alto entre el uso de CPU, E/S y registro en ese intervalo.


Use el portal de administración para obtener información general sobre el uso del porcentaje de DTU y, a continuación, profundice en los detalles usando las vistas del sistema.


### Visualización del consumo de DTU en el Portal de administración
El Portal de administración proporciona información sobre el consumo de DTU de una base de datos Web o Business existente.
La información de DTU está disponible en el portal de Azure actual.


**Portal de administración**

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com) y navegue a una base de datos Web o Business existente.
2. Haga clic en la pestaña **MONITOR**.
3. Haga clic en **AGREGAR MÉTRICAS**.
4. Seleccione **Porcentaje de DTU** y haga clic en la marca de verificación situada en la parte inferior para confirmar.

Después de confirmar, debería ver los datos del **porcentaje de DTU** en la tabla. Recuerde que este porcentaje se establece en comparación con el valor de DTU de una base de datos de nivel Standard (S2), que es de 50 DTU.

También hay que destacar que estos datos constituyen el valor medio de las muestras tomadas cada 5 minutos, por lo que puede haber ráfagas cortas de actividad entre las muestras que no se reflejan en estas métricas.

![DTU percentage data][2]

Observe que los datos del ejemplo anterior muestran un uso medio de aproximadamente 10 DTU (19,23 % de 50) y un porcentaje de DTU máximo de ~28 DTU (55,83 % x 50). 
Si suponemos que estos datos representan la carga de trabajo típica, probablemente habría que seleccionar el nivel Standard (S1) para la actualización inicial. La versión Standard (S0) proporciona 10 DTU, que es el promedio de uso, pero eso significaría que la base de datos funcionaría siempre al 100 % de la capacidad, lo que no es aconsejable. S1 probablemente sería una buena elección para este uso medio, pero ¿qué ocurre con las ocasiones en las que se alcanza el nivel máximo? Probablemente esos picos se deben algún proceso de mantenimiento durante la noche, por lo que el uso del cliente real no se vería afectado. Por ello, un rendimiento reducido durante ese período de tiempo podría ser aceptable. En el caso de que se desconozca cuándo se alcanzan esos picos máximos, el consumo de porcentaje de DTU puede requerir más análisis.

Para profundizar en los detalles del consumo de recursos de la base de datos, se pueden utilizar las vistas del sistema proporcionadas.


### Vistas del sistema


Para acceder a los datos sobre el consumo de recursos de la base de datos Web y Business, hay que utilizar la vista [sys.resource_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) de la base de datos maestra del servidor lógico en el que se encuentra la base de datos actual. Muestra los datos de consumo de recursos en porcentajes del límite del nivel de rendimiento. Esta vista proporciona datos de los últimos 14 días, a intervalos de 5 minutos.  Ejecute la siguiente consulta en la base de datos maestra para recuperar el consumo de DTU medio de una base de datos:

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

La información sobre el consumo de DTU en términos de un nivel de base de datos S2 permite normalizar el consumo actual de las bases de datos Web o Business en términos de nuevas bases de datos de servicios y ver dónde se ajustan mejor. Por ejemplo, si el consumo medio de porcentaje de DTU muestra un valor de 80 %, esto indica que la base de datos está consumiendo DTU a una velocidad de un 80 % del límite de una base de datos en el nivel de rendimiento S2. Si ve valores superiores al 100 % en la vista **sys.resource_stats**, significa que necesita un nivel de rendimiento superior al S2. Por ejemplo, supongamos que ve un valor de porcentaje máximo de DTU de 300 %.  Esto indica que está usando tres veces más recursos que los que estarían disponibles en un S2. Para determinar un tamaño inicial razonable, compare las DTU disponibles en un S2 (50 DTU) con los tamaños superiores siguientes (S3/P1 = 100 DTU, o 200 % de S2, P2 = 200 DTU o 400 % de S2). Debido a que está a un 300 % de S2, debería comenzar con un P2 y volver a probar. 

Según el porcentaje de uso de DTU y la edición necesaria para ajustar su carga de trabajo, puede determinar qué niveles de rendimiento y servicio son más adecuados para la carga de trabajo de la base de datos (como se indica a través del porcentaje de DTU y las potencias de DTU relativas de varios [niveles de rendimiento)](http://msdn.microsoft.com/library/azure/dn741336.aspx). En esta tabla se proporciona una asignación del porcentaje de consumo de recursos de Web o Business a nuevos niveles de rendimiento de servicio: 

![Resource consumption][4]

> **Nota:**
> Los números DTU relativos entre distintos niveles de rendimiento se basan en la carga de trabajo [Información general comparativa sobre Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn741327.aspx). Puesto que es probable que la carga de trabajo de la base de datos sea diferente de la prueba comparativa, debe usar los cálculos anteriores como criterio para un ajuste inicial de la base de datos Web o Business en los nuevos niveles. Una vez que se ha movido la base de datos al nuevo nivel, use el procedimiento descrito en la sección anterior para validar y ajustar el nivel de servicio correcto que mejor se adapte a sus necesidades de carga de trabajo.
> 
> Mientras que el nuevo nivel de rendimiento o de edición tiene en cuenta la actividad de la base de datos durante los últimos 14 días, estos datos se basan en los ejemplos de datos de consumo de recursos promediados en un periodo de 5 minutos. Como tal puede perder ráfagas cortas de actividad cuya duración es inferior a 5 minutos. Por lo tanto, esta guía debe usarse como punto de partida al que actualizar la base de datos. Después de actualizar la base de datos al nivel sugerido, se necesita más supervisión, pruebas y validaciones, y la base de datos se puede mover hacia arriba o hacia abajo hasta un nivel de rendimiento o de servicio diferente, según sea necesario.

Esta es una consulta en la base de datos maestra que realiza el cálculo de la base de datos de la capa Web o Business y sugiere qué edición tiene más posibilidades de ajustarse a la carga de trabajo para cada uno de estos intervalos de muestra de datos de 5 minutos.

> **Nota:** esta consulta es útil solo para las bases de datos Web o Business y **no** proporcionará resultados correctos para las bases de datos en los niveles nuevos.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**Resultado de ejemplo:**

![Sample Result](media/sql-database-upgrade-new-service-tiers/sample_result.png)

En el gráfico puede ver la tendencia del consumo de porcentaje promedio de DTU en el tiempo. Este es un gráfico de ejemplo para una base de datos que se encuentra dentro de un nivel S2 la mayoría del tiempo, con alguna actividad máxima que llega al nivel P1 de la base de datos.  El consumo de DTU a lo largo del tiempo varía desde los límites de 'Basic' hasta los límites de 'P1'. Para ajustar completamente esta base de datos al nuevo nivel, necesitará una base de datos de nivel de servicio Premium con un nivel de rendimiento 'P1'. Por otro lado, una base de datos de nivel S2 puede funcionar si esas ráfagas ocasionales a nivel P1 son poco frecuentes.

![DTU Usage](media/sql-database-upgrade-new-service-tiers/DTU_usage.png)

**Impacto de la memoria en el rendimiento:** aunque la memoria es una de las dimensiones de recursos que contribuye a la clasificación de DTU, la base de datos SQL está diseñada para usar toda la memoria disponible para las operaciones de la base de datos. Por eso, el consumo de la memoria no se incluye en el promedio de consumo de DTU en la consulta anterior. Por otro lado, si pasa a un nivel de rendimiento inferior, la memoria disponible se reduce para la base de datos. Esto podría dar lugar a un consumo de E/S superior que afectará a la DTU consumida. Por lo tanto, al reducir el tamaño a un nivel de rendimiento inferior, asegúrese de que tiene suficiente espacio disponible en el porcentaje de E/S. Use la DMV [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) mencionada anteriormente para supervisar esto.



## 3. ¿Por qué rendimiento existente de la base de datos Web o Business coincide con los niveles más altos de Premium?

Las bases de datos Web y Business no tienen una cantidad específica de capacidad de recursos reservada para ninguna base de datos individual. Además, no hay ningún mecanismo in situ para que los clientes escalen o reduzcan verticalmente el rendimiento para una base de datos Web o Business. Esto tiene como consecuencia que el rendimiento de las bases de datos Web y Business puede oscilar entre niveles realmente lentos hasta niveles Premium. Este intervalo variable de rendimiento depende *unfairly* del nivel global de consumo de recursos en cualquier momento por parte de otras bases de datos dentro del entorno de varios inquilinos que comparten recursos.  

Por supuesto, el objetivo de los servicios de base de datos SQL de Azure es tener todas las bases de datos Web y Business con un nivel de ejecución lo más próximo al 100 % que sea posible. Y se ha hecho un gran trabajo al mantener los niveles de rendimiento medios de las bases de datos Web y Business cerca de los niveles Premium. Esta es la razón por la que el rendimiento de la base de datos existente puede asignarse a los niveles Premium actuales. Desafortunadamente, esto generó unas expectativas poco realistas al comparar las bases de datos Web y Business con los nuevos niveles de rendimiento y de servicio a la hora de evaluar el nivel de servicio al que se debe actualizar.

Para comprender con mayor claridad las diferencias entre las bases de datos Web y Business y los niveles de servicio Basic, Standard y Premium, analicemos la siguiente imagen. Suponga que establecemos una comparación entre 9 bases de datos SQL que se ejecutan en el modelo Web/Business de recurso compartido y otras 9 bases de datos SQL que se ejecutan en el modelo de nivel de servicio Basic, Standard y Premium. En el modelo Web/Business, pueden apreciarse claramente los efectos que tienen los 'noisy neighbors' en las otras bases de datos que forman parte de este grupo de recursos compartidos. Cuando una base de datos está ejecutando una carga de trabajo intensiva de recursos, se ven afectadas todas las demás bases de datos del grupo y los recursos disponibles empiezan a disminuir. En los niveles de servicio Basic, Standard y Premium, ***se asigna*** una cantidad específica de recursos a cada base de datos, por lo que las demás bases de datos del entorno compartido se aíslan esencialmente del problema del "vecino ruidoso" y se vinculan al nivel de rendimiento seleccionado para la base de datos. 

![Predictable performance of the new service tiers][3]

Si el porcentaje de DTU global es muy alto, debe empezar la búsqueda en las métricas detalladas que constituyen las DTU. En concreto, hay que llegar hasta los detalles más precisos del uso de la memoria y de las E/S de registro de la base de datos. Se pueden descubrir áreas en las que sea posible optimizar y reducir el consumo de DTU.


## 4.	Optimizar la carga de trabajo de la base de datos para ajustarla a un nivel de rendimiento inferior
Si el análisis del uso histórico de los recursos de la base de datos indica que se debe actualizar a un nivel de rendimiento que es más costoso de lo que desearía, puede buscar áreas en las que sea posible realizar optimizaciones adicionales. 

Teniendo en cuenta sus conocimientos acerca de los detalles de la aplicación, si el uso de recursos parece muy alto en comparación con lo que cabría esperar de una carga de trabajo típica, es posible que haya algunas oportunidades donde la optimización del rendimiento pueda ser beneficiosa para su aplicación.

En realidad, todas las bases de datos pueden beneficiarse de otra ronda de optimización del rendimiento.

Además del mantenimiento de optimización típico (por ejemplo, el análisis de los índices, los planes de ejecución, etc.), debe optimizar sus rutinas de acceso a los datos de la base de datos SQL de Azure de destino. 

| Artículo | Descripción |
|:--|:--|
| [Guía de rendimiento de Base de datos SQL de Azure](http://msdn.microsoft.com/library/dn369873.aspx) | La 'Tuning Your Application' sección proporciona técnicas y recomendaciones detalladas para la optimización del rendimiento de una base de datos SQL de Azure.|
|[Herramientas de optimización y supervisión del rendimiento](https://msdn.microsoft.com/library/ms179428.aspx)| Vínculos y descripciones de las herramientas disponibles para supervisar eventos en SQL Server y para optimizar el diseño físico de la base de datos.|
|[Supervisión y optimización del rendimiento](https://msdn.microsoft.com/library/ms189081.aspx)|Sección de optimización del rendimiento de SQL Server 2014 en MSDN.|
| [Solucionar problemas de rendimiento en SQL Server 2008](https://msdn.microsoft.com/library/dd672789.aspx) | Notas del productos más antiguas aunque todavía relevantes que proporcionan orientación para la solución de problemas comunes de rendimiento, incluida una excelente información sobre cómo solucionar problemas de cuellos de botella de la CPU y memoria.|
|[Solucionar problemas y optimizar consultas con la base de datos SQL Azure](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|Tema más antiguo aunque todavía relevante sobre las vistas de administración dinámica y cómo se pueden usar para solucionar problemas.|



## 5. Actualizar al nuevo nivel de servicio o de rendimiento
Después de determinar el nivel adecuado de servicio y de rendimiento para la base de datos Web o Business, hay varias maneras de actualizar la base de datos al nuevo nivel:

| Herramienta de administración | Para cambiar el nivel de rendimiento y el nivel de servicio de una base de datos|
| :---| :---|
| [Portal de administración de Azure](https://manage.windowsazure.com) | haga clic en la pestaña **ESCALA** de la página del panel de la base de datos. |
| [PowerShell de Azure](http://msdn.microsoft.com/library/azure/dn546726.aspx) | utilice el cmdlet [Set-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546732.aspx). |
| [API de REST de administración del servicio](http://msdn.microsoft.com/library/azure/dn505719.aspx) | utilice el comando [Actualizar base de datos](http://msdn.microsoft.com/library/dn505718.aspx).|
| [Transact-SQL](http://msdn.microsoft.com/library/bb510741.aspx) | utilice la instrucción [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx). |

Para obtener información más detallada, consulte [Cambio de los niveles de servicio y de rendimiento de la base de datos](http://msdn.microsoft.com/library/dn369872.aspx)


## 6.	Supervisar la actualización al nuevo nivel de servicio o de rendimiento
La base de datos SQL de Azure proporciona información sobre el progreso de las operaciones de administración (como CREATE, ALTER o DROP) realizadas en una base de datos en la vista de administración dinámica sys.dm_operation_status en la base de datos maestra del servidor lógico donde se encuentra la base de datos actual [consulte la documentación de sys.dm _operation _status.](http://msdn.microsoft.com/library/azure/dn270022.aspx) Use la DMV del estado de la operación para determinar el progreso de la operación de actualización para una base de datos. Esta consulta de ejemplo muestra todas las operaciones de administración realizadas en una base de datos:

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

Si usa el portal de administración para la actualización, también hay una notificación disponible en el portal para la operación.

### ¿Qué ocurre cuando la carga de trabajo de la base de datos alcanza los límites de recursos después de la actualización?
Los niveles de rendimiento se calibran y rigen para proporcionar los recursos necesarios para ejecutar la carga de trabajo de la base de datos hasta los límites máximos permitidos para el nivel de rendimiento o de servicio seleccionado (es decir, el consumo de recursos está en un 100 %). Si la carga de trabajo alcanza los límites en uno de los límites de CPU/datos, registro de E/S o E/S, seguirá recibiendo los recursos en el nivel máximo permitido, pero es probable que perciba un aumento de las latencias en las consultas. Alcanzar uno de estos límites no provocará errores, sino una ralentización de la carga de trabajo, a menos que la ralentización sea tan severa que las consultas empiecen a agotar el tiempo de espera. Si alcanza el límite máximo permitido de sesiones y solicitudes de usuario simultáneas (subprocesos de trabajo), se mostrará [error 10928 o 10929](http://msdn.microsoft.com/library/azure/dn338078.aspx).


## 7.	Supervisar la base de datos después de la actualización
Después de la actualización de la base de datos Web o Business en el nuevo nivel, se recomienda supervisar activamente la base de datos para asegurar que las aplicaciones se ejecutan con el rendimiento deseado y optimizar el uso según sea necesario. Se recomiendan los siguientes pasos adicionales para supervisar la base de datos.


**Datos de consumo de recursos:** para las bases de datos Basic, Standard y Premium, hay disponibles datos de consumo de recursos más detallados a través de una nueva DMV denominada [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) en la base de datos del usuario. Esta DMV proporciona información de consumo de recursos casi en tiempo real a intervalos de 15 segundos para la hora de funcionamiento anterior. El consumo de porcentaje de DTU para un intervalo se calcula como el consumo de porcentaje máximo de las dimensiones de registro, CPU y E/S. Esta es una consulta para calcular el consumo medio de porcentaje de DTU durante la última hora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
La [documentación](http://msdn.microsoft.com/library/dn800981.aspx) adicional contiene detalles sobre cómo usar esta DMV.  [La Guía de rendimiento de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/dn369873.aspx) explica cómo supervisar y ajustar la aplicación.


- **Alertas:** configure 'Alerts' el Portal de administración de Azure para recibir una notificación cuando el consumo de DTU para una base de datos actualizada alcance un determinado nivel alto. Las alertas de la base de datos pueden configurarse en el Portal de administración de Azure para diferentes métricas de rendimiento como DTU, CPU, E/S y registro. 

	Por ejemplo, puede configurar una alerta de correo electrónico en "Porcentaje de DTU" si el valor de porcentaje medio de DTU supera el 75 % en los últimos 5 minutos. Consulte [Procedimiento: recibir notificaciones de alerta y administrar reglas de alerta en Azure](http://msdn.microsoft.com/library/azure/dn306638.aspx) para obtener más información acerca de cómo configurar las notificaciones de alerta.


- **Actualización o degradación programada del nivel de rendimiento:** si su aplicación tiene escenarios específicos que requieren más rendimiento solo a determinadas horas del día o de la semana, puede usar [Automatización de Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx) para actualizar o degradar la base de datos a un nivel de rendimiento superior o inferior, como una operación planeada.

	Por ejemplo, actualice la base de datos a un nivel más alto de rendimiento durante un trabajo de mantenimiento o por lotes semanal y degrádela cuando se complete el trabajo. Este tipo de programación también es útil para cualquier operación que consuma muchos recursos grandes, como carga de datos, reconstrucción de índices, etc. Tenga en cuenta que el modelo de facturación de la base de datos SQL de Azure se basa en el uso por horas de un nivel de rendimiento o de servicio. Esta flexibilidad permite planear actualizaciones programadas o planeadas de una manera más rápida y económica.



## Resumen
El servicio de la Base de datos SQL de Azure proporciona datos de telemetría y herramientas para evaluar las cargas de trabajo de la base de datos Web o Business y determina el nivel de servicio más adecuado para la actualización. El proceso de actualización es bastante sencillo y se puede realizar sin dejar la base de datos sin conexión y sin pérdida de datos. Las bases de datos actualizadas se benefician de un rendimiento predecible y de características adicionales proporcionadas por los nuevos niveles de servicio.




<!--Image references-->
[1]: ./media/sql-database-upgrade-new-service-tiers/service-tier-features.png
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png


<!--HONumber=47-->
 