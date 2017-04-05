

Desde su lanzamiento, la extensión de scripts personalizados se ha usado ampliamente para configurar cargas de trabajo en máquinas virtuales de Windows y Linux. Con la introducción de las plantillas del Administrador de recursos de Azure, los usuarios ahora pueden crear una única plantilla que no solo aprovisiona la máquina virtual sino que también configura sus cargas de trabajo.

## <a name="about-azure-resource-manager-templates"></a>Acerca de las plantillas de Azure Resource Manager
Las plantillas de Azure Resource Manager le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje JSON definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

* [Información general del grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md)
* [Administración de máquinas virtuales de Azure con Resource Manager y PowerShell](../articles/virtual-machines/windows/ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>Requisitos previos
1. Descargue [aquí](https://azure.microsoft.com/downloads/) las herramientas de la línea de comandos de Azure para su sistema operativo.
2. Si los scripts se van a ejecutar en una máquina virtual existente, asegúrese de que hay algún agente de máquina virtual habilitado en la máquina virtual; si no lo está, siga las indicaciones para este [Linux](../articles/virtual-machines/linux/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) o [Windows](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para instalar uno.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. También pueden cargarse los scripts en una cuenta de GitHub.
5. La secuencia de comandos debe crearse de forma tal que la secuencia de comandos de entrada que inicia la extensión inicie, a su vez, otras secuencias de comandos.

## <a name="using-the-custom-script-extension"></a>Uso de la extensión de script personalizado
Para realizar la implementación con plantillas, usamos la misma versión de la extensión de scripts personalizados que está disponible para las API de administración de servicios de Azure. La extensión admite los mismos parámetros y escenarios que la carga de archivos en la cuenta de Azure Storage o la ubicación de GitHub. La diferencia clave cuando usa plantillas es que debe especificarse la versión exacta de la extensión, al contrario de la especificación de la versión en el formato majorversion.*.

