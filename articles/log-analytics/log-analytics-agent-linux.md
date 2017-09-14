---
title: "Conexión de equipos Linux a Operations Management Suite (OMS) | Microsoft Docs"
description: "En este artículo se describe cómo conectar equipos Linux hospedados en Azure, en otra nube o locales a OMS mediante el agente de OMS para Linux."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/05/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 17b451b1fc91cf9fdc895ad28f2c455af5d28b07
ms.contentlocale: es-es
ms.lasthandoff: 09/06/2017

---

# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>Conexión de equipos Linux a Operations Management Suite (OMS) 

Con Microsoft Operations Management Suite (OMS), puede recopilar y actuar en los datos generados desde equipos Linux y soluciones de contenedor como Docker, que residen en el centro de datos local como servidores físicos o máquinas virtuales, máquinas virtuales en un servicio hospedado en la nube como Amazon Web Services (AWS) o Microsoft Azure. También puede usar soluciones de administración disponibles en OMS, como Change Tracking, para identificar los cambios de configuración y Update Management para administrar las actualizaciones de software con el fin de administrar de manera proactiva el ciclo de vida de las máquinas virtuales Linux. 

El agente OMS para Linux se comunica con el servicio OMS en el puerto TCP 443 y, si el equipo se conecta a un servidor proxy o firewall para comunicarse a través de Internet, consulte [Configuración del agente para su uso con un servidor proxy HTTP o con OMS Gateway](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway) para comprender qué cambios de configuración deben aplicarse.  Si va a supervisar el equipo con System Center 2016 - Operations Manager u Operations Manager 2012 R2, puede tener hosts múltiples con el servicio OMS para recopilar datos y reenviarlos al servicio, y ser supervisados por Operations Manager.  Los equipos de Linux supervisados por un grupo de administración de Operations Manager que se integran con OMS no reciben la configuración de los orígenes de datos y reenvían los datos recopilados a través del grupo de administración.  No se puede configurar el agente de OMS para informar a varias áreas de trabajo.  

Si las directivas de seguridad de TI no permiten que los equipos de la red se conecten a Internet, el agente se puede configurar para que se conecte a OMS Gateway a fin de recibir información de configuración y enviar los datos recopilados según la solución que haya habilitado. Para obtener más información y pasos sobre cómo configurar el agente Linux de OMS para que se comuniquen por una puerta de enlace de OMS con el servicio OMS, consulte [Conexión de equipos sin acceso a OMS mediante la puerta de enlace de OMS](log-analytics-oms-gateway.md).  

El siguiente diagrama muestra la conexión entre los equipos administrados con agente de Linux y OMS, incluidos los puertos y la dirección.

