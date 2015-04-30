<properties 
   pageTitle="Consideraciones sobre Operations Manager con Operational Insights"
   description="Si utiliza Microsoft Azure Operational Insights con Operations Manager, su configuración se basa en una distribución de agentes y grupos de administración de Operations Manager para recopilar y enviar datos al servicio Operational Insights para su análisis"
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
   ms.date="03/20/2015"
   ms.author="banders" />

# Consideraciones sobre Operations Manager con Operational Insights

Si utiliza Microsoft Azure Operational Insights con Operations Manager, su configuración se basa en una distribución de agentes y grupos de administración de Operations Manager para recopilar y enviar datos al servicio Operational Insights para su análisis Sin embargo, si utiliza agentes que se conectan directamente al servicio web, entonces no necesita Operations Manager. Tenga en cuenta los siguientes problemas cuando use Operational Insights con Operations Manager.

## Funciones y requisitos de software de Operational Insights

Operational Insights consta de un servicio web en la nube y agentes que se conectan directamente al servicio web o agentes y grupos de Operations Manager que administrar equipos en su entorno.

Antes de seleccionar agentes de Operations Manager (para recopilar datos) y grupos de administración (para administrar agentes y enviar datos al servicio Operational Insights), asegúrese de entender lo siguiente:

- El agente de Operations Manager se instala en cualquier servidor del que desee recopilar y analizar datos.

- El grupo de administración de Operations Manager transfiere los datos desde los agentes hasta el servicio web de Operational Insights. No analiza ninguno de los datos.

- El grupo de administración de Operations Manager debe tener acceso a Internet para cargar datos en el servicio web.

- Para obtener los mejores resultados, no instale el servidor de administración de Operations Manager en un equipo que sea también un controlador de dominio.

- Un agente de Operations Manager debe tener conectividad de red al grupo de administración de Operations Manager para poder transferir datos.

Operational Insights puede usar el Servicio de mantenimiento de System Center para recopilar y analizar datos. Operations Manager depende del Servicio de mantenimiento de System Center. Cuando vea los programas que están instalados en su servidor, verá el software de agente de System Center Operations Manager, en concreto la característica Agregar o quitar programas. No lo elimine porque Operational Insights depende de él. Si elimina el software de agente de Operations Manager, Operational Insights dejará de funcionar.

## Coexistencia con Operations Manager

Cuando se utiliza Operations Manager, Operational Insights solo se admite con el agente de Operations Manager en System Center Operations Manager 2012 R2 o System Center Operations Manager 2012 SP1. No es compatible con versiones anteriores de System Center Operations Manager. Como el agente de Operations Manager se utiliza para recopilar datos, emplea credenciales específicas (cuentas de acción o cuentas de ejecución) para admitir algunas de las cargas de trabajo analizadas, como SharePoint 2012.

## Operational Insights y SQL Server 2012

Al usar Operations Manager, se ejecuta el Servicio de mantenimiento de System Center con la cuenta de sistema local. En versiones de SQL Server anteriores a SQL Server 2008 R2, la cuenta de sistema local se habilitaba de forma predeterminada y era miembro del rol de servidor de administrador del sistema. En SQL Server 2012, el inicio de sesión de sistema local no forma parte del rol de servidor de administrador del sistema. Como consecuencia, cuando utiliza Operational Insights, no se puede supervisar la instancia de SQL Server 2012 por completo, y no todas las reglas pueden generar alertas.

## Conectividad de red interna y de Internet

Cuando se usan agentes que se conectan directamente al servicio web, los agentes deben acceder a Internet para enviar datos al servicio web y para recibir información de configuración actualizada del servicio web.

Cuando se utiliza Operations Manager, el servidor de administración debe acceder a Internet para enviar datos al servicio web y para recibir información de configuración actualizada del servicio web. Sin embargo, sus agentes no necesitan acceder a Internet. Si tiene agentes de Operations Manager en servidores que no están conectados a Internet, puede usar el servicio web si ellos se pueden comunicar con un servidor de administración conectado a Internet.

## Compatibilidad con la agrupación en clústeres

El agente de Operations Manager se admite en equipos que ejecutan Windows Server 2012, Windows Server 2008 R2 o Windows Server 2008 cuando están configurados para formar parte de un clúster de conmutación por error de Windows. Puede ver los cústeres en el portal de Operational Insights. En la página Servidores, los clústeres se identifican como TYPE=CLUSTER (por oposición a TYPE=AGENT, que es como se identifican los equipos físicos).

Las reglas de detección y configuración se ejecutan en los nodos activos y pasivos del clúster, pero cualquier alerta generada en los nodos pasivos se omite. Si un nodo cambia de pasivo a activo, se muestran alertas para ese nodo automáticamente, sin que sea necesario que el usuario intervenga.

Algunas alertas se podrían generar dos veces, según la regla que genere la alerta. Por ejemplo, una regla que detecta un controlador incorrecto mediante el examen del sistema operativo genera alertas para el servidor físico y para el clúster.

El análisis de la configuración de los nodos pasivos no se admite.

Operational Insights no admite la agrupación o la vinculación de equipos que ejecutan Windows Server que sean parte del mismo clúster de conmutación por error.

## Escalado del entorno de Operational Insights

Cuando planee su implementación de Operational Insights (en especial, cuando analice el número de agentes de Operations Manager a los que desea transferir datos a través de un único grupo de administración), tenga en cuenta la capacidad de ese servidor en términos de espacio de archivo.

Tenga en cuenta las siguientes variables:

- Número de agentes por grupo de administración

- El tamaño medio de los datos transferidos desde el agente al grupo de administración por día. De forma predeterminada, cada agente carga archivos CAB en el grupo de administración dos veces al día. El tamaño de los archivos CAB depende de la configuración del servidor (como el número de motores de SQL Server y el número de bases de datos) y del estado del servidor (como el número de alertas generadas). En la mayoría de los casos, el tamaño diario de carga suele ser inferior a 100 KB.

- Periodo de archivo para mantener datos en el grupo de administración (el valor predeterminado son 5 días)

Por ejemplo, si considera un tamaño diario de carga de 100 KB por agente y el período de archivo predeterminado, necesitaría el siguiente almacenamiento para el grupo de administración:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Número de agentes</b></td>
		<td><b>Espacio estimado necesario para el grupo de administración</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2,5 MB (5 agentes x 100 KB datos/día x 5 días = 2.500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 agentes x 100 KB datos/día x 5 días = 25.000 KB)</td>
    </tr>

    </tbody>
    </table>

## Ubicaciones geográficas

Si desea analizar datos de servidores situados en diversas ubicaciones geográficas, podría tener un grupo de administración por ubicación. Esto puede mejorar el rendimiento de la transferencia de datos entre el agente y el grupo de administración.


<!--HONumber=52-->