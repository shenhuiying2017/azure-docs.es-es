---
title: "Solución de supervisión de VMware en Log Analytics | Microsoft Docs"
description: "Obtenga información sobre cómo la solución de supervisión de VMware puede ayudar a administrar registros y supervisar los hosts ESXi."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: f54d24659ad13aa02462938711482326c5bf763c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Solución de supervisión de VMware (versión preliminar)de Log Analytics

![Símbolo de VMware](./media/log-analytics-vmware/vmware-symbol.png)

La solución de supervisión de VMware de Log Analytics es una solución que ayuda a crear un enfoque de supervisión y registro centralizado para registros de gran tamaño de VMware. En este artículo se escribe cómo solucionar problemas, capturar y administrar hosts ESXi en una sola ubicación usando la solución. Gracias a ella, puede ver datos detallados de todos los hosts ESXi en una sola ubicación. Puede ver los recuentos de eventos principales, el estado y las tendencias de los hosts de máquina virtual y ESXi proporcionados a través de los registros de host ESXi. Puede solucionar errores viendo y buscando registros de host ESXi centralizados, además de crear alertas basadas en consultas de búsqueda de registros.

La solución utiliza la funcionalidad nativa syslog del host ESXi para insertar datos en una máquina virtual de destino, que tiene el agente de OMS. Sin embargo, la solución no escribe archivos en Syslog dentro de la máquina virtual de destino. El agente de OMS abre el puerto 1514 y realiza en este el proceso de escucha. Una vez que recibe los datos, el agente de OMS los inserta en Log Analytics.

## <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

