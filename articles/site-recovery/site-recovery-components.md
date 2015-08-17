<properties
	pageTitle="Componentes de Site Recovery" 
	description="Este artículo proporciona información general acerca de los componentes de Site Recovery y de cómo administrarlos" 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/09/2015"
	ms.author="raynew"/>

# Componentes de Site Recovery

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores virtuales. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. [Información general](site-recovery-overview.md).

Este artículo resume y describe los componentes de Site Recovery que se instalan en servidores y en máquinas virtuales.

Puede publicar preguntas acerca de este artículo en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Los componentes de Site Recovery varían ligeramente según el escenario de protección.

### Protección entre dos centros de datos con VMM

**Escenario** | **Descripción** | **Componentes necesarios** | **Detalles**
--- | --- | --- | ---
Se implementa Azure Site Recovery para replicar máquinas virtuales entre dos centros de datos. | <p>Cada centro de datos tiene un servidor VMM.</p><p>Cada servidor VMM tiene una nube privada que contiene uno o varios servidores host Hyper-V con las máquinas virtuales que desea proteger</p>. | El proveedor de Azure Site Recovery estará instalado en ambos servidores VMM. | <p>En los servidores host Hyper-V y en las máquinas virtuales protegidas no se instala ningún componente.</p><p>El Proveedor de Azure Site Recovery del el servidor VMM se comunica con el servicio de recuperación del sitio mediante el protocolo HTTPS 443 para coordinar la protección.</p><p>La replicación se produce entre los servidores host Hyper-V primario y secundario a través de Internet utilizando autenticación Kerberos y autenticación de certificados en los puertos 8083 y 8084.</p>

![De local a local](./media/site-recovery-components/Components_Onprem2Onprem.png)


### Protección entre un centro de datos con VMM y Azure

**Escenario** | **Descripción** | **Componentes necesarios** | **Detalles**
--- | --- | --- | ---
Se implementa Azure Site Recovery para replicar máquinas virtuales entre un centro de datos y Azure. | <p>El centro de datos local tiene un servidor VMM con una nube privada que contiene uno o varios servidores host Hyper-V con las máquinas virtuales que desea proteger</p>. | <p>El proveedor de Azure Site Recovery se instalará en el servidor VMM</p><p>En los servidores host Hyper-V de origen se instalará el agente de servicios de recuperación de Microsoft</p>. | <p>En las máquinas virtuales protegidas no se instala ningún componente.</p><p>El Proveedor de Azure Site Recovery del servidor VMM se comunica con el servicio Site Recovery mediante el protocolo HTTPS 443 para coordinar la protección.</p><p>La replicación se produce entre el agente de servicios de recuperación de Microsoft que se ejecuta en los servidores host Hyper-V de origen y Azure mediante el protocolo HTTPS 443.</p>

![De VMM local a Azure](./media/site-recovery-components/Components_OnpremVMM2Azure.png)

###  Protección entre un sitio de Hyper-V y Azure

**Escenario** | **Descripción** | **Componentes necesarios** | **Detalles**
--- | --- | --- | ---
Se implementa Azure Site Recovery para replicar máquinas virtuales entre un centro de datos y Azure. | <p>El centro de datos local tiene uno o varios servidores host de Hyper-V con máquinas virtuales que desea proteger.</p><p>Durante la configuración se define un sitio de Hyper-V que contiene uno o varios de estos servidores host de Hyper-V.</p> | <p>Se ejecuta una instalación de un componente único para instalar tanto el Proveedor de Azure Site Recovery y el agente de servicios de recuperación de Microsoft en los servidores host de Hyper-V.</p> | <p>En la implementación no hay ningún servidor VMM.</p><p>En las máquinas virtuales protegidas no se instala ningún componente.</p><p>El proveedor de Azure Site Recovery del servidor host Hyper-V se comunica con el servicio Site Recovery a través del protocolo HTTPS 443 para coordinar la protección.</p><p>La replicación entre el agente de los servicios de recuperación de Microsoft que se ejecuta en el servidor host Hyper-V y Azure se realiza mediante el protocolo HTTPS 443.</p>

