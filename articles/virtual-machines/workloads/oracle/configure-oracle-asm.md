---
title: "Configuración de ASM de Oracle en una máquina virtual Linux en Azure | Microsoft Docs"
description: "Ponga en funcionamiento rápidamente ASM de Oracle en el entorno de Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8290885cd4f3d2faede2900971cd7a5b5a59f5bc
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configuración de ASM de Oracle en una máquina virtual Linux en Azure  

En este artículo, aprenda a instalar y configurar Automatic Storage Management de Oracle (ASM de Oracle) en una máquina virtual Linux de Oracle en Azure.

Antes de comenzar, asegúrese de que esté instalada la CLI de Azure. Para obtener más información, vea la [guía de instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparación del entorno

### <a name="sign-in-to-azure"></a>Inicio de sesión en Azure 

En la CLI de Azure, para iniciar sesión en su suscripción de Azure, use el comando [az login](/cli/azure/#login). Después, siga las instrucciones que aparecen en pantalla.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

Para crear un grupo de recursos, use el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado myResourceGroup en la ubicación westus.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>Creación de una VM

Para crear una máquina virtual para usarla con ASM de Oracle, complete los pasos siguientes:

1. Para crear una máquina virtual, use el comando [az vm create](/cli/azure/vm#create). 

   En el ejemplo siguiente se crea una máquina virtual denominada myVM. También se crean claves SSH si aún no existen en una ubicación de claves predeterminada. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`.  

   ```azurecli
   az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
   ```

   Después de crear la máquina virtual, la CLI de Azure muestra información similar al ejemplo siguiente. Tome nota del valor de `publicIpAddress`, Use esta dirección para acceder a la máquina virtual.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "westus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

2. Agregue discos para usarlos para la configuración de ASM de Oracle:

   ```azurecli
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
   az vm disk attach -g myResourceGroup --vm-name myVM  --disk myDataDisk4 --new --size-gb 50
   ```

### <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Para crear una sesión de SSH con la máquina virtual, use el siguiente comando. Reemplace la dirección IP por el valor `publicIpAddress` para la máquina virtual.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalación de ASM de Oracle

Para instalar ASM de Oracle, complete los pasos siguientes. 

Para más información sobre cómo instalar ASM de Oracle, consulte [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html) (Descargas de Oracle ASMLib para Oracle Linux 6).  

1. Ejecute `yum list`:

   ```bash
   $ sudo su -
   # yum list
   ```
   `yum list` puede tardar varios minutos en cargarse la primera vez que lo ejecute.

2. Ejecute estos comandos adicionales:

   ```bash
   # yum list | grep oracleasm
   # yum -y install kmod-oracleasm.x86_64
   # yum -y install oracleasm-support.x86_64
   # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
   # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
   # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Compruebe que ASM de Oracle esté instalado:

   ```bash
   # rpm -qa |grep oracleasm
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
   ```

4. Agregue usuarios y grupos:

   ```bash
   # groupadd -g 54345 asmadmin
   # groupadd -g 54346 asmdba
   # groupadd -g 54347 asmoper
   # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
   # usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Compruebe usuarios y grupos:

   ```bash
   # id grid
   uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
   ```

6. Cree una carpeta y cambie el propietario:

   ```bash
   # mkdir /u01/app/grid
   # chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Configuración de ASM de Oracle

Para este tutorial, el usuario predeterminado es *grid* y el grupo predeterminado, *asmadmin*. Asegúrese de que el usuario *oracle* forme parte del grupo asmadmin. Para configurar la instalación de ASM de Oracle, complete los pasos siguientes:

1. Establezca el controlador de la biblioteca de ASM de Oracle:

   ```bash
   # /usr/sbin/oracleasm configure -i

   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Vea la configuración del disco:
   ```bash
   # cat /proc/partitions
   ```

3. Formatee el disco:

   ```bash
   # fdisk /dev/sdc
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Repita el paso anterior para /dev/sdd, /dev/sde y /dev/sdf.

5. Compruebe la configuración del disco:

   ```bash
   # cat /proc/partitions
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11        0    1048575 sr0
   ```

6. Compruebe el estado de servicio de ASM de Oracle:

   ```bash
   # service oracleasm status
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   ```

7. Inicie el servicio de ASM de Oracle:

   ```bash
   # service oracleasm start
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

8. Cree discos de ASM de Oracle:

   ```bash
   # service oracleasm createdisk ASMSP /dev/sdc1
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

   # service oracleasm createdisk DATA /dev/sdd1
   Marking disk "DATA" as an ASM disk:                        [  OK  ]

   # service oracleasm createdisk DATA1 /dev/sde1
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]

   # service oracleasm createdisk FRA /dev/sdf1
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

9. Enumere discos de ASM de Oracle:

   ```bash
   # service oracleasm listdisks
   ASMSP
   DATA
   DATA1
   FRA
   ```

10. Cambie las contraseñas de los usuarios root, oracle y grid. (Usará las contraseñas más adelante, durante la instalación).

    ```bash
    # passwd oracle
    # passwd grid
    # passwd root
    ```

11. Cambie los permisos de carpeta:

    ```bash
    # chmod -R 775 /opt
    # chown grid:oinstall /opt
    # chown oracle:oinstall /dev/sdc1
    # chown oracle:oinstall /dev/sdd1
    # chown oracle:oinstall /dev/sde1
    # chown oracle:oinstall /dev/sdf1
    # chmod 600 /dev/sdc1
    # chmod 600 /dev/sdd1
    # chmod 600 /dev/sde1
    # chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Descarga y preparación de Oracle Grid Infrastructure

Para descargar y preparar el software Oracle Grid Infrastructure, complete los pasos siguientes:

1. Descargue Oracle Grid Infrastructure de la [página de descargas de ASM de Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Bajo el título de la descarga **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** descargue los dos archivos .zip.

2. Después de descargar los archivos .zip en el equipo cliente, puede usar el protocolo de copia segura (SCP) para copiar los archivos a la máquina virtual:

   ```bash
   scp *.zip <publicIpAddress>:<folder>
   ```

3. Mueva los archivos .zip a la carpeta /opt. A continuación, cambie el propietario de los archivos:

   ```bash
   # mv <folder>/*.zip /opt
   # cd /opt
   # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Descomprima los archivos. (Instale la herramienta de descompresión de Linux si aún no está instalada).
   
   ```bash
   # yum install unzip
   # unzip linuxamd64_12102_grid_1of2.zip
   # unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Cambie el permiso:
   
   ```bash
   # chown -R grid:oinstall /opt/grid
   ```

6. Desactive el firewall:
   
   ```bash
   # service iptables status
   # service iptables stop
   ```

7. Compruebe el espacio de intercambio disponible. Necesita al menos 6,8 GB de espacio de intercambio para instalar Grid. Las máquinas virtuales de Azure no tienen el intercambio habilitado y configurado de forma predeterminada.

Es muy recomendable que use waagent para configurar el espacio de intercambio para que siempre esté creado en el disco efímero (disco temporal). Para más información, consulte [Incorporación de un archivo de intercambio en máquinas virtuales Linux de Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Preparación del cliente y la máquina virtual para ejecutar x11 (solo para clientes Windows)
Se trata de un paso opcional. Puede omitir este paso si está utilizando un cliente Linux o ya ha configurado x11.

1. [Descargue PuTTY](http://www.putty.org/) y [Xming](https://xming.en.softonic.com/) en el equipo Windows.

2. Después de instalar PuTTY, en la carpeta PuTTY (por ejemplo, C:\Archivos de programa\PuTTY), ejecute puttygen.exe (PuTTY Key Generator).

3. En PuTTY Key Generator:
   
   1. Para generar una clave, seleccione el botón **Generate** (Generar).
   2. Copie el contenido de la clave (Ctrl+C).
   3. Seleccione el botón **Save private key** (Guardar clave privada).
   4. Pase por alto la advertencia que aparece y seleccione **OK** (Aceptar).

   ![Captura de pantalla de PuTTY Key Generator](./media/oracle-asm/puttykeygen.png)

4. En la máquina virtual, ejecute estos comandos:

   ```bash
   # sudo su - grid
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Cree un archivo denominado authorized_keys. Pegue el contenido de la clave en este archivo y guárdelo.

   > [!NOTE]
   > La clave debe contener la cadena `ssh-rsa`. Además, el contenido de la clave debe ser una sola línea de texto.
   >  

6. Inicie PuTTY. En el panel **Category** (Categoría), vaya a **Connection** > **SSH** > **Auth** (Conexión > SSH > Autenticación). En el cuadro **Private key file for authentication** (Archivo de clave privada para autenticación), vaya a la clave que generó antes.

   ![Captura de pantalla de las opciones de autenticación de SSH](./media/oracle-asm/setprivatekey.png)

7. En el panel **Category** (Categoría), vaya a **Connection** > **SSH** > **X11** (Conexión > SSH > X11). Seleccione la casilla **Enable X11 forwarding** (Habilitar reenvío a X11).

   ![Captura de pantalla de las opciones de reenvío a SSH X11](./media/oracle-asm/enablex11.png)

8. En el panel **Category** (Categoría), vaya a **Session** (Sesión). Escriba la información del host y seleccione **Open** (Abrir).

   ![Captura de pantalla de las opciones de sesión de PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalación de Oracle Grid Infrastructure

Para instalar Oracle Grid Infrastructure, complete los pasos siguientes:

1. Inicie sesión como grid. (Debe poder iniciar sesión sin que se le solicite una contraseña). 

   > [!NOTE]
   > Asegúrese de que se esté ejecutando Xming antes de comenzar la instalación.

   ```bash
   $ cd /opt/grid
   $ ./runInstaller
   ```

   Se abre el instalador de Oracle Grid Infrastructure 12c versión 1. (Se puede tardar unos minutos en iniciar el instalador).

2. En la página **Select Installation Option** (Seleccionar opción de instalación), seleccione **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Instalar y configurar Oracle Grid Infrastructure para un servidor independiente).

   ![Captura de pantalla de la página Select Installation Option (Seleccionar opción de instalación) en el instalador](./media/oracle-asm/install01.png)

3. En la página **Select Product Languages** (Seleccionar idiomas de producto), seleccione **English** (Inglés) o el idioma que desee.

   ![Captura de pantalla de la página Select Product Languages (Seleccionar idiomas de producto) en el instalador](./media/oracle-asm/install02.png)

4. En la página **Create ASM Disk Group** (Crear grupo de discos de ASM):
   - Escriba un nombre para el grupo de discos.
   - En **Redundancy** (Redundancia), seleccione **External** (Externa).
   - En **Allocation Unit Size** (Tamaño de la unidad de asignación), seleccione **4**.
   - En **Add Disks** (Agregar discos), seleccione **ORCLASMSP**.

   ![Captura de pantalla de la página Create ASM Disk Group (Crear grupo de discos de ASM) en el instalador](./media/oracle-asm/install03.png)

5. En la página **Specify ASM Password** (Especificar contraseña de ASM), seleccione la opción **Use same passwords for these accounts** (Usar las mismas contraseñas para estas cuentas) y escriba una contraseña.

   ![Captura de pantalla de la página Specify ASM Password (Especificar contraseña de ASM) en el instalador](./media/oracle-asm/install04.png)

6. (Opcional) En la página **Specify Management Options** (Especificar opciones de administración), active la casilla **Register with Enterprise Manager (EM) Cloud Control** (Registro con Enterprise Manager (EM) Cloud Control).

   ![Captura de pantalla de la página Specify Management Options (Especificar opciones de administración) en el instalador](./media/oracle-asm/install05.png)

7. En la página **Privileged Operating System Groups** (Grupos de sistemas operativos con privilegios), use la configuración predeterminada.

   ![Captura de pantalla de la página Privileged Operating System Groups (Grupos de sistemas operativos con privilegios) en el instalador](./media/oracle-asm/install06.png)

8. En la página **Specify Installation Location** (Especificar ubicación de instalación), use la configuración predeterminada.

   ![Captura de pantalla de la página Specify Installation Location (Especificar ubicación de instalación) en el instalador](./media/oracle-asm/install07.png)

9. En la página **Create Inventory** (Crear inventario), escriba o busque la ubicación de carpeta.

   ![Captura de pantalla de la página Create Inventory (Crear inventario) en el instalador](./media/oracle-asm/install08.png)

10. En la página **Root script execution configuration** (Configuración de ejecución de script de root), active la casilla **Automatically run configuration scripts** (Ejecutar scripts de configuración automáticamente). Después, seleccione la opción **Use "root" user credential** (Usar las credenciales del usuario "root") y escriba la contraseña del usuario root.

    ![Captura de pantalla de la página Root script execution configuration (Configuración de ejecución de script de root) en el instalador](./media/oracle-asm/install09.png)

11. En la página **Perform Prerequisite Checks** (Realizar comprobaciones de requisitos previos), seleccione **Fix & Check Again** (Corregir y volver a comprobar).

    ![Captura de pantalla de la página Perform Prerequisite Checks (Realizar comprobaciones de requisitos previos) en el instalador](./media/oracle-asm/install10.png)

12. En el cuadro de diálogo **Fixup Script** (Script de corrección), seleccione **Aceptar**.

    ![Captura de pantalla del cuadro de diálogo Fixup Script (Script de corrección)](./media/oracle-asm/install11.png)

13. En la página **Summary** (Resumen), revise las opciones de configuración y seleccione **Install** (Instalar).

    ![Captura de pantalla de la página Summary (Resumen) en el instalador](./media/oracle-asm/install12.png)

14. Aparece un cuadro de diálogo de advertencia. Seleccione **Yes** (Sí) para continuar.

    ![Captura de pantalla del cuadro de diálogo de advertencia](./media/oracle-asm/install14.png)

15. En la página **Finish** (Finalizar), seleccione **Close** (Cerrar) para finalizar la instalación.

    ![Captura de pantalla de la página Finish (Finalizar) en el instalador](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Configuración de la instalación de ASM de Oracle

Para configurar la instalación de ASM de Oracle, complete los pasos siguientes:

1. Inicie sesión como grid desde su sesión de X11:

   ```bash
   $ cd /u01/app/grid/product/12.1.0/grid/bin
   $ ./asmca
   ```

   Se abre el asistente para la configuración de ASM de Oracle.

2. En el cuadro de diálogo **Configure ASM: Disk Groups** (Configurar ASM: grupos de discos), seleccione el botón **Create** (Crear) y **Show Advanced Options** (Mostrar opciones avanzadas).

   ![Captura de pantalla del cuadro de diálogo Configure ASM: Disk Groups (Configurar ASM: grupos de discos)](./media/oracle-asm/asm01.png)

3. En el cuadro de diálogo **Create Disk Group** (Crear grupo de discos):

   - Escriba el nombre del grupo de discos.
   - En **Select Member Disks** (Seleccionar discos miembro), seleccione **ORCL_DATA** y **ORCL_DATA1**.
   - En **Allocation Unit Size** (Tamaño de la unidad de asignación), seleccione **4**.

   ![Captura de pantalla del cuadro de diálogo Create Disk Group (Crear grupo de discos)](./media/oracle-asm/asm02.png)

4. En el cuadro de diálogo **Configure ASM: Disk Groups** (Configurar ASM: grupos de discos), seleccione el botón **Create** (Crear) y **Show Advanced Options** (Mostrar opciones avanzadas).

   ![Captura de pantalla del cuadro de diálogo Configure ASM: Disk Groups (Configurar ASM: grupos de discos)](./media/oracle-asm/asm03.png)

5. En el cuadro de diálogo **Create Disk Group** (Crear grupo de discos):

   - Escriba el nombre del grupo de discos.
   - En **Redundancy** (Redundancia), seleccione **External** (Externa).
   - En **Select Member Disks** (Seleccionar discos miembro), seleccione **ORCL_FRA**.
   - En **Allocation Unit Size** (Tamaño de la unidad de asignación), seleccione **4**.

   ![Captura de pantalla del cuadro de diálogo Create Disk Group (Crear grupo de discos)](./media/oracle-asm/asm04.png)

6. Seleccione **Exit** (Salir) para cerrar el asistente para la configuración de ASM.

   ![Captura de pantalla del cuadro de diálogo Configure ASM: Disk Groups (Configurar ASM: grupos de discos) con el botón Exit (Salir)](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Creación de la base de datos

El software de Oracle ya está instalado en la imagen de Azure Marketplace. Para instalar la base de datos, complete los pasos siguientes:

1. Cambie de usuario al superusuario Oracle e inicialice el agente de escucha para el registro:

   ```bash
   $ su - oracle
   Password:
   $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   $ ./dbca
    ```
   Se abre el asistente para la configuración de la base de datos.

2. En la página **Database Operation** (Operación de base de datos), seleccione **Create Database** (Crear base de datos).

   ![Captura de pantalla de la página Database Operation (Operación de base de datos)](./media/oracle-asm/createdb01.png)
  
3. En la página **Creation Mode** (Modo de creación):

   - Escriba un nombre para la base de datos.
   - Para **Storage Type** (Tipo de almacenamiento), seleccione **Automatic Storage Management (ASM)**.
   - Para **Database Files Location** (Ubicación de archivos de base de datos), vaya a la carpeta que desea usar.
   - Para **Fast Recovery Area** (Área de recuperación rápida), vaya a la carpeta que desea usar.

   ![Captura de pantalla de la página Creation Mode (Modo de creación)](./media/oracle-asm/createdb02.png)

4. En la página **Summary** (Resumen), revise las opciones de configuración elegidas y seleccione **Finish** (Finalizar) para crear la base de datos.

   ![Captura de pantalla de la página Summary (Resumen)](./media/oracle-asm/createdb03.png)

5. (Opcional) En la página **Finish** (Finalizar), para cambiar las contraseñas, seleccione **Password Management** (Administración de contraseñas).

   ![Captura de pantalla de la página Finish (Finalizar)](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Cuando ya no necesite la máquina virtual, puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

[Tutorial de creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)

[Ejemplos de la CLI de Azure para implementación de máquinas virtuales](../../linux/cli-samples.md)

