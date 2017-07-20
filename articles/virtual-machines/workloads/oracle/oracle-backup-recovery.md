---
title: "Copia de seguridad y recuperación de una base de datos de Oracle Database 12c en una máquina virtual Linux de Azure | Microsoft Docs"
description: Aprenda a realizar una copia de seguridad y recuperar una base de datos de Oracle Database 12c en el entorno de Azure.
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
ms.date: 5/17/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7a53ca814170a6651fc499c41395fe088a6f869a
ms.contentlocale: es-es
ms.lasthandoff: 06/15/2017

---

# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Copia de seguridad y recuperación de una base de datos de Oracle Database 12c en una máquina virtual Linux de Azure

Puede usar la CLI de Azure para crear y administrar recursos de Azure en un símbolo del sistema o usar scripts en su lugar. En este artículo, se usan scripts de la CLI de Azure para implementar una base de datos de Oracle Database 12c desde una imagen de la galería de Azure Marketplace.

Antes de comenzar, asegúrese de que esté instalada la CLI de Azure. Para obtener más información, consulte la [guía de instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparación del entorno
### <a name="step-1-assumptions"></a>Paso 1: Presuposiciones

*   Para llevar a cabo el proceso de copia de seguridad y recuperación, debe crear una máquina virtual Linux con una instancia de Oracle Database 12C instalada. La imagen de Marketplace que se usa para crear las máquinas virtuales se denomina *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Para instrucciones sobre cómo crear una base de datos de Oracle, consulte la [guía rápida de creación de una base de datos de Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Paso 2: Conexión a la máquina virtual

*   Para crear una sesión de Secure Shell (SSH) con la máquina virtual, use el comando siguiente. Cambie la combinación de dirección IP y nombre de host por el valor `publicIpAddress` de la máquina virtual.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Paso 3: Preparación de la base de datos

1.  En este paso se presupone que tiene una instancia de Oracle (cdb1) que se ejecuta en una máquina virtual denominada *myVM*.

    Ejecute el superusuario *oracle* (raíz) e inicialice el agente de escucha:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Asegúrese de que la base de datos está en modo de registro de archivo (paso opcional):

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  Cree una tabla para probar la confirmación (paso opcional):

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Compruebe o cambie la ubicación y el tamaño del archivo de copia de seguridad:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Use Oracle Recovery Manager (RMAN) para hacer una copia de seguridad de la base de datos:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Paso 4: Copias de seguridad coherentes con la aplicación de las máquinas virtuales Linux

Esta es una característica nueva de Azure Backup que permite a los usuarios especificar scripts (anterior y posterior) que se ejecuten antes y después de la instantánea de la máquina virtual.

#### <a name="1-download-vmsnapshotscriptpluginconfigjson-from-httpsgithubcommicrosoftazurebackupvmsnapshotpluginconfig-content-should-be-similar-to-following"></a>1. Descargue VMSnapshotScriptPluginConfig.json desde https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. El contenido debe ser similar al siguiente:

```azurecli
{
    "pluginName" : "ScriptRunner",
    "preScriptLocation" : "",
    "postScriptLocation" : "",
    "preScriptParams" : ["", ""],
    "postScriptParams" : ["", ""],
    "preScriptNoOfRetries" : 0,
    "postScriptNoOfRetries" : 0,
    "timeoutInSeconds" : 30,
    "continueBackupOnFailure" : true,
    "fsFreezeEnabled" : true
}
```

#### <a name="2-create-etcazure-folder-on-vm"></a>2. Cree la carpeta /etc/azure en la máquina virtual

```bash
$ sudo su -
# mkdir /etc/azure
# cd /etc/azure
```

#### <a name="3-copy-vmsnapshotscriptpluginconfigjson-file-to-folder-etcazure"></a>3. Copie el archivo VMSnapshotScriptPluginConfig.json en la carpeta /etc/azure.

#### <a name="4-edit-the-vmsnapshotscriptpluginconfigjson-file-to-included-the-prescriptlocation-and-postscriptlocation-parameters-for-example"></a>4. Edite el archivo VMSnapshotScriptPluginConfig.json para incluir los parámetros PreScriptLocation y PostScriptlocation. Por ejemplo:
```azurecli
{
    "pluginName" : "ScriptRunner",
    "preScriptLocation" : "/etc/azure/pre_script.sh",
    "postScriptLocation" : "/etc/azure/post_script.sh",
    "preScriptParams" : ["", ""],
    "postScriptParams" : ["", ""],
    "preScriptNoOfRetries" : 0,
    "postScriptNoOfRetries" : 0,
    "timeoutInSeconds" : 30,
    "continueBackupOnFailure" : true,
    "fsFreezeEnabled" : true
}
```
#### <a name="5-create-the-pre-and-post-script-files"></a>5. Cree los archivos de los scripts anterior y posterior

Este es un ejemplo de script anterior y posterior de una copia de seguridad en frío (apagado y reinicio)

Para /etc/azure/pre_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
```

Para /etc/azure/post_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
```

Este es un ejemplo de script anterior y posterior de una copia de seguridad en caliente

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
```

Para /etc/azure/post_script.sh

```bash
v_date=`date +%Y%m%d%H%M`
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle
su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
```

Para /etc/azure/pre_script.sql, debe modificar el contenido del archivo según las necesidades

```bash
alter tablespace SYSTEM begin backup;
...
...
alter system switch logfile;
alter system archive log stop;
```

Para /etc/azure/post_script.sql, debe modificar el contenido del archivo según las necesidades

```bash
alter tablespace SYSTEM end backup;
...
...
alter system archive log start;
```

#### <a name="6-change-file-permissions"></a>6. Cambie los permisos de los archivos

```bash
# chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
# chmod 700 /etc/azure/pre_script.sh
# chmod 700 /etc/azure/post_script.sh
```

#### <a name="7-test-the-scripts-sign-in-as-root-make-sure-no-errors"></a>7. Pruebe los scripts (inicie sesión como raíz) y asegúrese de que no hay errores

```bash
# /etc/azure/pre_script.sh
# /etc/azure/post_script.sh
```

Para más información, consulte el artículo sobre la [copia de seguridad coherente con la aplicación para máquinas virtuales Linux](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Paso 5: Uso de los almacenes de Azure Recovery Services para las copias de seguridad de máquina virtual

1.  Inicie sesión en Azure Portal y busque un almacén de Recovery Services.
![Página de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Haga clic en el botón **Agregar** para agregar un almacén nuevo.
![Página de incorporación de almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Para continuar, haga clic en **myVault**. La página de detalles aparecerá.
![Página de detalles de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Haga clic en el botón **Copia de seguridad**. A continuación, agregue el objetivo, la directiva y los elementos de la copia de seguridad.
![Página de la copia de seguridad de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Como **Backup goal** (Objetivo de la copia de seguridad), use los valores **Azure** y **Máquina virtual** predeterminados. Haga clic en **Aceptar** para continuar.
![Página de detalles de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Para **Directiva de copia de seguridad**, use **DefaultPolicy** (Directiva predeterminada) o **Crear nueva directiva**. Haga clic en **Aceptar** para continuar.
![Página de los detalles de la directiva de copia de seguridad de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Seleccione la casilla **myVM1**, haga clic en **Aceptar** y en **Enable backup** (Habilitar copia de seguridad).
![Página de los detalles de los elementos de la copia de seguridad de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Tras hacer clic en **Enable backup** (Habilitar copia de seguridad), el proceso no se inicia hasta que expira el tiempo programado. Para configurar la copia de seguridad inmediata, complete el paso siguiente.

8.  Haga clic en **Elementos de copia de seguridad** y, en **Backup Item Count** (Recuento de elementos de copia de seguridad), haga clic en el número de elementos de copia de seguridad.

    ![Página de detalles del almacén myVault de Recovery Services](./media/oracle-backup-recovery/recovery_service_08.png)

9.  En el lado derecho de la página, haga clic en el botón de puntos suspensivos (**...** ) y en **Realizar copia de seguridad ahora**.

    ![Comando Realizar copia de seguridad ahora de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_09.png)

10. Haga clic en el botón **Copia de seguridad**, espere a que termine el proceso y vaya al paso 6: Eliminación de los archivos de la base de datos.
Para ver el estado del trabajo de copia de seguridad, haga clic en **Trabajos**.
![Página del trabajo de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_10.png)

    El estado del trabajo de copia de seguridad aparece en la siguiente imagen.

    ![Página del trabajo de los almacenes de Recovery Services con el estado](./media/oracle-backup-recovery/recovery_service_11.png)

11. Para una copia de seguridad coherente con la aplicación, resuelva los errores del archivo de registro /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Paso 6: Eliminación de los archivos de la base de datos 
Más adelante en este artículo aprenderá a probar el proceso de recuperación. Para poder probarlo, tiene que eliminar los archivos de la base de datos.

1.  Elimine los archivos de copia de seguridad y el espacio de tabla:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Cierre la instancia de Oracle (paso opcional):

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-recovery-services-vaults"></a>Restauración de los archivos eliminados de almacenes de Recovery Services
Para restaurar los archivos eliminados, realice el procedimiento siguiente:

1. Inicie sesión en Azure Portal y busque el almacén *myVault* de Recovery Services. En la esquina superior derecha, en **Elementos de copia de seguridad**, haga clic en el número de elementos.
![Elementos de copia de seguridad del almacén myVault de Recovery Services](./media/oracle-backup-recovery/recovery_service_12.png)

2. En **Backup Item Count** (Recuento de elementos de copia de seguridad), haga clic en el número de elementos.
![Número de máquinas virtuales de Azure para la copia de seguridad en los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_13.png)

3. Haga clic en **Recuperación de archivos (vista previa)**.
![Captura de pantalla de la página de recuperación de archivos de los almacenes de Recovery Services](./media/oracle-backup-recovery/recovery_service_14.png)

4. Haga clic en **Descargar Script**y guarde el archivo de descarga (.sh) en una carpeta de la máquina del cliente.
![Opciones de almacenamiento del archivo de script descargado](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copie el archivo .sh en la máquina virtual.

    En el ejemplo siguiente muestra cómo utilizar un comando de copia de seguridad (scp) para mover el archivo a la máquina virtual. También puede copiar el contenido en el portapapeles y pegarlo en un nuevo archivo que se haya configurado en la máquina virtual.

    > [!IMPORTANT]
    > En el ejemplo siguiente, asegúrese de actualizar los valores de dirección IP y carpeta. Los valores deben corresponder a la carpeta donde se guarda el archivo.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Cambie el archivo para que sea propiedad de la raíz.

    En el ejemplo siguiente, cambie el archivo para que sea propiedad de la raíz y, después, los permisos.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    En el ejemplo siguiente se muestra lo que verá después de ejecutar el script anterior. Cuando se le pregunte si desea continuar, escriba **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Se confirmará el acceso a los volúmenes montados.

    Para salir, escriba **q** y busque los volúmenes montados. Para crear una lista de los volúmenes agregados, en un símbolo del sistema, escriba **df -k**.

    ![comando df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Use el siguiente script para copiar los archivos que faltan en las carpetas.

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. En el siguiente script, utilice RMAN para recuperar la base de datos:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Desmonte el disco.

    En Azure Portal, haga clic en **Unmount Disks** (Desmontar discos).

    ![Comando Unmount Disks (Desmontar discos)](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Restauración de toda la máquina virtual

En lugar de restaurar los archivos eliminados de los almacenes de Recovery Services, puede restaurar toda la máquina virtual.

### <a name="step-1-drop-the-myvm"></a>Paso 1: Eliminación de myVM

*   Inicie sesión en Azure Portal, vaya al almacén *myVM* y seleccione **Eliminar**.

    ![Comando Eliminar](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Paso 2: Recuperación de la máquina virtual

1.  Vaya a **Almacenes de Recovery Services** y seleccione **myVault**.
![Entrada de myVault que aparece en la interfaz de usuario](./media/oracle-backup-recovery/recover_vm_02.png)

2.  En la esquina superior derecha, en **Elementos de copia de seguridad**, haga clic en el número de elementos.
![Elementos de copias de seguridad de myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  En **Backup Item Count** (Recuento de elementos de copia de seguridad), haga clic en el número de elementos.
![Página de la máquina virtual de recuperación](./media/oracle-backup-recovery/recover_vm_04.png)

4.  En el lado derecho de la página, haga clic en el botón de puntos suspensivos (**...** ) y en **Restore VM** (Restaurar máquina virtual).
![Comando Restore VM (Restaurar máquina virtual)](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Seleccione el elemento que desea restaurar y haga clic en **Aceptar**.
![Seleccione un punto de restauración](./media/oracle-backup-recovery/recover_vm_06.png) Verá una barra vertical en azul si ha habilitado la copia de seguridad coherente con la aplicación.

6.  Seleccione el **Nombre de la máquina Virtual**, seleccione el **Grupo de recursos** y haga clic en **Aceptar**.
![Valores de Restore configuration (Configuración de la restauración)](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Para restaurar la máquina virtual, en la esquina inferior izquierda de la interfaz de usuario (consulte la imagen anterior), haga clic en **Restaurar**.

8.  Para ver el estado del proceso de restauración, haga clic en **Trabajos**.
![Comando de estado de los trabajos de copia de seguridad](./media/oracle-backup-recovery/recover_vm_08.png)

    La siguiente imagen muestra el estado del proceso de restauración.

    ![Estado del proceso de restauración](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Paso 3: Configuración de la dirección IP pública
Una vez restaurada la máquina virtual, configure la dirección IP pública.

1.  Utilice el cuadro de búsqueda para buscar **Direcciones IP públicas**.

    ![Lista de direcciones IP públicas](./media/oracle-backup-recovery/create_ip_00.png)

2.  En la esquina superior izquierda, haga clic en **Agregar**. En **Nombre**, seleccione el nombre de la IP pública del **Grupo de recursos** y seleccione **Usar existente**. Haga clic en **Crear**.

    ![Creación de la dirección IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Para asociar la dirección IP pública a la interfaz NIC para la máquina virtual, busque y seleccione **myVMip** y haga clic en **Asociar**.

    ![Asociación de la dirección IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Para el **Tipo de recurso**, seleccione **Interfaz de red** y la NIC que utiliza la instancia de myVM y, a continuación, haga clic en **Aceptar**.

    ![Selección del tipo de recurso y los valores de NIC](./media/oracle-backup-recovery/create_ip_03.png)

5.  Busque y abra la instancia de myVM que se transfiere desde el portal. La dirección IP asociada a la máquina virtual aparece en la esquina superior derecha.

    ![Valor de la dirección IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Paso 4: Conexión a la máquina virtual

*   Para la conexión, use el script siguiente:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Paso 5: Comprobación de accesibilidad de la base de datos
*   Para probar la accesibilidad, use el script siguiente:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

> [!IMPORTANT]
> Si el comando **startup** de la base de datos genera un error, para recuperarla, consulte el paso 6: uso de RMAN para recuperar la base de datos.

### <a name="step-6-use-rman-to-recover-the-database-optional-step"></a>Paso 6: Uso de RMAN para recuperar la base de datos (paso opcional)
*   Para recuperar la base de datos, utilice el script siguiente:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

La copia de seguridad y la recuperación de la base de datos de Oracle 12C en una máquina virtual Linux de Azure habrán terminado.
## <a name="delete-the-vm"></a>Eliminar la máquina virtual

Cuando ya no necesite la máquina virtual, puede usar el comando siguiente para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Creación de máquinas virtuales de alta disponibilidad](../../linux/create-cli-complete.md)

[Ejemplos de la CLI de Azure para implementación de máquinas virtuales](../../linux/cli-samples.md)

