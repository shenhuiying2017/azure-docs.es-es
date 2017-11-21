---
title: "Solución de problemas y supervisión de SAP HANA en Azure (Instancias grandes) | Microsoft Docs"
description: "Solución de problemas y supervisión de SAP HANA en Azure (Instancias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583f3d1949614dbba4d2f91d72e4ac6b4d03d1c
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>Procedimiento para solucionar problemas y supervisar SAP HANA en Azure (instancias grandes)


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>Supervisión de SAP HANA en Azure (Instancias grandes)

SAP HANA en Azure (Instancias grandes) no se diferencia de cualquier otra implementación de IaaS: necesita supervisar lo que están haciendo el sistema operativo y la aplicación y cómo consumen los siguientes recursos:

- CPU
- Memoria
- Ancho de banda de red
- Espacio en disco

Al igual que con Azure Virtual Machines, debe averiguar si las clases de recursos indicadas anteriormente serán suficientes, o si se agotarán. A continuación se proporcionan más detalles sobre cada una de las clases:

**Consumo de recursos de CPU:** se fuerza la relación definida por SAP para una determinada carga de trabajo con HANA con el fin de asegurarse de que haya suficientes recursos de CPU disponibles para trabajar con los datos almacenados en la memoria. No obstante, puede haber casos en los que HANA consuma una gran cantidad de CPU al ejecutar consultas debido a que falten índices o problemas similares. Esto significa que debe supervisar el consumo de los recursos de CPU por parte de la unidad de Instancias grandes de HANA, así como los recursos de CPU utilizados por servicios específicos de HANA.

**Consumo de memoria:** es importante supervisarlo desde dentro y desde fuera de HANA (en la unidad). Dentro de HANA, supervise cómo los datos utilizan la memoria asignada a HANA con el fin de que se mantengan dentro de las directrices de tamaño obligatorias de SAP. También puede supervisar el consumo de memoria en el nivel de Instancias grandes para asegurarse de que el software instalado no perteneciente a HANA no utiliza demasiada memoria y compite por la memoria con HANA.

**Ancho de banda de red:** la puerta de enlace de red virtual de Azure tiene una limitación en el ancho de banda de los datos que se transfieren a la red virtual de Azure, por lo que es útil supervisar los datos recibidos por todas las máquinas virtuales de Azure dentro de una red virtual para averiguar si está cerca de los límites de la SKU de puerta de enlace de Azure que seleccionó. En la unidad de Instancias grandes de HANA, tiene sentido supervisar también el tráfico de red de entrada y de salida, así como realizar un seguimiento de los volúmenes que se administran a lo largo del tiempo.

**Espacio en disco:** normalmente su uso aumenta a lo largo del tiempo. Hay muchas razones para ello, pero las principales son: aumenta el volumen de datos, la ejecución de copias de seguridad de los registros de transacciones, el almacenamiento de los archivos de seguimiento y la realización de instantáneas de almacenamiento. Por lo tanto, es importante supervisarlo y administrar el espacio en disco asociado a la unidad de Instancias grandes de HANA.