![De VMM local a Azure](./media/site-recovery-components/Components_OnpremHyperVSite2Azure.png)

### Protección entre un servidor físico local o una máquina virtual de VMware y Azure 

En este escenario, la replicación se puede realizar de dos maneras:

- Mediante una conexión VPN (utilizando Azure ExpressRoute o una VPN de sitio a sitio)
- Mediante una conexión segura en Internet

#### Mediante una conexión VPN de sitio a sitio (o ExpressRoute)

Las comunicaciones de los servidores locales se dirigen a puertos internos de la red virtual de Azure a la que están conectadas las máquinas virtuales de configuración y de destino principal.

![De una máquina de VMware o física a Azure a través de Internet](./media/site-recovery-components/Components_OnpremVMware2AzureVPN.png)

#### Por Internet

Todas las comunicaciones desde los servidores locales se dirigen a extremos públicos asignados en el servicio en la nube de Azure para la máquina virtual de servidor de configuración y la máquina virtual del servidor principal de destino. Los extremos se crean dinámicamente al implementar las máquinas virtuales.

![De una máquina de VMware o física a Azure a través de Internet](./media/site-recovery-components/Components_OnpremVMware2AzureInternet.png)

#### Puertos

**Componente** | **Puerto** | **Detalles** 
--- | --- | --- | ---
**Servidor de proceso** |9080 | Las máquinas protegidas envían los datos para replicación al servidor de proceso a través de TCP 9080.
**Servidor de configuración** | HTTPS/443 | El servicio de movilidad que se ejecuta en las máquinas protegidas envía metadatos de replicación al servidor de configuración por el puerto 443.
 | HTTPS/443 | El servidor de configuración coordina y organiza la protección de las máquinas. El servidor de proceso se comunica con el servidor de configuración por el puerto 443 o a través del extremo público asignado para recibir información de administración y control. 
 | 9443 | En la dirección de la conmutación por recuperación, la herramienta vContinuum solicita control y metadatos al servidor de configuración en el puerto 9443 (no se muestra en el diagrama)
 | 5986 | La Administración remota con PowerShell usa el puerto 5986 (no se muestra en el diagrama)
 | 3389 | Conexión RDP al servidor de configuración utilizando el puerto 3389 (no se muestra en el diagrama)
**Servidor de destino principal** | 80 | El sitio de proceso envía las comunicaciones del tráfico de replicación al servidor de destino principal por el puerto 9080
 | HTTP/443 | El servidor de proceso replica los datos en el servidor de destino principal a través de HTTP o 443 (VPN)
 | HTTP/443 | El servidor de proceso replica los datos en el servidor de destino principal a través de HTTP o 443 (VPN)
**Reglas de firewall** | | <p>Para que la instalación de inserción de cliente del servicio de movilidad funcione correctamente, el firewall de las máquinas protegidas debe permitir el uso compartido de archivos e impresora y el Instrumental de administración de Windows.</p><p>Las reglas de firewall de las máquinas que desea proteger deben permitirles tener acceso al servidor de configuración.</p><p>Para conectarse a máquinas virtuales de Azure a través de Internet después de la conmutación por error, las reglas de firewall de las máquinas deben permitir conexiones de Escritorio remoto a través de Internet. Para conectarse a una máquina Linux con conmutación por error en Azure, el servicio de Shell seguro debe estar configurado para iniciarse automáticamente en el sistema y las reglas de firewall deben permitir las conexiones ssh.</p>


## Componentes de Site Recovery

