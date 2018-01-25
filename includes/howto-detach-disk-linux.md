Cuando ya no necesite un disco de datos que se encuentra conectado a una máquina virtual, puede desconectarlo fácilmente. Cuando se separa un disco de la máquina virtual, el disco no lo elimina del almacenamiento. Si desea volver a usar los datos existentes en el disco, puede volver a conectarlo a la misma máquina virtual (o a otra).  

> [!NOTE]
> Una máquina virtual en Azure utiliza distintos tipos de discos, como un disco del sistema operativo, un disco temporal local y discos de datos opcionales. Para más información, consulte [Acerca de los discos y los discos duros virtuales para Virtual Machines](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Un disco del sistema operativo no se puede desconectar, salvo que también se elimine la máquina virtual.

## <a name="find-the-disk"></a>Buscar el disco
Antes de poder desconectar un disco de una máquina virtual, es necesario conocer el número de unidad lógica (LUN), que es un identificador para el disco que se va a desconectar. Para ello, sigue estos pasos:

1. Abra la CLI de Azure y [conéctese a su suscripción de Azure](/cli/azure/authenticate-azure-cli). Procure estar en el modo de administración de servicios de Azure (`azure config mode asm`).
2. Compruebe qué discos están conectados a la máquina virtual. En el ejemplo siguiente se enumeran los discos de la máquina virtual denominada `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Anota el LUN o **número de unidad lógica** para el disco que quieres desacoplar.

## <a name="remove-operating-system-references-to-the-disk"></a>Supresión de las referencias al sistema operativo en el disco
Antes de desconectar el disco del invitado de Linux, debe asegurarse de que todas las particiones del disco no están en uso. Asegúrese de que el sistema operativo no intenta volver a montarlas después de un reinicio. Estos pasos deshacen la configuración que probablemente se creó al [conectar](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) el disco.

1. Use el comando `lsscsi` para detectar el identificador de dispositivo. `lsscsi` puede instalarse mediante `yum install lsscsi` (en distribuciones basadas en Red Hat) o mediante `apt-get install lsscsi` (en distribuciones basadas en Debian). Puede encontrar el identificador de disco que está buscando utilizando el número de LUN. El último número de la tupla en cada fila es el LUN. En el siguiente ejemplo de `lsscsi`, LUN 0 se asigna a */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Use `fdisk -l <disk>` para detectar las particiones asociadas con el disco que se va a desconectar. En el siguiente ejemplo se muestra la salida de `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Desmonte cada partición enumerada para el disco. El siguiente ejemplo se desmonta `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Utilice el comando `blkid` para detectar los UUID de todas las particiones. La salida es similar a la del ejemplo siguiente:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Quite las entradas del archivo **/etc/fstab** asociado a las rutas de acceso del dispositivo o los UUID de todas las particiones del disco que se va a desvincular.  Las entradas de este ejemplo podrían ser:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    o
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Desacoplar el disco
Después de encontrar el número LUN del disco y de quitar las referencias del sistema operativo, está listo para desvincularlo:

1. Separe el disco seleccionado de la máquina virtual mediante la ejecución del comando `azure vm disk detach
   <virtual-machine-name> <LUN>`. En el ejemplo siguiente se separa LUN `0` de la máquina virtual denominada `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. Para comprobar si el disco se ha desconectado, vuelva a ejecutar el comando `azure vm disk list`. En el ejemplo siguiente se comprueba la máquina virtual denominada `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    El resultado es similar al ejemplo siguiente, que muestra que el disco de datos no está conectado:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

El disco desacoplado permanece en el almacenamiento pero ya no estará acoplado a una máquina virtual.

