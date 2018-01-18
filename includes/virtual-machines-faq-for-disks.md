# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Preguntas más frecuentes sobre los discos de máquina virtual de IaaS de Azure y los discos premium administrados y no administrados

En este artículo se responden algunas de las preguntas más frecuentes acerca de Azure Managed Disks y Azure Premium Storage.

## <a name="managed-disks"></a>Managed Disks

**¿Qué es Azure Managed Disks?**

Managed Disks es una característica que simplifica la administración de discos para máquinas virtuales de IaaS de Azure al controlar automáticamente la administración de la cuenta de almacenamiento. Para obtener más información, consulte [Introducción a Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

**Si creo un disco administrado estándar a partir un disco duro virtual existente con 80 GB de tamaño, ¿cuánto me costará?**

Un disco administrado estándar creado a partir de un disco duro virtual de 80 GB se trata como el siguiente tamaño de disco estándar disponible, es decir, un disco S10. Se le cobrará según el precio de disco S10. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage)para obtener más información.

**¿Existen costes de transacción para los discos administrados estándar?**

Sí. Sí, se le cobrará por cada transacción. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage)para obtener más información.

**Para un disco administrado estándar, ¿se me cobrará por el tamaño real de los datos en el disco o la capacidad aprovisionada del disco?**

Se le cobra en función de la capacidad aprovisionada del disco. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage)para obtener más información.

**¿En qué se diferencian los precios de los discos administrados premium de los de los discos no administrados?**

Los precios de los discos administrados premium son iguales que los de los discos no administrados premium.

**¿Puedo cambiar el tipo de cuenta de almacenamiento (Estándar o Premium) de mis discos administrados?**

Sí. Puede cambiar el tipo de cuenta de almacenamiento de los discos administrados mediante Azure Portal, PowerShell o la CLI de Azure.

**¿Hay alguna manera de copiar o exportar un disco administrado a una cuenta de almacenamiento privada?**

Sí. Puede exportar los discos administrados mediante Azure Portal, PowerShell o la CLI de Azure.

**¿Puedo usar un archivo de disco duro virtual en una cuenta de Azure Storage para crear un disco administrado con una suscripción distinta?**

Nº

**¿Puedo usar un archivo de disco duro virtual en una cuenta de Azure Storage para crear un disco administrado en una región diferente?**

Nº

**¿Hay alguna limitación de escala para los clientes que usen discos administrados?**

Managed Disks elimina los límites asociados a las cuentas de almacenamiento. Aun así, el número de discos administrados por suscripción está limitado a 2000 de forma predeterminada. Puede llamar al soporte técnico para aumentar este número.

**¿Puedo tomar una instantánea incremental de un disco administrado?**

Nº La funcionalidad de instantánea actual realiza una copia completa de un disco administrado. Sin embargo, está previsto admitir instantáneas incrementales en el futuro.

**¿Pueden las máquinas virtuales de un conjunto de disponibilidad estar compuestas de una combinación de discos administrados y no administrados?**

Nº Las máquinas virtuales de un conjunto de disponibilidad deben utilizar únicamente discos administrados o no administrados. Cuando cree un conjunto de disponibilidad, puede elegir qué tipo de discos desea usar.

**¿Es Managed Disks la opción predeterminada en Azure Portal?**

Sí. 

**¿Puedo crear un disco administrado vacío?**

Sí. Puede crear un disco vacío. Un disco administrado se puede crear de forma independiente de una máquina virtual, por ejemplo, sin conectarlo a una máquina virtual.

**¿Qué número de dominios de error se admite para un conjunto de disponibilidad que use Managed Disks?**

En función de la región en la que se encuentre el conjunto de disponibilidad que use Managed Disks, el número de dominios de error admitidos es de 2 o 3.

**¿Cómo se configura una cuenta de almacenamiento estándar para el diagnóstico?**

Se configura una cuenta de almacenamiento privada para el diagnóstico de máquinas virtuales. En el futuro, está previsto cambiar también el diagnóstico a Managed Disks.

**¿Qué tipo de compatibilidad con el Control de acceso basado en roles está disponible para Managed Disks?**

Managed Disks admite tres roles predeterminados fundamentales:

* Propietario: puede administrar todo, incluido el acceso.
* Colaborador: puede administrar todo, excepto el acceso.
* Lector: puede ver todo, pero no realizar cambios.

**¿Hay alguna manera de copiar o exportar un disco administrado a una cuenta de almacenamiento privada?**

Puede obtener un identificador URI de firma de acceso compartido de solo lectura para el disco administrado y usarlo para copiar el contenido a una cuenta de almacenamiento privada o al almacenamiento local.

**¿Puedo crear una copia de mi disco administrado?**

Los clientes pueden crear una instantánea de sus discos administrados y usarla para crear otro disco administrado.

**¿Siguen siendo compatibles los discos no administrados?**

Sí. Admitimos discos administrados y no administrados. Recomendamos que empiece a usar discos administrados para las cargas de trabajo nuevas y migre las cargas de trabajo actuales a discos administrados.


