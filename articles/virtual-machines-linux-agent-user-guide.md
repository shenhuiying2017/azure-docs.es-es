<properties 
	pageTitle="Guía de usuario del Agente de Linux para Azure" 
	description="Aprenda a instalar y configurar el Agente de Linux (waagent) para administrar la interacción de la máquina virtual con el controlador de tejido de Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="szarkos"/>





#Guía de usuario del Agente de Linux de Azure

##Introducción

El Agente de Linux de Azure (waagent) administra la interacción de la máquina virtual con el controlador de tejido de Azure. Hace lo siguiente:

* **Aprovisionamiento de imágenes**
  - Crea una cuenta de usuario
  - Configura los tipos de autenticación de SSH
  - Implementa las claves públicas y los pares de claves de SSH
  - Configura el nombre de host
  - Publica el nombre de host para DNS de plataforma
  - Informa de la huella digital de clave de host de SSH para la plataforma
  - Administra el disco de recursos
  - Formatea y monta discos de recursos
  - Configura el espacio de intercambio
* **Redes**
  - Administra las rutas para mejorar la compatibilidad con los servidores DHCP de la plataforma
  - Asegura la estabilidad del nombre de interfaz de red
* **Kernel**
  - Configura NUMA virtual
  - Consume entropía de Hyper-V para /dev/random
  - Configura tiempos de espera SCSI para el dispositivo raíz (que podría ser remoto)
* **Diagnóstico**
  - Remite la consola al puerto serie
* **Implementaciones de SCVMM**
    - Detecta y arranca el agente VMM para Linux cuando se ejecuta en un entorno de System Center Virtual Machine Manager 2012 R2

El flujo de información desde la plataforma hasta el agente se produce a través de dos canales:

* Un DVD conectado de tiempo de arranque para las implementaciones de IaaS. Este DVD incluye un archivo de configuración conforme a OVF que incluye toda la información de aprovisionamiento diferente de los pares de clave reales de SSH.

* Un extremo de TCP que expone una API de REST que se usa para obtener la configuración de implementación y topología.

###Obtención del Agente de Linux
Puede obtener el Agente de Linux más reciente directamente de:

