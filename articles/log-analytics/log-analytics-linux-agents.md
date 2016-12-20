---
title: "Conexión de equipos Linux a Log Analytics | Microsoft Docs"
description: El uso de Log Analytics permite recopilar y actuar sobre los datos generados en equipos Linux.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 218ffec4601c5b0b4ee9872b5bbd03489cb3ddcf


---
# <a name="connect-linux-computers-to-log-analytics"></a>Conexión de equipos Linux a Log Analytics
El uso de Log Analytics permite recopilar y actuar sobre los datos generados en equipos Linux. Agregar datos recopilados de Linux a OMS permite administrar sistemas Linux y soluciones de contenedor como Docker, independientemente de dónde se encuentren los equipos, (prácticamente en cualquier lugar). Por lo tanto, esos orígenes de datos pueden residir en su centro de datos local como servidores físicos, en equipos virtuales en un servicio hospedado en la nube como Amazon Web Services (AWS) o Microsoft Azure, o incluso en el equipo portátil en su escritorio. Además, OMS también recopila datos de los equipos de Windows de forma similar, por lo que es compatible con un entorno informático realmente híbrido.

Puede ver y administrar los datos de todos esos orígenes con Log Analytics en OMS con un portal de administración único. Esto reduce la necesidad de supervisar usando varios sistemas distintos, simplifica su uso y le permite exportar cualquier dato que desee a cualquier solución de análisis de negocio o sistema que ya tenga.

