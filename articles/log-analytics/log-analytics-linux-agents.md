<properties
	pageTitle="Conexión de equipos Linux a Log Analytics | Microsoft Azure"
	description="El uso de Log Analytics permite recopilar y actuar sobre los datos generados en equipos Linux."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="banders"/>

# Conexión de equipos Linux a Log Analytics

El uso de Log Analytics permite recopilar y actuar sobre los datos generados en equipos Linux. Agregar datos recopilados de Linux a OMS permite administrar sistemas Linux y soluciones de contenedor como Docker, independientemente de dónde se encuentren los equipos, (prácticamente en cualquier lugar). Por lo tanto, esos orígenes de datos pueden residir en su centro de datos local como servidores físicos, en equipos virtuales en un servicio hospedado en la nube como Amazon Web Services (AWS) o Microsoft Azure, o incluso en el equipo portátil en su escritorio. Además, OMS también recopila datos de los equipos de Windows de forma similar, por lo que es compatible con un entorno informático realmente híbrido.

Puede ver y administrar los datos de todos esos orígenes con Log Analytics en OMS con un portal de administración único. Esto reduce la necesidad de supervisar usando varios sistemas distintos, simplifica su uso y le permite exportar cualquier dato que desee a cualquier solución de análisis de negocio o sistema que ya tenga.