* Agregue la solución VMware Monitoring a la suscripción mediante el proceso que se describe en [Incorporación de una solución de administración](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Hosts ESXi de VMware compatibles
Hosts ESXi de vSphere 5.5 y 6.0

#### <a name="prepare-a-linux-server"></a>Preparación de servidores Linux
Cree una máquina virtual con el sistema operativo de Linux para recibir todos los datos de Syslog de los hosts ESXi. El [agente de Linux de OMS](log-analytics-linux-agents.md) es el punto de recopilación de todos los datos de Syslog de los hosts ESXi. Puede usar varios hosts ESXi para reenviar los registros a un único servidor Linux, como en el ejemplo siguiente.  

   ![Flujo de Syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configuración de recopilaciones de Syslog
1. Configure el reenvío de Syslog en vSphere. Para obtener información detallada sobre cómo configurar el reenvío de Syslog, consulte el artículo sobre cómo [configurar Syslog en ESXi 5.x y 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vaya a **ESXi Host Configuration (Configuración de hosts ESXi)** > **Software** > **Advanced Settings (Configuración avanzada)** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. En el campo *Syslog.global.logHost*, agregue el servidor Linux y el número de puerto *1514*. Por ejemplo, `tcp://hostname:1514` o `tcp://123.456.789.101:1514`.
3. Abra el firewall del host ESXi de Syslog. **ESXi Host Configuration (Configuración de hosts ESXi)** > **Software** > **Security Profile (Perfil de seguridad)** > **Firewall** y abra **Properties (Propiedades)**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Compruebe la consola de vSphere para comprobar que Syslog se ha configurado correctamente. Confirme que el puerto **1514** está configurado en el host ESXi.
5. Descargue e instale el agente de OMS para Linux en el servidor Linux. Consulte la [documentación del agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) si quiere obtener más información.
6. Después de instalar el agente de OMS para Linux, vaya al directorio /etc/opt/microsoft/omsagent/sysconf/omsagent.d y copie el archivo de vmware_esxi.conf en el directorio /etc/opt/microsoft/omsagent/conf/omsagent.d y el cambie el propietario o grupo y los permisos del archivo. Por ejemplo: 

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Reinicie el agente de OMS para Linux ejecutando los comandos `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. Probar la conectividad entre el servidor Linux y el host ESXi mediante el comando `nc` en el ESXi Host. Por ejemplo: 

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. En Azure Portal, realice una búsqueda de registro de `VMware_CL`. Cuando Log Analytics recopila los datos de Syslog, conserva el formato de registro del sistema. En el portal, se capturan algunos campos específicos, como *Hostname* y *ProcessName*.  

    ![Tipo](./media/log-analytics-vmware/type.png)  

    Si los resultados de búsqueda de registros de visualización son similares a la imagen anterior, significa que puede usar el panel de la solución VMware Monitoring.  

## <a name="vmware-data-collection-details"></a>Detalles de la colección de datos de VMware
La solución de supervisión de VMware recopila varios datos de registro y métricas de rendimiento de hosts ESXi con los agentes de OMS para Linux que tiene habilitados.

La siguiente tabla muestra los métodos de recolección de datos y otros detalles acerca de cómo se recopilan los datos.

| plataforma | Agente de OMS para Linux | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |Cada 3 minutos |

En la tabla siguiente se muestran ejemplos de campos de datos recopilados por la solución de supervisión de VMware:

| Nombre del campo | Descripción |
| --- | --- |
| Device_s |Dispositivos de almacenamiento de VMware |
| ESXIFailure_s |Tipos de error |
| EventTime_t |Hora en la que se produjo el evento |
| HostName_s |Nombre de host ESXi |
| Operation_s |Creación o eliminación de máquinas virtuales |
| ProcessName_s |Nombre del evento |
| ResourceId_s |Nombre del host de VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Estado de SCSI de VMware |
| SyslogMessage_s |Datos de syslog |
| UserName_s |Usuario que creó o eliminó la máquina virtual |
| VMName_s |Nombre de la máquina virtual |
| Equipo |Equipo host |
| TimeGenerated |Hora en la que se generaron los datos |
| DataCenter_s |Centro de datos de VMware |
| StorageLatency_s |Latencia de almacenamiento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Información general de la solución de supervisión de VMware
El icono VMware aparece en el área de trabajo de Log Analytics. Proporciona una visión general de los errores. Al hacer clic en el icono, accederá a una vista de panel.

![Icono](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Acceso a la vista de panel
En la vista de panel de **VMware**, los módulos se organizan por:

* Número de estados de error
* Hosts principales por números de eventos
* Números de eventos principales
* Actividades de máquina virtual
* Eventos de disco de host ESXi

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Haga clic en cualquier hoja para abrir el panel de búsqueda de Log Analytics que muestra información detallada específica de la hoja.

Desde aquí, puede editar la consulta de búsqueda para modificar elementos concretos. Para detalles sobre cómo crear búsquedas de registros, consulte [Descripción de las búsquedas de registros en Log Analytics](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Búsqueda de eventos de hosts ESXi
Un único host ESXi genera varios registros en función de sus procesos. La solución de supervisión de VMware centraliza y resume los números de eventos. Esta vista centralizada ayudar a comprender qué host ESXi tiene un gran volumen de eventos y qué eventos ocurren con más frecuencia en su entorno.

![event](./media/log-analytics-vmware/events.png)

Puede obtener más detalles haciendo clic en un host ESXi o un tipo de evento.

Al hacer clic en un nombre de host ESXi, verá la información de ese host ESXi. Si quiere restringir los resultados con el tipo de evento, agregue `“ProcessName_s=EVENT TYPE”` en la consulta de búsqueda. Puede seleccionar **ProcessName** en el filtro de búsqueda. Esta acción limita la información.

![Detalles](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Búsqueda de actividades de máquina virtual que usan numerosos recursos
Una máquina virtual se puede crear y eliminar en cualquier host ESXi. Es útil para que un administrador identifique el número de máquinas virtuales que crea un host ESXi. Esto, a su vez, ayuda a entender la planeación de capacidad y rendimiento. Es fundamental realizar un seguimiento de los eventos de actividad de máquina virtual al administrar el entorno.

![Detalles](./media/log-analytics-vmware/vmactivities1.png)

Si quiere ver los datos de creación de máquinas virtuales de host ESXi adicionales, haga clic en un nombre de host ESXi.

![Detalles](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Consultas de búsqueda comunes
La solución incluye otras consultas útiles que pueden ayudarlo a administrar los hosts ESXi, como espacio elevado de almacenamiento, alta latencia de almacenamiento y errores de ruta.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Consultas](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Guardado de consultas
Guardar consultas de búsqueda es una característica estándar de Log Analytics que puede ayudarlo a sacar utilidad de las consultas encontradas. Después de crear una consulta que encuentre útil, guárdela haciendo clic en **Favoritos**. Una consulta guardada permite reutilizarse fácilmente más adelante desde la página [Mi panel](log-analytics-dashboards.md)m donde puede crear sus propios paneles personalizados.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Creación de alertas a partir de las consultas
Después de crear las consultas, recomendamos utilizarlas para saber cuando se producen eventos específicos. Consulte el artículo de [alertas de Log Analytics](log-analytics-alerts.md) para obtener información sobre cómo crear alertas. Para ejemplos de consultas de alertas y otros ejemplos de consultas, consulte la entrada de blog [Monitor VMware using Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) (Supervisión de VMware mediante Log Analytics).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>¿Qué se necesita en la configuración del host ESXi? ¿Qué impacto tendrá en mi entorno actual?
La solución utiliza el mecanismo de reenvío nativo de syslog del host ESXi. No necesita otro software de Microsoft en el host ESXi para capturar los registros. El impacto en su entorno debería ser mínimo. Sin embargo, debe configurar el reenvío de syslog, que es la funcionalidad ESXi.

### <a name="do-i-need-to-restart-my-esxi-host"></a>¿Hay que reiniciar el host ESXi?
Nº Este proceso no requiere reiniciar. A veces, vSphere no actualiza correctamente syslog. En tal caso, inicie sesión en el host ESXi y vuelva a cargar syslog. De nuevo, no necesita reiniciar el host, así que este proceso no afecta al rendimiento de su entorno.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>¿Puedo aumentar o disminuir el volumen de datos de registro enviados a Log Analytics?
Sí, puede. Puede usar la configuración de nivel de registro del host ESXi en vSphere. La recopilación de registros se basa en el nivel *info*. Por lo tanto, si desea auditar la eliminación o creación de máquinas virtuales, debe mantener el nivel *info* en Hostd. Para obtener más información, consulte la [base de conocimientos de VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>¿Por qué Hostd no proporciona datos a Log Analytics? La configuración de registro está establecida en info.
Se produjo un error de host ESXi en la marca de tiempo de syslog. Para obtener más información, consulte la [base de conocimientos de VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Después de aplicar la solución, Hostd debe funcionar con normalidad.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>¿Puedo tener varios hosts ESXi reenviando de datos de syslog a una sola máquina virtual con omsagent?
Sí. Puede varios hosts ESXi reenviando de datos a una sola máquina virtual con omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>¿Por qué no veo los datos que se transmiten a Log Analytics?
Puede haber varios motivos:

* El host ESXi no está enviando correctamente los datos en la máquina virtual que ejecuta omsagent. Para realizar una prueba, realice los pasos siguientes:

  1. Para confirmar la acción, inicie sesión en el host ESXi mediante ssh y ejecute el siguiente comando: `nc -z ipaddressofVM 1514`

      Si no se realiza correctamente, es probable que los ajustes de vSphere de la configuración avanzada no sean correctos. Consulte el artículo sobre cómo [configurar la recopilación de syslog](#configure-syslog-collection) para obtener información relacionada con el procedimiento de configuración del host ESXi para reenviar datos de syslog.
  2. Si la conectividad del puerto de syslog es correcta, pero todavía no ve ningún dato, vuelva a cargar la funcionalidad syslog en el host ESXi mediante ssh para ejecutar el comando siguiente: ` esxcli system syslog reload`
* La máquina virtual con el agente de OMS no está configurada correctamente. Para realizar una prueba, realice los siguientes pasos:

  1. Log Analytics escucha en el puerto 1514. Para comprobar que está abierto, ejecute el siguiente comando: `netstat -a | grep 1514`
  2. Debería ver el puerto `1514/tcp` abierto. Si no lo hace, compruebe que omsagent está instalado correctamente. Si no ve la información del puerto, significa que no está abierto que no está abierto en la máquina virtual.

    a. Compruebe que el agente de OMS se está ejecutando utilizando `ps -ef | grep oms`. Si no se está ejecutando, inicie el proceso utilizando el comando ` sudo /opt/microsoft/omsagent/bin/service_control start`.

    b. Abra el archivo `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` .

    c. Compruebe que la configuración de grupos y usuarios pertinente es válida; debe ser similar a `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Si el archivo no existe o la configuración de grupos y usuarios no es correcta, adopte medidas correctivas [preparando un servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>pasos siguientes
* Use [Búsquedas de registros](log-analytics-log-searches.md) en Log Analytics para ver datos detallados sobre los datos de hosts de VMware.
* [Cree sus propios paneles](log-analytics-dashboards.md) que muestren datos de hosts de VMware.
* [Cree alertas](log-analytics-alerts.md) cuando se produzcan eventos específicos de hosts de VMware.