- [Los diferentes proveedores de distribución que admiten Linux en Azure](http://support.microsoft.com/kb/2805216)
- o del [repositorio de código abierto de Github para el Agente de Linux de Azure](https://github.com/WindowsAzure/WALinuxAgent)


###Distribuciones de Linux compatibles
* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Otros sistemas compatibles:

* FreeBSD 9+ (WALinuxAgent v2.0.0+)


###Requisitos

Waagent depende de algunos paquetes de sistema para funcionar correctamente:

* Python 2.5+
* Openssl 1.0+
* Openssh 5.3+
* Utilidades del sistema de archivos: sfdisk, fdisk, mkfs
* Herramientas de contraseña: chpasswd, sudo
* Herramientas de procesamiento de texto: sed, grep
* Herramientas de red: ip-route

##Instalación

La instalación con RPM o un paquete de DEB del repositorio de paquetes de su distribución es el método preferido para instalar y actualizar el Agente de Linux de Azure.

Si se va a realizar una instalación manual, se debe copiar waagent a /usr/sbin/waagent e instalarse mediante la ejecución de: 

	# sudo chmod 755 /usr/sbin/waagent
	# /usr/sbin/waagent -install -verbose

El archivo de registro del agente se guarda en /var/log/waagent.log.


##Opciones de la línea de comandos

###Marcas

- verbose: Aumenta el nivel de detalle de un comando específico
- force: Omite la confirmación interactiva de algunos comandos

###Comandos:

- help: Enumera los comandos y las marcas compatibles.

- install: Instalación manual del agente
 * Comprueba las dependencias requeridas del sistema

 * Crea el script init SysV (/etc/init.d/waagent), el archivo de configuración logrotate (/etc/logrotate.d/waagent) y configura la imagen para que ejecute el script init en el arranque

 * Escribe un archivo de configuración de muestra en /etc/waagent.conf

 * Todo archivo de configuración existente se mueve a /etc/waagent.conf.old

 * Detecta la versión del kernel y aplica la solución alternativa VNUMA si es necesario

 * Mueve las reglas de udev que pueden interferir con la red (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) a /var/lib/waagent/  

- uninstall: Quita waagent y los archivos asociados
 * Quita del registro el script init del sistema y lo elimina

 * Elimina la configuración de logrotate y el archivo de configuración de waagent en /etc/waagent.conf

 * Restaura toda regla udev que se haya movido durante la instalación

 * No se admite la reversión automática de la solución alternativa VNUMA, edite los archivos de configuración de GRUB manualmente para volver a habilitar NUMA, si es necesario.

- deprovision: Intento de limpiar el sistema y dejarlo adecuado para un reaprovisionamiento. Esta operación eliminó lo siguiente:
 * Todas las claves de host de SSH (si Provisioning.RegenerateSshHostKeyPair es "y" en el archivo de configuración)

 * Configuración de Nameserver en /etc/resolv.conf

 * Contraseña raíz desde /etc/shadow (si Provisioning.DeleteRootPassword es "y" en el archivo de configuración)

 * Concesiones del cliente de DHCP en caché

 * Restablece el nombre de host a localhost.localdomain

 **Advertencia:** el desaprovisionamiento no garantiza que se haya borrado toda información sensible de la imagen y que sea adecuada para su redistribución.

- deprovision+user: Realiza todo según -deprovision (arriba) y también elimina la última cuenta de usuario aprovisionada (obtenida de /var/lib/waagent) y los datos asociados. Este parámetro está presente cuando se desaprovisiona una imagen que se estaba aprovisionando anteriormente en Azure de modo que se pueda capturar y volver a usar.

- version: Muestra la versión de waagent

- serialconsole: Configura GRUB para marcar ttyS0 (el primer puerto serie) como
   la consola de arranque. Garantiza que los registros de arranque del kernel se envían al
   puerto serie y están disponibles para depuración.

- daemon: Ejecuta waagent como demonio para administrar la interacción con la plataforma.
   Este argumento se especifica en waagent en el script init de waagent.

##Configuración

Un archivo de configuración (/etc/waagent.conf) controla las acciones de waagent. 
Abajo se muestra un archivo de configuración de ejemplo:
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

A continuación se describen en detalle las diferentes opciones de configuración. 
Las opciones de configuración son de tres tipos: booleana, cadena o entero. 
Las opciones de configuración booleana se pueden especificar como "y" o "n". 
La palabra clave especial "None" se puede usar para algunas entradas de la configuración tipo cadena como se detalla a continuación.

**Role.StateConsumer:**

Tipo: String  
Predeterminado: None

Si se específica una ruta a un programa ejecutable, se invoca cuando waagent ha aprovisionado la imagen y el estado "Ready" está a punto de informarse al tejido. El argumento especificado para el programa será "Ready". El agente no esperará a que el programa regrese antes de continuar.

**Role.ConfigurationConsumer:**

Tipo: String  
Predeterminado: None

Si se especifica una ruta a un programa ejecutable, el programa se invoca cuando el Tejido indica que hay un archivo de configuración disponible para la máquina virtual. La ruta al archivo de configuración XML se proporciona como un argumento para el ejecutable. Este se puede invocar todas las veces que el archivo de configuración cambia. En el Anexo se proporciona un archivo de muestra. La ruta actual de este archivo es /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer:**

Tipo: String  
Predeterminado: None

Si se específica una ruta a un programa ejecutable, el programa se invoca cuando el Tejido indica que hay un nuevo diseño de topología de red disponible para la máquina virtual. La ruta del archivo de configuración XML se proporciona como un argumento para el ejecutable. Este se puede invocar varias veces cada vez que la topología de red cambia (debido a, por ejemplo, la recuperación del servicio). En el Anexo se proporciona un archivo de muestra. La ubicación actual de este archivo es /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled:**

Tipo: Boolean  
Predeterminado: y

Esta opción permite que el usuario habilite o deshabilite la funcionalidad de aprovisionamiento en el agente. Los valores válidos son "y" o "n". Si se deshabilita el aprovisionamiento, se mantiene el host SSH y las claves de usuario en la imagen y se ignora toda configuración que se especifique en la API de aprovisionamiento de Azure.

**Provisioning.DeleteRootPassword:**

Tipo: Boolean  
Predeterminado: n

Si se establece, se borra la contraseña raíz en el archivo /etc/shadow durante el proceso de aprovisionamiento.

**Provisioning.RegenerateSshHostKeyPair:**

Tipo: Boolean  
Predeterminado: y

Si se establece, se eliminan todos los pares de clave de host de SSH (ecdsa, dsa y rsa) durante el proceso de aprovisionamiento desde /etc/ssh/. Además, se genera un solo par de clave nuevo.

El tipo de cifrado para el par de clave nuevo se puede configurar mediante la entrada Provisioning.SshHostKeyPairType. Tenga en cuenta que algunas distribuciones volverán a crear los pares de clave de SSH para cualquier tipo de cifrado faltante cuando el demonio de SSH se reinicie (por ejemplo, en un nuevo arranque del equipo).

**Provisioning.SshHostKeyPairType:**

Tipo: String  
Predeterminado: rsa

Esta opción se puede establecer en un tipo de algoritmo de cifrado que es compatible con el demonio de SSH en la máquina virtual. Los valores generalmente admitidos son "rsa", "dsa" y "ecdsa". Tenga en cuenta que "putty.exe" en Windows no admite "ecdsa". Por lo tanto, si pretende usar putty.exe en Windows para conectarse a una implementación de Linux, use "rsa" o "dsa".

**Provisioning.MonitorHostName:**

Tipo: Boolean  
Predeterminado: y

Si se configura, waagent supervisará la máquina virtual de Linux en busca de cambios en el nombre de host (según los devuelve el comando "hostname") y actualizará automáticamente la configuración de red en la imagen para reflejar el cambio. Para insertar el cambio de nombre en los servidores DNS, la red se reiniciará en la máquina virtual. Esta acción tiene como resultado una breve pérdida de la conectividad con Internet.

**ResourceDisk.Format:**

Tipo: Boolean  
Predeterminado: y

Si se establece, el disco de recursos proporcionado por la plataforma se formatea y se monta a través de waagent si el tipo de sistema de archivos solicitado por el usuario en "ResourceDisk.Filesystem" es cualquiera, salvo "ntfs". Se dejará disponible una sola partición de tipo Linux (83) en el disco. Tenga en cuenta que esta partición no se va a formatear si se puede montar correctamente.

**ResourceDisk.Filesystem:**

Tipo: String  
Predeterminado: ext4

De esta manera se especifica el tipo de sistema de archivos para el disco de recursos. Los valores admitidos varían según la distribución de Linux. Si la cadena es X, entonces mkfs.X debe estar presente en la imagen de Linux. Las imágenes de SLES 11 deben usar generalmente "ext3". Las imágenes de FreeBSD deben usar "ufs2".

**ResourceDisk.MountPoint:**

Tipo: String  
Predeterminado: /mnt/resource 

Esta opción especifica la ruta en la cual se monta el disco de recursos. Tenga en cuenta que el disco de recursos es un disco  *temporary* que debe vaciarse cuando la máquina virtual se desaprovisiona.

**ResourceDisk.EnableSwap:**

Tipo: Boolean  
Predeterminado: n 

Si se establece, se crea un archivo de intercambio (/swapfile) en el disco de recursos y se agrega al espacio de intercambio del sistema.

**ResourceDisk.SwapSizeMB:**

Tipo: entero  
Predeterminado: 0

El tamaño del archivo de intercambio en megabytes.

**LBProbeResponder:**

Tipo: Boolean  
Predeterminado: y

Si se establece, waagent responderá a las sondas del equilibrador de carga desde la plataforma (si está presente).

**Logs.Verbose:**

Tipo: Boolean  
Predeterminado: n

Si se establece, se aumenta el nivel de detalle del registro. Waagent crea los registros en /var/log/waagent.log y aprovecha la funcionalidad logrotate del sistema para alternar los registros.

**OS.RootDeviceScsiTimeout:**

Tipo: entero  
Predeterminado: 300

Esta opción configura el tiempo de espera SCSI en segundos en el disco del SO y las unidades de datos. Si no se establece, se usan los valores predeterminados del sistema.

**OS.OpensslPath:**

Tipo: String  
Predeterminado: None

Esta opción se puede usar para especificar una ruta alternativa para que el binario openssl la use para las operaciones criptográficas.

##Anexo

###Archivo de configuración de rol de muestra

	<?xml version="1.0" encoding="utf-8"?>
	<HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
	  <StoredCertificates>
	    <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
	  </StoredCertificates>
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
	  <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
	    <CAS mode="full" />
	    <PrivilegeLevel mode="max" />
	    <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
	    <ApplicationSettings>
	      <Setting name="__ModelData" value="&lt;m role=&quot;LinuxVM&quot; xmlns=&quot;urn:azure:m:v1&quot;>&lt;r name=&quot;LinuxVM&quot;>&lt;e name=&quot;HTTP&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput&quot; />&lt;e name=&quot;SSH&quot; />&lt;/r>&lt;/m>" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
	      <Setting name="startpage" value="Hello World!" />
	      <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
	    </ApplicationSettings>
	    <ResourceReferences>
	      <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore" disableQuota="false" />
	    </ResourceReferences>
	  </HostingEnvironmentConfig>

###Archivo de topología de rol de muestra

	<?xml version="1.0" encoding="utf-8"?>
	<SharedConfig version="1.0.0.0" goalStateIncarnation="2">
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_1" guid="{a7b94774-db5c-4007-8707-0b9e91fd808d}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" settleTimeSeconds="10" />
	  <LoadBalancerSettings timeoutSeconds="32" waitLoadBalancerProbeCount="8">
	    <Probes>
	      <Probe name="LinuxVM" />
	      <Probe name="03F7F19398C4358108B7ED059966EEBD" />
	      <Probe name="47194D0E3AB3FCAD621CAAF698EC82D8" />
	    </Probes>
	  </LoadBalancerSettings>
	  <OutputEndpoints>
	    <Endpoint name="LinuxVM:Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" type="SFS">
	      <Target instance="LinuxVM_IN_0" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_1" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_2" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	    </Endpoint>
	  </OutputEndpoints>
	  <Instances>
	    <Instance id="LinuxVM_IN_1" address="10.115.38.202">
	      <FaultDomains randomId="1" updateId="1" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="HTTP" address="10.115.38.202:80" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:80" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="80" to="80" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.38.202:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	          <RemoteInstances>
	            <RemoteInstance instance="LinuxVM_IN_0" />
	            <RemoteInstance instance="LinuxVM_IN_2" />
	          </RemoteInstances>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" address="10.115.38.202:20000" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:3389" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="20000" to="20000" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="SSH" address="10.115.38.202:22" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:22" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="22" to="22" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_0" address="10.115.58.82">
	      <FaultDomains randomId="0" updateId="0" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.82:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_2" address="10.115.58.148">
	      <FaultDomains randomId="0" updateId="2" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.148:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	  </Instances>
	</SharedConfig>

<!--HONumber=45--> 