**Si creo un disco de 128 GB y después lo aumento a 130 GB, ¿se me cobrará por el siguiente tamaño de disco (512 GB)?**

Sí.

**¿Puedo crear discos administrados para el almacenamiento con redundancia local, almacenamiento con redundancia geográfica y almacenamiento con redundancia de zona?**

Actualmente, Azure Managed Disks solo admite discos administrados de almacenamiento con redundancia local.

**¿Puedo reducir mis discos administrados?**

Nº En la actualidad no se admite esta característica. 

**¿Se puede interrumpir una concesión en el disco?**

Nº Esto no es posible actualmente, ya que la concesión está presente para evitar una eliminación accidental cuando se está utilizando el disco.

**¿Se puede cambiar la propiedad de nombre de equipo cuando se usa un disco de sistema operativo especializado (no creado mediante la herramienta de preparación del sistema o generalizado) para aprovisionar una máquina virtual?**

Nº No se puede actualizar la propiedad de nombre de equipo. La nueva máquina virtual lo hereda de la máquina virtual principal que se usó para crear el disco del sistema operativo. 

**¿Dónde puedo encontrar plantillas de Azure Resource Manager de ejemplo para crear máquinas virtuales con discos administrados**
* [Lista de plantillas mediante discos administrados](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

## <a name="migrate-to-managed-disks"></a>Migración a Managed Disks 

**¿Qué cambios son necesarios en una configuración del servicio Azure Backup preexistente antes y después de la migración a Managed Disks?**

No es preciso realizar cambios. 

**¿Las copias de seguridad de mi máquina virtual creadas con el servicio Azure Backup antes de la migración seguirán funcionando?**

Sí, las copias de seguridad funcionan sin problemas.

**¿Qué cambios son necesarios en una configuración de Azure Disks Encryption ya existente antes y después de la migración a Managed Disks?**

No es preciso realizar cambios. 

**¿Está permitida la migración automática de un conjunto de escalado de máquinas virtuales (VMSS) existente de discos no administrados a Managed Disks?**

Nº Puede crear un nuevo VMSS con Managed Disks con la imagen del antiguo conjunto de escalado de máquinas virtuales con discos no administrados. 

**¿Puedo crear un disco administrado desde una instantánea de blob en páginas tomada antes de migrar a Managed Disks?**

Nº Puede exportar una instantánea del blob en páginas como un blob en páginas y, a continuación, crear un disco administrado a partir del blob en páginas exportado. 

**¿Se puede realizar una conmutación por error de mis equipos locales protegidos por Azure Site Recovery a una máquina virtual con Managed Disks?**

Sí, puede realizar la conmutación por error a una máquina virtual con Managed Disks.

**¿Hay algún impacto en la migración de las máquinas virtuales de Azure protegidas por Azure Site Recovery (ASR) mediante la replicación de Azure a Azure?**

Sí. No se admite la protección de máquinas virtuales con Managed Disks con ASR de Azure a Azure. Se admitirá a partir de finales del primer trimestre de 2018. 

**¿Puedo migrar máquinas virtuales con discos no administrados que se encuentran en las cuentas de almacenamiento que se hayan cifrado previamente en discos administrados?**

Sí

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks y Storage Service Encryption 

**¿Está habilitado el servicio Storage Service Encryption de forma predeterminada al crear un disco administrado?**

Sí.

**¿Quién administra las claves de cifrado?**

Microsoft administra las claves de cifrado.

**¿Puedo deshabilitar Storage Service Encryption para Managed Disks?**

Nº

**¿Storage Service Encryption está solo disponible en determinadas regiones?**

Nº Está en todas las regiones donde esté disponible Managed Disks. Managed Disks está disponible en todas las regiones públicas y Alemania.

**¿Cómo averiguo si mi disco administrado está cifrado?**

Puede averiguar la hora de creación de un disco administrado desde Azure Portal, la CLI de Azure y PowerShell. Si la hora es posterior al 9 de junio de 2017, el disco está cifrado. 

**¿Cómo puedo cifrar mis discos existentes que se crearon antes del 10 de junio de 2017?**

A partir del 10 de junio de 2017 los nuevos datos escritos en los discos administrados existentes se cifran automáticamente. También tenemos previsto cifrar los datos existentes y el cifrado se realizará de forma asincrónica en segundo plano. Si debe cifrar ahora los datos existentes, cree una copia del disco. Los discos nuevos se cifrarán.

* [Copia de discos administrados mediante la CLI de Azure](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Copia de discos administrados mediante PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**¿Están cifradas las imágenes e instantáneas administradas?**

Sí. Todas las instantáneas e imágenes administradas creadas después del 9 de junio de 2017 se cifran automáticamente. 

**¿Puedo convertir máquinas virtuales con discos no administrados que se encuentran en las cuentas de almacenamiento o que se hayan cifrado previamente en discos administrados?**

Sí

**¿Se cifrará también un VHD exportado de un disco administrado o de una instantánea?**

Nº Pero si exporta un disco duro virtual a una cuenta de almacenamiento cifrada desde un disco administrado cifrado o una instantánea, en este caso se cifra. 

## <a name="premium-disks-managed-and-unmanaged"></a>Discos premium: tanto administrados como no administrados

**Si una máquina virtual usa una serie de tamaño que admite Premium Storage, como DSv2, ¿puedo conectar discos de datos tanto premium como estándar?** 

Sí.

**¿Puedo conectar discos de datos tanto premium como estándar a una serie de tamaño que no admita Premium Storage, por ejemplo, las series D, Dv2, G o F?**

Nº Solo puede conectar discos de datos estándar a máquinas virtuales que no utilicen una serie de tamaño que admita Premium Storage.

**Si creo un disco de datos premium a partir un disco duro virtual existente con 80 GB, ¿cuánto me costará?**

Un disco de datos premium creado a partir de un disco duro virtual de 80 GB se trata como el siguiente tamaño de disco premium disponible, es decir, P10. Se le cobrará según el precio de disco P10.

**¿Hay costos de transacción cuando se usa Premium Storage?**

Existe un costo fijo para cada tamaño de disco que esté aprovisionado con límites específicos de IOPS y rendimiento. Los demás costes son por el ancho de banda de salida y la capacidad para instantáneas, si corresponde. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/storage)para obtener más información.

**¿Cuáles son los límites de IOPS y rendimiento que puedo obtener de la caché de disco?**

Los límites combinados de memoria caché y SSD local para la serie DS son 4,000 IOPS por núcleo y 33 MB por segundo y núcleo. La serie GS ofrece 5000 IOPS por núcleo y 50 MB por segundo y núcleo.

**¿Es el SSD local compatible con máquinas virtuales de Managed Disks?**

El SSD local es un almacenamiento temporal que se incluye con una máquina virtual de discos administrados. No existe ningún costo extra para este almacenamiento temporal. Recomendamos que no use este SSD local para almacenar los datos de la aplicación, ya que no se conserva en Azure Blob Storage.

**¿Hay alguna repercusión en el uso de TRIM en discos premium?**

No hay ningún inconveniente a la hora de usar TRIM en discos de Azure, ya sea en discos estándar o premium.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Nuevos tamaños de discos: tanto administrados como no administrados

**¿Cuál es el mayor tamaño de disco compatible con discos de datos y sistema operativo?**

El tipo de partición compatible con Azure para un disco del sistema operativo es el registro de arranque maestro (MBR). El formato MBR admite un disco de hasta 2 TB. El tamaño máximo admitido por Azure para un disco del sistema operativo es de 2 TB. Azure admite hasta 4 TB para discos de datos. 

**¿Cuál es el mayor tamaño de blob en páginas admitido?**

El mayor tamaño blob en páginas que admite Azure es 8 TB (8191 GB). No se admiten blobs en páginas mayores de 4 TB (4095 GB) conectados a una máquina virtual como discos de datos o de sistema operativo.

**¿Es necesario usar una nueva versión de las herramientas de Azure para crear, conectar, cambiar el tamaño y cargar discos de más de 1 TB?**

No es necesario actualizar las herramientas de Azure existentes para crear, conectar o cambiar el tamaño de los discos de más de 1 TB. Para cargar el archivo de disco duro virtual del entorno local directamente en Azure como un blob en páginas o un disco no administrado, debe usar los conjuntos de herramientas más recientes:

|Herramientas de Azure      | Versiones compatibles                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Número de versión 4.1.0: versión de junio de 2017 o posterior|
|CLI de Azure v1     | Número de versión 0.10.13: versión de mayo de 2017 o posterior|
|AzCopy           | Número de versión 6.1.0: versión de junio de 2017 o posterior|

La compatibilidad con CLI de Azure v2 y Azure Storage Explorer estará disponible próximamente. 

**¿Se admiten los tamaños de disco P4 y P6 para blobs en páginas o discos no administrados?**

Nº Los tamaños de disco P4 (32 GB) y P6 (64 GB) solo son compatibles con discos administrados. La compatibilidad para blobs en páginas y discos no administrados estará disponible próximamente.

**Si mi disco administrado premium existente de menos de 64 GB se creó antes de habilitar el disco pequeño (alrededor del 15 de junio de 2017), ¿cómo se factura?**

Los discos pequeños premium de menos de 64 GB se siguen facturando según el plan de tarifa P10. 

**¿Cómo puedo cambiar el nivel de disco de los discos pequeños premium menores de 64 GB de P10 a P4 o P6?**

Puede crear una instantánea de los discos pequeños y, después, crear un disco para cambiar automáticamente el plan de tarifa a P4 o P6, en función del tamaño aprovisionado. 


## <a name="what-if-my-question-isnt-answered-here"></a>Mi pregunta no está respondida aquí. ¿Qué debo hacer?

Si su pregunta no aparece aquí, háganoslo saber y lo ayudaremos a encontrar una respuesta. Puede publicar una pregunta al final de este artículo en los comentarios. Para ponerse en contacto con el equipo de Azure Storage y otros miembros de la Comunidad sobre este artículo, use el [foro de Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) de MSDN.

Para solicitar características, envíe sus solicitudes e ideas al [foro de comentarios de Azure Storage](https://feedback.azure.com/forums/217298-storage).
