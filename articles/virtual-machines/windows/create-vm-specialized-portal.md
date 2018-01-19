---
title: "Creación de una VM Windows desde un VHD especializado en Azure Portal| Microsoft Docs"
description: Cree una nueva VM Windows desde un VHD en Azure Portal.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Creación de una VM desde un VHD en Azure Portal


Hay varias maneras de crear VM en Azure. Si ya tiene un VHD que puede usar o quiere copiar el VHD de una VM existente para usarlo, puede crear una nueva VM adjuntando el VHD como disco del SO. Este proceso *conecta* el VHD a una nueva VM como disco del SO.

También puede crear una nueva VM desde el VHD de una VM que se eliminó. Por ejemplo, si tiene una VM de Azure que no funciona correctamente, puede eliminarla y usar el VHD para crear una nueva. Puede volver a usar el mismo VHD o crear una copia del VHD mediante la creación de una instantánea y, a continuación, de un disco administrado desde la instantánea. Se necesitan algunos pasos más para hacerlo, pero garantiza que pueda conservar el VHD original y también le proporciona una instantánea que puede revertir en caso necesario.

Tiene una VM local que quiere usar para crear una VM en Azure. Puede cargar el VHD y conectarlo a una nueva máquina virtual. Para cargar un VHD, tiene que usar PowerShell u otra herramienta para cargarlo en una cuenta de almacenamiento y, a continuación, crear un disco administrado desde el VHD. Para obtener más información, consulte [Cargar un VHD especializado](create-vm-specialized.md#option-2-upload-a-specialized-vhd).

Si quiere usar una VM o un VHD para crear varias VM, no use este método. Para implementaciones más grandes, debe [crear una imagen](capture-image-resource.md) y, a continuación, [usarla para crear varias VM](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copiar un disco

Cree una instantánea y, a continuación, cree un disco a partir de la instantánea. De este modo, podrá conservar el VHD original como reserva.

1. En el menú izquierdo, haga clic en **Todos los recursos**.
2. En el desplegable **Todos los tipos**, desactive **Seleccionar todo** y, a continuación, desplácese hacia abajo y seleccione **Discos** para buscar los discos disponibles.
3. Haga clic en el disco que quiera usar. Se abrirá la página **Información general** para el disco.
4. En la página Información general, en el menú situado en la parte superior, haga clic en **+ Crear instantánea**. 
5. Escriba un nombre para la instantánea.
6. Elija un **Grupo de recursos** para la instantánea. Puede usar un grupo de recursos existente o crear uno nuevo.
7. Elija si quiere usar almacenamiento estándar (HDD) o Premium (SDD).
8. Cuando haya terminado, haga clic en **Crear** para crear la instantánea.
9. Una vez creada la instantánea, haga clic en **+ Crear un recurso** en el menú izquierdo.
10. En la barra de búsqueda, escriba **disco administrado** y seleccione **Discos administrados** en la lista.
11. En la página **Discos administrados**, haga clic en **Crear**.
12. Escriba un nombre para el disco.
13. Elija un **Grupo de recursos** para el disco. Puede usar un grupo de recursos existente o crear uno nuevo. También será el grupo de recursos en el que creará la VM desde el disco.
14. Elija si quiere usar almacenamiento estándar (HDD) o Premium (SDD).
15. En **Tipo de origen**, asegúrese de que la opción **Instantánea** está seleccionada.
16. En el desplegable **Instantánea de origen**, seleccione la instantánea que quiere usar.
17. Realice cualquier otro ajuste que sea necesario y, a continuación, haga clic en **Crear** para crear el disco.

## <a name="create-a-vm-from-a-disk"></a>Creación de una VM desde un disco

Una vez que tenga el VHD del disco administrado que quiere usar, puede crear la VM en el portal.

1. En el menú izquierdo, haga clic en **Todos los recursos**.
2. En el desplegable **Todos los tipos**, desactive **Seleccionar todo** y, a continuación, desplácese hacia abajo y seleccione **Discos** para buscar los discos disponibles.
3. Haga clic en el disco que quiera usar. Se abrirá la página **Información general** para el disco.
En la página Información general, asegúrese de que **ESTADO DEL DISCO** aparezca como **Sin adjuntar**. Si no es el caso, es posible que tenga que desasociar el disco de la VM o eliminar la VM para liberar el disco.
4. En el menú de la parte superior del panel, haga clic en **+ Crear VM**.
5. En la página **Aspectos básicos** de la nueva VM, escriba un nombre y seleccione un grupo de recursos existente o cree uno nuevo.
6. En la página **Tamaño**, seleccione una página de tamaño de VM y, a continuación, haga clic en **Seleccionar**.
7. En la página **Configuración**, puede dejar que el portal cree todos los recursos nuevos o puede seleccionar una **Red virtual** y un **grupo de seguridad de red** existentes. El portal crea siempre una nueva NIC y una dirección IP pública para la nueva VM. 
8. Realice cambios en las opciones de supervisión y agregue extensiones si es necesario.
9. Cuando haya terminado, haga clic en **Aceptar**. 
10. Si la configuración de la VM supera la validación, haga clic en **Aceptar** para iniciar la implementación.

## <a name="next-steps"></a>pasos siguientes

También puede usar PowerShell para [cargar un VHD en Azure y crear una VM especializada](create-vm-specialized.md).


