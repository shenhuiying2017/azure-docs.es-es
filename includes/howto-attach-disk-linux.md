
Para más información acerca de los discos, consulte [Acerca de los discos y los discos duros virtuales para Virtual Machines](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Conectar un disco vacío
1. Abra la CLI de Azure 1.0 y [conéctese a su suscripción de Azure](/cli/azure/authenticate-azure-cli). Procure estar en el modo de administración de servicios de Azure (`azure config mode asm`).
2. Escriba `azure vm disk attach-new` para crear y asociar un nuevo disco, como se muestra en el siguiente ejemplo. Reemplace *myVM* por el nombre de la máquina virtual Linux y especifique el tamaño del disco, en GB *100 GB* en este ejemplo:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Una vez creado y conectado el disco de datos, este se enumera en la salida de `azure vm disk list <virtual-machine-name>` como se muestra en el siguiente ejemplo:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    La salida es similar a la del ejemplo siguiente:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>un disco existente
El acoplamiento de un disco existente requiere que disponga de un .vhd disponible en la cuenta de almacenamiento.

1. Abra la CLI de Azure 1.0 y [conéctese a su suscripción de Azure](/cli/azure/authenticate-azure-cli). Procure estar en el modo de administración de servicios de Azure (`azure config mode asm`).
2. Compruebe si el VHD que quiere asociar ya se ha cargado en la suscripción de Azure:
   
    ```azurecli
    azure vm disk list
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Si no encuentra el disco que desea usar, puede cargar un disco duro virtual local en su suscripción mediante `azure vm disk create` o `azure vm disk upload`. Un ejemplo de `disk create` sería el siguiente:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   También puede usar el comando `azure vm disk upload` para cargar un VHD a una cuenta de almacenamiento específica. [Aquí](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) puede encontrar más información sobre los comandos para administrar discos de datos de una máquina virtual de Azure.

4. Ahora conectamos el VHD que queramos a la máquina virtual:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Asegúrese de reemplazar *myVM* por el nombre de su máquina virtual y *myHD* por el disco duro virtual que desee.

5. Puede comprobar si el disco está conectado a la máquina virtual con `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Después de agregar un disco de datos, tendrá que iniciar sesión en la máquina virtual e inicializar el disco para que la máquina virtual pueda usarlo para el almacenamiento (para más información sobre cómo realizar la inicialización del disco, consulte los pasos siguientes).
> 
> 

