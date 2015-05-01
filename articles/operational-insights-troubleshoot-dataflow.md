<properties 
	pageTitle="Visión operativa: solución de problemas con flujo de datos de Operations Manager o agentes" 
	description="Aprenda a solucionar problemas relacionados con flujo de datos de Operations Manager y agentes con conexión directa a Visión operativa de Azure" 
	services="operational-insights" 
	documentationCenter="" 
	authors="dani3l3" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/23/2015" 
	ms.author="dmuscett"/>


#Solucione problemas de flujo de Operations Manager o agentes a Visión operativa
El siguiente conjunto de procedimientos sirve como guía para ayudar a solucionar problemas con las implementaciones de Operations Manager o los agentes con conexión directa configuradas para informar datos a Visión operativa de Azure.

##Procedimiento 1: valide que los módulos de administración correctos se descargaron al entorno de Operations Manager
*Nota: si solo usa Direct Agent, puede ir al siguiente procedimiento.*

Según los paquetes de inteligencia que ha habilitado en el Portal de OpInsights, podrá ver más o menos de estos módulos de administración. Busque la palabra clave "Advisor" o "Intelligence" en el nombre. 
Puede buscar estos módulos de administración con PowerShell de Operations Manager:

      Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
      Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
    
Nota: si está solucionando problemas con el paquete de inteligencia de capacidad, compruebe CUÁNTOS módulos de administración tiene con la palabra 'capacity' en el nombre: existen dos módulos de administración con el mismo nombre para mostrar (pero con un identificador interno distinto) que vienen en el mismo paquete de módulos de administración; si no es posible importar alguno de los dos (a menudo debido a una dependencia de VMM faltante), el otro módulo de administración no se importa y la operación no se vuelve a intentar.

Debiera ver los siguientes tres módulos de administración relacionados con 'capacity'
1. Microsoft System Center Advisor Capacity Intelligence Pack 
1. Microsoft System Center Advisor Capacity Intelligence Pack 
1. Microsoft System Center Advisor Capacity Storage Data 

Si solo ve uno o dos de ellos, pero no los tres, quítelo y espere entre cinco y diez minutos para que Operations Manager vuelva a descargarlo e importarlo; revise los registros de eventos para consultar los errores durante este periodo.

##Procedimiento 2: valide que los paquetes de inteligencia correctos se descargaron a Direct Agent
*Nota: si solo usa Operations Manager, puede omitir este procedimiento.*

En Direct Agent, debiera ver la directiva de colección de paquetes de inteligencia que se almacenan en caché en **C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs**


##Procedimiento 3: Valide que los datos se estén enviando al servicio Advisor (o que se está intentando hacerlo)
Dependiendo de si usa Operations Manager o agentes con conexión directa, puede realizar el siguiente procedimiento en la máquina del agente directo o en el servidor de administración de Operations Manager:

1. - Abra 'Monitor de rendimiento' 
1. - Seleccione 'Grupos de administración de servicio de mantenimiento'
1. - Agregue todos los contadores que comienzan con 'HTTP'

Si todo está configurado correctamente, debe ver actividad en estos contadores, a medida que se cargan eventos y otros elementos de datos (según los paquetes de inteligencia incorporados en el portal y la directiva de colección de registros configurada). Esos contadores no necesariamente deben estar continuamente 'ocupados': si ve poca o nula actividad, es posible que no esté incorporado en muchos paquetes de inteligencia o que tenga una directiva de colección ligera. 

##Procedimiento 4: revise los errores en los registros de eventos del servidor de administración o en el agente directo 
Como paso final, si todos los pasos anteriores fallan y sigue sin ver datos recibidos por el servicio, revise si ve algún error en el **Visor de eventos**.

Abra **Visor de eventos** -> **Aplicación y servicios** -> **Operations Manager** y filtre por Orígenes de eventos: **Asesor**, **Módulos de servicio de mantenimiento**, **Servicio de mantenimiento** y **Conector de servicio** (este último solo se aplica a Direct Agent). 

La mayoría de estos eventos sería similar en Operations Manager y en Direct Agent y los pasos para solucionar problemas también serían similares. 
Lo único que difiere entre Operations Manager y Direct Agent es el proceso de registro (GUI en Operations Manager, combinación de identificador/clave de área de trabajo en Direct Agent) pero, después del registro inicial, los certificados que se intercambian y usan y todo lo relacionado con la comunicación con el servicio es igual.

Por lo tanto, muchos de estos eventos se aplican a ambos tipos de infraestructura de informes. La siguiente es una lista de los eventos más comunes que podría encontrar.