Este artículo es una guía de inicio rápido que le ayudará a recopilar y administrar los datos de los equipos de Linux mediante el agente de OMS para Linux. Para más detalles técnicos, como la configuración del servidor proxy, información acerca de las métricas de CollectD y orígenes de datos JSON personalizados, encontrará esa información en [OMS Agent for Linux overview](https://github.com/Microsoft/OMS-Agent-for-Linux) (Información general sobre el agente de OMS para Linux) y la [documentación completa sobre el agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) en GitHub.

Actualmente puede recopilar los siguientes tipos de datos de equipos Linux:

* Métricas de rendimiento
* Eventos Syslog
* Alertas de Nagios y Zabbix
* Registros, inventario y métricas de rendimiento del contenedor de Docker

## <a name="supported-linux-versions"></a>Versiones de Linux compatibles
Tanto la versión x86 como la x64 son compatibles oficialmente en una variedad de distribuciones de Linux. Aunque también se puede ejecutar el agente de OMS para Linux en otras distribuciones que no se enumeran.

* Amazon Linux 2012.09 hasta 2015.09
* CentOS Linux 5, 6 y 7
* Oracle Linux 5, 6 y 7
* Red Hat Enterprise Linux Server 5, 6 y 7
* Debian GNU/Linux 6, 7 y 8
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
* SUSE Linux Enterprise Server 11 y 12

## <a name="oms-agent-for-linux"></a>Agente de OMS para Linux
El agente de Operations Management Suite para Linux consta de varios paquetes. El archivo de versión contiene los siguientes paquetes disponibles mediante la ejecución de la agrupación de shell con `--extract`.

| **Paquete** | **Versión** | **Descripción** |
| --- | --- | --- |
| omsagent |1.1.0 |El agente de Operations Management Suite para Linux |
| omsconfig |1.1.1 |Agente de configuración para el agente de OMS |
| omi |1.0.8.3 |Infraestructura de administración abierta (OMI), un servidor de CIM ligero |
| scx |1.6.2 |Proveedores de CIM OMI para métricas de rendimiento del sistema operativo |
| apache-cimprov |1.0.0 |Proveedor de supervisión de rendimiento de servidor HTTP de Apache para OMI. Solo se instala si se detecta un servidor HTTP de Apache. |
| mysql-cimprov |1.0.0 |Proveedor de supervisión de rendimiento de servidor MySQL Server para OMI. Solo se instala si se detecta un servidor MySQL/MariaDB de Apache. |
| docker-cimprov |0.1.0 |Proveedor de Docker para OMI. Solo se instala si se detecta Docker. |

### <a name="additional-installation-artifacts"></a>Artefactos de instalación adicionales
Después de instalar al agente de OMS para los paquetes de Linux, se aplican los siguientes cambios de configuración adicionales a todo el sistema. Estos artefactos se quitan cuando se desinstala el paquete omsagent.

* Se crea un usuario sin privilegios llamado: `omsagent` . Esta es la cuenta que el demonio omsagent usa para ejecutarse
* Se crea un archivo "include" de sudoers en /etc/sudoers.d/omsagent Esto autoriza a omsagent a reiniciar los demonios Syslog y omsagent. Si no se admiten directivas "incluir" de sudo en la versión instalada de sudo, estas entradas se escribirá en /etc/sudoers.
* Se modifica la configuración de Syslog para reenviar un subconjunto de eventos al agente. Para más información consulte la sección sobre **configuración de recopilación de datos** a continuación

### <a name="linux-data-collection-details"></a>Detalles de la recopilación de datos de Linux
La siguiente tabla muestra los métodos de recolección de datos y otros detalles acerca de cómo se recopilan los datos.

| de origen | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 minuto |
| Nagios |![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |a la llegada |
| syslog |![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |De Almacenamiento de Azure: 10 minutos; del agente: a la llegada |
| Contadores de rendimiento de Linux |![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |Mínimo de 10 segundos, según lo programado |
| Seguimiento de cambios |![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |![No](./media/log-analytics-linux-agents/oms-bullet-red.png) |Cada hora |

### <a name="package-requirements"></a>Requisitos de paquete
| **Paquetes necesarios** | **Descripción** | **Versión mínima** |
| --- | --- | --- |
| Glibc |Biblioteca GNU C |2.5-12 |
| Openssl |Bibliotecas OpenSSL |0.9.8E o 1.0 |
| Curl |Cliente web de cURL |7.15.5 |
| Python ctypes |Bibliotecas de funciones |N/D |
| PAM |Módulos de autenticación conectables |N/D |

> [!NOTE]
> Rsyslog o Syslog son necesarios para recopilar mensajes de Syslog. El demonio predeterminado de Syslog en la versión 5 de Red Hat Enterprise Linux, CentOS y Oracle Linux (Sysklog) no se admite para la recopilación de eventos de Syslog. Para recopilar datos de Syslog de esta versión de estas distribuciones, es necesario instalar configurar el demonio de Rsyslog para reemplazar Sysklog.
> 
> 

## <a name="quick-install"></a>Instalación rápida
Ejecute los siguientes comandos para descargar omsagent, validar la suma de comprobación e instalar e incorporar el agente. Los comandos son para 64 bits. El identificador de área de trabajo y la clave principal se encuentran en el portal de OMS en **Configuración** en la pestaña **Orígenes conectados**.

![detalles de área de trabajo](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Hay una variedad de otros métodos para instalar al agente y actualizarlo. Puede leer más sobre ellas en [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)(Pasos para instalar el agente de OMS para Linux).

También puede ver el [tutorial en vídeo sobre Azure](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Selección del método de recopilación de datos de Linux
La forma de elegir los tipos de datos que desea recopilar depende de si desea usar el portal OMS o si quier editar varios archivos de configuración directamente en los clientes de Linux. Si elige usar el portal, la configuración se envía automáticamente a todos sus clientes de Linux. Si necesita diferentes configuraciones para distintos clientes de Linux, tendrá que editar los archivos de cliente individualmente, o usar una alternativa como PowerShell DSC, Chef o Puppet.

Puede especificar los eventos de Syslog y los contadores de rendimiento que quiere recopilar mediante los archivos de configuración en los equipos de Linux. *Si decide configurar la recopilación de datos editando los archivos de configuración del agente, debe deshabilitar la configuración centralizada.*   A continuación se proporcionan instrucciones para configurar la recopilación de datos en los archivos de configuración del agente, así como para deshabilitar la configuración central para todos los agentes de OMS para Linux o equipos individuales.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Deshabilitación de la administración de OMS para un equipo individual de Linux
La recopilación de datos centralizada para datos de configuración se deshabilita para un equipo individual de Linux con el script OMS_MetaConfigHelper.py. Esto puede ser útil si un subconjunto de equipos debe tener una configuración especializada.

Para deshabilitar la configuración centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Para volver a habilitar la configuración centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Contadores de rendimiento de Linux
Los contadores de rendimiento de Linux son similares a los contadores de rendimiento de Windows, ambos funcionan de forma parecida. Puede utilizar los siguientes procedimientos para agregarlos y configurarlos. Una vez que se agregan a OMS, sus datos se recopilan cada 30 segundos.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Para agregar un contador de rendimiento de Linux en OMS
1. Para configurar agentes de OMS para Linux mediante el portal de OMS, puede agregar contadores de rendimiento de Linux en la página Configuración, haga clic en **Datos**.  
2. En la página **Configuración** en **Datos**, haga clic en **Contadores de rendimiento de Linux** y seleccione o escriba el nombre del contador que desea agregar.  
    ![Datos](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Si no sabe el nombre completo del contador, puede empezar a escribir un nombre parcial y aparecerá una lista de contadores disponibles. Cuando encuentre el contador que desea agregar, haga clic en el nombre de la lista y luego haga clic en el icono del signo más para agregarlo.
4. Después de agregar el contador, este aparece en la lista de contadores resaltado con una barra de color.
5. De forma predeterminada, la opción **Apply below configuration to my machines** (Aplicar la siguiente configuración a mis equipos) está seleccionada. Si desea deshabilitar el envío de datos de configuración, anule la selección.
6. Cuando haya terminado de modificar los contadores de rendimiento, en la parte inferior de la página, haga clic en **Guardar** para finalizar los cambios. Los cambios de configuración realizados se envían a todos los agentes de OMS para Linux que están registrados en OMS, normalmente en un máximo de 5 minutos.

### <a name="configure-linux-performance-counters-in-oms"></a>Configuración de los contadores de rendimiento de Linux en OMS
Para los contadores de rendimiento de Windows, puede elegir una instancia específica para cada contador de rendimiento. Sin embargo, para los contadores de rendimiento de Linux, cualquier instancia de un contador que elija se aplica a todos los contadores secundarios del contador primario. La siguiente tabla muestra las instancias comunes disponibles para los contadores de rendimiento de Windows y de Linux.

| **Nombre de la instancia** | **Significado** |
| --- | --- |
| \_Total |Total de todas las instancias |
| \* |Todas las instancias |
| (/&#124;/var) |Coincide con las instancias con nombre: / o /var |

De forma similar, el intervalo de muestra que elija para un contador primario se aplica a todos sus contadores secundarios. En otras palabras, todos los intervalos de muestra y las instancias del contador secundario están vinculadas entre sí.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Incorporación y configuración de las métricas de rendimiento con Linux
Las métricas de rendimiento para recopilar se controlan mediante la configuración en /etc/opt/microsoft/omsagent/conf/omsagent.conf. Consulte [Available performance metrics](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) (Métricas de rendimiento disponibles) para ver las clases disponibles y las métricas para el agente de OMS para Linux.

Cada objeto, o categoría, de métricas de rendimiento para recopilar debe definirse en el archivo de configuración como un solo elemento `<source>` . La sintaxis sigue el modelo siguiente.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Los parámetros configurables de este elemento son:

* **Object\_name**: el nombre de objeto de la colección.
* **Instance\_regex**: una *expresión regular* que define las instancias que desea recopilar. El valor: `.*` especifica todas las instancias. Para recopilar métricas de procesador solamente de la instancia \_Total, puede especificar `_Total`. Para recopilar métricas de procesamiento solamente de las instancias rond o sshd, puede especificar `(crond|sshd)`.
* **Counter\_name\_regex**: una *expresión regular* que define los contadores (para el objeto) que desea recopilar. Para recopilar todos los contadores para el objeto, especifique: `.*`. Para recopilar solo contadores de espacio de intercambio para el objeto de memoria, puede especificar: `.+Swap.+`
* **Intervalo:**: la frecuencia con la que se recopilan los contadores del objeto.

La configuración predeterminada para las métricas de rendimiento es:

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Habilitación de los contadores de rendimiento de MySQL mediante comandos de Linux
Si se detecta en el equipo un servidor de MySQL o de MariaDB cuando se instala la agrupación de omsagent, se instalará automáticamente un proveedor de supervisión de rendimiento para el servidor MySQL. Este proveedor se conecta al servidor MySQL o MariaDB local para exponer las estadísticas de rendimiento. Tiene que configurar las credenciales de usuario de MySQL para que el proveedor pueda tener acceso al servidor de MySQL.

Para definir una cuenta de usuario predeterminada para el servidor MySQL en localhost, utilice el siguiente ejemplo de comando.

> [!NOTE]
> El archivo de credenciales tiene que ser legible para la cuenta omsagent. Se recomienda ejecutar el comando mycimprovauth como omsgent.
> 
> 

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Como alternativa, puede especificar las credenciales necesarias de MySQL en un archivo creando el archivo: /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Para más información acerca de cómo administrar las credenciales de MySQL para la supervisión a través del archivo mysql-auth, consulte [Administración de las credenciales de supervisión de MySQL en el archivo de autenticación](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Consulte [Permisos necesarios de la base de datos para los contadores de rendimiento de MySQL](#database-permissions-required-for-mysql-performance-counters) para más detalles acerca de los permisos de objeto requeridos por el usuario de MySQL para recopilar datos de rendimiento de MySQL Server.

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Habilitación de los contadores de rendimiento del servidor HTTP de Apache mediante comandos de Linux
Si se detecta en el equipo un servidor HTTP de Apache cuando se instala la agrupación de omsagent, se instalará automáticamente un proveedor de supervisión de rendimiento para el servidor HTTP de Apache. Este proveedor se basa en un "módulo" de Apache que se tiene que cargar en el servidor HTTP de Apache para tener acceso a los datos de rendimiento.

Puede cargar el módulo con el siguiente comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para cargar el módulo de supervisión de Apache, ejecute el siguiente comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Visualización de los datos de rendimiento con Log Analytics
1. En el portal de Operations Management Suite, haga clic en el icono de búsqueda de registros.
2. En la barra de búsqueda, escriba `* (Type=Perf)` para ver todos los contadores de rendimiento.

Como OMS también recopila datos del contador de rendimiento de Windows, debe restringir el alcance de los resultados de la búsqueda a los datos específicos de Linux. Así en el ejemplo siguiente se muestran los datos de rendimiento específicos de un servidor Linux de ejemplo denominado Chorizo21.

```
Type=Perf Computer=chorizo*
```

![servidor de ejemplo que se muestra en los resultados de búsqueda](./media/log-analytics-linux-agents/oms-perfsearch01.png)

En los resultados, haga clic en **Métricas** para ver los contadores para los que se recopilaron los datos. Se muestran los datos en tiempo real como gráficos para cada contador.

![Métricas](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
Syslog es un protocolo de registro de eventos similar a los registros de eventos de Windows, ambos funcionan de forma parecida al mostrarlos en OMS.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Incorporación de una nueva utilidad Syslog de Linux en OMS
1. En la página **Configuración** en **Datos**, haga clic en **Syslog** y escriba a la izquierda del icono del signo más el nombre de la utilidad Syslog que desea agregar.
    ![Syslog de Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. Si no sabe el nombre completo de la utilidad, puede empezar a escribir un nombre parcial y aparecerá una lista de utilidades Syslog disponibles. Cuando encuentre la utilidad que desea agregar, haga clic en el nombre de la lista y luego haga clic en el icono del signo más para agregarla.
3. Después de agregar la utilidad, esta aparece en la lista de utilidades de Syslog resaltada con una barra de color. A continuación, seleccione los niveles de gravedad (categorías de información de la utilidad de Syslog) que se van a recopilar.
4. Para finalizar los cambios, haga clic en **Guardar** en la parte inferior de la página. Los cambios de configuración realizados se envían a todos los agentes de OMS para Linux que están registrados en OMS, normalmente en un máximo de 5 minutos.

### <a name="configure-linux-syslog-facilities-in-linux"></a>Configuración de las utilidades de Syslog de Linux en Linux
Los eventos Syslog se envían desde el demonio Syslog, por ejemplo Rsyslog o Syslog-ng, a un puerto local en el que el agente está escuchando. De forma predeterminada al puerto 25224. Cuando se instala el agente, se aplica una configuración de Syslog predeterminada. Esto se encuentra en:

Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf

La configuración predeterminada de Syslog del agente de OMS carga eventos Syslog desde todas las utilidades con una gravedad de advertencia o superior.

> [!NOTE]
> Si modifica la configuración de Syslog, tiene que reiniciar el demonio Syslog para que los cambios surtan efecto.
> 
> 

La configuración predeterminada de Syslog para el agente de OMS para Linux para OMS es:

#### <a name="rsyslog"></a>Rsyslog
```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Syslog ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Visualización de todos los eventos de Syslog con Log Analytics
1. En el portal de Operations Management Suite, haga clic en el icono de **búsqueda de registros** .
2. En la agrupación **Log Management** (administración de registros), elija una búsqueda predefinida de Syslog y selecciónela para ejecutarla.

Este ejemplo muestra todos los eventos de Syslog.

![Eventos de Syslog que se muestran en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Ahora puede profundizar en los resultados de la búsqueda.

## <a name="linux-alerts"></a>Alertas de Linux
Si utiliza Nagios o Zabbix para administrar los equipos Linux, OMS puede recibir las alertas generadas desde esas herramientas. Sin embargo, en la actualidad no hay ningún método para configurar los datos entrantes de la alerta usando el portal de OMS. En su lugar, tendrá que modificar un archivo de configuración para empezar a enviar alertas a OMS.

### <a name="collect-alerts-from-nagios"></a>Recopilación de alertas de Nagios
Para recopilar las alertas de un servidor Nagios, tiene que realizar los siguientes cambios de configuración.

1. Conceda al usuario **omsagent** acceso de lectura al archivo de registro Nagios (es decir, /var/log/nagios/nagios.log/var/log/nagios/nagios.log). Suponiendo que el archivo nagios.log es propiedad del grupo **nagios**, puede agregar al usuario **omsagent** al grupo **nagios**.
   
    ```
    sudo usermod –a -G nagios omsagent
    ```
2. Modifique el archivo omsagent.conf (/etc/opt/microsoft/omsagent/conf/omsagent.conf). Asegúrese de que las entradas siguientes están presentes y no comentadas:
   
    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>
   
    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```
3. Reinicie el demonio de omsagent:
   
    ```
    sudo service omsagent restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Recopilación de alertas de Zabbix
Para recopilar las alertas de un servidor Zabbix, tendrá que llevar cabo pasos similares a los que se han expuesto para Nagios, excepto que aquí tendrá que especificar un usuario y una contraseña *sin cifrar*. No es una solución ideal, pero seguramente cambiará pronto. Para solucionar este problema, recomendamos que cree el usuario y le conceda permiso solamente para supervisar.

Una sección de ejemplo del archivo de configuración omsagent.conf (/etc/opt/microsoft/omsagent/conf/omsagent.conf) para Zabbix debería parecerse a esto:

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Visualización de alertas en búsqueda de Log Analytics
Después de configurar los equipos Linux para enviar alertas a OMS, puede utilizar algunas simples consultas de búsqueda de registro para ver las alertas. El siguiente ejemplo de consulta de búsqueda devuelve todas las alertas grabadas que se generaron. Por ejemplo, si se produce algún tipo de problema en su infraestructura de TI, los resultados de la consulta del ejemplo siguiente podrían indicar en dónde se ha originado el problema. Y puede fácilmente profundizar las alertas por sistema de origen para ayudarle a acotar su investigación. La ventaja es que no tiene necesariamente que ir a varios sistemas de administración desde el principio, siempre que las alertas se envíen a OMS, puede empezar por ahí.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Visualización de todas las alertas de Nagios con Log Analytics
1. En el portal de Operations Management Suite, haga clic en el icono de **búsqueda de registros** .
2. En la barra de consulta, escriba la siguiente consulta de búsqueda:
   
    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![Alertas de Nagios que se muestran en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Después de ver los resultados de la búsqueda, puede profundizar en los detalles adicionales como el elemento *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Visualización de todas las alertas de Zabbix con Log Analytics
1. En el portal de Operations Management Suite, haga clic en el icono de **búsqueda de registros** .
2. En la barra de consulta, escriba la siguiente consulta de búsqueda:
   
    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![Alertas de Zabbix que se muestran en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Después de ver los resultados de la búsqueda, puede profundizar en los detalles adicionales como el elemento *AlertName*.

## <a name="compatibility-with-system-center-operations-manager"></a>Compatibilidad con System Center Operations Manager
El agente de OMS para Linux comparte archivos binarios del agente con el agente de System Center Operations Manager. Instalar al agente de OMS para Linux en un sistema que está administrado por Operations Manager actualiza los paquetes OMI y SCX en el equipo a una versión más reciente. El agente de OMS para Linux y System Center 2012 R2 son compatibles. Sin embargo, **System Center 2012 SP1 y las versiones anteriores no son compatibles ni están admitidas en la actualidad por el agente de OMS para Linux.**

> [!NOTE]
> Si el agente de OMS para Linux se instala en un equipo que no está administrado actualmente por Operations Manager y posteriormente desea administrar el equipo con Operations Manager, tiene que modificar la configuración de OMI antes de detectar el equipo. **Este paso no es necesario si está instalado el agente Operations Manager antes que el agente de OMS para Linux.**
> 
> 

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Habilitación del agente de OMS para Linux para comunicarse con Operations Manager
1. Editar el archivo /etc/opt/omi/conf/omiserver.conf
2. Asegúrese de que la línea que comienza con **httpsport =** define el puerto 1270. Por ejemplo `httpsport=1270`
3. Reinicie el servidor OMI:
   
    ```
    service omiserver restart or systemctl restart omiserver
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>Permisos necesarios de la base de datos para los contadores de rendimiento de MySQL
Para conceder permisos a un usuario de supervisión de MySQL, el usuario que concede el permiso tiene que tener el privilegio 'GRANT option', además del privilegio que se va a conceder.

Para que el usuario de MySQL devuelva los datos de rendimiento el usuario tendrá acceso a las siguientes consultas:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Además de estas consultas el usuario de MySQL necesita el acceso SELECT a las siguientes tablas predeterminadas:

* information_schema
* mysql

Estos privilegios se pueden conceder ejecutando los siguientes comandos de concesión.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Administración de las credenciales de supervisión de MySQL en el archivo de autenticación
Las secciones siguientes le ayudan a administrar las credenciales de MySQL.

### <a name="configure-the-mysql-omi-provider"></a>Configuración del proveedor de MySQL para OMI
El proveedor de MySQL para OMI necesita un usuario de MySQL preconfigurado y bibliotecas de cliente de MySQL instaladas para consultar la información de rendimiento y mantenimiento de la instancia de MySQL.

### <a name="mysql-omi-authentication-file"></a>Archivo de autenticación de MySQL para OMI
El proveedor de MySQL OMI utiliza un archivo de autenticación para determinar la dirección de enlace y el puerto que usa la instancia de MySQL para escuchar y las credenciales que tiene que usar para recopilar métricas. Durante la instalación el proveedor de MySQL para OMI examinará los archivos de configuración de my.cnf (ubicaciones predeterminadas) para encontrar la dirección de enlace y el puerto, y establecer parcialmente el archivo de autenticación de MySQL para OMI.

Para completar la supervisión de una instancia del servidor de MySQL, agregue un archivo de autenticación pregenerado de MySQL para OMI en el directorio correcto.

### <a name="authentication-file-format"></a>Formato del archivo de autenticación
El archivo de autenticación de MySQL para OMI es un archivo de texto que contiene información sobre:

* Port
* Dirección de enlace
* Nombre de usuario de MySQL
* Contraseña codificada en Base64

El archivo de autenticación de MySQL para OMI solo concede privilegios de lectura y escritura al usuario de Linux que lo generó.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Un archivo de autenticación de MySQL para OMI predeterminado contiene una instancia y un número de puerto predeterminados, dependiendo de qué información esté disponible y analizada en el archivo de configuración de MySQL que se encontró.

La instancia predeterminada es un medio para facilitar la administración de varias instancias de MySQL en un host de Linux y viene indicado por la instancia con el puerto 0. Todas las instancias agregadas heredarán las propiedades establecidas en la instancia predeterminada. Por ejemplo, si se agrega la instancia de MySQL que escucha en el puerto '3308', se utilizará la dirección de enlace, el nombre de usuario y la contraseña codificada en Base64 de la instancia predeterminada para probar y supervisar la instancia que escucha en 3308. Si la instancia en 3308 está enlazada a otra dirección y usa el mismo par de nombre de usuario y contraseña que MySQL, solo será necesario volver a especificar la dirección de enlace y las demás propiedades se heredarán.

Los ejemplos del archivo de autenticación son similares al siguiente.

Instancia predeterminada e instancia con el puerto 3308:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Instancia predeterminada e instancia con el puerto 3308 + diferente contraseña codificada en Base 64:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **Propiedad** | **Descripción** |
| --- | --- |
| Port |Puerto representa el puerto actual en el que está escuchando la instancia de MySQL.  El puerto 0 implica que las propiedades que siguen se usan para la instancia predeterminada. |
| Dirección de enlace |La dirección de enlace es la actual dirección de enlace de MySQL |
| nombre de usuario |Este es el nombre de usuario del usuario de MySQL que desee usar para supervisar la instancia del servidor MySQL. |
| Contraseña codificada en Base64 |Esta es la contraseña del usuario de supervisión de MySQL codificada en Base64. |
| Actualización automática |Cuando el proveedor de MySQL para OMI se actualiza, el proveedor volverá a examinar para detectar cambios en el archivo my.cnf y sobrescribir el archivo de autenticación de MySQL para OMI. Establezca esta marca en verdadero o falso dependiendo de las actualizaciones necesarias para el archivo de autenticación de MySQL para OMI. |

#### <a name="authentication-file-location"></a>Ubicación del archivo de autenticación
El archivo de autenticación de MySQL para OMI se debe colocar en la siguiente ubicación con el nombre "mysql-auth":

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

El archivo (y directorio auth/omsagent) deben pertenecer al usuario omsagent.

## <a name="agent-logs"></a>Registros del agente
Los registros para el agente de OMS para Linux se encuentran en:

/var/opt/microsoft/omsagent/log/

Los registros para el agente de OMS para Linux para el programa omsconfig (configuración del agente) se encuentran en:

/var/opt/microsoft/omsconfig/log/

Los registros para los componentes OMI y SCX (que proporcionan los datos de métricas de rendimiento) se encuentran en:

/var/opt/omi/log/ y /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Solución de problemas del agente de OMS para Linux
Use la siguiente información para diagnosticar y solucionar problemas habituales.

Si la información sobre solución de problemas de esta sección no le resulta de ayuda, también puede usar los siguientes recursos para resolver el problema.

* Los clientes con soporte técnico Premier pueden registrar un caso de soporte técnico mediante [Premier](https://premier.microsoft.com/).
* Los clientes con contratos de soporte técnico de Azure pueden registrar casos de soporte técnico en [Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Registre un [problema de GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Obtenga ideas y cree un informe de errores en el foro de comentarios [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback).

### <a name="important-log-locations"></a>Ubicación de registros importantes
| Archivo | Ruta de acceso |
| --- | --- |
| Archivo de registro del agente de OMS para Linux |`/var/opt/microsoft/omsagent/log/omsagent.log ` |
| Archivo de registro de la configuración del agente de OMS |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>Archivos de configuración importantes
| Categoría | Ubicación del archivo |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf`, `/etc/rsyslog.conf` o `/etc/rsyslog.d/95-omsagent.conf` |
| Rendimiento, Nagios, Zabbix, salida de OMS y agente general |`/etc/opt/microsoft/omsagent/conf/omsagent.conf` |
| Configuraciones adicionales |`/etc/opt/microsoft/omsagent/conf.d/*.conf` |

> [!NOTE]
> Los archivos de configuración de los contadores de rendimiento y Syslog se sobrescriben si se habilita la configuración del portal de OMS. Puede deshabilitar la configuración en el portal de OMS (para todos los nodos) o para nodos únicos ejecutando lo siguiente:
> 
> 

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Habilitación del registro de depuración
Para habilitar el registro de depuración, puede usar el complemento de salida de OMS y la salida detallada.

#### <a name="oms-output-plugin"></a>Complemento de salida de OMS
FluentD permite que el complemento especifique niveles de registro para los diferentes niveles de registro de entradas y salidas. Para especificar un nivel de registro distinto para la salida de OMS, edite la configuración del agente general en el archivo `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

Hacia el final del archivo de configuración, cambie la propiedad `log_level` de `info` a `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

El registro de depuración permite ver cargas por lotes al servicio OMS separadas por tipo, número de elementos de datos y tiempo de envío.

*Ejemplo de registro con depuración habilitada:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Salida detallada
En lugar de usar el complemento de salida de OMS, también puede generar la salida de elementos de datos directamente a `stdout`, que es visible en el archivo de registro del agente de OMS para Linux.

En el archivo de configuración del agente general de OMS en `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, convierta en comentario el complemento de salida de OMS agregando `#` al principio de cada línea.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Bajo el complemento de salida, quite el símbolo `#` al principio de cada línea para quitar el comentario en la siguiente sección.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>Los mensajes de Syslog reenviados no aparecen en el registro
#### <a name="probable-causes"></a>Causas probables
* La configuración aplicada al servidor Linux no permite la recopilación de las utilidades enviadas ni de los niveles de registro.
* Syslog no se reenvía correctamente al servidor Linux.
* El número de mensajes que se reenvía por segundo es demasiado grande para la configuración básica del agente de OMS para Linux.

#### <a name="resolutions"></a>Soluciones
* Compruebe que la configuración de Syslog en el portal de OMS tenga todas las utilidades y los niveles de registro correctos.
  * **Portal de OMS > Configuración > Datos > Syslog**
* Compruebe que los demonios de mensajería de Syslog nativos (`rsyslog`, `syslog-ng`) puedan recibir los mensajes reenviados.
* Compruebe la configuración de firewall en el servidor de Syslog para asegurarse de que no se estén bloqueando los mensajes.
* Simule un mensaje de Syslog enviado a OMS utilizando el comando `logger`, por ejemplo:
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Problemas al conectarse a OMS cuando se usa un proxy
#### <a name="probable-causes"></a>Causas probables
* El proxy especificado al instalar y configurar al agente es incorrecto.
* Los puntos de conexión del servicio OMS no están incluidos en la lista de permitidos en su centro de datos.

#### <a name="resolutions"></a>Soluciones
* Vuelva a instalar el agente de OMS para Linux mediante el comando siguiente con la opción `-v` habilitada. Esto permite la salida detallada del agente que se conecta a través del proxy al servicio OMS.
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * Revise la documentación para el proxy de OMS en [Configuring the agent for use with an HTTP proxy server](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server) (Configuración del agente para usarlo con un servidor proxy HTTP).
* Compruebe que los siguientes puntos de conexión del servicio OMS estén incluidos en la lista de permitidos.

| Recurso del agente | Puertos |
| --- | --- |
| &#42;.ods.opinsights.azure.com |Puerto 443 |
| &#42;.oms.opinsights.azure.com |Puerto 443 |
| ods.systemcenteradvisor.com |Puerto 443 |
| &#42;.blob.core.windows.net/ |Puerto 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>Se muestra un error 403 durante la incorporación
#### <a name="probable-causes"></a>Causas probables
* La fecha y la hora son incorrectas en el servidor Linux.
* El identificador y la clave de área de trabajo usados son incorrectos.

#### <a name="resolution"></a>Resolución
* Compruebe la hora en el servidor Linux con el comando `date`. Si los datos son de 15 minutos anteriores o posteriores a la hora actual, la incorporación genera un error. Para corregir este problema, actualice la fecha o la zona horaria del servidor Linux.
* La versión más reciente del agente de OMS para Linux notifica si una diferencia horaria está causando el error de incorporación.
* Repita la incorporación con el identificador y la clave de área de trabajo correctos. Para más información, consulte [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Incorporación mediante la línea de comandos).

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>Aparece un error 500 o 404 en el archivo de registro después de la incorporación
Se trata de un problema conocido que se produce durante la primera carga de datos de Linux en un área de trabajo de OMS. Esto no afecta a los datos que se envían ni causa otros problemas. Puede pasar por alto los errores durante la incorporación inicial.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Los datos de Nagios no aparecen en el portal de OMS
#### <a name="probable-causes"></a>Causas probables
* El usuario omsagent no tiene permisos para leer el archivo de registro de Nagios.
* Las secciones de origen y filtro de Nagios todavía incluyen comentarios en el archivo omsagent.conf.

#### <a name="resolutions"></a>Soluciones
* Agregue al usuario omsagent para leer el archivo de Nagios. Consulte [Nagios Alerts](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) (Alertas de Nagios) para más información.
* En el archivo de configuración del agente de OMS general para Linux en `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, asegúrese de que se hayan quitado los comentarios de **ambas** secciones, la de origen y la de filtro de Nagios, como en el ejemplo siguiente.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Los datos de Linux no aparecen en el portal de OMS
#### <a name="probable-causes"></a>Causas probables
* Se produjo un error en la incorporación al servicio OMS.
* La conexión al servicio OMS se bloquea.
* Se está haciendo la copia de seguridad de los datos del agente de OMS para Linux.

#### <a name="resolutions"></a>Soluciones
* Compruebe que la incorporación al servicio OMS se hiciera correctamente; para ello, compruebe que `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` existe.
* Repita la incorporación con la línea de comandos de omsadmin.sh. Para más información, consulte [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Incorporación mediante la línea de comandos).
* Si usa un proxy, siga los pasos anteriores para solucionar problemas con un proxy.
* En algunos casos, cuando el agente de OMS para Linux no puede comunicarse con el servicio OMS, la copia de seguridad de los datos del agente ocupa el tamaño de búfer total de 50 MB. Reinicie el agente de OMS para Linux ejecutando el comando `service omsagent restart` o `systemctl restart omsagent`.
  >[AZURE.NOTE] Este problema está corregido en el agente versión 1.1.0-28 y posteriores.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>La configuración de contador de rendimiento de Linux para Syslog no se aplica en el portal de OMS
#### <a name="probable-causes"></a>Causas probables
* El agente de configuración en el agente de OMS para Linux no ha recuperado la configuración más reciente del portal de OMS.
* No se aplicó la configuración modificada en el portal.

#### <a name="resolutions"></a>Soluciones
`omsconfig` es el agente de configuración en el agente de OMS para Linux que recupera los cambios de configuración del portal de OMS cada 5 minutos. A continuación, esta configuración se aplica a los archivos de configuración del agente de OMS para Linux que se encuentran en `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

* En algunos casos, es posible que el agente de configuración del agente de OMS para Linux no pueda comunicarse con el servicio de configuración del portal y, por tanto, no se aplique la configuración más reciente.
* Compruebe que el agente `omsconfig` esté instalado con lo siguiente:
  
  * `dpkg --list omsconfig` o `rpm -qi omsconfig`
  * Si no está instalado, vuelva a instalar la versión más reciente del agente de OMS para Linux.
* Compruebe que el agente `omsconfig` pueda comunicarse con el servicio OMS.
  
  * Ejecute el comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.
    * El comando anterior devuelve la configuración que el agente recupera del portal, incluida la configuración de Syslog, los contadores de rendimiento de Linux y los registros personalizados.
    * Si se produce un error con el comando anterior, ejecute el comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Este comando fuerza al agente de omsconfig a comunicarse con el servicio OMS para recuperar la configuración más reciente.

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>Los datos de registros personalizados de Linux no aparecen en el portal de OMS
#### <a name="probable-causes"></a>Causas probables
* Se produjo un error en la incorporación al servicio OMS.
* No se ha seleccionado la configuración **Aplicar la configuración que aparece a continuación a mis máquinas con Linux**.
* omsconfig no ha recuperado el registro personalizado más reciente del portal
* El usuario `omsagent` no puede acceder al registro personalizado por un problema de permisos o no se encontró `omsagent`. En este caso, verá la siguiente salida:
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* Se trata de un problema conocido con la condición de carrera que se corrigió en el agente de OMS para Linux versión 1.1.0-217.

#### <a name="resolutions"></a>Soluciones
* Compruebe que la incorporación se haya llevado a cabo correctamente; para hacerlo, determine si el archivo `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` existe.
  * Si es necesario, repita la incorporación con la línea de comandos de omsadmin.sh. Para más información, consulte [Onboarding using the command line](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) (Incorporación mediante la línea de comandos).
* En el portal de OMS, en **Configuración** en la pestaña **Datos**, asegúrese de que la configuración **Aplicar la configuración que aparece a continuación a mis máquinas con Linux** esté seleccionada.  
  ![Aplicar configuración](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* Compruebe que el agente `omsconfig` pueda comunicarse con el servicio OMS.
  
  * Ejecute el comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.
  * El comando anterior devuelve la configuración que el agente recupera del portal, incluida la configuración de Syslog, los contadores de rendimiento de Linux y los registros personalizados.
  * Si se produce un error con el comando anterior, ejecute el comando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Este comando fuerza al agente de omsconfig a comunicarse con el servicio OMS y a recuperar la configuración más reciente.

El agente de OMS para Linux, en lugar de ejecutarse como un usuario `root` con privilegios, se ejecuta como el usuario `omsagent`. En la mayoría de los casos, se deben conceder permisos explícitos al usuario para que lea determinados archivos.

Para conceder permiso al usuario `omsagent`, ejecute los siguientes comandos:

1. Agregue al usuario `omsagent` a un grupo específico con `sudo usermod -a -G <GROUPNAME> <USERNAME>`.
2. Concédale acceso universal de lectura al archivo necesario con `sudo chmod -R ugo+rw <FILE DIRECTORY>`.

Existe un problema conocido con la condición de carrera que se corrigió en el agente de OMS para Linux versión 1.1.0-217. Después de actualizar al agente más reciente, ejecute el siguiente comando para obtener la versión más reciente del complemento de salida:

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/conf/omsagent.conf
```

## <a name="known-limitations"></a>Limitaciones conocidas
Revise las siguientes secciones para informarse sobre las limitaciones actuales del agente de OMS para Linux.

### <a name="azure-diagnostics"></a>Diagnóstico de Azure
Para las máquinas virtuales de Linux que se ejecutan en Azure es posible que se necesiten pasos adicionales para permitir la recopilación de datos por parte de Diagnóstico de Azure y Operations Management Suite. **Versión 2.2** de la extensión de diagnósticos de Linux.

Para más información sobre la instalación y la configuración de la extensión de diagnóstico de Linux, consulte [Uso del comando de la CLI de Azure para habilitar la extensión de diagnóstico de Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension).

**Actualización de la extensión de diagnósticos de Linux de 2.0 a 2.2 ASM de la CLI de Azure:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

Estos ejemplos de comandos hacen referencia a un archivo denominado PrivateConfig.json. El formato de ese archivo debe ser similar al del ejemplo siguiente.

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>Sysklog no se admite.
Rsyslog o Syslog son necesarios para recopilar mensajes de Syslog. El demonio predeterminado de Syslog en la versión 5 de Red Hat Enterprise Linux, CentOS y Oracle Linux (Sysklog) no se admite para la recopilación de eventos de Syslog. Para recopilar datos de Syslog de esta versión de estas distribuciones, es necesario instalar configurar el demonio de Rsyslog para reemplazar Sysklog. Para más información acerca de cómo sustituir Sysklog por Rsyslog, consulte [Install the newly built rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)(instalar el recién creado rsyslog RPM).

## <a name="next-steps"></a>Pasos siguientes
* [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Incorporación de soluciones de Log Analytics desde la galería de soluciones) para agregar funcionalidad y recopilar datos.
* Familiarícese con las [búsquedas de registros](log-analytics-log-searches.md) para ver información detallada recopilada por soluciones.
* Use los [paneles](log-analytics-dashboards.md) para guardar y mostrar sus propias búsquedas personalizadas.




<!--HONumber=Nov16_HO3-->


