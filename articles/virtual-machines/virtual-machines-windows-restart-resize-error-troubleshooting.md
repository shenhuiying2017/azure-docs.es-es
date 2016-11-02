<properties
   pageTitle="Problemas de reinicio o cambio de tamaño de las máquinas virtuales | Microsoft Azure"
   description="Solución de problemas de la implementación de Resource Manager con el reinicio o el cambio de tamaño de una máquina virtual de Windows existente en Azure"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# Solución de problemas de la implementación de Resource Manager con el reinicio o el cambio de tamaño de una máquina virtual de Windows existente en Azure

Al intentar iniciar una máquina virtual de Azure detenida o cambiar el tamaño de una máquina virtual de Azure existente, es común encontrarse un error de asignación. Dicho error se produce cuando el clúster o la región no tienen recursos disponibles o no admiten el tamaño de máquina virtual solicitado.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Recopilación de registros de auditoría

Para iniciar la solución de problemas, recopile los registros de auditoría para identificar el error asociado con el problema. Los vínculos siguientes contienen información detallada sobre el proceso:

[Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure ](../resource-manager-troubleshoot-deployments-portal.md)

[Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)

## Problema: Error al iniciar una máquina virtual detenida

Intenta iniciar una máquina virtual detenida, pero obtiene un error de asignación.

### Causa

La solicitud de iniciar la máquina virtual detenida se debe intentar en el clúster original que hospeda el servicio en la nube. Sin embargo, el clúster no tiene espacio libre disponible para responder a la solicitud.

### Resolución

*	Detenga todas las máquinas virtuales del conjunto de disponibilidad y reinicie las máquinas virtuales.

  1. Haga clic en **Grupos de recursos** > _su grupo de recursos_ > **Recursos** > _su conjunto de disponibilidad_ > **Máquinas virtuales** > _su máquina virtual_ > **Detener**.

  2. Después de detener todas las máquinas virtuales, selecciónelas y haga clic en Iniciar.

*	Vuelva a intentar solicitar el reinicio más tarde.

## Problema: error al reiniciar una máquina virtual existente

Intenta cambiar el tamaño de una máquina virtual existente, pero obtiene un error de asignación.

### Causa

La solicitud de cambiar el tamaño de la máquina virtual se debe realizar en el clúster original que alberga el servicio en la nube. Sin embargo, el clúster no admite el tamaño de máquina virtual solicitado.

### Resolución

* Vuelva a intentar la solicitud con un tamaño de máquina virtual menor.

* Si no se puede cambiar el tamaño de la máquina virtual solicitada:

  1. Detenga todas las máquinas virtuales en el conjunto de disponibilidad.

    * Haga clic en **Grupos de recursos** > _su grupo de recursos_ > **Recursos** > _su conjunto de disponibilidad_ > **Máquinas virtuales** > _su máquina virtual_ > **Detener**.

  2. Después de detener todas las máquinas virtuales, aumente el tamaño de la máquina virtual que quiera.
  3. Seleccione la máquina virtual cuyo tamaño ha cambiado, haga clic en **Iniciar** e inicie las máquinas virtuales detenidas.

## Pasos siguientes

Si surgen problemas al crear una nueva máquina virtual Windows en Azure, consulte [Solución de problemas de implementación de Resource Manager con la creación de una máquina virtual de Windows en Azure](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md).

<!---HONumber=AcomDC_0914_2016-->