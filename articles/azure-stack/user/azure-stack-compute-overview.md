---
title: "Introducción a máquinas virtuales de Azure Stack"
description: "Más información sobre máquinas virtuales de Azure Stack"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 68da653052d0e3dfd66d6b65958046e42cefce73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introducción a máquinas virtuales de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

## <a name="overview"></a>Información general
Una máquina virtual de Azure Stack es un tipo de recurso informático escalable y a petición que ofrece Azure Stack. Por lo general, elegirá una máquina virtual cuando necesite más control sobre su entorno informático del que ofrecen las otras opciones. En este artículo se proporciona información sobre lo que debe considerar antes de crear una máquina virtual, cómo crearla y cómo administrarla.

Una máquina virtual de Azure Stack le da la flexibilidad de virtualización sin necesidad de administrar máquinas o clústeres individuales. Sin embargo, aún necesita mantener la máquina virtual con tareas como configurar, aplicar revisiones e instalar el software que se ejecuta en ella.

Las máquinas virtuales de Azure Stack pueden usarse de diversas maneras. Por ejemplo:

* **Desarrollo y pruebas**: las máquinas virtuales de Azure Stack ofrecen una manera rápida y sencilla de crear un equipo con configuraciones específicas necesarias para codificar y probar una aplicación.

* **Aplicaciones en la nube**: como la demanda de la aplicación puede fluctuar, tendría sentido desde el punto de vista económico ejecutarla en una máquina virtual en Azure Stack. Paga por las máquinas virtuales adicionales cuando las necesite y las desactiva cuando ya no sean necesarias.

* **Centro de datos ampliado**: las máquinas virtuales de una red virtual de Azure Stack se pueden conectar fácilmente a la red de su organización o a Azure.

El número de máquinas virtuales usadas por su aplicación se puede escalar vertical y horizontalmente a la cifra necesaria para satisfacer sus necesidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>¿Qué hay que considerar antes de crear una máquina virtual?

Siempre hay gran cantidad de consideraciones de diseño cuando se crea una infraestructura de aplicaciones en Azure Stack. Es importante pensar en estos aspectos de una máquina virtual antes de empezar:

- Los nombres de los recursos de la aplicación
- El tamaño de la máquina virtual
- El número máximo de máquinas virtuales que se pueden crear
- El sistema operativo que ejecuta la máquina virtual
- La configuración de la máquina virtual después de iniciarse 
- Los recursos relacionados que necesita la máquina virtual

### <a name="naming"></a>Nomenclatura

Una máquina virtual tiene un nombre asignado y un nombre de equipo configurado como parte del sistema operativo. El nombre de una máquina virtual puede tener hasta 15 caracteres.

Si usa Azure Stack para crear el disco de sistema operativo, el nombre del equipo y el nombre de la máquina virtual son iguales. Si carga y usa su propia imagen que contenga un sistema operativo ya configurado y la usa para crear una máquina virtual, los nombres pueden ser diferentes. Como procedimiento recomendado, cuando cargue su propio archivo de imagen, haga que el nombre del equipo en el sistema operativo y el nombre de la máquina virtual sean iguales.

### <a name="vm-size"></a>Tamaño de VM

El tamaño de la máquina virtual que use depende de la carga de trabajo que vaya a ejecutar. El tamaño que elija determina factores tales como la capacidad de almacenamiento, la memoria y la capacidad de procesamiento. Azure Stack ofrece una amplia variedad de tamaños para admitir muchos tipos de usos.

### <a name="vm-limits"></a>Límites de máquina virtual

Su suscripción tiene límites de cuota predeterminados que pueden afectar a la implementación de numerosas máquinas virtuales en su proyecto. El límite actual por suscripción es 20 máquinas virtuales por región.

### <a name="operating-system-disks-and-images"></a>Imágenes y discos del sistema operativo

Las máquinas virtuales utilizan discos duros virtuales (VHD) para almacenar el sistema operativo y datos. Estos discos también se usan para las imágenes entre las que se puede elegir para instalar un sistema operativo.
Azure Stack proporciona un Marketplace para usar con diversas versiones y tipos de sistemas operativos. Las imágenes de Marketplace se identifican mediante el publicador de la imagen, la oferta, la SKU y la versión (normalmente, la versión se especifica como la más reciente).

En la tabla siguiente se muestran algunas maneras de encontrar la información sobre una imagen:


