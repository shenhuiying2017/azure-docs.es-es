<properties
	pageTitle="Soluciones de Visión operativa"
	description="Visión operativa es un servicio de análisis que permite a los administradores de TI obtener un visión profunda en entornos locales y en la nube. Le permite interactuar con datos de máquina en tiempo real e históricos para desarrollar con rapidez una visión personalizada, y proporciona patrones desarrollados por Microsoft y la comunidad para analizar datos."
	services="operational-insights"
	documentationCenter="n/a"
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="NA"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="05/20/2015"
	ms.author="alfran"/>

# Soluciones en Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Las soluciones son una colección de **lógica**, **visualización** y **reglas de adquisición de datos** que solucionan los desafíos clave a los que se enfrentan los clientes hoy en día. La búsqueda de Vista operativa ofrece soluciones para proporcionarle las métricas relativas a un área de problemas determinada. Le permiten obtener información más exhaustiva para ayudar a investigar y resolver problemas de funcionamiento más rápido, recopilar y correlacionar los diversos tipos de datos del equipo y le ayuda a ser proactivo con actividades como la planificación de capacidad, los informes de estado de revisión y la auditoría de seguridad.

Esta guía le orienta a través de las distintas soluciones disponibles y proporciona información sobre su uso.

>[AZURE.NOTE]Para obtener más información sobre cómo *agregar* soluciones, consulte [Incorporación de soluciones](operational-insights-add-solution.md)

## Evaluación de SQL

La solución de evaluación de SQL evalúa el riesgo y el estado de los entornos de SQL Server en un intervalo normal. De forma predeterminada, se examinarán los sistemas SQL semanalmente y la información se presentará en forma de paquete acumulativo mensual. Proporciona una lista prioritaria de recomendaciones adaptada a las implementaciones. Estas recomendaciones se clasifican en seis áreas de enfoque que permiten a usted y a su equipo lo siguiente:

- comprender **rápidamente** el **riesgo y el estado** de los entornos
- realizar **acciones** fácilmente para reducir el riesgo y mejorar el estado
- **dar prioridad** al trabajo y ser más **productivo**

Evaluación de SQL requiere .NET 4 para ejecutarse en los agentes. Es compatible con las ediciones Standard, Developer y Enterprise de SQL Server, todas las versiones compatibles actualmente. Consulte [Optimización del entorno con soluciones de evaluación](operational-insights-assessment.md).


## Evaluación de la configuración

La evaluación de la configuración proporciona información detallada sobre el estado actual de la infraestructura de servidores.

A diferencia de las soluciones, la evaluación de la configuración está disponible cuando se inicia por primera mediante Vista operativa con System Center Operations Manager. No está disponible si solo usa a agentes conectados directamente.

Los datos de configuración se recopilan a partir de servidores supervisados y, a continuación, se envían al servicio de Vista operativa en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Los datos procesados para los servidores se muestran para las siguientes áreas:

- **Alertas:** muestra las alertas proactivas relacionadas con la configuración que han surgido para los servidores supervisados. Estas alertas se generan a partir de reglas creadas por la organización de asistencia al cliente de Microsoft (CSS) con procedimientos recomendados del campo
- **Recomendaciones de conocimiento**: muestra los artículos de Microsoft Knowledge Base recomendados para cargas de trabajo que se encuentran en su infraestructura; estos se sugieren automáticamente según la configuración mediante el uso del aprendizaje automático
- **Servidores y cargas de trabajo analizados**: muestra los servidores y las cargas de trabajo que supervisa Vista operativa
- **Instantánea actual**: muestra la información más reciente acerca de los servidores que informaron de datos al servicio de Vista operativa
- **Historial de cambios**: muestra una lista de los cambios de configuración realizados en los servidores supervisados

> [AZURE.IMPORTANT]La evaluación de la configuración solo puede habilitarse para los campos administrados por **Operations Manager**. Para obtener información adicional, consulte [Conexión de Operations Manager](operational-insights-connect-scom.md)


## Evaluación de malware
Si se detecta que la protección es insuficiente, se muestran servidores con amenazas activas y con protección insuficiente en la página **Malware**. Utilice la información de la página **Malware** para desarrollar un plan para la aplicación de la protección a los servidores que lo necesitan.

> [AZURE.IMPORTANT]La evaluación de malware solo es compatible actualmente con clientes en tiempo real de Windows Defender y System Center Endpoint Protection (SCEP). Si no se detecta ninguno de estos clientes, utiliza datos de la herramienta de eliminación de software malintencionado y marca el servidor como si no tuviera protección en tiempo real. Puede leer más sobre ello [aquí](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). También estamos haciendo un seguimiento de [un problema con equipos con Windows 7 y Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)





