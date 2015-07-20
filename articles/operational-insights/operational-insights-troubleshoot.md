<properties
   pageTitle="Solución de problemas de Operational Insights"
   description="Conozca los procedimientos de solución de problemas de Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

#Solución de problemas de Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede usar la información de las siguientes secciones para ayudarle a resolver problemas. Si el problema que tiene no se describe en este artículo, puede probar con el [blog del equipo de Visión operativa](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnóstico de problemas de conexión en Operational Insights

Como Microsoft Azure Operational Insights se basa en los datos que se mueven a y desde la nube, los problemas de conexión pueden ser enormes. Use la siguiente información para comprender y resolver los problemas de conexión.


**Mensaje de error:** se comprobó la conectividad de Internet, pero no se pudo establecer conexión con el servicio Visión operativa. Inténtelo de nuevo más tarde.

**Causas posibles:** -El servicio Visión operativa está en mantenimiento. Espere a que termine el mantenimiento de Visión operativa. -La red bloqueó el servicio Visión operativa. Póngase en contacto con su administrador de red y solicite acceso a Operational Insights, o use otro servidor como puerta de enlace.

**Mensaje de error:** no se pudo establecer la conexión a Internet. Compruebe la configuración de proxy.

**Causas posibles:** - Este servidor no está conectado a Internet. Compruebe el estado de conectividad de Internet y conecte el servidor a Internet. - La configuración del proxy no es correcta. Vea [Configuración del proxy y el firewall](operational-insights-proxy-firewall.md) para obtener información sobre cómo configurar o cambiar la configuración del proxy. - El servidor proxy requiere autenticación. Consulte [Configuración del proxy y el firewall](operational-insights-proxy-firewall.md) para obtener información sobre cómo configurar Operations Manager para usar un servidor proxy.


## Solución de problemas de detección de SQL Server

Si ejecuta Microsoft SQL Server 2008 R2 y, a pesar de implementar el agente Operations Manager, no ve alertas para este servidor, puede que tenga un problema de detección.

Para confirmar si este es el origen de su problema, compruebe los dos siguientes problemas:

- En el registro de eventos de Operations Manager, se muestra el Id. de evento 4001. Este evento indica que hay una clase no válida.

- En el Administrador de configuración de SQL Server, cuando ve los servicios de SQL Server, se muestra el mensaje de error: “Error en la llamada a procedimiento remoto. [0x0800706be]”

Si ambos problemas se dan, deberá instalar SQL Server 2008 R2 Service Pack 2. Para descargar este Service Pack, vea [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) en el Centro de descarga de Microsoft.

Después de instalar el Service Pack, debería ver datos de Operational Insights para el servidor al cabo de 24 horas.

## Solucione problemas de flujo de Operations Manager o agentes a Visión operativa

El siguiente conjunto de procedimientos sirve como guía para ayudar a solucionar problemas con las implementaciones de Operations Manager o los agentes con conexión directa configuradas para informar datos a Visión operativa de Azure.

### Procedimiento 1: Validar que los módulos de administración correctos se descargaron en el entorno de Operations Manager
>[AZURE.NOTE]Si solo usa Direct Agent, puede pasar al siguiente procedimiento.

En función de las soluciones (anteriormente llamadas paquetes de inteligencia) que habilite en el Portal de OpInsights verá mayor o menor cantidad de estos módulos de administración. Busque la palabra clave "Advisor" o "Intelligence" en el nombre. Puede buscar estos módulos de administración con PowerShell de Operations Manager:

```Powershell
Get-SCOMManagementPack | where {$_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version
Get-SCOMManagementPack | where {$_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version
```

>[AZURE.NOTE]Si soluciona problemas con la solución de capacidad, compruebe *cuántos* módulos de administración tiene con la palabra ’capacity’ en el nombre: existen dos módulos de administración con el mismo nombre para mostrar (pero con un identificador interno distinto) que vienen en el mismo paquete de módulos de administración; si no es posible importar alguno de los dos (a menudo debido a una dependencia de VMM que falta), el otro módulo de administración no se importa y la operación no se vuelve a intentar.

Debe ver los tres módulos de administración siguientes relacionados con 'capacity' - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Intelligence Pack - Microsoft System Center Advisor Capacity Storage Data

Si solo ve uno o dos de ellos, pero no los tres, quítelo y espere entre cinco y diez minutos para que Operations Manager vuelva a descargarlo e importarlo; revise los registros de eventos para consultar los errores durante este periodo.

### Procedimiento 2: Validar que las soluciones correctas se descargaron en Direct Agent
>[AZURE.NOTE]Si solo usa Operations Manager, puede omitir este procedimiento.

