1. Conéctese a su suscripción de Azure mediante los pasos enumerados en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../articles/xplat-cli-connect.md).

2. Asegúrese de que está en el modo Administración de servicios, para lo que debe usar:

        azure config mode asm

3. Busque la imagen de Linux que desea cargar entre las imágenes disponibles:

        azure vm image list | grep "Linux"

4. Utilice `azure vm create` para crear una nueva máquina virtual con la imagen de Linux de la lista anterior. Este paso crea un nuevo servicio én la nube, así como una cuenta de almacenamiento nueva. Esta máquina virtual también se puede conectar a un servicio de nube existente con una opción `-c`. También crea un extremo SSH para iniciar sesión en la máquina virtual de Linux con la opción `-e`.

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE]En el caso de una máquina virtual Linux, es preciso especificar la opción `-e` en`vm create`; SSH no se puede habilitar después de que la máquina virtual se haya creado. Para obtener más información sobre SSH, consulte [Utilización de SSH con Linux en Azure](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md).

    Tenga en cuenta que la imagen *b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-opensuse-13.1-20141216-x86-64* es la elegida en la lista en el paso anterior. *MyTestVM* es el nombre de la nueva máquina virtual, y *adminUser* es el nombre de usuario que se utilizará para SSH en la máquina virtual. Estás variables se pueden reemplazar en función de los requisitos. Para obtener más información sobre este comando, consulte [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de servicios de Azure](../articles/virtual-machines/virtual-machines-command-line-tools.md).

5. La máquina virtual con Linux recién creada aparecerá en la lista proporcionada por:

        azure vm list

6. Los atributos de la máquina virtual se pueden comprobar con el comando:

        azure vm show MyTestVM

7. La máquina virtual recién creada está lista para iniciarse con el comando `azure vm start`.

Para obtener más información acerca de estos comandos de la máquina virtual de la CLI de Azure, consulte [Uso de la CLI de Azure con la API de administración de servicios](../articles/virtual-machines/virtual-machines-command-line-tools.md).

<!---HONumber=August15_HO8-->