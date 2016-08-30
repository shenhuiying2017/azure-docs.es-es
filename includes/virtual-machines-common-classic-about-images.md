

Para proporcionar una nueva máquina virtual con un sistema operativo en Azure se usan imágenes. Una imagen también pueden tener uno o más discos de datos. Las imágenes están disponibles desde diferentes fuentes:

-	Azure ofrece imágenes en [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/). Encontrará versiones recientes de Windows Server y distribuciones del sistema operativo Linux. Algunas imágenes también contienen aplicaciones, como SQL Server. Los suscriptores de Pago por uso de MSDN y Ventajas de MSDN tienen acceso a imágenes adicionales.
-	La comunidad de código abierto ofrece imágenes a través de [VM Depot](http://vmdepot.msopentech.com/List/Index).
-	También puede almacenar y usar sus propias imágenes en Azure, bien a través de una captura de una máquina virtual de Azure ya existente para usarla como imagen o a través de la carga de una imagen.

## Acerca de las imágenes de máquina virtual y las de sistema operativo

Pueden usarse dos tipos de imágenes en Azure: *imagen de la máquina virtual* e *imagen de SO*. Una imagen de máquina virtual incluye un sistema operativo y todos los discos conectados a una máquina virtual cuando se crea la imagen. Este es el nuevo tipo de imagen. Antes de la introducción de las imágenes de máquina virtual, una imagen en Azure podía tener sólo un sistema operativo generalizado sin discos adicionales. Una imagen de máquina virtual que contenga sólo un sistema operativo generalizado es básicamente igual que el tipo de imagen original, la imagen SO.

Puede crear sus propias imágenes a partir de una máquina virtual en Azure o una máquina virtual que se ejecute en cualquier sitio desde donde pueda copiar y cargar. Si desea usar una imagen para crear más de una máquina virtual, tendrá que prepararla para usarla como una imagen generalizándola. Para crear una imagen de Windows Server, ejecute el comando Sysprep en el servidor para generalizar antes de cargar el archivo .vhd. Para más información sobre Sysprep, consulte [How to Use Sysprep: An Introduction](http://go.microsoft.com/fwlink/p/?LinkId=392030) (Introducción al uso de Sysprep) y [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Compatibilidad de Sysprep con roles de servidor). Para crear una imagen de Linux, dependiendo de la distribución de software, necesitará ejecutar un conjunto de comandos que son específicos de la distribución, además de ejecutar el Agente de Linux de Azure.

<!---HONumber=AcomDC_0817_2016-->