En las **SKU de tipo II** de las instancias grandes de HANA, el servidor viene ya con las herramientas de diagnóstico del sistema cargadas. Puede usar estas herramientas de diagnóstico para realizar la comprobación de mantenimiento del sistema. Ejecute el siguiente comando para generar el archivo de registro de comprobación de mantenimiento en /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Cuando trabaje con el equipo de Soporte técnico de Microsoft para solucionar un problema, también se le pedirá que proporcione los archivos de registro mediante el uso de esta herramienta de diagnóstico. Puede comprimir el archivo con el comando siguiente.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```


## <a name="monitoring-and-troubleshooting-from-hana-side"></a>Supervisión y solución de problemas en el lado HANA

Con el fin de analizar de forma eficaz los problemas relacionados con SAP HANA en Azure (Instancias grandes), es conveniente averiguar la causa raíz de un problema. SAP ha publicado una gran cantidad de documentación para ayudarle.

Las preguntas más frecuentes relacionadas con el rendimiento de SAP HANA pueden encontrarse en las siguientes notas de SAP:

- [SAP Note #2222200 – FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200) (Nota de SAP 2222200: preguntas más frecuentes sobre la red de SAP HANA)
- [SAP Note #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040) (Nota de SAP 2100040: preguntas más frecuentes sobre la CPU de SAP HANA)
- [SAP Note #199997 – FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064) (Nota de SAP 199997: preguntas más frecuentes sobre la memoria de SAP HANA)
- [SAP Note #200000 – FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000) (Nota de SAP 199997: preguntas más frecuentes sobre la optimización del rendimiento de SAP HANA)
- [SAP Note #199930 – FAQ: SAP HANA I/O Analysis](https://launchpad.support.sap.com/#/notes/1999930) (Nota de SAP 199930: preguntas más frecuentes sobre el análisis de E/S de SAP HANA)
- [SAP Note #2177064 – FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064) (Nota de SAP #2177064: preguntas más frecuentes sobre el reinicio y la caída del servicio)

**Alertas de SAP HANA**

Como primer paso, compruebe los registros de alerta de SAP HANA actuales. En SAP HANA Studio, vaya a **Consola de administración: Alertas: Mostrar: Todas las alertas**. En esta pestaña se muestran todas las alertas de SAP HANA para determinados valores (memoria física libre, uso de CPU, etc.) que se encuentran fuera de los umbrales mínimo y máximo establecidos. De forma predeterminada, las comprobaciones se actualizan automáticamente cada 15 minutos.

![En SAP HANA Studio, vaya a Consola de administración: Alertas: Mostrar: Todas las alertas.](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

En el caso de una alerta activada debido a una configuración incorrecta del umbral, una solución es restablecerlo al valor predeterminado o a un valor más razonable.

![Restablecer el umbral al valor predeterminado o a un valor más razonable](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Las siguientes alertas pueden indicar problemas de recursos de CPU:

- Uso de CPU del host (alerta 5)
- Operación del punto de retorno más reciente (alerta 28)
- Duración del punto de retorno (alerta 54)

Puede observar el uso de CPU elevado en la base de datos de SAP HANA de las siguientes formas:

- La alerta 5 (Uso de CPU del host) se desencadena para el uso de CPU actual o antiguo
- El uso de CPU mostrado en la pantalla de información general

![Uso de CPU mostrado en la pantalla de información general](./media/troubleshooting-monitoring/image3-cpu-usage.png)

El gráfico Carga podría mostrar un uso de CPU elevado o un uso de elevado en el pasado:

![El gráfico Carga podría mostrar un uso de CPU elevado o un uso de elevado en el pasado](./media/troubleshooting-monitoring/image4-load-graph.png)

Una alerta desencadenada debido al uso elevado de CPU puede tener varias causas; entre ellas, la ejecución de ciertas transacciones, la carga de datos, trabajos que no responden, instrucciones SQL de larga ejecución y un rendimiento de consulta incorrecto (por ejemplo, con BW en cubos HANA).

Consulte el sitio sobre la [solución de problemas de SAP HANA: causas relacionadas con la CPU y soluciones](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) para ver los pasos detallados para la solución de problemas.

**Sistema operativo**

Una de las comprobaciones más importantes para SAP HANA en Linux es asegurarse de que las páginas gigantes transparentes están deshabilitadas; consulte la [SAP Note #2131662 – Transparent Huge Pages (THP) on SAP HANA Servers](https://launchpad.support.sap.com/#/notes/2131662) (Nota de SAP #2131662: páginas gigantes transparentes (THP) en servidores de SAP HANA).

- Puede comprobar si las páginas gigantes transparentes están habilitadas con el siguiente comando de Linux: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Si _always_ está encerrado entre corchetes como a continuación, significa que las páginas gigantes transparentes están habilitadas: [always] madvise never; si _never_ está encerrado entre corchetes como a continuación, significa que las páginas gigantes transparentes están deshabilitadas: always madvise [never]

El siguiente comando de Linux no debe devolver nada: **rpm -qa | grep ulimit.** Si indica que _ulimit_ está instalado, desinstálelo inmediatamente.

**Memoria**

Puede observar que la cantidad de memoria asignada por la base de datos de SAP HANA es mayor de lo esperado. Las siguientes alertas indican problemas con un uso de memoria elevado:

- Uso de memoria física del host (alerta 1)
- Uso de memoria del servidor de nombres (alerta 12)
- Uso total de la memoria de las tablas de almacenamiento de columnas (alerta 40)
- Uso de memoria de los servicios (alerta 43)
- Uso de memoria del almacenamiento principal de las tablas de almacenamiento de columnas (alerta 45)
- Archivos de volcado de tiempo de ejecución (alerta 46)

Consulte el sitio sobre la [solución de problemas de SAP HANA: problemas de memoria](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) para ver los pasos detallados para la solución de problemas.

**Red**

Consulte [SAP Note #2081065 – Troubleshooting SAP HANA Network](https://launchpad.support.sap.com/#/notes/2081065) (Nota de SAP #2081065: solución de problemas de la red de SAP HANA) y realice los pasos para solucionar los problemas de red indicados en ella.

1. Analice el tiempo de ida y vuelta entre el cliente y el servidor.
  A. Ejecute el script SQL [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analice la comunicación entre nodos.
  A. Ejecute el script SQL [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Ejecute el comando de Linux **ifconfig** (la salida muestra si se producen las pérdidas de paquetes).
4. Ejecute el comando de Linux **tcpdump**.

Además, utilice la herramienta de código abierto [IPERF](https://iperf.fr/) (u otra similar) para medir el rendimiento de red real de la aplicación.

Consulte el sitio sobre la [solución de problemas de SAP HANA: problemas de conectividad y de rendimiento de red](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) para ver los pasos detallados para la solución de problemas.

**Storage**

Desde la perspectiva del usuario final, una aplicación (o el sistema en su conjunto) se ejecuta lentamente, deja de responder o incluso puede parecer que se cuelga si hay problemas de rendimiento de E/S. En la pestaña **Volúmenes** de SAP HANA Studio, puede ver los volúmenes conectados y los utilizados por cada servicio.

![En la pestaña Volúmenes de SAP HANA Studio, puede ver los volúmenes conectados y los utilizados por cada servicio](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Para los volúmenes conectados, en la parte inferior de la pantalla puede ver los detalles de los volúmenes, como los archivos y las estadísticas de E/S.

![Para los volúmenes conectados, en la parte inferior de la pantalla puede ver los detalles de los volúmenes, como los archivos y las estadísticas de E/S](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Consulte el sitio sobre la [solución de problemas de SAP HANA: causas raíz relacionadas con E/S y soluciones](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) y la [solución de problemas de SAP HANA: causas raíz relacionadas con el disco](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) para ver los pasos detallados para la solución de problemas.

**Herramientas de diagnóstico**

Realice una comprobación del estado de SAP HANA mediante HANA\_Configuration\_Minichecks. Esta herramienta devuelve problemas técnicos potencialmente críticos que ya deberían haber aparecido como alertas en SAP HANA Studio.

Consulte [SAP Note #1969700 – SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) (Nota de SAP #1969700: recopilación de instrucciones SQL para SAP HANA) y descargue el archivo SQL Statements.zip adjunto a esa nota. Almacene este archivo .zip en el disco duro local.

En SAP HANA Studio, en la pestaña **System Information** (Información del sistema), haga clic en la columna **Name** (Nombre) y seleccione **Import SQL Statements** (Instrucciones SQL de importación).

![En SAP HANA Studio, en la pestaña System Information (Información del sistema), haga clic en la columna Name (Nombre) y seleccione Import SQL Statements (Instrucciones SQL de importación)](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Seleccione el archivo SQL Statements.zip que está almacenado localmente y se importará una carpeta con las instrucciones SQL correspondientes. En este punto, se pueden ejecutar las numerosas comprobaciones de diagnóstico con estas instrucciones SQL.

Por ejemplo, para probar los requisitos de ancho de banda de la replicación del sistema de SAP HANA, haga clic en la instrucción **Bandwidth** (Bancho de banda) en **Replication: Bandwidth** (Replicación: ancho de banda) y seleccione **Open** (Abrir) en la consola de SQL.

La instrucción SQL completa se abre, lo que permite cambiar y, después, ejecutar los parámetros de entrada (sección de modificación).

![La instrucción SQL completa se abre, lo que permite cambiar y, después, ejecutar los parámetros de entrada (sección de modificación)](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Otro ejemplo es hacer clic en las instrucciones en **Replication: Overview** (Replicación: Información general). Seleccione **Execute** (Ejecutar) en el menú contextual:

![Otro ejemplo es hacer clic en las instrucciones en Replication: Overview (Replicación: Información general). Seleccione Execute (Ejecutar) en el menú contextual](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Esto genera información que ayuda a solucionar el problema:

![Esto generará información que ayudará a solucionar el problema](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Haga lo mismo para HANA\_Configuration\_Minichecks y compruebe si hay alguna marca _X_ en la columna _C_ (Crítico).

Ejemplo de salidas:

**HANA\_Configuration\_MiniChecks\_Rev102.01+1** para comprobaciones general de SAP HANA.

![HANA\_Configuration\_MiniChecks\_Rev102.01+1 para comprobaciones general de SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_Services\_Overview** para información general sobre los servicios de SAP HANA que se están ejecutando.

![HANA\_Services\_Overview para información general sobre los servicios de SAP HANA que se están ejecutando](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_Services\_Statistics**para información sobre los servicio de SAP HANA (CPU, memoria, etc.).

![HANA\_Services\_Statistics para información sobre los servicio de SAP HANA ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_Overview\_Rev110+** para información general sobre la instancia de SAP HANA.

![HANA\_Configuration\_Overview\_Rev110+ para información general sobre la instancia de SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_Parameters\_Rev70+** para comprobar los parámetros de SAP HANA.

![HANA\_Configuration\_Parameters\_Rev70+ para comprobar los parámetros de SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

