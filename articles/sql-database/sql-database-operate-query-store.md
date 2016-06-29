<properties
   pageTitle="Funcionamiento del Almacén de consultas de Base de datos SQL de Azure"
   description="Aprenda a utilizar el Almacén de consultas de Base de datos SQL de Azure."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="05/25/2016"
   ms.author="carlrab"/>

# Funcionamiento del almacén de consultas de Base de datos SQL de Azure 

El Almacén de consultas en Azure es una característica de base de datos completamente administrada que recopila y presenta constantemente información histórica detallada sobre todas las consultas. Es parecido a la caja negra de un avión y simplifica considerablemente la solución de problemas de rendimiento de las consultas tanto para los clientes locales como de nube. En este artículo se explican los aspectos específicos del funcionamiento del Almacén de consultas de Azure. Con estos datos de consulta previamente recopilados, los usuarios pueden diagnosticar y resolver rápidamente los problemas de rendimiento y así dedicar más tiempo a centrarse en sus negocios.

El Almacén de consultas está [disponible globalmente](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) en Base de datos SQL de Azure desde noviembre de 2015. Es la base de las características de análisis y ajuste del rendimiento, como el [Asesor de Base de datos SQL y el Panel de rendimiento](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). En el momento de publicar este artículo, el Almacén de consultas se ejecuta en más de 200 000 bases de datos de usuario de Azure y recopila información relativa a las consultas para varios meses, sin interrupción.

> [AZURE.IMPORTANT] Microsoft está en proceso de activar el Almacén de consultas para todas las bases de datos SQL de Azure (nuevas y existentes). Actualmente, este proceso de activación solo incluye bases de datos singleton, pero se expandirá a bases de datos del grupo elástico en un futuro próximo. Cuando utilice grupos elásticos, habilite el Almacén de consultas solo para el subconjunto pequeño de bases de datos. Habilitar el Almacén de consultas para todas las bases de datos de un grupo elástico puede dar lugar a un uso excesivo de recursos y podría hacer que el sistema dejara de responder.

## El Almacén de consultas como una característica administrada de Base de datos SQL de Azure

El Almacén de consultas que funciona en Base de datos SQL de Azure se basa en estos dos principios fundamentales:

- No provoca un impacto visible sobre le carga de trabajo del cliente
- Supervisa continuamente las consultas a menos que una carga de trabajo del cliente se vea afectada

El impacto en la carga de trabajo del cliente tiene dos dimensiones:

- ***Disponibilidad***: el [SLA (Acuerdo de Nivel de Servicio) para Base de datos SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/) no se reduce cuando el Almacén de consultas está en ejecución.
- ***Rendimiento***: la sobrecarga media introducida por el Almacén de consultas suele estar normalmente en el intervalo de 1-2 %.

El Almacén de consultas de Azure funciona con recursos limitados (CPU, memoria, E/S de disco, tamaño en disco, etc.). Respeta varias limitaciones del sistema con el fin de influir lo menos posible en la carga de trabajo normal:

- ***Limitaciones estáticas:*** las limitaciones impuestas por la capacidad de recursos de un nivel de servicio determinado (Básico, Estándar, Premium, Grupo elástico).
- ***Limitaciones dinámicas:*** las limitaciones impuestas por el consumo actual de la carga de trabajo (es decir, los recursos disponibles).

Para garantizar un funcionamiento continuo y confiable, Base de datos SQL de Azure ha creado una infraestructura de supervisión permanente alrededor de Almacén de consultas que recopila datos operativos clave de cada base de datos. Como resultado, se supervisan constantemente varios indicadores clave de rendimiento (KPI) técnicos con el fin de garantizar la confiabilidad:

- Número de excepciones y mitigaciones automáticas
- Número de bases de datos en estado READ\_ONLY y duración del estado READ\_ONLY
- Bases de datos principales con el consumo de memoria del Almacén de consultas por encima del límite.
- Bases de datos principales por frecuencia de limpieza automática y duración
- Bases de datos principales por duración de la carga de datos en memoria (inicialización del Almacén de consultas)
- Bases de datos principales por duración de vaciado de datos en el disco

Base de datos SQL de Azure usa los datos recopilados para:

- ***Aprender los patrones de uso sobre un gran número de bases de datos y, en consecuencia, mejorar la confiabilidad y la calidad de las características:*** el Almacén de consultas se ha mejorado con cada actualización de Base de datos SQL de Azure. 
- ***Resolver o mitigar los problemas causados por el Almacén de consultas:*** Base de datos SQL de Azure puede detectar y mitigar los problemas que tienen un impacto sustancial sobre la carga de trabajo del cliente, con una latencia baja (menos de una hora). La mayoría de las veces, los problemas se controlan configurando el Almacén de consultas como ***DESACTIVADO*** temporalmente.

De vez en cuando, las actualizaciones del Almacén de consultas introducen cambios en los valores predeterminados aplicados a configuraciones internas y pocas veces externas (orientados al cliente). En consecuencia, la experiencia de los clientes con el Almacén de consultas de Base de datos SQL de Azure puede diferir de la que tienen en entornos locales debido a las acciones automáticas realizadas por la plataforma de Azure:

