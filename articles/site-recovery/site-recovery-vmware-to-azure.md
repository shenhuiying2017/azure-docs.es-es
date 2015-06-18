<properties 
	pageTitle="Configuración de la protección entre las máquinas virtuales de VMWare locales o servidores físicos y Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas en servidores VMWare locales a Azure y entre servidores físicos locales y Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# Configuración de la protección entre las máquinas virtuales de VMWare locales o servidores físicos y Azure"

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores virtuales. Obtenga información acerca de los posibles escenarios de implementación posibles en [Información general de Azure Site Recovery](../hyper-v-recovery-manager-overview.md).

En este tutorial se describe cómo implementar la recuperación del sitio para:

- **Protección de las máquinas virtuales de VMWare locales en Azure**
- **Protección de servidores físicos de Windows y Linux locales en Azure**

Entre las ventajas de negocio se incluyen las siguientes:

- Protección de servidores físicos de Windows o Linux.
- Replicación sencilla, conmutación por error y recuperación mediante el portal de Azure Site Recovery.
- Replicación de datos a través de Internet, una conexión VPN de sitio a sitio o a través de ExpressRoute de Azure.   
- Conmutación por recuperación (restaurar) de Azure a una infraestructura de VMWare local. 
- Detección simplificada de máquinas virtuales de VMWare.
- Coherencia de múltiples máquinas virtuales para que las máquinas virtuales y los servidores físicos que ejecutan cargas de trabajo específicas se puedan recuperar juntos en un punto de datos coherente.
- Planes de recuperación para una conmutación por error simplificada y recuperación de las cargas de trabajo en niveles en varios equipos.

Esta característica está actualmente en versión de vista previa. Consulte los [Términos de uso complementarios para las versiones de vista previa](preview-supplemental-terms).

## Acerca de esta guía

La guía incluye información general requisitos previos de la implementación. Le guiará a través de la configuración de todos los componentes de implementación y de la habilitación de la protección para máquinas virtuales y servidores. Finaliza comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.

