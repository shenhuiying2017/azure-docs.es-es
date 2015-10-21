<properties 
   pageTitle="Pasos para la conmutación por recuperación de Azure a VMware" 
   description="Este artículo describe cómo se pueden usar Azure Site Recovery y la herramienta vContinuum utiliza para conmutar por recuperación una máquina virtual a VMware." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/07/2015"
   ms.author="ruturajd@microsoft.com"/>

# Pasos para la conmutación por recuperación de Azure a VMware

Este documento le guiará por los pasos necesarios para conmutar por recuperación de Azure a un sitio de VMware. Es preciso haber seguido los pasos indicados en el tutorial [Configuración de la protección entre las máquinas virtuales de VMWare locales o servidores físicos y Azure](site-recovery-vmware-to-azure.md).

Después de una conmutación por error a Azure correcta, las máquinas virtuales estarán disponibles en la pestaña de máquinas virtuales. Si decide conmutar por recuperación, a continuación encontrará los pasos que debe seguir.

Tenga en cuenta que cuando conmuta por recuperación de Azure a un sitio de VMware, la recuperación sólo puede realizarse en una máquina virtual. Aunque el origen inicial en VMware fuera una máquina física, el proceso de conmutación por error a Azure seguido de una conmutación por recuperación a VMware la convertirá en una máquina virtual.

## Información general

1.  Instalación local de un servidor de vContinuum

    a. Configuración para que apunte al servidor de configuración

2.  Implementación de un servidor de proceso en Azure

3.  Instalación de un destino maestro local

4.  Pasos para proteger las máquinas virtuales locales conmutadas por error

    a. Consideraciones de configuración

5.  Supervisión de la protección de máquinas virtuales en local

6.  Conmutación por error de las máquinas virtuales en local

A continuación encontrará información general sobre la configuración que se logra con los pasos siguientes. Una parte de esta configuración ya se ha completado durante la conmutación por error.

-   Las líneas azules son las conexiones utilizadas durante la conmutación por error.

-   Las líneas rojas son las conexiones utilizadas durante la conmutación por recuperación.

-   Las líneas con flechas van a través de Internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Instalación local de vContinuum

El programa de instalación de vContinuum estará disponible en la [ubicación de descarga](http://go.microsoft.com/fwlink/?linkid=526305). Además, instale la revisión que se proporciona aquí, en vContinuum (disponible en la [ubicación de descarga ](http://go.microsoft.com/fwlink/?LinkID=533813)).

1.  Inicie el programa de instalación para comenzar la instalación de vContinuum. Haga clic en **Siguiente**. ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
2.  Especifique tanto la dirección IP como el puerto del servidor de CX. Seleccione HTTPS.

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

3.  Para detectar la dirección IP de CX, vaya a la implementación de CS en Azure y vea el panel correspondiente.

	![](./media/site-recovery-failback-azure-to-vmware/image4.png)

4.  Para detectar el puerto público de CX, vaya a la pestaña de extremos en la página de la máquina virtual e identifique el puerto público de extremos HTTP.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

5.  Especifique la frase de contraseña del servidor de configuración. Deberá haber anotado la frase de contraseña durante el registro del servidor de configuración. La frase de contraseña también se usa en las implementaciones de un destino maestro y un servidor de proceso. Si no recuerda la frase de contraseña, puede entrar en el servidor de configuración en Azure y buscar la frase de contraseña almacenada en C:\\Archivos de programa (x86)\\InMage Systems\\private\\connection.passphrase

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

6.  Especifique la ubicación para instalar el servidor de vContinuum y comience la instalación.

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

7.  Una vez completada la instalación, puede iniciar vContinuum para ver su funcionamiento. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Instalación de un servidor de proceso en Azure

Para que las máquinas virtuales de Azure puedan devolver los datos a un destino maestro local, es preciso instalar un servidor de proceso en Azure. El servidor de proceso debe implementarse en Azure en la misma red que el servidor de configuración.

1.  En la página **Servidores de configuración** de Azure, seleccione la opción para agregar un nuevo servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especifique un nombre de servidor de procesos y escriba un nombre y una contraseña para conectarse a la máquina virtual como administrador. Seleccione el servidor de configuración en el que está registrando el servidor de procesos. Debe ser el mismo servidor que use para proteger y conmutar por error las máquinas virtuales. Especifique la red de Azure en la que debe implementarse el servidor de procesos. Debe ser la misma red que el servidor de configuración. Especifique una dirección IP única de la subred seleccionada y comience la implementación.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)