- El Almacén de consultas se puede cambiar a ***OFF*** (DESACTIVADO) para mitigar los problemas y volver a ***ON*** (ACTIVADO) cuando el problema se resuelva.
- La configuración del Almacén de consultas se puede cambiar para garantizar que funcione de manera confiable. Para ello, se pueden realizar:
    - Cambios individuales en la base de datos que solucionan los problemas de inestabilidad.
    - Implementación global de cambios óptimos en la configuración que proporcionan ventajas para todas las bases de datos que usan el Almacén de consultas.

La desactivación (***OFF***) del Almacén de consultas es una acción automática que tiene como ámbito las bases de datos individuales. Se produce cuando el comportamiento de un producto afecta de forma negativa a las bases de datos de usuario y, como consecuencia, el mecanismo de detección dispara una alerta. Para esas bases de datos en particular, el Almacén de consultas permanece ***desactivado*** hasta que una nueva versión con la implementación mejorada del Almacén de consultas esté disponible. Cuando se produce una transición al estado ***OFF*** (DESACTIVADO), se informa al cliente por correo electrónico y se le aconseja que se abstenga de volver a habilitar el Almacén de consultas hasta que se implemente una nueva versión. Después de una nueva implementación, la infraestructura de Base de datos SQL de Azure activa automáticamente el Almacén de consultas en cualquier base de datos que se haya establecido en ***OFF*** (DESACTIVADO).

Con menor frecuencia, Base de datos SQL de Azure puede aplicar nuevos valores predeterminados de configuración para todas las bases de datos de usuario, optimizados para el funcionamiento confiable y la recopilación continua de datos.

## Configuración óptima del Almacén de consultas

En esta sección se describen los valores predeterminados de una configuración óptima que están diseñados para garantizar el funcionamiento confiable del Almacén de consultas, así como de las características que de él dependen, como el [Asesor de Base de datos SQL y el Panel de rendimiento](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). La configuración predeterminada está optimizada para una recopilación continua de los datos, es decir, un tiempo mínimo en los estados OFF y READ\_ONLY.

| Configuración | Descripción | Valor predeterminado | Comentario |
| ------------- | ----------- | ------- | ------- |
| MAX\_STORAGE\_SIZE\_MB | Especifica el límite del espacio de datos que tomará el Almacén de consultas dentro de la base de datos de cliente. | 100 | Se aplica a nuevas bases de datos. |
| INTERVAL\_LENGTH\_MINUTES | Define el tamaño de la ventana de tiempo durante la que se agregan y conservan las estadísticas recopiladas en tiempo de ejecución para los planes de consulta. Todos los planes de consulta activa tendrán al menos una fila por un período de tiempo definido con esta configuración. | 60 | Se aplica a nuevas bases de datos. |
| STALE\_QUERY\_THRESHOLD\_DAYS | Directiva de limpieza basada en el tiempo que controla el período de retención de las estadísticas en tiempo de ejecución guardadas y las consultas inactivas. | 30 | Se aplica a nuevas bases de datos y bases de datos con la configuración predeterminada anterior (367). |
| SIZE\_BASED\_CLEANUP\_MODE | Especifica si limpieza automática de los datos se llevará a cabo cuando el tamaño de los datos del Almacén de consultas se aproximen al límite. | AUTO | Se aplica a todas las bases de datos. |
| QUERY\_CAPTURE\_MODE | Especifica si se realizará el seguimiento de todas las consultas o solo de un subconjunto de estas. | AUTO | Se aplica a todas las bases de datos. |
| FLUSH\_INTERVAL\_SECONDS | Especifica el período máximo durante el que las estadísticas en tiempo de ejecución capturadas se conservarán en memoria, antes de vaciarlas en el disco. | 900 | Se aplica a nuevas bases de datos. |
||||||

> [AZURE.IMPORTANT] Estos valores predeterminados se aplicarán automáticamente en la fase final de la activación del Almacén de consultas en todas las bases de datos de SQL de Azure (consulte arriba las observaciones importantes). Después de esta optimización, Base de datos SQL de Azure no cambiará los valores de configuración establecidos por los clientes, a no ser que influyan negativamente en la carga de trabajo principal o en las operaciones confiables del Almacén de consultas.

Si quiere permanecer con su configuración personalizada, utilice [ALTER DATABASE con las opciones del Almacén de consultas](https://msdn.microsoft.com/library/bb522682.aspx) para revertir la configuración al estado anterior. Consulte [Best Practices with the Query Store](https://msdn.microsoft.com/library/mt604821.aspx) (Procedimientos recomendados con el Almacén de consultas) para aprender a elegir los parámetros de configuración óptima.

## Pasos siguientes

[SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)](sql-database-performance.md)

## Recursos adicionales

Para más información, consulte los siguientes artículos:

- [A flight data recorder for your database (Una caja negra para la base de datos)](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Supervisar el rendimiento mediante el Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)

- [Query Store Usage Scenarios (Escenarios de uso del Almacén de consultas)](https://msdn.microsoft.com/library/mt614796.aspx)

- [Supervisar el rendimiento mediante el Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)

<!---HONumber=AcomDC_0615_2016-->