Si tiene problemas, envíe sus preguntas al [foro de Servicios de recuperación de Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

## Información general

En este diagrama se muestran los componentes de implementación.

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### Componentes de implementación

- **Máquinas locales**: el sitio local dispone de equipos que desea proteger. Pueden ser máquinas virtuales que se ejecutan en un hipervisor de VMWare o servidores físicos con Windows o Linux.

- **Servidor de proceso local**: los equipos protegidos envían datos de replicación al servidor de proceso local. El servidor de proceso realiza una serie de acciones en los datos. Los optimiza antes de enviarlos al servidor de destino principal de Azure. Dispone de una caché basada en disco para almacenar en caché los datos de replicación que recibe. También controla la instalación de inserción del servicio de movilidad que debe instalarse en cada máquina virtual o en un servidor físico que desea proteger, y realiza la detección automática de servidores VMWare vCenter. El servidor de proceso es un servidor virtual o físico que ejecuta Windows Server 2012 R2. Se recomienda que se ubique en la misa red y segmento LAN que los equipos que desea proteger, pero se puede ejecutar en una red diferente siempre que los equipos protegidos tengan visibilidad de red L3 en él. Durante la implementación podrá configurar el servidor de proceso y registrarlo en el servidor de configuración.

- **Almacén de Azure Site Recovery**: el almacén coordina y organiza la réplica de datos, la conmutación por error y la recuperación entre el sitio local y Azure.

- **Servidor de configuración de Azure**: el servidor de configuración coordina la comunicación entre máquinas protegidas, el servidor de proceso y los servidores de destino principales en Azure. Configura la replicación y coordina la recuperación en Azure cuando se produce la conmutación por error. El servidor de configuración se ejecuta en una máquina virtual A3 estándar de Azure en su suscripción de Azure. Durante la implementación podrá configurar el servidor y registrarlo en el almacén de Azure Site Recovery.

- **Servidor de destino principal**: el servidor de destino principal de Azure contiene los datos replicados de los equipos protegidos con VHD adjuntos creados en el almacenamiento de blobs de su cuenta de almacenamiento de Azure. Se implementa como una máquina virtual de Azure como un servidor de Windows basado en una galería de imágenes de Windows Server 2012 R2 (para proteger los equipos de Windows) o como un servidor Linux basado en una galería de imágenes de OpenLogic CentOS 6.6 (para proteger equipos Linux). Hay dos opciones de tamaño disponibles: A3 estándar y D14 estándar. El servidor está conectado a la misma red de Azure que el servidor de configuración. Durante la implementación creará el servidor y lo registrará en el servidor de configuración.

- **Servicio de movilidad **: instale el servicio de movilidad en cada máquina virtual de VMWare o servidor físico de Windows/Linux que desee proteger. El servicio envía los datos de replicación al servidor de proceso, que a su vez los envía al servidor de destino principal en Azure. El servidor de proceso puede instalar automáticamente el servicio de movilidad en los equipos protegidos, o puede implementar el servicio manualmente mediante el proceso de implementación de software interno.

- **Canal de comunicación y replicación de datos**: hay un par de opciones. Tenga en cuenta que ninguna opción requiere que se abra ningún puerto de red entrante en los equipos protegidos. Todas las comunicaciones de red se inician en el sitio local.
	- **A través de Internet**: se comunica y replica datos de servidores protegidos locales y Azure a través de una conexión a Internet pública segura. Esta es la opción predeterminada.
	- **VPN/ExpressRoute**: se comunica y replica datos entre servidores locales y Azure a través de una conexión VPN. Deberá configurar una VPN de sitio a sitio o una conexión [ExpressRoute](expressroute) entre el sitio local y la red Azure. 

 
## Planificación de capacidad

- Para un rendimiento óptimo y para aprovechar la característica de coherencia de múltiples máquinas virtuales que recupera varios equipos protegidos en un punto de datos coherente, se recomienda que reúna las máquinas virtuales en grupos de protección por carga de trabajo.
- No puede proteger un único equipo en varios servidores de destino principales porque cuando se replican los discos, se crea un disco duro virtual que refleja el tamaño del disco en el almacenamiento de blobs de Azure y se adjunta como un disco de datos al servidor de destino principal. Obviamente puede proteger varios equipos con un único servidor de destino principal.
- La máquina virtual del servidor de destino principal puede ser una máquina A4 o D14 estándar de Azure:
	- Con un disco A4 estándar puede agregar 16 discos de datos (un máximo de 1023 GB por disco de datos) a cada máquina virtual.
	- Con un disco D14 estándar puede agregar 32 discos de datos (un máximo de 1023 GB por disco de datos) a cada máquina virtual.
- Tenga en cuenta que un disco conectado al servidor de destino principal se reserva como unidad de retención. Azure Site Recovery permite definir ventanas de retención y recuperar equipos protegidos en cualquier momento dentro de esa ventana. La unidad de retención mantiene un diario de cambios del disco durante la duración de las ventanas.  Esto reduce el número máximo de discos para A4 a 15 y para D14 a 31. 
- Para escalar la implementación, agregue varios servidores de proceso y servidores de destino principales. Debe implementar un segundo servidor de destino principal si no tiene suficientes discos disponibles en un servidor de destino principal existente. Debe implementar un servidor de proceso adicional si la tasa de cambio de datos de los equipos protegidos supera la capacidad de un servidor de proceso existente. Tenga en cuenta que los servidores de proceso y los servidores de destino principales no requieren la asignación uno a uno. Puede implementar el primer servidor de proceso con el segundo servidor de destino principal y así sucesivamente.
El servidor de proceso utiliza la caché basada en disco. Asegúrese de que hay suficiente espacio libre en C:/ para la memoria caché. El tamaño de caché se verá afectado por la tasa de cambio de datos de los equipos que se van a proteger. Por lo general se recomienda un tamaño de directorio de caché de 600 GB para las implementaciones de tamaño medio, pero puede utilizar las siguientes directrices.

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## Antes de comenzar

### Requisitos previos de Azure

- Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [versión de evaluación gratuita](http://aka.ms/try-azure). 
- Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. Para obtener más información, consulte [Introducción al almacenamiento de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=398704).
- Necesitará una red virtual en la que se implementarán el servidor de configuración y el servidor de destino principal. Debe estar en la misma región y suscripción que el almacén de Azure Site Recovery.
- Asegúrese de que tiene recursos de Azure suficientes para implementar todos los componentes. Obtenga más información en [Límites de suscripción de Azure](azure-subscription-service-limits).
- Compruebe que los equipos que desea proteger cumplen con los requisitos de la máquina virtual de Azure. 

	- **Recuento de disco**: se admite un máximo de 31 discos en un único servidor protegido.
	- **Tamaños de disco**: la capacidad del disco Individual no debe ser superior a 1023 GB
	- **Agrupación en clústeres**: no se admiten los servidores en clúster.
	- **Arranque**: no se admite el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI).
	- **Volúmenes**: no se admiten los volúmenes cifrados de Bitlocker.
	- **Nombres de servidor**: los nombres deben contener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de proteger un equipo, puede modificar el nombre de Azure.	

	Obtenga más información sobre los requisitos de Azure en [Compatibilidad de máquina virtual](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Requisitos previos de los componentes del escenario

- Servidor de proceso:
	- Puede implementar el servidor de proceso en la máquina virtual o física que ejecuta Windows Server 2012 R2 con las actualizaciones más recientes. Instálelo en la unidad C:/.
	- El servidor necesita un mínimo de 8 núcleos de procesador y se recomienda 64 GB de RAM y 300 GB de espacio libre en la unidad C:.
	- Se recomienda que ubique el servidor en la misma red y subred que los equipos que desea proteger.
	- Instale VMware vSphere CLI 5.1 en el servidor para que pueda realizar la detección automática de los servidores VMWare vCenter.
- La ruta de instalación del servidor de configuración, el servidor de destino principal, el servidor de proceso y los servidores de conmutación por recuperación solo deben contener caracteres ingleses. Por ejemplo, la ruta debe ser **/usr/local/ASR** para un servidor de destino principal que ejecuta Linux.

### Requisitos previos de VMWare

- Un servidor VMWare vCenter que administra los hipervisores de VMware vSphere. Debe ejecutar vCenter versión 5.0 o posterior con las actualizaciones más recientes. 
- Uno o varios de los hipervisores de vSphere contienen las máquinas virtuales de VMWare que desea proteger. El hipervisor debe estar ejecutando ESX/ESXi versión 5.0 o posterior con las actualizaciones más recientes.
- Las máquinas virtuales de VMWare detectadas a través de un servidor vCenter deben tener las herramientas de VMware instaladas y en ejecución.

### Requisitos previos equipos Windows protegidos

Los servidores físicos protegidos o las máquinas virtuales VMWare con Windows deben disponer de lo siguiente:

- Un sistema operativo de 64 bits compatible: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 al menos.
- El nombre de host, los puntos de montaje, los nombres de dispositivo, la ruta de acceso de sistema de Windows (p. ej.: C:\Windows) deben estar en inglés únicamente.
- El sistema operativo debe instalarse en la unidad C:.
- Se admiten las opciones de almacenamiento normales para servidores Windows.
- Las reglas de firewall en los equipos protegidos deben permitirles llegar a la configuración y a los servidores de destino principales de Azure. 
- Después de la conmutación por error, si desea conectarse a máquinas virtuales de Microsoft Azure con Escritorio remoto, asegúrese de que Escritorio remoto está habilitado en el equipo local. Si no se conecta a través de las reglas de firewall de VPN, debe permitir las conexiones de escritorio remoto a través de Internet.

### Requisitos previos de equipos Linux protegidos

Los servidores físicos protegidos o las máquinas virtuales VMWare con Linux deben disponer de lo siguiente:

- Un sistema operativo compatible: CentOS 6.4, 6.5, 6.6 (32 o 64 bits); Oracle Enterprise Linux 6.4, 6.5 (32 o 64 bits) que ejecutan el kernel compatible de Red Hat o Unbreakable Enterprise Kern versión 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 (32 bits o 64 bits)
- El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/, /usr) deben estar en inglés únicamente.
- La protección puede habilitarse para los equipos locales con el almacenamiento siguiente:
	- Sistema de archivos: EXT3, ETX4, ReiserFS, XFS
	- Software de múltiples rutas: Asignador de dispositivo - múltiples rutas
	- Administrador de volúmenes: LVM2
	- No se admiten servidores físicos con almacenamiento de controlador HP CCISS.
- Las reglas de firewall en los equipos protegidos deben permitirles llegar a la configuración y a los servidores de destino principales de Azure.
- Si desea conectarse a la máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente Secure Shell (ssh), asegúrese de que el servicio de Secure Shell en el equipo protegido está configurado para iniciarse automáticamente en el arranque del sistema y que las reglas de firewall permiten un ssh conexión a ella.  

### Requisitos previos de terceros

Algunos componentes de implementación de este escenario dependen de software de terceros para poder funcionar correctamente. Para obtener una lista completa, consulte <a href="#thirdparty">Avisos e información de software de terceros</a>. 

## Paso 1: Creación de un almacén

1. Inicie sesión en el [Portal de administración](https://portal.azure.com).


2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Almacén de recuperación del sitio**.


3. Haga clic en **Crear nuevo** > **Creación rápida**.
	
4. En **Name**, escriba un nombre descriptivo para identificar el almacén.

5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](href="http://go.microsoft.com/fwlink/?LinkId=389880)

6. Haga clic en **Crear almacén**. 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de **Servicios de recuperación**.

## Paso 2: Implementación de un servidor de configuración


1. En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. En la lista desplegable, seleccione **Entre un sitio local con VMware/servidores físicos y Azure**.
3. En **Preparar los recursos de destino** haga clic en **Implementar el servidor de configuración**.

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. Especifique los detalles del servidor de configuración y las credenciales para conectarse al servidor. Seleccione la red en la que se debe encontrar el servidor. Especifique dirección IP interna y la subred para asignar al servidor. Al hacer clic en **Aceptar** se crea en su suscripción una máquina de virtual A3 estándar basada en una imagen de la galería de Windows Server 2012 R2 de Azure Site Recovery para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio en la nube con una dirección IP pública reservada.

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. Puede supervisar el progreso en la pestaña **Trabajos**.

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  Después de implementar el servidor de configuración, tome nota de la dirección IP pública asignada a él en la página **Máquinas virtuales** del portal de Azure. Después en la pestaña **Extremos**, tome nota del puerto HTTPS asignado al puerto privado 443. Necesitará esta información más adelante al registrar el destino principal y los servidores de proceso con el servidor de configuración. El servidor de configuración se implementa con 4 extremos públicos:

	- 443. El canal HTTPS se usa para coordinar la comunicación entre los servidores de componentes y Azure.
	- 9443. Se utiliza para la herramienta de conmutación por recuperación y de comunicación de conmutación por recuperación.
	- Escritorio remoto
	- PowerShell


## Paso 3: Registro del servidor de configuración en el almacén

1. En **Preparar los recursos de destino**, haga clic en **Descargar una clave de registro**. El archivo de clave se genera automáticamente. Es válido durante 5 días a partir del momento en que se genera. Copie el archivo en el servidor de configuración.
2. En la página **Panel** de la máquina virtual haga clic en **Conectar**. Utilice el archivo RDP descargado para iniciar sesión o el servidor de configuración con Escritorio remoto.  Al iniciar sesión por primera vez, se ejecuta automáticamente el Asistente para la instalación y registro de Azure Site Recovery.

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. Siga las instrucciones del asistente. Deberá descargar e instalar MySQL Server y especificar las credenciales. En la página **Registro de Azure Site Recovery**, busque el archivo de clave que ha copiado en el servidor.

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. Se genera una frase de contraseña cuando finaliza el registro. Cópiela en una ubicación segura. La necesitará para autenticarse y registrar los servidores de destino principales y de proceso con el servidor de configuración. También se utiliza para garantizar la integridad del canal en las comunicaciones del servidor de configuración. Puede volver a generar la frase de contraseña pero, a continuación, deberá volver a registrar el destino principal y los servidores de proceso mediante la nueva frase de contraseña.

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

Después de registrar el servidor de configuración, se mostrará en la página **Servidores de configuración** del almacén.

## Paso 4: Configuración de una conexión VPN
 
Puede conectarse al servidor de configuración a través de internet o mediante una VPN o una conexión ExpressRoute. Una conexión a Internet utiliza los extremos de la máquina virtual junto con la dirección IP virtual pública del servidor. La VPN utiliza la dirección IP interna del servidor junto con los puertos privados del extremo.
 
Puede configurar una conexión VPN al servidor de la forma siguiente:

1. Si no dispone de una conexión sitio a sitio o de ExpressRoute de Azure configurada, se puede obtener más información aquí:

	- [ExpressRoute o VPN - Qué es más adecuado](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/)
	- [Configuración de una conexión sitio a sitio en una máquina virtual de Azure](https://msdn.microsoft.com/library/azure/dn133795.aspx)
	- [Configuración de ExpressRoute](https://msdn.microsoft.com/library/azure/dn606306.aspx) 
	
2. En el almacén, haga clic en **Servidores** > **Servidores de configuración** > Servidor de configuración > **Configurar**.
3. En **Configuración de conectividad** establezca **Tipo de conectividad** en **VPN**. Tenga en cuenta que si ha configurado la VPN y no tiene acceso a Internet desde el sitio local, asegúrese de seleccionar la opción VPN. Si no lo hace, el servidor de proceso no podrá enviar datos de replicación al servidor de destino principal en sus extremos públicos.

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## Paso 5: Implementación del servidor de destino principal

1. En **Preparar los recursos de destino**, haga clic en **Implementar el servidor de destino principal**.
2. Especifique las credenciales y los detalles del servidor de destino principal. El servidor se implementará en la misma red de Azure que el servidor de configuración al que se ha registrado. Al hacer clic para completar una máquina virtual de Azure, se creará con una imagen de galería de Windows o Linux. 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Se crea una máquina virtual de servidor principal de Windows con estos extremos TCP públicos:

	- Personalizada: El puerto público se utiliza para enviar datos de replicación a través de Internet. El puerto privado 9443 lo utiliza el servidor de proceso para enviar datos al servidor de destino principal a través de VPN.
	- Custom1: El puerto privado 9080 lo utiliza el servidor de proceso para enviar datos al servidor de destino a través de VPN.
	- PowerShell: Puerto privado: 5986
	- Escritorio remoto: Puerto privado: 3389

4. Se crea una máquina virtual del servidor principal Linux con estos extremos:

	- Personalizada: El puerto público se utiliza para enviar datos de replicación a través de Internet. El puerto privado 9443 lo utiliza el servidor de proceso para enviar datos al servidor de destino principal a través de VPN.
	- Custom1: El puerto privado 9080 lo utiliza el servidor de proceso para enviar datos al servidor de destino principal a través de VPN.
	- SSH: Puerto privado 22

5. En **Máquinas virtuales** espere a que se inicie la máquina virtual. 

	- Si ha configurado el servidor con Windows, tome nota de los detalles del escritorio remoto.
	- Si lo ha configurado con Linux y se está conectando a través de VPN, anote la dirección IP interna de la máquina virtual. Si se conecta a través de Internet, anote la dirección IP pública.
6.  Inicie sesión en el servidor para completar la instalación y registrarlo con el servidor de configuración. Si está ejecutando Windows:

	1. Inicie una conexión de escritorio remoto con la máquina virtual. La primera vez que inicie sesión en un script se ejecutará en una ventana de PowerShell. No la cierre. Cuando haya terminado, se abre automáticamente la herramienta de configuración de agente de host para registrar el servidor.
	2. En **Configuración de agente de host** especifique la dirección IP interna del servidor de configuración y el puerto 443. Puede utilizar la dirección interna y el puerto privado 443, incluso si no se conecta en modo VPN porque la máquina virtual está conectada a la misma red de Azure que el servidor de configuración. Deje **Usar HTTPS** habilitado. Escriba la frase de contraseña del servidor de configuración que anotó anteriormente. Haga clic en **Aceptar** para registrar el servidor. Tenga en cuenta que puede ignorar las opciones de NAT en la página. No se usan.

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Si ejecuta Linux:
	1. Copie el [archivo tar del instalador del servidor](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409) a la máquina virtual mediante un cliente de sftp. También puede iniciar sesión y utilizar wget para descargar el archivo desde el vínculo de la página Inicio rápido. 
	2. Inicie sesión en el servidor mediante un cliente de Secure Shell. Tenga en cuenta que si está conectado a la red de Azure a través de VPN, utilice la dirección IP interna. En caso contrario, utilice la dirección IP externa y el extremo público SSH.
	3. Extraiga los archivos del instalador comprimido con gzip ejecutando: **tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***."   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. Asegúrese de que se encuentra en el directorio en el que extrajo el contenido del archivo tar y ejecute el comando "**sudo./install.sh**". Seleccione la opción **2. Destino principal**. Deje las demás opciones con la configuración predeterminada.

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. Después de que haya finalizado la instalación, se muestra la línea de comandos **Interfaz de configuración de host**. No cambie el tamaño de la ventana. 
	6. Utilice las teclas de dirección para seleccionar **Global** y presione Entrar.
	7. En **Escriba la dirección IP**, especifique la dirección interna del servidor de configuración y el puerto 22.
	8.  Especifique la frase de contraseña del servidor de configuración que anotó anteriormente y presione Entrar. Seleccione **Salir** para finalizar la instalación. Tenga en cuenta que si utiliza un cliente PuTTY para ssh, en la máquina virtual puede usar Mayús+Insertar para pegar. 
	9.  Utilice la tecla de flecha derecha para salir y presione Entrar.

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. Espere unos minutos (de 5 a 10) y en la página **Servidores** > **Servidores de configuración** compruebe que el servidor de destino principal se muestra como registrado en la pestaña **Detalles del servidor**. Si ejecuta Linux y no se ha registrado, vuelva a ejecutar la herramienta de configuración de host en /usr/local/ASR/Vx/bin/hostconfigcli. Deberá establecer los permisos de acceso mediante la ejecución de chmod como superusuario.

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## Paso 6: Implementación de un servidor de proceso local

1. Haga clic en Inicio rápido > **Instalar el servidor de proceso local** > **Descargar e instale el servidor de proceso**.

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. Copie el archivo comprimido descargado en el servidor en el que va a instalar al servidor de proceso. El archivo comprimido contiene dos archivos de instalación:

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. Descomprima el archivo y copie los archivos de instalación en una ubicación en el servidor.
4. Ejecute el archivo de instalación **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** y siga las instrucciones. Se instalan los componentes de terceros necesarios para la implementación.
5. A continuación, ejecute **Microsoft-ASR_CX_8.2.0.0_Windows***.
6. En la página **Modo servidor** seleccione **Servidor de proceso**.
7.	En **Detalles del servidor de configuración** si se conecta a través de VPN, especifique la dirección IP interna del servidor de configuración y 443 para el puerto. De lo contrario, especifique la dirección IP virtual pública y el extremo HTTP público asignado.
8.	Desactive **Comprobar firma de software del servicio de movilidad** si desea deshabilitar la comprobación cuando utiliza la inserción automática para instalar el servicio. La comprobación de la firma necesita conectividad a Internet en el servidor de proceso.
9.	Escriba la frase de contraseña del servidor de configuración.

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. Finalice la instalación del servidor. Recuerde que necesitará instalar VMware vSphere CLI 5.1 en el servidor para poder detectar servidores vCenter.

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

Valide que el servidor de proceso se ha registrado correctamente en el almacén > **Servidor de configuración** > **Detalles del servidor**.

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Tenga en cuenta que si no deshabilita la comprobación de firmas para el servicio de movilidad al registrar el servidor de proceso, puede hacerlo más adelante de la forma siguiente:

1. Inicie sesión en el servidor de proceso como administrador y abra el archivo C:\pushinstallsvc\pushinstaller.conf para modificarlo. En la sección **[PushInstaller.transport]** agregue esta línea: **SignatureVerificationChecks="0"**. Guarde y cierre el archivo.
1. Reinicie el servicio InMage PushInstall.


## Paso 7: Incorporación de servidores vCenter

1. En la pestaña **Servidores** > **Servidores de configuración**, seleccione el servidor de configuración y haga clic para agregar un servidor vCenter.

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Especifique los detalles para el servidor de vCenter y seleccione el servidor de proceso que se usará para detectarlo.  El servidor de proceso debe estar en la misma red que el servidor de vCenter y debe tener instalado VMware vSphere CLI 5.1.
3. Una vez completada la detección del servidor de vCenter, se mostrará en los detalles de configuración del servidor.
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## Paso 8: Creación de un grupo de protección

1. Abra **Elementos protegidos** > **Grupo de protección** y haga clic para agregar un grupo de protección.

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. En la página **Especificar la configuración del grupo de protección** especifique un nombre para el grupo y seleccione el servidor de configuración en el que desea crear el grupo.

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. En la página **Especificar la configuración de replicación** configure las opciones de replicación que se utilizarán en todos los equipos del grupo.   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Configuración:
	- **Coherencia de múltiples máquinas virtuales**: Si lo activa, se crean puntos de recuperación coherentes con las aplicaciones compartidas en los equipos del grupo de protección. Esta configuración es muy importante cuando todos los equipos del grupo de protección ejecutan la misma carga de trabajo. Se recuperarán todos los equipos en el mismo punto de datos. Solo disponible para servidores Windows Server. 
	- **Umbral RPO**: Se generan alertas cuando el RPO de replicación de protección de datos continua supera el valor de umbral RPO configurado.
	- **Retención de punto de recuperación**: Especifica el período de retención. Los equipos protegidos se pueden recuperar en cualquier punto dentro de este período.
	- **Frecuencia de las instantáneas coherentes con la aplicación**: Especifica la frecuencia con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación.

Puede supervisar el grupo de protección cuando se crean en la página **Elementos protegidos**. 

## Paso 9: Inserción del servicio de movilidad

Al agregar equipos a un grupo de protección, el servicio de movilidad se inserta automáticamente y el servidor de proceso lo instala en cada equipo. Si desea usar este mecanismo de inserción automática para equipos protegidos con Windows, deberá hacer lo siguiente en cada equipo:

1. Configurar el firewall de Windows para permitir el **uso compartido de archivos e impresoras** y el **Instrumental de administración de Windows**. Para los equipos que pertenecen a un dominio puede configurar la directiva de firewall con un GPO.
2. La cuenta utilizada para realizar la instalación de inserción debe encontrarse en el grupo de administradores en el equipo que desea proteger. Tenga en cuenta que estas credenciales únicamente se utilizan para la instalación de inserción. El servicio de movilidad no las almacena en ningún lugar y se descartan cuando el servidor está protegido. Podrá proporcionar estas credenciales cuando se agrega un equipo a un grupo de protección.

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. Si la cuenta de administrador no es una cuenta de dominio, deberá deshabilitar el control de acceso de usuarios remotos en el equipo local. Para hacerlo en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System cree la entrada LocalAccountTokenFilterPolicy si no existe y asígnele un valor DWORD. 

Si desea proteger los equipos que ejecutan Linux, deberá hacer lo siguiente:

1. Asegúrese de que la cuenta es un usuario raíz en el servidor Linux de origen.
1. Instale los paquetes openssh, openssh-server, openssl más recientes en el equipo que desea proteger.
1. Habilite el puerto 22 de ssh.
2. Habilite la autenticación de la contraseña y del subsistema Sftp en el archivo de configuración sshd:
	1. Inicie sesión en la cuenta de usuario raíz.
	2. En el archivo /etc/ssh/sshd_config guarde la línea que comienza con "PasswordAuthentication". Quite el comentario de la línea y cambie el valor de "no" a "yes". 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. Busque la línea que comienza con "Subsistema" y quite el comentario. 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## Paso 10: Incorporación de los equipos a un grupo de protección

1. Abra la pestaña **Elementos protegidos** > **Grupo de protección** > **Equipos** y agregue equipos físicos o virtuales administrados por un servidor de vCenter detectado. Se recomienda que los grupos de protección deben reflejar las cargas de trabajo para que puedan agregar los equipos que ejecutan una aplicación específica al mismo grupo.

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

2. En la página **Seleccionar máquinas virtuales** de **Agregar máquina virtual**, seleccione un servidor V-Center y después seleccione los equipos en él.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. Al agregar equipos a un grupo de protección, el servicio de movilidad se instala automáticamente desde el servidor de proceso local. Asegúrese de que ha configurado los equipos protegidos tal como se ha descrito en el paso anterior para que el mecanismo de inserción automática funcione.
4. En **Seleccionar máquinas virtuales** seleccione el servidor de vCenter que administre los equipos que desea proteger y, a continuación, seleccione las máquinas virtuales.

4. Seleccione los servidores y el almacenamiento que se utilizará para la replicación. 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. Proporcione las credenciales de usuario del servidor de origen. Es necesario para instalar automáticamente el servicio de movilidad en los equipos de origen. En Windows Server, la cuenta debe tener privilegios de administrador en el servidor de origen. En Linux, la cuenta debe tener privilegios de superusuario en el servidor.

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Haga clic en la marca de verificación para terminar de agregar equipos al grupo de protección y para iniciar la replicación inicial en cada equipo. Puede supervisar el estado en la página **Trabajos**.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. Además, haga clic en **Elementos protegidos** > <grupo de protección> > **Máquinas virtuales** para supervisar el estado de protección. Cuando se completa la replicación inicial y las máquinas están sincronizando datos, mostrarán el estado **protegido**.

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## Paso 11: Establecimiento de propiedades del equipo protegido

1. Cuando los equipos tienen el estado **protegido**, puede configurar sus propiedades de conmutación por error. En los detalles del grupo de protección, seleccione el equipo y abra la pestaña **Configurar**.
2. Puede modificar el nombre que se asignará al equipo de Azure después de la conmutación por error así como el tamaño de Azure. También puede seleccionar la red de Azure a la que se conectará el equipo después de la conmutación por error. Observe lo siguiente:

	- El nombre del equipo de Azure debe cumplir los requisitos de Azure descritos en los requisitos previos.
	- De forma predeterminada, las máquinas virtuales replicadas en Azure no están conectadas a una red de Azure. Si desea que se comuniquen las máquinas virtuales, asegúrese de establecer la misma red de Azure para ellas.

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## Paso 12: Ejecución de la conmutación por error

1. En la página **Planes de recuperación** agregue un plan de recuperación. Especifique los detalles del plan y seleccione **Azure** como destino.

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. En **Seleccionar máquina virtual** seleccione un grupo de protección y, a continuación, seleccione los equipos del grupo que se van a agregar al plan de recuperación.

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Si es necesario, puede personalizar el plan para crear grupos y organizar el orden en el los equipos del plan de recuperación se van a conmutar por error. También puede agregar avisos para las acciones manuales y los scripts.

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. En la página **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
6. En **Confirmar conmutación por error** compruebe la dirección de la conmutación por error (en Azure) y seleccione el punto de recuperación en el que se va a realizar la conmutación. 
7. Espere a que el trabajo de conmutación por error se complete y, a continuación, compruebe que la conmutación por error funciona de la forma prevista y que las máquinas virtuales replicadas se inician correctamente en Azure.

<a name="thirdparty"></a><h2>AVISOS E INFORMACIÓN DE SOFTWARE DE TERCEROS</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49--> 