**Componente** | **Detalles** | **Instalación** | **Escenario de implementación**
--- | --- | --- | ---
**Proveedor de Azure Site Recovery para VMM** | Controla la comunicación entre el servidor VMM y el servicio Site Recovery. | Instalado en un servidor VMM | Se utiliza cuando se configura la protección entre dos sitios de VMM o entre un sitio de VMM y Azure
**Proveedor de Azure Site Recovery para Hyper-V** | Controla la comunicación entre el host de Hyper-V y el servicio Site Recovery cuando VMM no está implementado. | Instalado en servidores host de Hyper-V | Se utiliza cuando se configura la protección entre un sitio de Hyper-V y Azure.   
**Agente de servicios de recuperación de Microsoft** | Controla la comunicación entre el servidor host de Hyper-V y el servicio Site Recovery | Instalado en el servidor host de Hyper-V | <p>Se utiliza cuando se configura la protección entre un sitio de Hyper-V y Azure.</p><p>Se descarga un único proveedor que incluye tanto el Proveedor de Azure Site Recovery para Hyper-V como el agente de servicios de recuperación de Microsoft.</p>
**Servidor de proceso/Servidor de proceso de conmutación por recuperación** | <p>Optimiza los datos de máquinas VMware protegidas o de un servidor físico Windows o Linux antes de enviarlo al servidor de destino principal en Azure.</p><p>Realiza la instalación de inserción de cliente del servicio de movilidad en máquinas virtuales de VMware o en servidores físicos.</p><p>Realiza la detección automática de las máquinas virtuales de VMware.</p> <p>Servidor de proceso de conmutación por recuperación: solo el primer punto para la optimización de datos antes de que la replicación sea aplicable al servidor de proceso de conmutación por recuperación.</p> | <p>Instalado en un servidor local que ejecute al menos Windows Server 2012 R2.</p><p>Servidor de proceso de conmutación por recuperación: se ejecuta en una máquina virtual estándar de Azure de tamaño A4.</p> | <p>Se utiliza cuando se configura la protección entre un servidor físico local o máquinas virtuales de VMware y Azure.</p><p>Servidor de proceso de conmutación por recuperación: se utiliza para la conmutación por recuperación desde Azure a equipos locales.</p>
**Servicio de movilidad** | Captura los cambios en las máquinas protegidas y se los comunica al servidor de proceso local para replicarlos en Azure. | Se instala en las máquinas virtuales de VMware locales o en los servidores físicos que se desea proteger.| Se utiliza cuando se configura la protección entre un servidor físico local o máquinas virtuales de VMware, y Azure.
**Servidor de destino principal/servidor de destino principal de conmutación por recuperación** | <p>Contiene los datos replicados de las máquinas protegidas usando VHD adjuntos creados en el almacenamiento de blobs de su cuenta de almacenamiento de Azure.</p><p>Servidor de destino principal de conmutación por recuperación: contiene los datos de replicación de máquinas virtuales conmutadas por error a Azure. Los datos se guardan en archivos VMDK creados en el almacén de datos que esté seleccionado cuando se habilita la replicación inversa para la conmutación por recuperación.</p> | <p>Se instala como una máquina virtual de Azure como un servidor de Windows basado en una imagen de galería de Windows Server 2012 R2 (para proteger las máquinas Windows) o como un servidor Linux basado en una imagen de galería de OpenLogic CentOS 6.6 (para proteger las máquinas Linux).</p><p>Hay disponibles dos opciones de tamaño: A3 estándar y D14 estándar.</p><p>Servidor maestro de destino de conmutación por recuperación: se ejecuta en una máquina virtual de VMware. Se aprovisiona en el mismo host en el que se recuperará la máquina en caso de error.</p>| <p>Se utiliza cuando se configura la protección entre un servidor físico local o máquinas virtuales de VMware, y Azure.</p><p>Servidor de destino principal de conmutación por recuperación: se utiliza para la conmutación por recuperación de máquinas virtuales desde Azure a equipos locales.</p>
**Servidor de configuración** | <p>Coordina la comunicación entre máquinas protegidas, el servidor de proceso y los servidores de destino principales en Azure.</p><p>Configura la replicación y coordina la recuperación en Azure cuando se produce una conmutación por error.</p> | Se instala en una máquina virtual A3 estándar de Azure en la misma suscripción de Azure donde está Recuperación del sitio. | Se utiliza cuando se configura la protección entre un servidor físico local o máquinas virtuales de VMware, y Azure.


## Planificación de la implementación de componentes

### Proveedor de Azure Site Recovery

