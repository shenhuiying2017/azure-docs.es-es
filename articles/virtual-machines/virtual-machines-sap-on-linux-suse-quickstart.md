<properties
   pageTitle="Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure | Microsoft Azure"
   description="Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="11/26/2015"
   ms.author="hermannd"/>

# Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure


A continuación se muestra una lista de elementos que hay que considerar cuando se prueba SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure. En este momento, no hay ninguna declaración de compatibilidad oficial de SAP para SAP-Linux-Azure. No obstante, los clientes pueden realizar algunas pruebas, demostraciones o prototipos siempre y cuando no dependan de ningún soporte técnico SAP oficial.

La lista siguiente solo debe ayudar a evitar algunos obstáculos potenciales y a facilitar la vida:



## Imágenes SUSE en Microsoft Azure para realizar pruebas de SAP 

Para las pruebas de SAP en Azure, solo se puede usar SLES 11SP4 y SLES 12. Se puede encontrar una imagen SUSE especial en la galería de imágenes de Azure: "SLES 11 SP3 para SAP CAL". Pero no está pensado para uso general. Está reservado para la solución de biblioteca de aplicaciones de nube de SAP, llamada SAP "CAL" ( <https://cal.sap.com/> ). No había ninguna opción para ocultar esta imagen del público. Así que no la utilice.

Todas las nuevas pruebas en Azure deben realizarse con el Administrador de recursos de Azure. Para buscar imágenes de SUSE SLES y versiones que usan Azure Powershell o CLI, use los siguientes comandos. El resultado puede usarse, por ejemplo, para definir la imagen de sistema operativo en una plantilla json para implementar una nueva máquina virtual SUSE Linux:

* Busque publicadores existentes que incluyan SUSE:

   ```
   PS  : Get-AzureVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Busque ofertas existentes de SUSE:
      
   ```
   PS  : Get-AzureVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
      
* Busque ofertas de SUSE SLES:
      
   ```
   PS  : Get-AzureVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```
      
* Busque una versión específica de una sku de SLES:
      
   ```
   PS  : Get-AzureVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```
     
## Instalación de WALinuxAgent en una máquina virtual de SUSE 
 
El agente forma parte de las imágenes SLES en la galería de Azure. Estos son los lugares donde se puede encontrar información sobre cómo instalarlo manualmente (por ejemplo, al cargar un disco duro virtual del sistema operativo de SLES desde local):

<http://software.opensuse.org/package/WALinuxAgent>

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/>

<https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/>

## Conexión de discos de datos de Azure a una máquina virtual Linux de Azure

NUNCA monte discos de datos de Azure en una máquina virtual Linux de Azure mediante el identificador de dispositivo. En su lugar, use el UUID. Tenga cuidado al usar, por ejemplo, herramientas gráficas para montar discos de datos de Azure. Compruebe las entradas de /etc/fstab. El problema con el identificador del dispositivo es que puede cambiar y la máquina virtual de Azure podría bloquearse en el proceso de arranque. Se podría agregar el parámetro nofail en /etc/fstab para mitigar el problema. Pero tenga en cuenta que con nofail las aplicaciones pueden usar el punto de montaje como antes y quizá escribir en el sistema de archivos raíz en caso de que no se haya montado un disco de datos externos de Azure durante el arranque.

## Carga de una máquina virtual de SUSE desde una instalación local a Azure

El blog siguiente describe los pasos: <https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/>

Si se quiere cargar una máquina virtual sin el paso de desaprovisionamiento al final para mantener, por ejemplo, una instalación de SAP existente, así como el nombre de host, deben comprobarse los elementos siguientes:

* Asegúrese de que el disco de sistema operativo se monte a través de UUID y no del identificador de dispositivo. Cambiar a UUID en /etc/fstab NO es suficiente para el disco de sistema operativo. No hay que olvidarse de adaptar el cargador de arranque, por ejemplo, a través de yast o mediante la modificación de /boot/grub/menu.lst
* En caso de usar el formato vhdx para el disco de sistema operativo de SUSE y de convertirlo en vhd para cargarlo en Azure es muy probable que el dispositivo de red cambie de eth0 a eth1. Para evitar problemas al arrancar después en Azure, hay que devolverlo a eth0, tal como se describe aquí: <https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/>

Además de lo que se describe en el artículo se recomienda quitar también

   /lib/udev/rules.d/75-persistent-net-generator.rules

La instalación de waagent también debería evitar cualquier posible problema siempre y cuando no haya varias tarjetas NIC.

## Clave de licencia y hardware de SAP

Para la certificación oficial SAP-Windows-Azure se introdujo un nuevo mecanismo para calcular la clave de hardware de SAP, que se usa para la licencia SAP. Había que adaptar el núcleo SAP para usarlo. Las versiones actuales de kernel de SAP para Linux NO incluyen este cambio en el código. Por lo tanto, puede ocurrir que, en determinadas situaciones (por ejemplo, el cambio de tamaño de la máquina virtual de Azure), los cambios en la clave de hardware de SAP y en la licencia de SAP ya no sean válidos.

## Recursos compartidos de NFS en instalaciones de SAP distribuidas

En el caso de una instalación distribuida donde se desea instalar, por ejemplo, la base de datos y los servidores de aplicaciones SAP en máquinas virtuales independientes, se puede compartir el directorio /sapmnt a través de NFS. Si se produjeron problemas con los pasos de instalación después de crear el recurso compartido NFS para /sapmnt, compruebe si se ha establecido "no\_root\_squash" para el recurso compartido. En esto radicó la solución en un caso de prueba interna.


## Volúmenes lógicos

La máquina virtual lógica no está totalmente validada en Azure. Si se necesita un gran volumen lógico en varios discos de datos de Azure (por ejemplo, para la base de datos SAP), debe usarse mdadm. Este es un blog que describe cómo configurar RAID de Linux en Azure mediante mdadm:

<https://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/>


## Repositorio de SUSE Azure

En caso de que haya un problema con el acceso al repositorio SUSE de Azure estándar, hay un comando simple para restablecerlo. Esto podría suceder al crear una imagen de sistema operativo privada en una región de Azure y, después, copiar la imagen en una región diferente donde se desea implementar nuevas máquinas virtuales basadas en esta imagen de sistema operativo privada. Solo ejecute los siguientes comandos dentro de la máquina virtual:

   ```
   service guestregister restart
   ```

## Escritorio Gnome

Si alguien quiere usar el escritorio Gnome para instalar un sistema completo de demostración SAP dentro de una máquina virtual única incluidos la GUI de SAP, el explorador, la consola de administración de SAP y así sucesivamente, esta es una sugerencia para su instalación en las imágenes de SLES Azure:

   SLES 11

   ```
   zypper in -t pattern gnome
   ```
      
   SLES 12
   
   ```
   zypper in -t pattern gnome-basic
   ```

## Compatibilidad de SAP-Oracle en Linux en la nube
 
Esto no es en realidad un tema específico de Azure, sino un tema general. No obstante, es importante entenderlo. Hay una restricción de soporte técnico de Oracle en Linux en entornos virtualizados. Al final, esto significa que SAP no admite Oracle en SUSE o también RedHat en una nube pública como Azure. Los clientes deben ponerse en contacto con Oracle directamente para tratar este tema.

<!---HONumber=AcomDC_1203_2015-->