![diagrama de comunicación de agente directo con OMS](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Requisitos del sistema
Antes de comenzar, revise los detalles siguientes para comprobar que cumple los requisitos previos.

### <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux compatibles
Las distribuciones Linux siguientes son compatibles oficialmente.  Aunque también se puede ejecutar el agente de OMS para Linux en otras distribuciones que no se enumeran.

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 y 7 (x86/x64)
* Oracle Linux 5, 6 y 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 y 7 (x86/x64)
* Debian GNU/Linux 6, 7 y 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 y 12 (x86/x64)

### <a name="network"></a>Red
A continuación se muestra la información de configuración de proxy y firewall requerida para que el agente de Linux se comunique con OMS. El tráfico es saliente desde la red al servicio de OMS. 

|Recurso del agente| Puertos |  
|------|---------|  
|*.ods.opinsights.azure.com | Puerto 443|   
|*.oms.opinsights.azure.com | Puerto 443|   
|*.blob.core.windows.net/ | Puerto 443|   
|*.azure-automation.net | Puerto 443|  

### <a name="package-requirements"></a>Requisitos de paquete

 **Paquetes necesarios**   | **Descripción**   | **Versión mínima**
--------------------- | --------------------- | -------------------
Glibc | Biblioteca GNU C   | 2.5-12 
Openssl | Bibliotecas OpenSSL | 0.9.8E o 1.0
Curl | Cliente web de cURL | 7.15.5
Python ctypes | | 
PAM | Módulos de autenticación conectables | 

> [!NOTE]
>  Rsyslog o Syslog son necesarios para recopilar mensajes de Syslog. El demonio predeterminado de Syslog en la versión 5 de Red Hat Enterprise Linux, CentOS y Oracle Linux (Sysklog) no se admite para la recopilación de eventos de Syslog. Para recopilar datos de Syslog de esta versión de estas distribuciones, es necesario instalar configurar el demonio de Rsyslog para reemplazar Sysklog. 

El agente incluye varios paquetes. El archivo de versión contiene los siguientes paquetes disponibles mediante la ejecución de la agrupación de shell con `--extract`:

**Paquete** | **Versión** | **Descripción**
----------- | ----------- | --------------
omsagent | 1.4.0 | El agente de Operations Management Suite para Linux
omsconfig | 1.1.1 | Agente de configuración para el agente de OMS
omi | 1.2.0 | Infraestructura de administración abierta (OMI), un servidor de CIM ligero
scx | 1.6.3 | Proveedores de CIM OMI para métricas de rendimiento del sistema operativo
apache-cimprov | 1.0.1 | Proveedor de supervisión de rendimiento de servidor HTTP de Apache para OMI. Se instala si se detecta un servidor HTTP de Apache.
mysql-cimprov | 1.0.1 | Proveedor de supervisión de rendimiento de servidor MySQL Server para OMI. Se instala si se detecta un servidor MySQL/MariaDB.
docker-cimprov | 1.0.0 | Proveedor de Docker para OMI. Se instala si se detecta Docker.

### <a name="compatibility-with-system-center-operations-manager"></a>Compatibilidad con System Center Operations Manager
El agente de OMS para Linux comparte archivos binarios del agente con el agente de System Center Operations Manager. Si instala el agente de OMS para Linux en un sistema que está administrado por Operations Manager, actualiza los paquetes OMI y SCX en el equipo a una versión más reciente. En esta versión, OMS y System Center 2016 - los agentes Operations Manager/Operations Manager 2012 R2 para Linux son compatibles. 

> [!NOTE]
> System Center 2012 SP1 y las versiones anteriores no son compatibles ni están admitidas en la actualidad por el agente OMS para Linux.<br>
> Si el agente OMS para Linux se instala en un equipo que no está administrado actualmente por Operations Manager y posteriormente desea administrar el equipo con Operations Manager, tiene que modificar la [configuración de OMI](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) antes de detectar el equipo. **Este paso *no* es necesario si está instalado el agente Operations Manager antes del agente OMS para Linux.**

### <a name="system-configuration-changes"></a>Cambios en la configuración del sistema
Después de instalar el agente OMS para los paquetes Linux, se aplican los siguientes cambios de configuración adicionales a todo el sistema. Estos artefactos se quitan cuando se desinstala el paquete omsagent.

* Se crea un usuario sin privilegios llamado: `omsagent` . Esta es la cuenta que el demonio omsagent usa para ejecutarse.
* Se crea un archivo "include" de sudoers en /etc/sudoers.d/omsagent. Esto autoriza a omsagent a reiniciar los demonios syslog y omsagent. Si no se admiten directivas "incluir" en la versión instalada de sudo, estas entradas se escriben en /etc/sudoers.
* Se modifica la configuración de Syslog para reenviar un subconjunto de eventos al agente. Para más información consulte la sección sobre **configuración de recopilación de datos** a continuación

### <a name="upgrade-from-a-previous-release"></a>Actualización desde una versión anterior
La actualización desde versiones anteriores a 1.0.0-47 se admite en esta versión. Si realiza la instalación con el comando `--upgrade`, se actualizan todos los componentes del agente a la versión más reciente.

## <a name="installing-the-agent"></a>Instalación del agente

En esta sección se describe cómo instalar al agente de OMS para Linux con un conjunto, que contiene los paquetes de Debian y RPM para cada uno de los componentes del agente.  Se puede instalar directamente o extraer para recuperar los paquetes individualmente.  

En primer lugar necesita el identificador y la clave del área de trabajo de OMS, que puede encontrar si va al [portal clásico de OMS](https://mms.microsoft.com).  En la página **Introducción**, en el menú superior, seleccione **Configuración** y vaya a **Connected Sources\Linux Servers**.  Encontrará los valores a la derecha de **Id. del área de trabajo** y **Clave principal**.  Copie y pegue ambos valores en el editor que prefiera.    

1. Descargue la última versión del [agente de OMS para Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) o del [agente de OMS para Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) desde GitHub.  
2. Transfiera el paquete adecuado (x86 o x x64) al equipo Linux mediante scp o sftp.
3. Instale el paquete mediante el argumento `--install` o `--upgrade`. 

    > [!NOTE]
    > Use el argumento `--upgrade` si se instalan todos los paquetes existentes como cuando ya está instalado el agente de System Center Operations Manager para Linux. Para conectarse a Operations Management Suite durante la instalación, proporcione los parámetros `-w <WorkspaceID>` y `-s <Shared Key>`.


#### <a name="to-install-and-onboard-directly"></a>Para instalarlos e incorporarlos directamente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Para actualizar el paquete del agente
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Para instalarlos e incorporarlos a un área de trabajo en la nube de US Government
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway"></a>Configuración del agente para su uso con un servidor proxy HTTP o puerta de enlace de OMS
El agente OMS para Linux admite la comunicación a través de un servidor proxy HTTP o HTTPS o la puerta de enlace de OMS para el servicio OMS.  Se admite la autenticación anónima y básica (nombre de usuario/contraseña).  

### <a name="proxy-configuration"></a>Configuración de proxy
El valor de configuración de proxy tiene la siguiente sintaxis:

`[protocol://][user:password@]proxyhost[:port]`

Propiedad|Descripción
-|-
Protocol|http o https
user|Nombre de usuario opcional para la autenticación de proxy
contraseña|Contraseña opcional para la autenticación de proxy
proxyhost|Dirección o el FQDN de la puerta de enlace de OMS/servidor proxy
puerto|Número de puerto opcional para el servidor proxy/puerta de enlace de OMS

Por ejemplo: `http://user01:password@proxy01.contoso.com:8080`

El servidor proxy se puede especificar durante la instalación o al modificar el archivo de configuración proxy.conf después de la instalación.   

### <a name="specify-proxy-configuration-during-installation"></a>Especificación de la configuración de proxy durante la instalación
El argumento `-p` o `--proxy` para el paquete de instalación de omsagent especifica la configuración de proxy que se va a usar. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p http://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Definición de la configuración de proxy en un archivo
Se puede establecer la configuración del proxy en los archivos `/etc/opt/microsoft/omsagent/proxy.conf` y `/etc/opt/microsoft/omsagent/conf/proxy.conf `. Estos archivos se pueden crear o editar directamente, pero se deben actualizar sus permisos para conceder al usuario omiuser permiso de lectura en los archivos. Por ejemplo:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:8080"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Eliminación de la configuración de proxy
Para quitar una configuración de proxy definida previamente y revertir a una conexión directa, quite el archivo proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-operations-management-suite"></a>Incorporación con Operations Management Suite
Si no se proporcionaron la clave y el identificador del área de trabajo durante la instalación del paquete, el agente debe registrarse posteriormente con Operations Management Suite.

### <a name="onboarding-using-the-command-line"></a>Incorporación con la línea de comandos
Ejecute el comando omsadmin.sh proporcionando el identificador y la clave del área de trabajo. Este comando debe ejecutarse como raíz (con elevación sudo):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>Incorporación mediante un archivo
1.  Cree el archivo `/etc/omsagent-onboard.conf`. El archivo debe ser de lectura y escritura para la raíz.
`sudo vi /etc/omsagent-onboard.conf`
2.  Inserte las líneas siguientes en el archivo con el identificador de área de trabajo y la clave compartida:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Ejecute el siguiente comando para incorporarse a OMS: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  Si se incorpora correctamente, el archivo se elimina.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Habilitación del agente OMS para Linux para notificar a System Center Operations Manager
Realice los pasos siguientes para configurar al agente OMS para Linux para informar a un grupo de administración de System Center Operations Manager.  

1. Edite el archivo `/etc/opt/omi/conf/omiserver.conf`
2. Asegúrese de que la línea que comienza con **httpsport =** define el puerto 1270. Por ejemplo: `httpsport=1270`
3. Reinicie el servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Registros del agente
Los registros del agente OMS para Linux se pueden encontrar en: `/var/opt/microsoft/omsagent/<workspace id>/log/` Los registros para el programa omsconfig (configuración del agente) se pueden encontrar en: `/var/opt/microsoft/omsconfig/log/` Los registros para los componentes OMI y SCX (que proporcionan datos de métricas de rendimiento) se pueden encontrar en: `/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Configuración de rotación de registros##
La configuración de rotación de registros para omsagent se puede encontrar en: `/etc/logrotate.d/omsagent-<workspace id>`

La configuración predeterminada es la siguiente: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Desinstalación del agente OMS para Linux
Los paquetes de agente se pueden desinstalar mediante la ejecución del archivo bundle.sh con el argumento `--purge`, que elimina completamente el agente y su configuración del equipo.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Solución de problemas

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>Problema: No es posible establecer la conexión a través del proxy a OMS

#### <a name="probable-causes"></a>Causas probables
* El proxy especificado durante la incorporación era incorrecto.
* Los puntos de conexión de servicio OMS no están incluidos en la lista de permitidos en su centro de datos. 

#### <a name="resolutions"></a>Soluciones
1. Vuelva a incorporase al servicio OMS con el agente de OMS para Linux mediante el comando siguiente con la opción `-v` habilitada. Esto permite la salida detallada del agente que se conecta a través del proxy al servicio OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Revise la sección [Configuración del agente para su uso con un servidor proxy HTTP (#configuring the-agent-for-use-with-a-http-proxy-server) para comprobar que el agente se haya configurado correctamente para comunicarse a través de un servidor proxy.    
* Vuelva a comprobar que los siguientes puntos de conexión del servicio OMS estén incluidos en la lista de permitidos.

    |Recurso del agente| Puertos |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Puerto 443|   
    |*.oms.opinsights.azure.com | Puerto 443|   
    |ods.systemcenteradvisor.com | Puerto 443|   
    |*.blob.core.windows.net/ | Puerto 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Recibe un error 403 al intentar incorporarse

#### <a name="probable-causes"></a>Causas probables
* La fecha y la hora son incorrectas en el servidor Linux 
* El identificador y la clave de área de trabajo usados son incorrectos

#### <a name="resolution"></a>Resolución

1. Compruebe la hora en el servidor Linux con el comando date. Si la hora es +/-15 minutos de la hora actual, la incorporación produce un error. Para corregir este problema, actualice la fecha o la zona horaria del servidor Linux. 
2. Compruebe que ha instalado la versión más reciente del agente de OMS para Linux.  La versión más reciente notifica ahora si el desfase temporal está causando el error de incorporación.
3. Vuelva a realizar la incorporación con un identificador de área de trabajo correcto y la clave de área de trabajo según las instrucciones de instalación de este tema.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Ve un error 404 y 500 en el archivo de registro justo después de la incorporación
Se trata de un problema conocido que se produce con la primera carga de datos de Linux en un área de trabajo de OMS. Esto no afecta a los datos que se envían ni a la experiencia del servicio.

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>Problema: No aparece ningún dato en el portal de OMS

#### <a name="probable-causes"></a>Causas probables

- Se produjo un error en la incorporación al servicio OMS.
- La conexión al servicio OMS se bloquea.
- Se está haciendo la copia de seguridad de los datos del agente OMS para Linux

#### <a name="resolutions"></a>Soluciones
1. Compruebe si la incorporación del servicio de OMS se realizó correctamente comprobando si existe el archivo siguiente: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Repita la incorporación con las instrucciones de la línea de comandos `omsadmin.sh`
3. Si utiliza un proxy, consulte los pasos de resolución de proxy que proporcionó anteriormente.
4. En algunos casos, cuando el agente OMS para Linux no puede comunicarse con el servicio OMS, los datos del agente ocupan el tamaño de búfer total de 50 MB. Se debe reiniciar el agente de OMS para Linux ejecutando el siguiente comando `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Este problema se ha corregido en la versión 1.1.0-28 y posteriores del agente.


