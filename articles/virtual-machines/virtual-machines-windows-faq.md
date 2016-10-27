<properties
    pageTitle="Preguntas más frecuentes sobre máquinas virtuales Windows | Microsoft Azure"
    description="Proporciona respuestas a algunas de las preguntas frecuentes sobre las máquinas virtuales Windows creadas con el modelo de Resource Manager."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>


# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Preguntas más frecuentes sobre máquinas virtuales Windows 


En este artículo se responden algunas preguntas frecuentes que los usuarios plantean sobre las máquinas virtuales Windows creadas en Azure mediante el modelo de implementación de Resource Manager. Para la versión de Linux de este tema, consulte [Preguntas frecuentes sobre las máquinas virtuales de Linux](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm?"></a>¿Qué puedo ejecutar en una máquina virtual de Azure?

Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Para obtener información sobre la directiva de soporte de software de servidor de Microsoft ejecutado en Azure, consulte [Microsoft server software support for Azure Virtual Machines](https://support.microsoft.com/kb/2721672)

Existen determinadas versiones de Windows 7 y Windows 8.1 para suscriptores de ventajas de MSDN Azure y suscriptores de pago por uso de desarrollo y prueba de MSDN, para tareas de desarrollo y prueba. Para obtener más información, como instrucciones y limitaciones, consulte [Imágenes de cliente de Windows para los suscriptores de MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine?"></a>¿Cuánto almacenamiento puedo usar con una máquina virtual?

Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](https://azure.microsoft.com/pricing/details/storage/).


## <a name="how-can-i-access-my-virtual-machine?"></a>¿Cómo puedo tener acceso a mi máquina virtual?

Establezca una conexión remota mediante conexión a Escritorio remoto (RDP) para una máquina virtual Windows. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](virtual-machines-windows-connect-logon.md). Se admite un máximo de 2 conexiones simultáneas, a menos que el servidor esté configurado como un host de sesión de servicios de escritorio remoto.  


Si tiene problemas con Escritorio remoto, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Si está familiarizado con Hyper-V, puede que esté buscando una herramienta similar a VMConnect. Azure no ofrece una herramienta similar porque no admite el acceso de la consola a una máquina virtual.

## <a name="can-i-use-the-temporary-disk-(the-d:-drive-by-default)-to-store-data?"></a>¿Puedo usar el disco temporal (la unidad D: de forma predeterminada) para almacenar datos?

No utilice el disco temporal para almacenar datos. Solo proporciona almacenamiento de forma temporal, por lo que se arriesgaría a perder datos que no se pueden recuperar. La pérdida de datos puede ocurrir cuando se mueve la máquina virtual a un host diferente. Cambiar el tamaño de una máquina virtual, actualizar el host o un error de hardware en el host son algunas de las razones por las que se puede mover una máquina virtual.

Si tiene una aplicación que necesita usar la letra de unidad D:, puede reasignar las letras de unidad para que el disco temporal utilice otra distinta a D:. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk?"></a>¿Cómo puedo cambiar la letra de la unidad del disco temporal?

Para cambiar la letra de la unidad, mueva el archivo de paginación y reasigne las letras de unidad; sin embargo, debe asegurarse de realizar los pasos en un orden específico. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set?"></a>¿Puedo agregar una máquina virtual existente a un conjunto de disponibilidad?

No. Si desea que la máquina virtual forme parte de un conjunto de disponibilidad, debe crearla dentro del conjunto. Actualmente no es posible agregar una máquina virtual a un conjunto de disponibilidad una vez creada.

## <a name="can-i-upload-a-virtual-machine-to-azure?"></a>¿Puedo cargar una máquina virtual en Azure?

Sí. Para obtener instrucciones, consulte [Carga de una imagen de VM de Windows en Microsoft Azure para implementaciones del Administrador de recursos ](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk?"></a>¿Puedo cambiar el tamaño del disco del sistema operativo?

Sí. Para obtener instrucciones, consulte [Cómo ampliar la unidad de sistema operativo de una máquina virtual en un grupo de recursos de Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm?"></a>¿Puedo copiar o clonar una máquina virtual de Azure existente?

Sí. Para más información, consulte, [Creación de una copia de una máquina virtual Windows en el modelo de implementación de Resource Manager](virtual-machines-windows-vhd-copy.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager?"></a>¿Por qué no veo las regiones de Canadá central y Canadá oriental por medio de Azure Resource Manager?

Las dos nuevas áreas Canadá central y Canadá oriental no se registran automáticamente para la creación de máquinas virtuales en las suscripciones de Azure existentes. Este registro se realizará automáticamente cuando se implementa una máquina virtual mediante el Portal de Azure en cualquier otra región usando Azure Resource Manager. Después de implementar una máquina virtual en cualquier otra región de Azure, las áreas nuevas deberán estar disponibles para las máquinas virtuales siguientes.

## <a name="does-azure-support-linux-vms?"></a>¿Se admiten máquinas virtuales Linux en Azure?

Sí. Para crear rápidamente una máquina virtual Linux de prueba, consulte [Creación de una máquina virtual de Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-it's-created?"></a>¿Puedo agregar una NIC a mi máquina virtual después de crearla?

No. Solo se puede agregar una NIC en el momento de la creación.

## <a name="are-there-any-computer-name-requirements?"></a>¿Hay algún requisito de nombre de equipo?

Sí. El nombre del equipo puede tener un máximo de 15 caracteres. Consulte [Directrices de nomenclatura de la infraestructura](virtual-machines-windows-infrastructure-naming-guidelines.md) para obtener más información sobre la nomenclatura de sus recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm?"></a>¿Cuáles son los requisitos de nombre de usuario cuando se crea una VM?

Los nombres de usuario pueden tener un máximo de 20 caracteres y no pueden terminar con un punto ("."). 

No se permiten los siguientes nombres de usuario:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> user </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> owner </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm?"></a>¿Cuáles son los requisitos de contraseña cuando se crea una VM?

Las contraseñas deben tener entre 8 y 123 caracteres y deben cumplir 3 de estos 4 requisitos de complejidad:

- Deben incluir caracteres en minúsculas.
- Deben incluir caracteres en mayúsculas.
- Deben incluir un dígito.
- Deben incluir un carácter especial (REGEX.MATCH [\W_]).

No se permiten las siguientes contraseñas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou!</td>
    </tr>
</table>



<!--HONumber=Oct16_HO2-->