## Administración de alertas

Con la solución de administración de alertas, puede ver las alertas de **Operations Manager** en todos los servidores. Esta herramienta puede ayudarle a evaluar fácilmente alertas e identificar las causas raíz en su entorno de una manera rápida y fluida.

Puede obtener información sobre escenarios clave, incluido lo siguiente:

- ¿Cuántas alertas se han generado dentro de un período especificado?
- Orígenes principales con el mayor número de alertas activas producidas dentro de un período de especificado.
- Alertas de advertencia e información crítica activa principales que se producen dentro de un período especificado.
- Capacidad de buscar en todas las alertas y ver todas las alertas detalladas

> [AZURE.IMPORTANT]La administración de alertas puede habilitarse únicamente cuando Vista operativa se usa junto con **System Center Operations Manager**. De esta forma, no se envía ningún dato desde agentes, sino que simplemente sincroniza las alertas de Operations Manager a la nube para ayudarle a identificarlas en Vista operativa y usar la búsqueda de registros. Para obtener información adicional, consulte [Conexión de Operations Manager](operational-insights-connect-scom.md)


## Planificación de capacidad

Puede utilizar la solución de administración de la capacidad en Vista operativa de Microsoft Azure para ayudarle a entender la capacidad de su infraestructura de servidor. La solución lee los contadores de rendimiento en el servidor supervisado y envía datos de uso al servicio de Visión operativa en la nube para su procesamiento. La lógica se aplica a los datos usados y el servicio en la nube registra los datos. Con el tiempo, se identifican los patrones de uso y se proyecta la capacidad según el consumo actual.

Por ejemplo, una proyección puede identificar cuándo serán necesarios núcleos de procesador adicionales o una memoria adicional para un servidor individual. En este ejemplo, la proyección podría indicar que en 30 días, el servidor necesitará memoria adicional. Esto puede ayudarle a planear una actualización de memoria durante la próxima ventana de mantenimiento del servidor, algo que puede ocurrir una vez cada dos semanas.

>[AZURE.IMPORTANT]La administración de la capacidad solo puede habilitarse cuando se utiliza Vista operativa junto con **System Center Operations Manager** y también debe habilitar el conector de Operations Manager con Virtual Machine Manager (VMM). Para obtener información adicional acerca de la conexión de los sistemas, consulte [Conexión de VMM con Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Para obtener más información sobre el uso de la solución de administración de la capacidad, consulte:

- [Uso de la página Proceso](operational-insights-capacity/#compute-page)
- [Uso de la página Almacenamiento con conexión directa](operational-insights-capacity/#direct-attached-storage-page)


## Seguimiento de cambios

Puede usar la solución de seguimiento de cambios de configuración para ayudarle a identificar fácilmente los cambios de los servicios de Windows y software que se producen en su entorno. La identificación de estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento. Con la información en la página de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores. Puede ver los cambios en la infraestructura y, a continuación, profundizar en los detalles de las siguientes categorías:

- Cambios realizados por el tipo de configuración para los servicios de Windows y el software
- Cambios de software en aplicaciones y actualizaciones para los servidores individuales
- Número total de cambios de software para cada aplicación
- Cambios de servicio de Windows para servidores individuales


## Evaluación de la actualización del sistema

Puede usar la solución de actualizaciones del sistema de Microsoft Azure Operational Insights para ayudarle a aplicar las actualizaciones que faltan a los servidores de su infraestructura. Si se detectan actualizaciones que faltan, se muestran en la página **Actualizaciones**. Puede utilizar la página **Actualizaciones** para trabajar con las actualizaciones que faltan y desarrollar un plan para aplicarlas a los servidores que lo requieran.

La página Actualizaciones detalla las categorías siguientes:

- Servidores a los que les faltan actualizaciones de seguridad
- Los servidores no se han actualizado recientemente
- Actualizaciones que se deben aplicar a servidores específicos
- Tipo de actualizaciones que faltan

Puede hacer clic en cualquier icono o elemento para ver sus detalles en la página **Buscar** y obtener más información sobre la actualización que falta. Los resultados de la búsqueda incluyen:

- Server
- Título de la actualización
- Id. de Knowledge Base
- Producto al que se aplica la actualización
- Gravedad de la actualización
- fecha de publicación

Los resultados de la búsqueda de servidor incluyen:

- Nombre de servidor
- Nombre de la versión del sistema operativo
- Método de habilitación de la actualización automática
- Días desde la última actualización
- Versión del agente de Windows Update
 

<!---HONumber=58_postMigration-->