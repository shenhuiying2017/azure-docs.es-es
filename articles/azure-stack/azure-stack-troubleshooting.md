---
title: "Solución de problemas de Microsoft Azure Stack | Microsoft Docs"
description: "Solución de problemas de Azure Stack."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: helaw
ms.openlocfilehash: 0a8e871a3a44cb14503832d2f3a096712f8112a7
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Solución de problemas de Microsoft Azure Stack

*Se aplica a: Azure Stack Development Kit*

Este documento proporciona información para solucionar problemas comunes de Azure Stack. 

Dado que Azure Stack Technical Development Kit se ofrece como un entorno de evaluación, no hay soporte técnico oficial de los Servicios de soporte al cliente (CSS) de Microsoft.  Si experimenta un problema no documentado, asegúrese de revisar el [foro MSDN de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) para obtener más ayuda e información.  

Las recomendaciones para solucionar los problemas que se describen en esta sección se derivan de varios orígenes y pueden resolver o no el problema en particular. Los ejemplos de código se proporcionan tal cual y no se pueden garantizar los resultados esperados. Esta sección está sujeta a modificaciones y actualizaciones frecuentes cuando se implementan mejoras del producto.

## <a name="deployment"></a>Implementación
### <a name="deployment-failure"></a>Error de implementación
Si experimenta un error durante la instalación, puede usar la opción para volver a ejecutar el script de implementación a fin de reiniciar la implementación desde el paso con errores.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al final de la implementación, la sesión de PowerShell todavía está abierta y no muestra ninguna salida.
Este comportamiento probablemente sea solo el resultado del comportamiento predeterminado de una ventana de comandos de PowerShell, cuando se ha seleccionado. La implementación del kit de desarrollo realmente se ha realizado correctamente, pero se pausó el script al seleccionar la ventana. Puede comprobar que este es el caso buscando la palabra "select" en la barra de título de la ventana de comandos.  Presione la tecla ESC para cancelar la selección; después debería mostrarse el mensaje de finalización.

## <a name="virtual-machines"></a>Máquinas virtuales
### <a name="default-image-and-gallery-item"></a>Elemento de la galería e imagen predeterminada
Antes de implementar máquinas virtuales en Azure Stack, primero debe agregar un elemento de la galería y una imagen de Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Después de reiniciar el host de Azure Stack, algunas máquinas virtuales podrían no iniciarse automáticamente.
Después de reiniciar el host, puede observar que los servicios de Azure Stack no están disponibles de inmediato.  Esto se debe a que las [máquinas virtuales de infraestructura](azure-stack-architecture.md#virtual-machine-roles) de Azure Stack y los RP tardan un poco en comprobar la coherencia, pero finalmente se iniciarán automáticamente.

También puede observar que las máquinas virtuales de ese inquilino no se inician de forma automática después de reiniciar el host de Azure Stack Development Kit.  Es un problema conocido y solo requiere algunos pasos manuales para ponerlas en línea:

1.  En el host de Azure Stack Development Kit, inicie el **Administrador de clústeres de conmutación por error** en el menú Inicio.
2.  Seleccione el clúster **Cluster.azurestack.local**.
3.  Seleccione **Roles**.
4.  Las máquinas virtuales del inquilino aparecerán en el estado *guardado*.  Una vez que se ejecutan todas las máquinas virtuales de la infraestructura, haga clic en las del inquilino y seleccione **Iniciar** para reanudar la máquina virtual.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>He eliminado algunas máquinas virtuales, pero sigo viendo los archivos del disco duro virtual en el disco. ¿Es normal este comportamiento?
Sí, es normal. Se diseñó así porque:

* Al eliminar una máquina virtual, no se eliminan los discos duros virtuales. Los discos son recursos independientes en el grupo de recursos.
* Cuando se elimina una cuenta de almacenamiento, la eliminación es visible de inmediato a través de Azure Resource Manager (Portal, PowerShell), pero los discos que puede contener todavía se conservan en el almacenamiento hasta que se ejecuta la recolección de elementos no utilizados.

Si ve discos duros virtuales "huérfanos", es importante saber si forman parte de la carpeta de una cuenta de almacenamiento que se eliminó. Si la cuenta de almacenamiento no se eliminó, es normal que sigan estando disponibles.

Puede leer más acerca de cómo configurar el umbral de conservación y las recuperaciones a petición en [Administración de cuentas de almacenamiento](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Recuperación de almacenamiento
La recuperación de la capacidad puede tardar hasta catorce horas en mostrarse en el portal. La recuperación de espacio depende de diversos factores, como el porcentaje de uso de archivos de contenedor internos en el almacén de blobs de bloque. Por lo tanto, en función de cuántos datos se eliminen, no hay ninguna garantía de la cantidad de espacio que se podría recuperar cuando se ejecute el recolector de elementos no utilizados.

## <a name="windows-azure-pack-connector"></a>Conector de Windows Azure Pack
* Si cambia la contraseña de la cuenta azurestackadmin después de implementar Azure Stack Development Kit, ya no se puede configurar el modo de varias nubes. Por lo tanto, no será posible conectar con el entorno de Windows Azure Pack de destino.
* Después de configurar el modo de varias nubes:
    * Un usuario puede ver el panel solo después de que restablezca la configuración del portal. (En el portal de usuarios, haga clic en el icono de configuración del portal, el icono de engranaje en la esquina superior derecha. En **Restaurar la configuración predeterminada**, haga clic en **Aplicar**).
    * Los títulos del panel pueden no aparecer. Si se produce este problema, debe volver a agregarlos manualmente.
    * Puede que algunos iconos no se muestren correctamente cuando los agrega por primera vez al panel. Para corregir este problema, actualice el explorador.