Se desencadenará un trabajo para implementar el servidor de procesos.

![](./media/site-recovery-failback-azure-to-vmware/image11.png)

Una vez que el servidor de procesos se implemente en Azure, podrá iniciar sesión en el servidor con las credenciales que especificó. Utilice los mismos pasos que usó en el reenvío de la dirección de protección para registrar el servidor de proceso.

![](./media/site-recovery-failback-azure-to-vmware/image12.png)

Los servidores registrados durante la conmutación por recuperación no se verán en las propiedades de la máquina virtual. Solo se verán en la pestaña Servidores del servidor de configuración en que se han registrado. El servidor de proceso puede tardar unos 10-15 minutos en aparecer en el servidor de configuración.

## Instalación de un servidor de destino maestro local

Dependiendo de las máquinas virtuales de origen necesitará instalar un servidor de destino maestro con Linux o con Windows local.

### Implementación de un destino maestro de Windows

El programa de instalación de vContinuum incluye un destino maestro de Windows. Al instalar vContinuum, se implementa un destino maestro en la misma máquina y se registra en el servidor de configuración.

1.  Para comenzar la implementación, cree una máquina vacía local en el host ESX en el que desea recuperar las máquinas virtuales de Azure.

2.  Asegúrese de que hay al menos dos discos conectados a la máquina virtual: uno se utiliza para el sistema operativo y el otra se usa para el disco de retención.

3.  Instale el sistema operativo.

4.  Instale vContinuum en el servidor. Así finalizaría también la instalación del destino maestro.

### Implementación de un destino maestro en Linux

1.  Para comenzar la implementación, cree una máquina vacía local en el host ESX en el que desea recuperar las máquinas virtuales de Azure.

2.  Asegúrese de que hay al menos dos discos conectados a la máquina virtual: uno se utiliza para el sistema operativo y el otra se usa para el disco de retención.

3.  Instale el sistema operativo Linux. El sistema de destino maestro de Linux (MT) no debe usar LVM para los espacios de almacenamiento raíz o de retención. Destino maestro de Linux configurado para evitar la detección de discos y particiones LVM de forma predeterminada.
4.  Estas son las particiones que puede crear

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Antes de comenzar la instalación de un destino maestro realice los siguientes pasos posteriores a la instalación.


#### Pasos posteriores a la instalación del sistema operativo:

Para obtener los Id. de SCSI de los discos duros SCSI de en una máquina virtual con Linux, debe habilitar el parámetro "disk. EnableUUID = TRUE ". Para habilitar este parámetro, siga los pasos que se indican a continuación:

1. Apague la máquina virtual.
2. Haga clic con el botón derecho en la entrada de la máquina virtual, en el panel izquierdo > **Editar configuración**.
3. Haga clic en la pestaña **Opciones**. Seleccione el elemento **Opciones avanzadas>General** a la izquierda y haga clic en **Parámetros de configuración** a la parte derecha. Cuando la máquina esté en ejecución, la opción "Parámetros de configuración" estará en estado desactivado". Para activar esta pestaña, apague la máquina.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. ¿Vea si existe una fila con **disk.EnableUUID**? Si existe y si el valor se establece en False sobrescriba el valor con True (los valores True y False no distinguen entre mayúsculas y minúsculas). Si existe y está establecida en True, haga clic en Cancelar y pruebe el comando SCSI en el sistema operativo invitado después de que haya arrancado. Si no existe, haga clic **Agregar fila.**
5. Agregue disk.EnableUUID en la columna Nombre. Establezca su valor en TRUE. No agregue los valores anteriores entre comillas dobles.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Descarga e instalación de los paquetes adicionales

Nota: asegúrese de que el sistema tiene conectividad a Internet antes de descargar e instalar los paquetes adicionales.

#yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

El comando anterior descargará los quince paquetes siguientes del repositorio de CentOS 6.6 y los instalará.

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

Nota: si la máquina de origen utiliza los sistemas de archivos Reiser o XFS para el dispositivo raíz o de inicio, los siguientes paquetes deben descargarse e instalarse en el sistema de destino maestro Linux antes de la protección.

# cd /usr/local

# wget 
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget 
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm 
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

# wget 
<http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicación de cambios en la configuración personalizada