###Eventos desde el origen 'Módulos de servicio de mantenimiento'
#####Id. de evento 2138
Esto significa que el proxy requiere autenticación. Siga los pasos para [configurar los servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

#####Id. de evento 2137
Operations Manager no puede leer el certificado de autenticación. Volver a ejecutar el asistente para registro del asesor corregirá las cuentas RunAs/certificados

#####Id. de evento 2132
Significa **No autorizado**. Podría tratarse de un problema con el certificado y/o el registro en el servicio; intente volver a ejecutar el asistente de registro que corregirá los certificados y las cuentas RunAs. Además, compruebe que el proxy está definido para permitir exclusiones. Para obtener más información, consulte [Configuración de servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx)

#####Id. de evento 2129
Esta es una conexión con errores debido a una negociación SSL errónea. Compruebe que los sistemas están configurados para usar TLS y no SSL. Podría haber algunos ajustes SSL extraños en este servidor respecto de los cifrados, ya sea en las opciones **avanzadas** de Internet Explorer o en el registro de Windows bajo la clave 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

#####Id. de evento 2127
Código de error recibido al fallar el envío de datos. Si solo se produce de vez en cuando, podría ser simplemente un problema técnico. Esté atento para ver si se produce con frecuencia. Si el mismo error ocurre frecuentemente (cada diez minutos o más durante un periodo extendido), podría tratarse de un problema real: revise la configuración de la red, los ajustes del servidor proxy descritos anteriormente y vuelva a ejecutar el asistente de registro. Pero si solo se produce de manera esporádica (es decir, un par de veces al día), todo debiera estar bien, puesto que los datos se pondrán en cola y se volverán a transmitir. Si ocurre con esa frecuencia, probablemente no sea más que solo un tiempo de espera de la red.
 
Algunos de los códigos de error HTTP tienen ciertos significados especiales, por ejemplo: 

- la PRIMERA vez que un servidor de administración o un agente directo de MMA intenta enviar datos a nuestro servicio, recibirá un error 500 con un código de error 404 interno (404 significa "No encontrado"). Este error indica que el área de almacenamiento que usaremos para esta área de trabajo todavía no está lista, sino que se está aprovisionando. Sin embargo, en el siguiente reintento estará lista y el flujo comenzará a funcionar (en condiciones normales).
- El código 403 podría indicar un problema de permisos/credenciales, etc. 

#####Id. de evento 2128
Error en la resolución de nombres DNS. El servidor no puede resolver nuestra dirección de Internet a la que debe enviar datos. Podría tener que ver con la configuración de la resolución de DNS en su máquina, con la configuración incorrecta del proxy o con un problema (temporal) con el DNS en el proveedor. Al igual que ocurre con el evento anterior, podría indicar que se trata de un problema real o no, dependiendo de si se produce de manera constante o 'de vez en cuando'.

#####Id. de evento 2130
Tiempo de espera. Al igual que ocurre con el evento anterior, podría indicar que se trata de un problema o no, dependiendo de si se produce de manera constante o "de vez en cuando".

### Eventos desde origen 'HealthService'
#####Id. de evento 4511
No es posible cargar el módulo "System.PublishDataToEndPoint" : no se encontró el archivo. Error en la inicialización de un módulo de tipo "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") con código de error El sistema no encontró el archivo especificado.  
Este error indica que tiene DLL antiguas en su equipo, las que no contienen los módulos requeridos. Para corregir este error, actualice los servidores de administración al paquete acumulativo de actualizaciones más reciente.

#####Id. de evento 4502
Bloqueo de módulo. Si ve este error en flujos de trabajo con nombres como **CollectInstanceSpace** o **CollectTypeSpace**, puede indicar que el servidor tiene problemas para enviar ciertos datos de configuración. Podría indicar que se trata de un problema o no, dependiendo de la frecuencia con que se produce: de manera constante o "de vez en cuando". Si se produce con una frecuencia mayor a una hora, definitivamente se trata de un problema. Si esta operación presenta un error solo una o dos veces al día, no existen problemas para que se realice una recuperación. Según la manera en que el módulo realmente presenta el error (la descripción mostrará más detalles), podría tratarse de un problema local (es decir, para coleccionar en la base de datos) o un problema en el envío a la nube. Compruebe la configuración de la red y del proxy y, en el peor de los casos, intente reiniciar el Servicio de mantenimiento.

#####Id. de evento 4501
Bloqueo del módulo "System.PublishDataToEndPoint". Un módulo del tipo "System.PublishDataToEndPoint" informó un error 87L que se ejecutaba como parte de la regla "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" en ejecución para la instancia "Grupo de administración de Operations Manager" con id:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" en el grupo de administración "SCOMTEST". 
YA NO debiera ver esto con este flujo de trabajo, módulo y error exactos; solía ser [un error *que ahora está corregido*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). Si vuelve a verlo, infórmelo a través del canal de soporte técnico de Microsoft de su preferencia.


### Eventos desde origen 'Service Connector'
#####Id. de evento 4002
El servicio devolvió un código de estado HTTP 403 en respuesta a una consulta.  Compruebe el estado del servicio con el administrador de servicios. La consulta se volverá a intentar más tarde. Puede recibir un código 403 durante la fase de registro inicial del agente; verá una dirección URL como https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest
El código de error 403 significa "Prohibido". Normalmente se trata de una clave o identificador de área de trabajo que se copió de manera incorrecta, o bien el reloj no se encuentra sincronizado con la máquina. Intente sincronizar con un origen de hora confiable y use la comprobación de conectividad en el applet del Panel de control para que Microsoft Monitoring Agent compruebe que posee la clave y el identificador de área de trabajo correctos. 





##Procedimiento 5: busque los agentes para enviar sus datos y haga que se indexen en el Portal
Revise el Portal de Visión operativa, en la página de información general, vaya al mosaico pequeño **Servidores de uso**; este mostrará si los grupos de administración (y sus agentes) y agentes directos informan datos a la búsqueda. El número de agentes que aparece en el mosaico deriva de los datos: si las máquinas no informan durante dos semanas, dejarán el radar.

Las exploraciones en profundidad le llevarán a la búsqueda y le mostrarán la marca de tiempo de los últimos datos indexados para cada máquina. Desde ahí puede explorar cuáles son los datos. Dependiendo de la cantidad configurada de recopilaciones de datos y de cuáles son los paquetes de inteligencia, la velocidad y la programación de carga de datos pueden variar.

Esta página también cuenta con información de medición (este no usa el índice de búsqueda, sino el sistema de facturación; se actualiza cada dos horas) sobre las cantidades de datos enviados al servicio desglosado por el paquete de inteligencia.


<!--HONumber=52-->