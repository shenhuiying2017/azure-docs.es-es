<properties 
   pageTitle="Registros de IIS en Log Analytics | Microsoft Azure"
   description="Internet Information Services (IIS) almacena la actividad de usuario en archivos de registro que Log Analytics puede recopilar. En este artículo se describe cómo configurar la recopilación de registros de IIS y detalles de los registros que crean en el repositorio de OMS."
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
   ms.date="05/02/2016"
   ms.author="bwren" />

# Registros de IIS en Log Analytics
Internet Information Services (IIS) almacena la actividad de usuario en archivos de registro que Log Analytics puede recopilar.

![Registros IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## Configuración de registros de IIS
Log Analytics recopila entradas de los archivos de registros que crea IIS, por lo que debe [configurar IIS para el registro](https://technet.microsoft.com/library/hh831775.aspx) y seleccionar los campos que desea que Log Analytics recopile. IIS no registra todos los campos de manera predeterminada, por lo que es posible que desee seleccionar manualmente campos adicionales más allá de los determinados.

Log Analytics solo admite archivos de registro de IIS almacenados en formato W3C. No recopilará registros en formato nativo IIS o NCSA.

Configure registros de IIS en Log Analytics desde el [menú de datos en la configuración de Log Analytics](log-analytics-data-sources.md/configuring-data-sources). No se requiere otra configuración que seleccionar **Collect W3C format IIS log files** (Recopilar archivos de registro de IIS en formato W3C).


## Colección de datos

Log Analytics recopilará entradas de registro de IIS de cada origen conectado a intervalos de, aproximadamente, 15 minutos. El agente registrará su lugar en cada registro de evento del que realiza la recopilación. Si el agente queda sin conexión durante un período, Log Analytics recopilará eventos desde donde quedó, incluso si esos eventos se crearon mientras el agente estaba sin conexión.


## Propiedades de registro de IIS

Los registros de IIS tienen un tipo **W3CIISLog** y tienen las propiedades que aparecen en la tabla siguiente.

| Propiedad | Descripción |
|:--|:--|
| Equipo | Nombre del equipo desde el que se recopiló el evento. |
| cIP | Dirección IP del cliente. |
| csMethod | Método de la solicitud, como GET o POST. |
| csReferer | Sitio cuyo vínculo el usuario siguió desde el sitio actual. |
| csUserAgent | Tipo de explorador del cliente. |
| csUserName | Nombre del usuario autenticado que obtuvo acceso al servidor. Los usuarios anónimos se indican con un guion. |
| csUriStem | El destino de la solicitud, como una página web. |
| csUriQuery | La consulta que el cliente intentaba realizar, si corresponde. |
| ManagementGroupName | El nombre del grupo de administración para los agentes de SCOM. En el caso de los otros agentes, es AOI-<id. de área de trabajo> |
| RemoteIPCountry | El país de la dirección IP del cliente. |
| RemoteIPLatitude | La latitud de la dirección IP del cliente. |
| RemoteIPLongitude | La longitud de la dirección IP del cliente. |
| scStatus | Código de estado HTTP. |
| scSubStatus | Código de error de subestado. |
| scWin32Status | Código de estado de Windows. |
| sIP | Dirección IP del servidor web. |
| SourceSystem | OpsMgr |
| sPort | Puerto en el servidor al que está conectado el cliente. |
| sSiteName | Nombre del sitio IIS. |
| TimeGenerated | Fecha y hora en que se registró la entrada. |
| TimeTaken | Duración del procesamiento de la solicitud, en milisegundos. |

## Búsquedas de registros con registros de IIS

La tabla siguiente proporciona ejemplos distintos de consultas de registro que recuperan registros de IIS.

| Consultar | Descripción |
|:--|:--|
| Type=IISLog | Todos los registros de IIS. |
| Type=IISLog EventLevelName=error | Todos los eventos de Windows con gravedad de error. |
| Type=W3CIISLog | Measure count() by cIP | Contador de entradas de registro de IIS por dirección IP del cliente. |
| Type=W3CIISLog csHost="www.contoso.com" | Measure count() by csUriStem | Contador de entradas de registro de IIS por dirección URL para el host www.contoso.com. |
| Type=W3CIISLog | Measure Sum(csBytes) by Computer | top 500000| Total de bytes recibidos por cada equipo de IIS |

## Pasos siguientes

- Configure Log Analytics para recopilar otros [orígenes de datos](log-analytics-data-sources.md) para su análisis.
- Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados desde soluciones y orígenes de datos. 
- Configure alertas en Log Analytics para recibir notificaciones de manera nativa con respecto a condiciones importantes encontradas en los registros de IIS.

<!---HONumber=AcomDC_0504_2016-->