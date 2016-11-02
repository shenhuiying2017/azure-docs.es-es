<properties 
   pageTitle="Registros de eventos de Windows en Log Analytics | Microsoft Azure"
   description="Los registros de eventos de Windows son uno de los orígenes de datos más comunes que usa Log Analytics.  En este artículo se describe cómo configurar la recopilación de registros de eventos de Windows y detalles de los registros que crean en el repositorio de OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />


# <a name="windows-event-log-data-sources-in-log-analytics"></a>Orígenes de datos de registros de eventos de Windows en Log Analytics

Los registros de eventos de Windows son uno de los [orígenes de datos](log-analytics-data-sources.md) más comunes que se usan para los agentes de Windows, debido a que se trata del método que la mayoría de las aplicaciones usa para registrar información y errores.  Puede recopilar eventos de registros estándar, como el sistema y la aplicación, además de especificar cualquier registro personalizado creado por las aplicaciones que debe supervisar.

![Eventos de Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configuración de registros de eventos de Windows

Configure los registros de eventos de Windows en el [menú Datos en Configuración de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics solo recopilará eventos de los registros de eventos de Windows que estén especificados en la configuración.  Para agregar un registro nuevo, escriba el nombre y haga clic en **+**.  Para cada registro, solo se recopilarán los eventos con las gravedades seleccionadas.  Compruebe las gravedades del registro determinado que desea recopilar.  No puede proporcionar criterios adicionales para filtrar eventos.

![Configurar eventos de Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Colección de datos

Log Analytics recopilará cada evento que coincida con una gravedad seleccionada de un registro de eventos supervisado cuando se cree el evento.  El agente registrará su lugar en cada registro de evento del que realiza la recopilación.  Si el agente queda sin conexión durante un período, Log Analytics recopilará eventos desde donde quedó, incluso si esos eventos se crearon mientras el agente estaba sin conexión.


## <a name="windows-event-records-properties"></a>Propiedades de los registros de eventos de Windows

Los registros de eventos de Windows tienen un tipo **Event** y tienen las propiedades que aparecen en la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| Equipo            | Nombre del equipo desde el que se recopiló el evento. |
| EventCategory       | Categoría del evento. |
| EventData           | Todos los datos con formato sin procesar. |
| EventId             | El número del evento. |
| EventLevel          | La gravedad del evento en formato numérico. |
| EventLevelName      | La gravedad del evento en formato de texto. |
| EventLog            | El nombre del registro de eventos desde el que se recopiló el evento. |
| ParameterXml        | Los valores de parámetro de evento en formato XML. |
| ManagementGroupName | El nombre del grupo de administración para los agentes de SCOM.  En el caso de los otros agentes, es AOI-<workspace ID> |
| RenderedDescription | La descripción del evento con valores de parámetro. |
| Origen              | El origen del evento. |
| SourceSystem  | El tipo de agente desde el que se recopiló el evento. <br> OpsManager: agente de Windows, ya sea una conexión directa o SCOM <br>  Linux: todos los agentes de Linux.  <br>  AzureStorage: Diagnósticos de Azure |
| TimeGenerated       | La fecha y la hora de creación del evento en Windows. |
| UserName            | El nombre de usuario de la cuenta que registró el evento. |



## <a name="log-searches-with-windows-events"></a>Búsquedas de registros con eventos de Windows

La tabla siguiente proporciona distintos ejemplos de búsquedas de registros que recuperar registros de eventos de Windows.

| Consultar | Descripción |
|:--|:--|
| Type=Event | Todos los eventos de Windows. |
| Type=Event EventLevelName=error | Todos los eventos de Windows con gravedad de error. |
| Type=Event &#124; Measure count() by Source | Contador de eventos de Windows por origen. |
| Type=Event EventLevelName=error &#124; Measure count() by Source | Contador de eventos de error de Windows por origen. |

## <a name="next-steps"></a>Pasos siguientes

- Configure Log Analytics para recopilar otros [orígenes de datos](log-analytics-data-sources.md) para su análisis.
- Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados desde soluciones y orígenes de datos.  
- Use [Campos personalizados](log-analytics-custom-fields.md) para redistribuir los registros de eventos en campos individuales.
- Configure la [recopilación de contadores de rendimiento](log-analytics-data-sources-performance-counters.md) desde los agentes de Windows.


<!--HONumber=Oct16_HO2-->