|Método|Descripción|
|---------|---------|
|Portal de Azure Stack|Los valores se especifican automáticamente cuando se selecciona una imagen para usarla.|
|PowerShell de Azure Stack|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|API de REST     |[List image publishers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers) (Lista de publicadores de imágenes)<br>[List image offers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers) (Lista de ofertas de imágenes)<br>[List image SKUs](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) (Lista de SKU de imágenes)|

Puede elegir cargar y usar su propia imagen. Si lo hace, el nombre del anunciante, la oferta y la SKU no se utilizan.

### <a name="extensions"></a>Extensiones

Las extensiones de máquina virtual ofrecen funcionalidades adicionales de máquina virtual por medio de la configuración posterior a la implementación y tareas automatizadas.
Pueden llevarse a cabo estas tareas comunes mediante las extensiones:

* Ejecutar scripts personalizados: la extensión de script personalizado ayuda a configurar cargas de trabajo en la máquina virtual al ejecutar su script cuando se aprovisiona la máquina virtual.
* Implementar y administrar configuraciones: la extensión de configuración de estado deseado (DSC) de PowerShell ayuda a configurar DSC en una máquina virtual para administrar entornos y configuraciones.
* Recopilar datos de diagnóstico: la extensión Azure Diagnostics ayuda a configurar la máquina virtual para que recopile datos de diagnóstico que sirvan para supervisar el estado de la aplicación.

### <a name="related-resources"></a>Recursos relacionados

Los recursos de la tabla siguiente se usan en la máquina virtual y deben ya existir o crearse al tiempo que la máquina virtual.


|Recurso|Obligatorio|Descripción|
|---------|---------|---------|
|Grupos de recursos|Sí|La máquina virtual debe encontrarse en un grupo de recursos.|
|Cuenta de almacenamiento|Sí|La máquina virtual necesita la cuenta de almacenamiento para almacenar sus discos duros virtuales.|
|Red virtual|Sí|La máquina virtual debe ser miembro de una red virtual.|
|Dirección IP pública|No|La máquina virtual puede tener una dirección IP pública asignada para acceder remotamente a ella.|
|Interfaz de red|Sí|La máquina virtual necesita la interfaz de red para comunicarse en la red.|
|Discos de datos|No|La máquina virtual puede incluir discos de datos para ampliar las funcionalidades de almacenamiento.|

## <a name="how-do-i-create-my-first-vm"></a>¿Cómo se crea la primera máquina virtual?

Hay varias opciones para crear una máquina virtual. La elección depende del entorno.
En la tabla siguiente se proporciona información para comenzar a crear la máquina virtual.


|Método|Artículo|
|---------|---------|
|Portal de Azure Stack|Creación de una máquina virtual Windows con el portal de Azure Stack<br>[Creación de máquinas virtuales Linux con el portal de Azure Stack](azure-stack-quick-linux-portal.md)|
|Plantillas|Las plantillas de inicio rápido de Azure Stack se encuentran en:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Creación de una máquina virtual Windows con PowerShell en Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Creación de una máquina virtual Linux con PowerShell en Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Creación de una máquina virtual Windows con CLI en Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Creación de una máquina virtual Linux con CLI en Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="how-do-i-manage-the-vm-that-i-created"></a>¿Cómo administro la máquina virtual que he creado?

Las máquinas virtuales pueden administrarse mediante un portal basado en el explorador, herramientas de línea de comandos con compatibilidad para scripts o directamente a través de API. Algunas tareas de administración habituales que puede realizar es obtener información sobre una máquina virtual, iniciar sesión en una máquina virtual, administrar la disponibilidad y realizar copias de seguridad.

### <a name="get-information-about-a-vm"></a>Obtención información acerca de una máquina virtual

En la tabla siguiente se muestran algunas maneras en que puede obtener información sobre una máquina virtual.


|Método|Descripción|
|---------|---------|
|Portal de Azure Stack|En el menú del concentrador, haga clic en Máquinas virtuales y seleccione la máquina virtual en la lista. En la página de la máquina virtual, puede ver información general, establecer valores y supervisar métricas.|
|Azure PowerShell|La administración de las máquinas virtuales es similar en Azure y Azure Stack. Para más información sobre el uso de PowerShell, consulte el siguiente tema de Azure:<br>[Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|SDK de cliente|El uso de C# para administrar máquinas virtuales es similar en Azure y Azure Stack. Para obtener más información, consulte el siguiente tema de Azure:<br>[Creación y administración de máquinas virtuales Windows en Azure mediante C#](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-the-vm"></a>Conexión a la máquina virtual

Puede usar el botón **Conectar** en el portal de Azure Stack para conectarse a la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
* [Consideraciones sobre máquinas virtuales en Azure Stack](azure-stack-vm-considerations.md)

