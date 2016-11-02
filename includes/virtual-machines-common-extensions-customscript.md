

Desde su lanzamiento, la extensión de scripts personalizados se ha usado ampliamente para configurar cargas de trabajo en máquinas virtuales de Windows y Linux. Con la introducción de las plantillas del Administrador de recursos de Azure, los usuarios ahora pueden crear una única plantilla que no solo aprovisiona la máquina virtual sino que también configura sus cargas de trabajo.

## Acerca de las plantillas del Administrador de recursos de Azure

Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

- [Información general del grupo de recursos](../articles/resource-group-overview.md)
- [Implementación de plantillas con Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-ps-manage.md)

### Requisitos previos

1. Descargue las herramientas de la línea de comandos de Azure para su sistema operativo [aquí](https://azure.microsoft.com/downloads/).
2. Si los scrips se ejecutan en una máquina virtual existente, asegúrese de que el agente de máquina virtual esté habilitado en dicha VM; en caso contrario, siga las instrucciones para [Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage extensions.md) o [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage extensions.md) para instalar una.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. También pueden cargarse los scripts en una cuenta de GitHub.
5. La secuencia de comandos debe crearse de forma tal que la secuencia de comandos de entrada que inicia la extensión inicie, a su vez, otras secuencias de comandos.

## Uso de la extensión de script personalizada

Para realizar la implementación con plantillas, usamos la misma versión de la extensión de scripts personalizados que está disponible para las API de administración de servicios de Azure. La extensión es compatible con los mismos parámetros y escenarios que la carga de archivos en la cuenta de Almacenamiento de Azure o la ubicación de Github. La diferencia clave cuando usa plantillas es que debe especificarse la versión exacta de la extensión, al contrario de la especificación de la versión en el formato majorversion.*.

<!---HONumber=AcomDC_0420_2016-->