El proveedor se ejecuta en los servidores VMM, en los servidores host de Hyper-V si no tiene un servidor VMM en su implementación, o en un servidor de configuración. Se conecta al servicio de recuperación del sitio a través de Internet mediante una conexión HTTPS cifrada. Observe lo siguiente:

- No es necesario agregar una excepción específica de firewall para conectar el Proveedor con el servicio de recuperación del sitio.
- Si desea que el servidor en el que se ejecuta el proveedor se conecte a Internet a través de un servidor proxy, puede usar la configuración de proxy existente o especificar un proxy personalizado.
- El proxy debe permitir el paso a estas direcciones a través del firewall:

	-  **.accesscontrol.windows.net
-  .backup.windowsazure.com
	-  **.blob.core.windows.net
-  **.store.core.windows.net
	
- Si tiene en el firewall con reglas basadas en direcciones IP, asegúrese de que permiten la comunicación desde el servidor de configuración a las direcciones IP indicadas en [Intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) y el protocolo HTTPS (443). Necesitará incluir en la lista blanca los intervalos de direcciones IP de la región de Azure que se va a utilizar y para el Oeste de EE.UU.
- Si está implementando Site Recovery con VMM y usa un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) automáticamente con las credenciales de proxy, especificadas en la configuración del proxy personalizada del portal de Site Recovery. Tendrá que configurar el servidor proxy para que esta cuenta pueda autenticarse correctamente.
- Si utiliza tráfico de proxy enviado desde el proveedor instalado en un servidor host de Hyper-V al servidor proxy, debe enviarse mediante HTTP.

### Agente de servicios de recuperación de Microsoft

El agente se conecta al servicio de recuperación del sitio a través de Internet mediante una conexión HTTPS cifrada. No se requieren excepciones de firewall específicas.

### Componentes para la protección de servidores de VMware o físicos

#### Servidor de destino principal

- El servidor de destino principal puede ser una máquina virtual A4 o D14 estándar de Azure.
- Con un destino principal A4 estándar se puede agregar 16 discos de datos (un máximo de 1023 GB por disco de datos) a cada máquina virtual.
- Con un destino principal D14 estándar se puede agregar 32 discos de datos (un máximo de 1023 GB por disco de datos) a cada máquina virtual.
- Solo se necesita un servidor de destino principal D14 tamaño estándar si se desea proteger un servidor que tenga más de 15 discos conectados; para todas las demás configuraciones se puede implementar servidores de destino principal de tamaño A4 estándar.
- Tenga en cuenta que un disco conectado al servidor de destino principal se reserva como unidad de retención. Azure Site Recovery permite definir ventanas de retención y recuperar máquinas protegidas desde un punto de recuperación incluido en dichas ventanas. La unidad de retención mantiene un diario de cambios del disco durante la duración de la ventana. Esto reduce el número máximo de discos disponible para la replicación a 15 en el tamaño A4, y a 31 en el tamaño D14.

#### Servidor de proceso 

- El servidor de proceso utiliza la caché basada en disco. Asegúrese de que hay suficiente espacio libre en C:/ para la memoria caché. El tamaño de caché se verá afectado por la tasa de cambio de datos de los equipos que se van a proteger. Por lo general, para las implementaciones de tamaño medio se recomienda un tamaño de directorio de memoria caché de 600 GB.
- Debe implementar un servidor de proceso adicional si la tasa de cambio de datos de los equipos protegidos supera la capacidad de un servidor de proceso existente.
- Para escalar la implementación, agregue varios servidores de proceso y servidores de destino principales. Debe implementar un segundo servidor de destino principal si no tiene suficientes discos disponibles en un servidor de destino principal existente.
-  Tenga en cuenta que los servidores de proceso y los servidores de destino principales no requieren la asignación uno a uno. Puede implementar el primer servidor de proceso con el segundo servidor de destino principal y así sucesivamente.

#### Servidor de configuración

- El servidor de configuración es una máquina virtual A3 estándar basada en una imagen de la galería de Windows Server 2012 R2 de Azure Site Recovery que se creará en su suscripción para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio en la nube con una dirección IP pública reservada.
- La ruta de instalación solo debe tener caracteres de inglés.

#### Servicio de movilidad

