
1. Inicie sesión en su suscripción de Azure mediante los pasos explicados en [Conexión a Azure desde la CLI de Azure 1.0](/cli/azure/authenticate-azure-cli).

2. Asegúrese de que está en el modo de implementación clásica, como se muestra a continuación:

    ```azurecli
    azure config mode asm
    ```

3. Entre las imágenes de Linux disponibles, elija la que desea cargar como se indica a continuación:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    En una ventana de símbolo del sistema de Windows, use **find** en lugar de grep.
   
4. Use `azure vm create` para crear una máquina virtual con la imagen de Linux de la lista anterior. En este paso se crea un servicio en la nube y una cuenta de almacenamiento. Esta máquina virtual también se puede conectar a un servicio en la nube existente con una opción `-c`. Cree un punto de conexión SSH para iniciar sesión en la máquina virtual Linux con la opción `-e`. En el ejemplo siguiente se crea una máquina virtual denominada `myVM` mediante la imagen `Ubuntu-14_04_4-LTS` de la ubicación `West US` y se agrega un nombre de usuario `ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    La salida es similar a la del ejemplo siguiente:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Para una máquina virtual Linux, debe proporcionar la opción `-e` en `vm create`. No es posible habilitar SSH después de que se haya creado la máquina virtual. Para más información sobre SSH, lea [Creación de claves SSH en Linux y Mac para máquinas virtuales de Linux en Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Los atributos de la máquina virtual se pueden comprobar con el comando `azure vm show`. En el ejemplo siguiente se muestra información de la máquina virtual denominada `myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. Inicie la máquina virtual con el comando `azure vm start` como sigue:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre estos comandos de máquina virtual de la CLI 1.0 de Azure, consulte [Uso de la CLI 1.0 de Azure con la API de implementación clásica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

