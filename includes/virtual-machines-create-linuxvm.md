
1. Conéctese a su suscripción de Azure mediante los pasos explicados en [Conexión a Azure desde la CLI de Azure](../articles/xplat-cli-connect.md).

2. Asegúrese de que está en el modo de implementación clásica, para lo que debe usar:

        azure config mode asm

3. Busque la imagen de Linux que desea cargar entre las imágenes disponibles:

        azure vm image list | grep "Linux"

   En una ventana de símbolo del sistema de Windows, use **find** en lugar de grep.

4. Use `azure vm create` para crear una máquina virtual con la imagen de Linux de la lista anterior. Este paso crea un servicio en la nube y una cuenta de almacenamiento. Esta máquina virtual también se puede conectar a un servicio de nube existente con una opción `-c`. También crea un punto de conexión SSH para iniciar sesión en la máquina virtual Linux con la opción `-e`.

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-es-ES-30GB -g adminUser -p P@ssw0rd! -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-es-ES-30GB
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Para una máquina virtual Linux, debe proporcionar la opción `-e` en `vm create`. No es posible habilitar SSH después de que se haya creado la máquina virtual. Para obtener más información sobre SSH, vea [Uso de SSH con Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md).

    La imagen *b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-es-ES-30GB* es la elegida de la lista de imágenes en el paso anterior. *MyTestVM* es el nombre de la nueva máquina virtual, y *adminUser* es el nombre de usuario para SSH en la máquina virtual. Estás variables se pueden reemplazar en función de los requisitos. Para más información sobre este comando, vaya a [Uso de la CLI de Azure con el modelo de implementación clásica](virtual-machines-command-line-tools.md).

5. La máquina virtual Linux recién creada aparece en la lista proporcionada por:

        azure vm list

6. Los atributos de la máquina virtual se pueden comprobar con el comando:

        azure vm show MyTestVM

7. La máquina virtual recién creada está lista para iniciarse con el comando `azure vm start`.

## Pasos siguientes
Para más información sobre estos comandos de máquina virtual de la CLI de Azure, consulte [Uso de la CLI de Azure con la API de implementación clásica](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0824_2016-->