Antes de aplicar los cambios realizados en la configuración personalizada, asegúrese de que ha completado los pasos posteriores a la instalación

Para aplicar los cambios realizados en la configuración personalizada, siga los pasos que se indican a continuación:

1. Copie al binario de RHEL 6-64 Unified Agent en el sistema operativo recién creado.

2. Ejecute el siguiente comando para descomprimir el binario.

**tar - zxvf <nombreDeArchivo>**

3. Ejecute el siguiente comando para conceder permisos.

# **chmod 755 ./ApplyCustomChanges.sh**

4. Ejecute el siguiente comando para ejecutar el script.

**# ./ApplyCustomChanges.sh**

Nota: ejecute el script sólo una vez en el servidor. **REINICIE** el servidor cuando el script anterior se haya ejecutado correctamente.

### Inicio de la instalación de un destino maestro

[Descargue](http://go.microsoft.com/fwlink/?LinkID=529757) el archivo de instalación del servidor de destino principal de Linux.

Copie el archivo descargado a la máquina virtual del servidor de destino principal de Linux con la utilidad cliente de sftp que prefiera. O bien, puede iniciar sesión en la máquina virtual del servidor de destino principal de Linux y utilice wget para descargar el paquete de instalación desde el vínculo proporcionado.

Inicie sesión en la máquina virtual del servidor de destino principal de Linux con el cliente ssh que prefiera.

Si está conectado a la red de Azure en la que implementó el servidor de destino principal de Linux a través de una conexión VPN, utilice la dirección IP interna para el servidor de destino principal de Linux obtenida del panel de la máquina virtual y el puerto 22 para conectarse al servidor de destino principal de Linux mediante Secure seguro.

Si se conecta al servidor de destino principal de Linux a través de una conexión a Internet pública, use la dirección IP virtual pública de dicho servidor (desde la página de panel máquinas virtuales) y el extremo público creado para ssh para iniciar sesión en el servidor de destino principal de Linux.

Extraiga los archivos del archivo tar del instalador del servidor de destino principal de Linux comprimido con gzip, para lo que debe ejecutar

*"tar – xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64 *"* desde el directorio en el que había copiado el instalador del servidor de destino principal de Linux.

![](./media/site-recovery-failback-azure-to-vmware/image16.png)

Si ha extraído los archivos del instalador en un directorio de cambio de directorio que no sea en se extrajo el contenido del archivo tar. En la ruta de acceso a este directorio, ejecute "sudo. / install.sh"

![](./media/site-recovery-failback-azure-to-vmware/image17.png)

Cuando se le solicite que elija el rol principal de este agente, seleccione 2 (Destino maestro)

Deje las restantes opciones de la instalación interactiva con sus valores predeterminados.

Espere hasta que se realice el resto de la instalación y aparezca la interfaz de configuración de host. La utilidad Configuración de host del servidor de destino principal de Linux es una utilidad de línea de comandos. No cambie el tamaño de la ventana de la utilidad de cliente ssh. Use las teclas de dirección para seleccionar la opción Global y presione Entrar en el teclado.

![](./media/site-recovery-failback-azure-to-vmware/image18.png)

![](./media/site-recovery-failback-azure-to-vmware/image19.png)

1.  En el campo IP escriba la dirección IP interna del servidor de configuración obtenida de la página Panel de las máquinas virtuales y presione Entrar.

2.  En el campo Puerto, escriba 22 y presione Entrar.

3.  Deje la opción Sí en Usar https:. Vuelve a presionar Entrar.

4.  Escriba la frase de contraseña que se generó en el servidor de configuración. Si usa un cliente PuTTY desde una máquina con Windows para ssh en la máquina virtual del servidor de destino principal de Linux, puede utilizar Mayus+Insertar para pegar el contenido del Portapapeles. Copie la frase de contraseña en el Portapapeles local mediante Ctrl-C y presione Mayús+Insertar para pegarlo. Presione Entrar

5.  Utilice la tecla de dirección derecha para salir y presione Entrar.

Espere hasta que la instalación se complete

![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si por alguna razón no pudo registrar el servidor de destino principal de Linux en el servidor de configuración, puede hacerlo ejecutando la utilidad configuración del host desde /usr/local/ASR/Vx/bin/hostconfigcli (antes tendrá que establecer los permisos de acceso a este directorio mediante la ejecución de chmod como superusuario).


#### Validación del registro del servidor de destino principal en el servidor de configuración

Para validar que el servidor de destino principal se ha registrado correctamente en el servidor de configuración, visite la página Detalles del servidor de la página Servidor de configuración de servidor del almacén de Azure Site Recovery.

Nota: después de registrar el destino maestro, es posible que este presente errores de configuración porque se haya eliminado la máquina virtual de Azure o porque los extremos no se hayan configurado correctamente. Esto se debe a que la configuración del destino maestro se detecta por los Extremos de Azure cuando el destino maestro se implementa en Azure. Sin embargo, esto no se mantiene para el destino maestro local y el error se puede ignorar. La conmutación por recuperación no tendrá problemas por este motivo.


## Inicio de la protección de las máquinas virtuales en local

Antes de realizar la conmutación por recuperación de las máquinas virtuales a local, es preciso protegerlas. Para proteger las máquinas virtuales de una aplicación, siga los pasos que se indican a continuación.

### Nota sobre la unidad temporal

Cuando una máquina virtual se conmuta por error a Azure, agrega una unidad temporal adicional para el archivo de paginación. La máquina virtual que se ha conmutado por error no suele requerir dicha unidad, ya que es posible que disponga de una unidad dedicada al archivo de paginación.

Antes de comenzar la protección inversa de las máquinas virtuales, es preciso que se asegure de que la unidad está sin conexión, con el fin de que no reciba protección.

Para ello,

1.  Abra Administración de equipos (mediante la herramienta administrativa del Panel de control, o bien haciendo clic con el botón derecho en Este equipo en la ventana Explorador y seleccionando Administrar).

2.  Seleccione Administración de almacenamiento para que enumere tanto los discos en línea como los conectados a la máquina.

3.  Seleccione el disco temporal conectado a la máquina y elija ponerlo sin conexión.

4.  Una vez que se haya puesto sin conexión correctamente, puede continuar con la protección de la máquina virtual en la dirección inversa.

### Plan de protección de máquinas virtuales

En el Portal de Azure, examine los estados de la máquina virtual y asegúrese de que está conmutada por error.

![](./media/site-recovery-failback-azure-to-vmware/image21.png)

Nota: durante la conmutación por error de Azure a local, la máquina virtual de Azure se considera similar a una máquina virtual física. La conmutación por error a local es a una máquina virtual.

1.  Inicie vContinuum en el equipo

![](./media/site-recovery-failback-azure-to-vmware/image8.png)

2.  En la opción **Choose Application (Elegir aplicación)**, seleccione **P2V**.

3.  Haga clic en la opción **New Protection (Nueva protección)** para comenzar.

4.  En la ventana nueva que se abre empezará a proteger las máquinas virtuales devueltas a local.

    a. En **OS type (Tipo de sistema operativo)**, seleccione el tipo de sistema operativo en función de las máquinas virtuales en que desea realizar la conmutación por recuperación y, a continuación, seleccione **Get Details (Obtener detalles)**.

    b. En **Primary server details (Detalles de servidor principal)**, debe identificar las máquinas virtuales que desea proteger.

    c. Las máquinas virtuales se enumeran por sus nombres de host de equipo, no por si se ven en vCenter o Azure

    d. Las máquinas virtuales se enumeran bajo el nombre de host de vCenter en que estaban antes de la conmutación por error.

    e. Una vez que haya identificado las máquinas virtuales que desea proteger, selecciónelas una a una.

5.  Cuando seleccione una de las máquinas virtuales que va proteger (y ya se ha conmutado por error a Azure), aparecerá una ventana emergente con dos entradas para la máquina virtual. Esto se debe a que el servidor de configuración ha detectado dos instancias de las máquinas virtuales registradas. Debe quitar la entrada de la VM local, con el fin de que pueda proteger la máquina virtual correcta. Tenga en cuenta que verá las entradas por su nombre de host de equipo. Para identificar la entrada de la máquina virtual de Azure correcta, puede iniciar sesión en la máquina virtual de Azure y e ir a C:\\Archivos de programa (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc. En el archivo drscout.conf, identifique el Id. del host. En el cuadro de diálogo vContinuum, conserve la entrada en que se encuentra hostID en la máquina virtual. Elimine las restantes entradas.

![](./media/site-recovery-failback-azure-to-vmware/image22.png)

6.  Para seleccionar la máquina virtual correcta, puede consultar su dirección IP. El rango local de direcciones IP será la máquina virtual local.
7.  Haga clic en **Eliminar** para eliminar la entrada.

![](./media/site-recovery-failback-azure-to-vmware/image23.png)

8.  Vaya a vCenter y detenga la máquina virtual.
9.  A continuación, también puede eliminar las máquinas virtuales locales.
10.  A continuación, especifique el servidor de destino maestro local en el que desea proteger las máquinas virtuales.
11.  Para ello, conéctese al vCenter en el que desee realizar la conmutación por recuperación.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

12.  Seleccione el servidor de destino maestro según el host en el que desee recuperar las máquinas virtuales.

![](./media/site-recovery-failback-azure-to-vmware/image24.png)

13.  A continuación, proporcione la opción de replicación de cada una de las máquinas virtuales.

![](./media/site-recovery-failback-azure-to-vmware/image25.png)

14.  Para ello, deberá seleccionar el lado recuperación **Almacén de datos**; se trata del almacén de datos en el que se recuperarán las máquinas virtuales.

Las diferentes opciones que debe proporcionar por máquina virtual son

**Opción** | **Valor recomendado de la opción**
---|---
Process Server IP (IP de servidor de proceso) | Seleccione el servidor de proceso que ha implementado en Azure
Retention size in MB (Tamaño de retención en MB)| 
Retention value (Valor de retención) | 1
Days/Hours (Días y horas) | Days (Días)
Consistency Interval (Intervalo de consistencia) | 1
Select Target Datastore (Seleccionar almacén de datos de destino) | El almacén de datos disponible en el lado de la recuperación. Este almacén de datos debe tener espacio suficiente y estar disponible en el host ESX en el que desea que se encuentre la máquina virtual.
15.  A continuación, puede configurar las propiedades que va a adquirir la máquina virtual después de la conmutación por error en el sitio local. Estas son las distintas propiedades que puede configurar

![](./media/site-recovery-failback-azure-to-vmware/image26.png)


**Propiedad** | **Cómo configurarla**
---|---
Network Configuration (Configuración de red)|Para cada NIC detectado, configure la dirección IP de conmutación por recuperación de la máquina virtual. Seleccione la NIC y haga clic en **Cambiar** para especificar los detalles de la dirección IP.
Hardware Configuration (Configuración de hardware)|Puede especificar los valores de memoria y CPU de la máquina virtual. Esta configuración se puede aplicar a todas las máquinas virtuales que intenta proteger.
Display Name (Nombre para mostrar)|Para identificar los valores correctos de la CPU y memoria, puede consultar el tamaño del rol de las máquinas virtuales de IAAS y ver el número de núcleos y la memoria asignados.
Display Name (Nombre para mostrar)|Después de la conmutación por error a local, puede elegir cambiar el nombre de las máquinas virtuales que se verán en el inventario de vCenter. Tenga en cuenta que el valor predeterminado que se ve aquí es el nombre de host del equipo de la máquina virtual. Para identificar el nombre de la máquina virtual, puede hacer referencia a la lista de máquinas virtuales del grupo de protección.
NAT Configuration (Configuración de NAT)|Se describe en detalle a continuación.

![](./media/site-recovery-failback-azure-to-vmware/image27.png)



> **Opciones de configuración de NAT del servidor de proceso**
>
> Para habilitar la protección de las máquinas virtuales, es preciso establecer dos canales de comunicación.
>
> El primer canal está entre la máquina virtual y el servidor de proceso. Este canal recopila los datos de la máquina virtual y los envía al servidor de proceso. A continuación, el servidor de proceso envía estos datos al destino principal. Si el servidor de proceso y la máquina virtual que se va a proteger están en la misma red virtual de Azure, no es preciso utilizar la configuración de NAT. Si el servidor de proceso y la máquina virtual que se van a proteger están en dos redes virtuales diferentes, es preciso especificar la configuración de NAT del servidor de proceso y marcar la primera opción.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image28.png)
>
> Para identificar el dirección IP pública del servidor de proceso, puede ir a la implementación del servidor de proceso de Azure y ver su dirección IP pública.
>
> El segundo canal está entre el servidor de proceso y destino principal. La opción de usar NAT, o no, depende de si se usa una conexión basada en VPN entre el destino maestro y el servidor de proceso, o la protección se realiza a través de Internet. Si el servidor de proceso se comunica con el destino maestro a través de una VPN, no debe seleccionar esta opción. Si el destino principal necesita comunicarse con el servidor de proceso a través de Internet, especifique la configuración de NAT para el servidor de proceso.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image29.png)
>
> Para identificar el dirección IP pública del servidor de proceso, puede ir a la implementación del servidor de proceso de Azure y ver su dirección IP pública.
>
> ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

1.  Si no ha eliminado las máquinas virtuales locales como se especifica en el paso 5.d y si el almacén de datos al que se realiza la conmutación por recuperación, seleccionado en el paso 7.a, aún contiene el VMDK antiguo, también deberá asegurarse de que la máquina virtual de conmutación por recuperación se crea en un lugar nuevo. Para ello, puede seleccionar Advanced settings (Configuración avanzada) y especificar una carpeta alternativa para realizar la restauración en la sección **Folder Name Settings (Configuración de nombre de carpeta)** de Advanced Settings (Configuración avanzada).

![](./media/site-recovery-failback-azure-to-vmware/image31.png)

Las restantes opciones de la Advanced Settings (Configuración avanzada) se pueden dejar con sus valores predeterminados. Asegúrese de aplicar la configuración de los nombres de las carpetas a todos los servidores.

2.  A continuación, muévase a la fase final de la protección. Aquí tendrá que ejecutar una comprobación de idoneidad para asegurarse de que las máquinas virtuales están listas para protegerse en local.

![](./media/site-recovery-failback-azure-to-vmware/image32.png)

	a.  Click on the readiness check and wait for it to complete.

	b.  The Readiness Report tab will give the information if all the
    virtual machines are ready.

	c.  If the Readiness Report is successful on all the virtual machines it
    will allow you to specify a name to the Protection plan

![](./media/site-recovery-failback-azure-to-vmware/image33.png)

	d.  Give the plan a new Name and begin Protect by clicking the button
    below.


3.  Ahora comenzará la protección.

    a. El progreso de la protección se puede ver en vContinuum

![](./media/site-recovery-failback-azure-to-vmware/image34.png)

	b.  Once the step **Activating Protection Plan** is completed, you can
    monitor the protection of the virtual machines via the ASR Portal.

	c.  You can also monitor the protection via Azure Site Recovery.

![](./media/site-recovery-failback-azure-to-vmware/image35.png)

También se puede ver el estado exacto de los pares de haciendo clic en la máquina virtual y supervisando su progreso en la sección de replicación del volumen.

![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Preparación del plan de conmutación por recuperación

Puede preparar un plan de conmutación por recuperación mediante vContinuum, con el fin de que pueda volver a realizase la conmutación por recuperación de la aplicación en cualquier momento. Estos planes de recuperación son muy similares a los planes de recuperación de ASR.

1.  Inicie vContinuum y seleccione la opción **Manage plans (Administrar planes)**.

2.  En las opciones secundarias, seleccione **Recover (Recuperar)**.

![](./media/site-recovery-failback-azure-to-vmware/image37.png)

3.  Puede ver la lista de todos los planes que se han utilizado para proteger las máquinas virtuales. Son los mismos planes que se puede utilizar para realizar la recuperación.

4.  Seleccione el plan de protección y todas las máquinas virtuales que desea recuperar dentro de él.

    a. Al seleccionar cada máquina virtual, puede ver más detalles sobre la máquina virtual de origen, el servidor ESX donde se recuperarán la máquina virtual y el disco de máquina virtual de origen.

5.  Haga clic en Siguiente para comenzar el Asistente para **recuperación**.

6.  Seleccione las máquinas virtuales que desea recuperar.

    a. Vea la lista de las máquinas virtuales que puede recuperar.

![](./media/site-recovery-failback-azure-to-vmware/image38.png)

	b.  You can **recover based on** multiple options however we recommend
    the **Latest Tag.** This will ensure that the latest data from the
    virtual machine will be used.

	c.  Select **Apply for All VMs** to ensure that the latest tag will be
    chosen for all the virtual machines.


7.  Ejecute la **comprobación de idoneidad.** Esto le informará si están configurados los parámetros correctos para habilitar la recuperación de las etiquetas más recientes de la máquina virtual. Haga clic en Siguiente si el resultado de todas las comprobaciones es satisfactorio; de lo contrario, examine el registro y resuelva los errores.

![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  En el paso de configuración de máquina virtual del asistente, asegúrese de que la configuración de recuperación está establecida correctamente. En caso de que la configuración de la máquina virtual no sea la que necesita, puede cambiarla. Dado que ya hemos completado esta acción durante la protección, puede omitirla.

![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9.  Por último, revise la lista de máquinas virtuales que se van a recuperar.

    a. Especifique un orden de recuperación en las máquinas virtuales.

Nota: las máquinas virtuales se enumeran con el nombre de host del equipo. Puede que sea difícil asignar el nombre de host del equipo a la máquina virtual. Para asignar los nombres, puede ir al panel de máquinas virtuales de Azure IAAS y examinar el nombre de host de la máquina virtual.

![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10.  Indique el **nombre del plan de recuperación** y seleccione **Recovery later (Recuperar más tarde)** en **Recovery options (Opciones de recuperación)**.

    a. Si desea que la recuperación se realice de inmediato, puede elegir **Recovery now (Recuperar ahora)** en **Recovery options (Opciones de recuperación)**.

    b. Se recomienda elegir la opción Recovery later (Recuperar más tarde), ya puede que no se haya completado la replicación inicial de protección.

    c. Por último, haga clic en el botón **Recover (Recuperar)** para guardar el plan o para desencadenar la recuperación, según lo que haya elegido en **Recovery options (Opciones de recuperación)**.

11.  Puede ver el estado de recuperación para saber si el plan se ha guardado correctamente.

![](./media/site-recovery-failback-azure-to-vmware/image42.png)

12.  Si ha elegido que la recuperación se realice más tarde, se le indicará que el plan se ha creado y que puede recuperarlo más tarde.

![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Recuperación de máquinas virtuales

Una vez que se crea el plan, puede elegir recuperar las máquinas virtuales. Como requisito previo, es preciso que se asegure de que ha finalizado la sincronización de las máquinas virtuales.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Si en Replication Status (Estado de replicación) aparece el valor OK (Correcto), significa que la protección ha finalizado y se ha alcanzado el umbral de RPO. Para confirmar el mantenimiento del par de replicación, puede ir a las propiedades de la máquina virtual y ver el mantenimiento de la replicación.

**Antes de iniciar la recuperación desactive las máquinas virtuales de Azure. Esto garantizará que no existen clústeres con nodos malinformados y que los clientes finales serán atendidos mediante una copia de la aplicación. Continúe con los pasos siguientes solo cuando se haya asegurado de que las máquinas virtuales de Azure están desconectadas.**

Para comenzar la recuperación de las máquinas virtuales en local, es preciso iniciar el plan que se guarda.

1.  En vContinuum, seleccione **supervisar** los planes.

2.  El asistente enumerará los planes que se han ejecutado hasta ahora.

![](./media/site-recovery-failback-azure-to-vmware/image45.png)

3.  Seleccione el nodo **Recovery (Recuperación)** y seleccione el plan que desea recuperar.

    a. Le informará de que el plan aún no se ha iniciado.

4.  Haga clic en **Iniciar** para comenzar la recuperación.

5.  Puede supervisar la recuperación de las máquinas virtuales.


![](./media/site-recovery-failback-azure-to-vmware/image46.png)

6. Una vez que las máquinas virtuales se han encendido, puede conectarlas a las máquinas virtuales del vCenter.

## Reprotección en Azure después de la conmutación por recuperación

Una vez completada la conmutación por recuperación, es aconsejable volver a proteger las máquinas virtuales. Los pasos siguientes le ayudarán a volver a configurar la protección

1.  Compruebe que funcionan los máquinas virtuales locales y que la aplicación puede acceder a los clientes finales.

2.  En el portal de Azure Site Recovery, seleccione las máquinas virtuales y elimínelas.

    a. Seleccione esta opción para deshabilitar la protección de las máquinas virtuales. Esto garantizará que las máquinas virtuales dejan de estar protegidas.

3.  Vaya a las máquinas virtuales de Azure IAAS y eliminar las máquinas virtuales con conmutación por error.

4.  Eliminar las máquinas virtuales antiguas en vSpehere: se trata de las máquinas virtuales que anteriormente conmutaron por error a Azure.

5.  En el Portal de Azure Site Recovery, seleccione esta opción para proteger las máquinas virtuales que recientemente se han conmutado por error.

6.  Una vez que las máquinas virtuales están protegidas, puede agregarlas a un plan de recuperación, ya que así estarán continuamente protegidas.


 

<!----HONumber=Oct15_HO2-->