Instale en máquinas virtuales VMware o en servidores físicos. Las máquinas y los servidores deben cumplir los siguientes requisitos:

- **Servidores Windows**:
	-  Sistema operativo de 64 bits: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 como mínimo.
	-  El nombre de host, los puntos de montaje, los nombres de dispositivos y la ruta de acceso de sistema de Windows (p. ej.: C:\\Windows) solo deben tener caracteres de inglés.
	-  El sistema operativo se encuentra en la unidad C:\\.
	-  Solo se admiten discos básicos. No se admiten discos dinámicos.

- **Servidores Linux**:
	- Un sistema operativo de 64 bits admitido: Centos 6.4, 6.5, 6.6; Oracle Enterprise Linux 6.4, 6.5 que ejecute el kernel compatible de Red Hat o Unbreakable Enterprise Kernel versión 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.
	- Los archivos /etc/hosts de los equipos protegidos deben contener entradas que asignen el nombre de host local a las direcciones IP asociadas con todas las tarjetas NIC.
	- El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso al sistema Linux y los nombres de archivo (por ejemplo, /etc/, /usr) deben estar en inglés únicamente.
	-  Se admiten estos tipos de almacenamiento: sistema de archivos: EXT3, EXT4, ReiserFS, XFS/Software de múltiples rutas-Asignador de dispositivos (múltiples rutas)/Administrador de volúmenes: LVM2. No se admiten servidores físicos con almacenamiento con controlador HP CCISS.


Para obtener información detallada de la planeación acerca de estos componentes, consulte la sección de planeación de capacidad en [este artículo](site-recovery-vmware-to-azure.md).


## Mantenimiento de los componentes actualizados

**Componente** | **Cómo actualizar** 
--- | --- 
<p>**Proveedor de Azure Site Recovery para VMM **</p><p>**Agente de los servicios de recuperación de Azure**</p> | <p></p>**Instalación por primera vez**: descargue la versión más reciente de la página de Inicio rápido<p></p>**Continua**: puede descargar las versiones más recientes (y anteriores) desde el Panel de recuperación de sitio. O bien, si activa Microsoft Updates, se instalará automáticamente en el servidor la versión más reciente del Proveedor.
<p>**Servidor de proceso**</p><p>**Servidor de configuración**</p><p>**Servidor de destino principal**</p> | Compruebe si hay actualizaciones en el Panel de recuperación del sitio. 
**Servicio de movilidad** | <p>Asegúrese de que todos los equipos que desea proteger poseen las actualizaciones más recientes del servicio de movilidad:<p><p>Desde aquí puede descargar las actualizaciones más recientes:</p><p>[Windows](http://download.microsoft.com/download/7/C/7/7C70CA53-2D8E-4FE0-BD85-8F7A7A8FA163/Microsoft-ASR_UA_8.3.0.0_Windows_GA_03Jul2015_release.exe)</p><p>[RHELP6 64](http://download.microsoft.com/download/B/4/5/B45D1C8A-C287-4339-B60A-70F2C7EB6CFE/Microsoft-ASR_UA_8.3.0.0_RHEL6-64_GA_03Jul2015_release.tar.gz)</p><p>[OL6 64](http://download.microsoft.com/download/9/4/8/948A2D75-FC47-4DED-B2D7-DA4E28B9E339/Microsoft-ASR_UA_8.3.0.0_OL6-64_GA_03Jul2015_release.tar.gz)</p><p>[SLES11-SP3-64](http://download.microsoft.com/download/6/A/2/6A22BFCD-E978-41C5-957E-DACEBD43B353/Microsoft-ASR_UA_8.3.0.0_SLES11-SP3-64_GA_03Jul2015_release.tar.gz)</p><p>O bien, después de asegurarse de que el servidor de proceso está actualizado, puede descargar la versión más reciente del servicio de movilidad desde la carpeta C:\\pushinstallsvc\\repository del servidor de proceso.</p>  

## Pasos siguientes

Comience a configurar los componentes para el escenario de implementación. [Más información](site-recovery-overview.md).

<!---HONumber=August15_HO6-->