En Direct Agent, debe ver la directiva de colección de soluciones que se almacenan en caché en **C:\\Archivos de programa\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs**


### Procedimiento 3: Validar que se enviaron datos al servicio Advisor (o que se intentó hacerlo)
Dependiendo de si usa Operations Manager o agentes con conexión directa, puede realizar el siguiente procedimiento en la máquina del agente directo o en el servidor de administración de Operations Manager:

1. - Abrir el "Monitor de rendimiento"
2. - Seleccionar "Grupos de administración de servicio de mantenimiento"
3. - Agregar todos los contadores que empiezan por "HTTP"

Si todo está configurado correctamente, debe ver actividad en estos contadores, a medida que se cargan eventos y otros elementos de datos (según las soluciones incorporadas al portal y la directiva de colección de registros configurada). Esos contadores no necesariamente deben estar continuamente ocupados: si ve poca o nula actividad, es posible que no esté incorporado a muchas soluciones o que tenga una directiva de colección ligera.

### Procedimiento 4: Comprobar si hay errores en los registros de eventos del servidor de administración o de Direct Agent
Como paso final si ninguno de los pasos anteriores se ejecuta correctamente y sigue sin ver datos recibidos por el servicio, compruebe si ve algún error en el **Visor de eventos**.

Abra **Visor de eventos** –> **Aplicaciones y servicios** –> **Operations Manager** y filtre por orígenes de eventos: **Advisor**, **Módulos de servicio de mantenimiento **, **HealthService** y **Conector de servicios** (esta última solo se aplica Direct Agent).

La mayoría de estos eventos sería similar en Operations Manager y en Direct Agent y los pasos para solucionar problemas también serían similares. Lo único que difiere entre Operations Manager y Direct Agent es el proceso de registro (GUI en Operations Manager, combinación de identificador/clave de área de trabajo en Direct Agent) pero, después del registro inicial, los certificados que se intercambian y usan y todo lo relacionado con la comunicación con el servicio es igual.

Por lo tanto, muchos de estos eventos se aplican a ambos tipos de infraestructura de informes. La siguiente es una lista de los eventos más comunes que podría encontrar.

