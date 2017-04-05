


En este tema se describe cómo:

* Inyectar datos en una máquina virtual (VM) de Azure cuando se está aprovisionando.
* Recuperarlos tanto para Windows como para Linux.
* Usar herramientas especiales disponibles en algunos sistemas para detectar y administrar datos personalizados automáticamente.

> [!NOTE]
> En este artículo se describe cómo se pueden insertar datos personalizados mediante una máquina virtual creada con la API de Administración de servicios de Azure. Para ver cómo se usa la API de Administración de recursos de Azure, consulte [la plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Inyección de datos personalizados en su máquina virtual de Azure
Esta característica solamente se admite actualmente en la [interfaz de la línea de comandos de Azure](https://github.com/Azure/azure-xplat-cli). Aquí creamos un archivo `custom-data.txt` que contiene nuestros datos y después los inyectamos en la VM durante el aprovisionamiento. Aunque puede usar cualquiera de las opciones para el comando `azure vm create` , a continuación se muestra un enfoque muy básico:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Uso de datos personalizados en la máquina virtual
* Si la VM de Azure es una VM Windows, el archivo de datos personalizado se guarda en `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Aunque se codificara con base64 para transferirla del equipo local a la nueva VM, se descodifica automáticamente y se puede abrir o usar de inmediato.
  
  > [!NOTE]
  > Si el archivo existe se sobrescribe. La seguridad del directorio se establece en **Sistema: Control total** y **Administradores: Control total**.
  > 
  > 
* Si la VM de Azure es una VM Linux, el archivo de datos personalizados se ubicará en uno de los dos lugares siguientes dependiendo de la distribución. Los datos pueden estar codificados con base64, por lo que tiene que descifrarlos en primer lugar:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Cloud-init en Azure
Si la VM de Azure procede de una imagen de Ubuntu o CoreOS, puede usar CustomData para enviar una cloud-config a cloud-init. O bien, si el archivo de datos personalizados es un script, cloud-init puede simplemente ejecutarlo.

### <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Images
En la mayoría de las imágenes de Linux de Azure, debe modificar "/etc/waagent.conf" para configurar el disco de recursos temporal y el archivo de intercambio. Para más información, consulte la [Guía de usuario del Agente de Linux de Azure](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Sin embargo, en Ubuntu Cloud Images, debe usar cloud-init para configurar el disco de recursos (es decir, el disco "efímero") y la partición de intercambio. Consulte la siguiente página en la wiki de Ubuntu para obtener más detalles: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Pasos siguientes: uso de cloud-init
Para obtener más información, consulte la [documentación de cloud-init para Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Referencia de la API de REST de administración de servicios: Agregar rol](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfaz de línea de comandos de Azure](https://github.com/Azure/azure-xplat-cli)