Este artículo es una guía de inicio rápido que le ayudará a recopilar y administrar los datos de los equipos de Linux mediante el agente de OMS para Linux. Si desea información técnica más detallada puede encontrarla en [OMS Agent for Linux - Overview](https://github.com/Microsoft/OMS-Agent-for-Linux) (Agente de OMS para Linux. Información general) y [OMS Agent for Linux - Full documentation](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (Agente de OMS para Linux. Documentación completa) en Github.


Actualmente puede recopilar los siguientes tipos de datos de equipos Linux:

- Métricas de rendimiento
- Eventos Syslog
- Alertas de Nagios y Zabbix
- Registros, inventario y métricas de rendimiento del contenedor de Docker

## Versiones de Linux compatibles

Tanto la versión x86 como la x64 son compatibles oficialmente en una variedad de distribuciones de Linux. Aunque también se puede ejecutar el agente de OMS para Linux en otras distribuciones que no se enumeran.

- Amazon Linux 2012.09 hasta 2015.09
- CentOS Linux 5,6 y 7
- Oracle Linux 5,6 y 7
- Red Hat Enterprise Linux 5, 6 y 7
- Debian GNU/Linux 6, 7 y 8
- Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
- SUSE Linux Enterprise Server 11 y 12

## Agente de OMS para Linux
El agente de Operations Management Suite para Linux consta de varios paquetes. El archivo de versión contiene los siguientes paquetes disponibles mediante la ejecución de la agrupación de shell con `--extract`.

**Paquete** | **Versión** | **Descripción**
----------- | ----------- | --------------
omsagent | 1\.1.0 | El agente de Operations Management Suite para Linux
omsconfig | 1\.1.1 | Agente de configuración para el agente de OMS
omi | 1\.0.8.3 | Infraestructura de administración abierta (OMI), un servidor de CIM ligero
scx | 1\.6.2 | Proveedores de CIM OMI para métricas de rendimiento del sistema operativo
apache-cimprov | 1\.0.0 | Proveedor de supervisión de rendimiento de servidor HTTP de Apache para OMI. Solo se instala si se detecta un servidor HTTP de Apache.
mysql-cimprov | 1\.0.0 | Proveedor de supervisión de rendimiento de servidor MySQL Server para OMI. Solo se instala si se detecta un servidor MySQL/MariaDB de Apache.
docker-cimprov | 0\.1.0 | Proveedor de Docker para OMI. Solo se instala si se detecta Docker.

### Artefactos de instalación adicionales
Después de instalar al agente de OMS para los paquetes de Linux, se aplican los siguientes cambios de configuración adicionales a todo el sistema. Estos artefactos se quitan cuando se desinstala el paquete omsagent.
- Se crea un usuario sin privilegios llamado: `omsagent`. Esta es la cuenta que el demonio omsagent usa para ejecutarse
- Se crea un archivo "include" de sudoers en /etc/sudoers.d/omsagent Esto autoriza a omsagent a reiniciar los demonios Syslog y omsagent. Si no se admiten directivas "incluir" de sudo en la versión instalada de sudo, estas entradas se escribirá en /etc/sudoers.
- Se modifica la configuración de Syslog para reenviar un subconjunto de eventos al agente. Para más información consulte la sección sobre **configuración de recopilación de datos** a continuación

### Detalles de la recopilación de datos de Linux

La siguiente tabla muestra los métodos de recolección de datos y otros detalles acerca de cómo se recopilan los datos.

| de origen | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Es necesario SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|
|Zabbix|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|1 minuto|
|Nagios|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|a la llegada|
|syslog|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|De Almacenamiento de Azure: 10 minutos; del agente: a la llegada|
|Contadores de rendimiento de Linux|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|Mínimo de 10 segundos, según lo programado|
|Seguimiento de cambios|![Sí](./media/log-analytics-linux-agents/oms-bullet-green.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|![No](./media/log-analytics-linux-agents/oms-bullet-red.png)|Cada hora|



### Requisitos de paquete
| **Paquetes necesarios** | **Descripción** | **Versión mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |	Biblioteca GNU C | 2\.5-12|
|Openssl | Bibliotecas OpenSSL | 0\.9.8E o 1.0|
Curl | Cliente web de cURL | 7\.15.5
|Python ctypes |Bibliotecas de funciones | N/D|
|PAM | Módulos de autenticación conectables |N/D |

>[AZURE.NOTE] Rsyslog o Syslog son necesarios para recopilar mensajes de Syslog. El demonio predeterminado de Syslog en la versión 5 de Red Hat Enterprise Linux, CentOS y Oracle Linux (Sysklog) no se admite para la recopilación de eventos de Syslog. Para recopilar datos de Syslog de esta versión de estas distribuciones, es necesario instalar configurar el demonio de Rsyslog para reemplazar Sysklog.

## Instalación rápida

Ejecute los siguientes comandos para descargar omsagent, validar la suma de comprobación e instalar e incorporar el agente. Los comandos son para 64 bits. El identificador de área de trabajo y la clave principal se encuentran en el portal OMS en **Configuración** en la pestaña **Connected Sources** (orígenes conectados).

![detalles de área de trabajo](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Hay una variedad de otros métodos para instalar al agente y actualizarlo. Puede leer más sobre ellas en [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux) (Pasos para instalar el agente de OMS para Linux).


## Selección del método de recopilación de datos de Linux

La forma de elegir los tipos de datos que desea recopilar depende de si desea usar el portal OMS o si quier editar varios archivos de configuración directamente en los clientes de Linux. Si elige usar el portal, la configuración se envía automáticamente a todos sus clientes de Linux. Si necesita diferentes configuraciones para distintos clientes de Linux, tendrá que editar los archivos de cliente individualmente, o usar una alternativa como PowerShell DSC, Chef o Puppet.

Puede especificar los eventos de Syslog y los contadores de rendimiento que quiere recopilar mediante los archivos de configuración en los equipos de Linux. *Si decide configurar la recopilación de datos editando los archivos de configuración del agente, debe deshabilitar la configuración centralizada.* A continuación se proporcionan instrucciones para configurar la recopilación de datos en los archivos de configuración del agente, así como para deshabilitar la configuración central para todos los agentes de OMS para Linux o equipos individuales.

### Deshabilitación de la administración de OMS para un equipo individual de Linux

La recopilación de datos centralizada para datos de configuración se deshabilita para un equipo individual de Linux con el script OMS\_MetaConfigHelper.py. Esto puede ser útil si un subconjunto de equipos debe tener una configuración especializada.

Para deshabilitar la configuración centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Para volver a habilitar la configuración centralizada:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## Contadores de rendimiento de Linux

Los contadores de rendimiento de Linux son similares a los contadores de rendimiento de Windows, ambos funcionan de forma parecida. Puede utilizar los siguientes procedimientos para agregarlos y configurarlos. Una vez que se agregan a OMS, sus datos se recopilan cada 30 segundos.

### Para agregar un contador de rendimiento de Linux en OMS

1. Para configurar agentes de OMS para Linux mediante el portal de OMS, puede agregar contadores de rendimiento de Linux en la página Configuración, haga clic en **Datos**. ![data](./media/log-analytics-linux-agents/oms-settings-data01.png)
2. En la página **Configuración** en **Datos** , haga clic en **Linux performance counters** (Contadores de rendimiento de Linux) y escriba a la izquierda del icono del signo más el nombre del contador que desea agregar. ![Contadores de rendimiento de Linux](./media/log-analytics-linux-agents/oms-linuxperfcounter01.png)
3. Si no sabe el nombre completo del contador, puede empezar a escribir un nombre parcial y aparecerá una lista de contadores disponibles. Cuando encuentre el contador que desea agregar, haga clic en el nombre de la lista y luego haga clic en el icono del signo más para agregarlo.
4. Después de agregar el contador, este aparece en la lista de contadores resaltado con una barra de color.
5. De forma predeterminada, la opción **Apply below configuration to my machines** (Aplicar la siguiente configuración a mis equipos) está seleccionada. Si desea deshabilitar el envío de datos de configuración, anule la selección.
6. Cuando haya terminado de modificar los contadores de rendimiento, en la parte inferior de la página, haga clic en **Guardar** para finalizar los cambios. Los cambios de configuración realizados se envían a todos los agentes de OMS para Linux que están registrados en OMS, normalmente en un máximo de 5 minutos.

### Configuración de los contadores de rendimiento de Linux en OMS

Para los contadores de rendimiento de Windows, puede elegir una instancia específica para cada contador de rendimiento. Sin embargo, para los contadores de rendimiento de Linux, cualquier instancia de un contador que elija se aplica a todos los contadores secundarios del contador primario. La siguiente tabla muestra las instancias comunes disponibles para los contadores de rendimiento de Windows y de Linux.

| **Nombre de la instancia** | **Significado** |
| --- | --- |
| \_Total | Total de todas las instancias |
| * | Todas las instancias |
| (/&#124;/var) | Coincide con las instancias con nombre: / o /var |


De forma similar, el intervalo de muestra que elija para un contador primario se aplica a todos sus contadores secundarios. En otras palabras, todos los intervalos de muestra y las instancias del contador secundario están vinculadas entre sí.

### Incorporación y configuración de las métricas de rendimiento con Linux

Las métricas de rendimiento para recopilar se controlan mediante la configuración en /etc/opt/microsoft/omsagent/conf/omsagent.conf. Consulte [Available performance metrics](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) (Métricas de rendimiento disponibles) para ver las clases disponibles y las métricas para el agente de OMS para Linux.

Cada objeto, o categoría, de métricas de rendimiento para recopilar debe definirse en el archivo de configuración como un solo elemento `<source>`. La sintaxis sigue el modelo siguiente.

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

- **Object\_name**: el nombre de objeto de la colección.
- **Instance\_regex**: un *expresión regular* que define las instancias que desea recopilar. El valor: `.*` especifica todas las instancias. Para recopilar métricas de procesador solamente de la instancia \_Total, puede especificar `_Total`. Para recopilar métricas de procesamiento solamente de las instancias rond o sshd, puede especificar `(crond|sshd)`.
- **Counter\_name\_regex**: una *expresión regular* que define los contadores (para el objeto) que desea recopilar. Para recopilar todos los contadores para el objeto, especifique: `.*`. Para recopilar solo contadores de espacio de intercambio para el objeto de memoria, puede especificar: `.+Swap.+`
- **Intervalo:**: la frecuencia con la que se recopilan los contadores del objeto.

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

### Habilitación de los contadores de rendimiento de MySQL mediante comandos de Linux

Si se detecta en el equipo un servidor de MySQL o de MariaDB cuando se instala la agrupación de omsagent, se instalará automáticamente un proveedor de supervisión de rendimiento para el servidor MySQL. Este proveedor se conecta al servidor MySQL o MariaDB local para exponer las estadísticas de rendimiento. Tiene que configurar las credenciales de usuario de MySQL para que el proveedor pueda tener acceso al servidor de MySQL.

Para definir una cuenta de usuario predeterminada para el servidor MySQL en localhost, utilice el siguiente ejemplo de comando.

>[AZURE.NOTE] El archivo de credenciales tiene que ser legible para la cuenta omsagent. Se recomienda ejecutar el comando mycimprovauth como omsgent.


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Como alternativa, puede especificar las credenciales necesarias de MySQL en un archivo creando el archivo: /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Para más información acerca de cómo administrar las credenciales de MySQL para la supervisión a través del archivo mysql-auth, consulte [Administración de las credenciales de supervisión de MySQL en el archivo de autenticación](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Consulte [Permisos necesarios de la base de datos para los contadores de rendimiento de MySQL](#database-permissions-required-for-mysql-performance-counters) para más detalles acerca de los permisos de objeto requeridos por el usuario de MySQL para recopilar datos de rendimiento de MySQL Server.

### Habilitación de los contadores de rendimiento del servidor HTTP de Apache mediante comandos de Linux

Si se detecta en el equipo un servidor HTTP de Apache cuando se instala la agrupación de omsagent, se instalará automáticamente un proveedor de supervisión de rendimiento para el servidor HTTP de Apache. Este proveedor se basa en un "módulo" de Apache que se tiene que cargar en el servidor HTTP de Apache para tener acceso a los datos de rendimiento.

Puede cargar el módulo con el siguiente comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Para cargar el módulo de supervisión de Apache, ejecute el siguiente comando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### Visualización de los datos de rendimiento con Log Analytics

1. En el portal de Operations Management Suite, haga clic en el icono de búsqueda de registros.
2. En la barra de búsqueda, escriba `* (Type=Perf)` para ver todos los contadores de rendimiento.


Como OMS también recopila datos del contador de rendimiento de Windows, debe restringir el alcance de los resultados de la búsqueda a los datos específicos de Linux. Así en el ejemplo siguiente se muestran los datos de rendimiento específicos de un servidor Linux de ejemplo denominado Chorizo21.

```
Type=Perf Computer=chorizo*
```

![servidor de ejemplo que se muestra en los resultados de búsqueda](./media/log-analytics-linux-agents/oms-perfsearch01.png)

En los resultados, haga clic en **Métricas** para ver los contadores para los que se recopilaron los datos. Se muestran los datos en tiempo real como gráficos para cada contador.

![metrics](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## Syslog

Syslog es un protocolo de registro de eventos similar a los registros de eventos de Windows, ambos funcionan de forma parecida al mostrarlos en OMS.

### Incorporación de una nueva utilidad Syslog de Linux en OMS

1. En la página **Configuración** en **Datos**, haga clic en **Syslog** y escriba a la izquierda del icono del signo más el nombre de la utilidad Syslog que desea agregar. ![Syslog de Linux](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.	Si no sabe el nombre completo de la utilidad, puede empezar a escribir un nombre parcial y aparecerá una lista de utilidades Syslog disponibles. Cuando encuentre la utilidad que desea agregar, haga clic en el nombre de la lista y luego haga clic en el icono del signo más para agregarla.
3.	Después de agregar la utilidad, esta aparece en la lista de utilidades de Syslog resaltada con una barra de color. A continuación, seleccione los niveles de gravedad (categorías de información de la utilidad de Syslog) que se van a recopilar.
4.	Para finalizar los cambios, haga clic en **Guardar** en la parte inferior de la página. Los cambios de configuración realizados se envían a todos los agentes de OMS para Linux que están registrados en OMS, normalmente en un máximo de 5 minutos.


### Configuración de las utilidades de Syslog de Linux en Linux

Los eventos Syslog se envían desde el demonio Syslog, por ejemplo Rsyslog o Syslog-ng, a un puerto local en el que el agente está escuchando. De forma predeterminada al puerto 25224. Cuando se instala el agente, se aplica una configuración de Syslog predeterminada. Esto se encuentra en:


Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf


La configuración predeterminada de Syslog del agente de OMS carga eventos Syslog desde todas las utilidades con una gravedad de advertencia o superior.

>[AZURE.NOTE] Si modifica la configuración de Syslog, tiene que reiniciar el demonio Syslog para que los cambios surtan efecto.

La configuración predeterminada de Syslog para el agente de OMS para Linux para OMS es:

#### Rsyslog

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

#### Syslog ng

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### Visualización de todos los eventos de Syslog con Log Analytics

1. En el portal de Operations Management Suite, haga clic en el icono de **búsqueda de registros**.
2. En la agrupación **Log Management** (administración de registros), elija una búsqueda predefinida de Syslog y selecciónela para ejecutarla.

Este ejemplo muestra todos los eventos de Syslog.

![Eventos de Syslog que se muestran en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Ahora puede profundizar en los resultados de la búsqueda.

## Alertas de Linux

Si utiliza Nagios o Zabbix para administrar los equipos Linux, OMS puede recibir las alertas generadas desde esas herramientas. Sin embargo, en la actualidad no hay ningún método para configurar los datos entrantes de la alerta usando el portal de OMS. En su lugar, tendrá que modificar un archivo de configuración para empezar a enviar alertas a OMS.



### Recopilación de alertas de Nagios

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

### Recopilación de alertas de Zabbix

Para recopilar las alertas de un servidor Zabbix, tendrá que llevar cabo pasos similares a los que se han expuesto para Nagios, excepto que aquí tendrá que especificar un usuario y una contraseña *sin cifrar*. No es una solución ideal, pero seguramente cambiará pronto. Para solucionar este problema, recomendamos que cree el usuario y le conceda permiso solamente para supervisar.

Una sección de ejemplo del archivo de configuración omsagent.conf (/etc/opt/microsoft/omsagent/conf/omsagent.conf) para Zabbix debe ser similar a esto:

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

### Visualización de alertas en búsqueda de Log Analytics

Después de configurar los equipos Linux para enviar alertas a OMS, puede utilizar algunas simples consultas de búsqueda de registro para ver las alertas. El siguiente ejemplo de consulta de búsqueda devuelve todas las alertas grabadas que se generaron. Por ejemplo, si se produce algún tipo de problema en su infraestructura de TI, los resultados de la consulta del ejemplo siguiente podrían indicar en dónde se ha originado el problema. Y puede fácilmente profundizar las alertas por sistema de origen para ayudarle a acotar su investigación. La ventaja es que no tiene necesariamente que ir a varios sistemas de administración desde el principio, siempre que las alertas se envíen a OMS, puede empezar por ahí.

```
Type=Alert
```

#### Visualización de todas las alertas de Nagios con Log Analytics
1. En el portal de Operations Management Suite, haga clic en el icono de **búsqueda de registros**.
2. En la barra de consulta, escriba la siguiente consulta de búsqueda:

    ```
    Type=Alert SourceSystem=Nagios
    ```
![Alertas de Nagios que se muestran en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Después de ver los resultados de la búsqueda, puede profundizar en los detalles adicionales como el elemento *AlertState*.

### Visualización de todas las alertas de Zabbix con Log Analytics
1. En el portal de Operations Management Suite, haga clic en el icono de **búsqueda de registros**.
2. En la barra de consulta, escriba la siguiente consulta de búsqueda:

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![Alertas de Zabbix que se muestran en la búsqueda de registros](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Después de ver los resultados de la búsqueda, puede profundizar en los detalles adicionales como el elemento *AlertName*.


## Compatibilidad con System Center Operations Manager

El agente de OMS para Linux comparte archivos binarios del agente con el agente de System Center Operations Manager. Instalar al agente de OMS para Linux en un sistema que está administrado por Operations Manager actualiza los paquetes OMI y SCX en el equipo a una versión más reciente. El agente de OMS para Linux y System Center 2012 R2 son compatibles. Sin embargo, **System Center 2012 SP1 y las versiones anteriores no son compatibles ni están admitidas en la actualidad por el agente de OMS para Linux.**

>[AZURE.NOTE] Si el agente de OMS para Linux se instala en un equipo que no está administrado actualmente por Operations Manager y posteriormente desea administrar el equipo con Operations Manager, tiene que modificar la configuración de OMI antes de detectar el equipo. **Este paso no es necesario si está instalado el agente Operations Manager antes que el agente de OMS para Linux.**

### Habilitación del agente de OMS para Linux para comunicarse con Operations Manager

1. Editar el archivo /etc/opt/omi/conf/omiserver.conf
2. Asegúrese de que la línea que comienza con **httpsport =** define el puerto 1270. Por ejemplo `httpsport=1270`
3. Reinicie el servidor OMI:

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## Permisos necesarios de la base de datos para los contadores de rendimiento de MySQL

Para conceder permisos a un usuario de supervisión de MySQL, el usuario que concede el permiso tiene que tener el privilegio 'GRANT option', además del privilegio que se va a conceder.

Para que el usuario de MySQL devuelva los datos de rendimiento el usuario tendrá acceso a las siguientes consultas:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Además de estas consultas el usuario de MySQL necesita el acceso SELECT a las siguientes tablas predeterminadas:

- information\_schema
- mysql

Estos privilegios se pueden conceder ejecutando los siguientes comandos de concesión.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## Administración de las credenciales de supervisión de MySQL en el archivo de autenticación

Las secciones siguientes le ayudan a administrar las credenciales de MySQL.

### Configuración del proveedor de MySQL para OMI

El proveedor de MySQL para OMI necesita un usuario de MySQL preconfigurado y bibliotecas de cliente de MySQL instaladas para consultar la información de rendimiento y mantenimiento de la instancia de MySQL.

### Archivo de autenticación de MySQL para OMI

El proveedor de MySQL OMI utiliza un archivo de autenticación para determinar la dirección de enlace y el puerto que usa la instancia de MySQL para escuchar y las credenciales que tiene que usar para recopilar métricas. Durante la instalación el proveedor de MySQL para OMI examinará los archivos de configuración de my.cnf (ubicaciones predeterminadas) para encontrar la dirección de enlace y el puerto, y establecer parcialmente el archivo de autenticación de MySQL para OMI.

Para completar la supervisión de una instancia del servidor de MySQL, agregue un archivo de autenticación pregenerado de MySQL para OMI en el directorio correcto.

### Formato del archivo de autenticación

El archivo de autenticación de MySQL para OMI es un archivo de texto que contiene información sobre:

- Port
- Dirección de enlace
- Nombre de usuario de MySQL
- Contraseña codificada en Base64

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
| Port | Puerto representa el puerto actual en el que está escuchando la instancia de MySQL. El puerto 0 implica que las propiedades que siguen se usan para la instancia predeterminada. |
| Dirección de enlace | La dirección de enlace es la actual dirección de enlace de MySQL |
| nombre de usuario | Este es el nombre de usuario del usuario de MySQL que desee usar para supervisar la instancia del servidor MySQL. |
| Contraseña codificada en Base64 | Esta es la contraseña del usuario de supervisión de MySQL codificada en Base64. |
| Actualización automática | Cuando el proveedor de MySQL para OMI se actualiza, el proveedor volverá a examinar para detectar cambios en el archivo my.cnf y sobrescribir el archivo de autenticación de MySQL para OMI. Establezca esta marca en verdadero o falso dependiendo de las actualizaciones necesarias para el archivo de autenticación de MySQL para OMI. |

#### Ubicación del archivo de autenticación

El archivo de autenticación de MySQL para OMI se debe colocar en la siguiente ubicación con el nombre "mysql-auth":

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

El archivo (y directorio auth/omsagent) deben pertenecer al usuario omsagent.

## Registros del agente

Los registros para el agente de OMS para Linux se encuentran en:

/var/opt/microsoft/omsagent/log/

Los registros para el agente de OMS para Linux para el programa omsconfig (configuración del agente) se encuentran en:

/var/opt/microsoft/omsconfig/log/

Los registros para los componentes OMI y SCX (que proporcionan los datos de métricas de rendimiento) se encuentran en:

/var/opt/omi/log/ y /var/opt/microsoft/scx/log

## Limitaciones conocidas
Revise las siguientes secciones para informarse sobre las limitaciones actuales del agente de OMS para Linux.

### Diagnóstico de Azure

Para las máquinas virtuales de Linux que se ejecutan en Azure es posible que se necesiten pasos adicionales para permitir la recopilación de datos por parte de Diagnóstico de Azure y Operations Management Suite. Por razones de compatibilidad con el agente de OMS para Linux, es necesaria la **Versión 2.2** de la extensión de diagnósticos de Linux.

Para más información sobre la instalación y configuración de la extensión de diagnóstico de Linux, consulte [Uso del comando de la CLI de Azure para habilitar la extensión de diagnóstico de Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension/#use-the-azure-cli-command-to-enable-linux-diagnostic-extension).

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

### Sysklog no se admite.

Rsyslog o Syslog son necesarios para recopilar mensajes de Syslog. El demonio predeterminado de Syslog en la versión 5 de Red Hat Enterprise Linux, CentOS y Oracle Linux (Sysklog) no se admite para la recopilación de eventos de Syslog. Para recopilar datos de Syslog de esta versión de estas distribuciones, es necesario instalar configurar el demonio de Rsyslog para reemplazar Sysklog. Para más información acerca de cómo sustituir Sysklog por Rsyslog, consulte [Install the newly built rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM) (instalar el recién creado rsyslog RPM).

## Pasos siguientes

- Consulte [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Incorporación de soluciones de Log Analytics desde la galería de soluciones) para agregar funcionalidad y recopilar datos.
- Familiarícese con las [búsquedas de registros](log-analytics-log-searches.md) para ver información detallada recopilada por soluciones.
- Use los [paneles](log-analytics-dashboards.md) para guardar y mostrar sus propias búsquedas personalizadas.

<!---HONumber=AcomDC_0504_2016-->