### Eventos de los "Módulos de servicio de mantenimiento" de origen
##### Id. de evento 2138
Esto significa que el proxy requiere autenticación. Siga los pasos para [configurar los servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### Id. de evento 2137
Operations Manager no puede leer el certificado de autenticación. Volver a ejecutar el asistente para registro del asesor corregirá las cuentas RunAs/certificados

##### Id. de evento 2132
Significa **No autorizado**. Podría tratarse de un problema con el certificado y/o el registro en el servicio; intente volver a ejecutar el asistente de registro que corregirá los certificados y las cuentas RunAs. Además, compruebe que el proxy está definido para permitir exclusiones. Para obtener más información, vea [Configuración de servidores proxy](https://msdn.microsoft.com/library/azure/dn884643.aspx).

##### Id. de evento 2129
Esta es una conexión con errores debido a una negociación SSL errónea. Compruebe que los sistemas están configurados para usar TLS y no SSL. Podría haber algunos ajustes SSL extraños en este servidor respecto de los cifrados, ya sea en las opciones **Avanzadas** de Internet Explorer o en el Registro de Windows bajo la clave.

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### Id. de evento 2127
Código de error recibido al fallar el envío de datos. Si solo se produce de vez en cuando, podría ser simplemente un problema técnico. Esté atento para ver si se produce con frecuencia. Si el mismo error ocurre frecuentemente (cada diez minutos o más durante un periodo extendido), podría tratarse de un problema real: revise la configuración de la red, los ajustes del servidor proxy descritos anteriormente y vuelva a ejecutar el asistente de registro. Pero si solo se produce de manera esporádica (es decir, un par de veces al día), todo debiera estar bien, puesto que los datos se pondrán en cola y se volverán a transmitir. Si ocurre con esa frecuencia, probablemente no sea más que solo un tiempo de espera de la red.

Algunos de los códigos de error HTTP tienen ciertos significados especiales, por ejemplo:

- la PRIMERA vez que un servidor de administración o un agente directo de MMA intenta enviar datos a nuestro servicio, recibirá un error 500 con un código de error 404 interno (404 significa "No encontrado"). Este error indica que el área de almacenamiento que usaremos para esta área de trabajo todavía no está lista, sino que se está aprovisionando. Sin embargo, en el siguiente reintento estará lista y el flujo comenzará a funcionar (en condiciones normales).
- El código 403 podría indicar un problema de permisos/credenciales, etc.

##### Id. de evento 2128
Error en la resolución de nombres DNS. El servidor no puede resolver nuestra dirección de Internet a la que debe enviar datos. Podría tener que ver con la configuración de la resolución de DNS en su máquina, con la configuración incorrecta del proxy o con un problema (temporal) con el DNS en el proveedor. Al igual que ocurre con el evento anterior, podría indicar que se trata de un problema real o no, dependiendo de si se produce de manera constante o de vez en cuando.

##### Id. de evento 2130
Tiempo de espera. Al igual que ocurre con el evento anterior, podría indicar que se trata de un problema o no, dependiendo de si se produce de manera constante o "de vez en cuando".

### Eventos del "HealthService" de origen
##### Id. de evento 4511
No se puede cargar el módulo "System.PublishDataToEndPoint": no se encuentra el archivo. Error de inicialización de un módulo de tipo "System.PublishDataToEndPoint" (CLSID "{D407D659-65E4-4476-BF40-924E56841465}") con el código de error El sistema no encuentra el archivo especificado. Este error indica que tiene DLL antiguas en su equipo, las que no contienen los módulos requeridos. Para corregir este error, actualice los servidores de administración al paquete acumulativo de actualizaciones más reciente.

##### Id. de evento 4502
Bloqueo de módulo. Si ve este error en flujos de trabajo con nombres como **CollectInstanceSpace** o **CollectTypeSpace**, puede indicar que el servidor tiene problemas para enviar ciertos datos de configuración. Podría indicar que se trata de un problema o no, dependiendo de la frecuencia con que se produce: de manera constante o "de vez en cuando". Si se produce con una frecuencia mayor a una hora, definitivamente se trata de un problema. Si esta operación presenta un error solo una o dos veces al día, no existen problemas para que se realice una recuperación. Según la manera en que el módulo realmente presenta el error (la descripción mostrará más detalles), podría tratarse de un problema local (es decir, para coleccionar en la base de datos) o un problema en el envío a la nube. Compruebe la configuración de la red y del proxy y, en el peor de los casos, intente reiniciar el Servicio de mantenimiento.

##### Id. de evento 4501
Bloqueo del módulo "System.PublishDataToEndPoint". Un módulo del tipo "System.PublishDataToEndPoint" informó de un error 87L que se ejecutaba como parte de la regla "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" en ejecución para la instancia "Grupo de administración de Operations Manager" con el identificador:"{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}" en el grupo de administración "SCOMTEST". Ya *no* debería ver esto con este flujo de trabajo, módulo y error exactos; era un [error que *ahora está corregido*](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale). Si vuelve a verlo, infórmelo a través del canal de soporte técnico de Microsoft de su preferencia.


### Evento del "Conector de servicios" de origen
##### Id. de evento 4002
El servicio devolvió un código de estado HTTP 403 en respuesta a una consulta. Compruebe el estado del servicio con el administrador de servicios. La consulta se volverá a intentar más tarde. Puede obtener un código 403 durante la fase de registro inicial del agente, verá una dirección URL como https://<YourWorkspaceID>.oms.opinsights.azure.com/ AgentService.svc/AgentTopologyRequest. El código de error 403 significa "prohibido": normalmente se trata de un WorkspaceId o una clave copiada erróneamente o de que el reloj del equipo no está sincronizado. Intente sincronizar con un origen de hora confiable y use la comprobación de conectividad en el applet del Panel de control para que Microsoft Monitoring Agent compruebe que posee la clave y el identificador de área de trabajo correctos.


### Procedimiento 5: Comprobar que los agentes envían sus datos y están indexados en el Portal
Regístrese en el Portal de OpInsights, desde la página Información general, navegue hasta el icono pequeño **Servidores y uso**; este mostrará si los grupos de administración (y sus agentes) y los agentes directos notifican datos en la búsqueda de registros. El número de agentes que aparece en el mosaico deriva de los datos: si las máquinas no informan durante dos semanas, dejarán el radar.

Las exploraciones en profundidad le llevarán a la búsqueda de registros y le mostrarán la marca de tiempo de los últimos datos indexados para cada máquina. Desde ahí puede explorar cuáles son los datos. Dependiendo de la cantidad configurada de recopilaciones de datos y de cuáles sean las soluciones, la programación de carga de datos y la velocidad pueden variar.

Esta página también presenta información de medición (esta no usa el índice de búsqueda de registros, sino el sistema de facturación y se actualiza cada dos horas) sobre las cantidades de datos enviados al servicio desglosado por la solución.

<!---HONumber=July15_HO2-->