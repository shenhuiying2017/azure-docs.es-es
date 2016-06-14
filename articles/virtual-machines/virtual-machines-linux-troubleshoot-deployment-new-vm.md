<properties
   pageTitle="Solución de problemas de implementación de máquinas virtuales de Linux o problemas de Resource Manager | Microsoft Azure"
   description="Solución de problemas de implementación de Resource Manager cuando crea una nueva máquina virtual de Linux en Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="05/06/2016"
  ms.author="cjiang"/>

# Solución de problemas de la implementación de Resource Manager con la creación de una nueva máquina virtual de Linux en Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Recopilación de registros de auditoría

Para iniciar la solución de problemas, recopile los registros de auditoría para identificar el error asociado con el problema. Los vínculos siguientes contienen información detallada sobre el proceso que se debe seguir.

[Solución de problemas de implementaciones de grupo de recursos con el Portal de Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** si el sistema operativo es Linux generalizado y se carga o captura con la configuración generalizada, no habrá errores. De forma similar, si el sistema operativo es Linux especializado y se carga o captura con la configuración especializada, no habrá errores.

**Errores de carga:**

**N<sup>1</sup>:** si el sistema operativo es Linux generalizado y se carga como especializado, recibirá un error de tiempo de espera de aprovisionamiento porque la máquina virtual se atascará en la fase de aprovisionamiento.

**N<sup>2</sup>:** si el sistema operativo es Linux especializado y se carga como generalizado, recibirá un error de aprovisionamiento porque la nueva máquina virtual se ejecuta con el nombre del equipo, el nombre de usuario y la contraseña originales.

**Resolución:**

Para resolver estos errores, cargue el VHD original, disponible en el entorno local, con la misma configuración que para el sistema operativo (generalizada o especializada). Para cargarlo como generalizado, no olvide ejecutarlo o desaprovisionarlo antes.

**Errores de captura:**

**N<sup>3</sup>:** si el sistema operativo es Linux generalizado y se captura como especializado, recibirá un error de tiempo de espera de aprovisionamiento porque la máquina virtual original no se puede utilizar, ya que está marcada como generalizada.

**N<sup>4</sup>:** si el sistema operativo es Linux especializado y se captura como generalizado, recibirá un error de aprovisionamiento porque la nueva máquina virtual se ejecuta con el nombre del equipo, el nombre de usuario y la contraseña originales. Además, no se puede utilizar la máquina virtual original ya que está marcada como especializada.

**Resolución:**

Para resolver estos errores, elimine la imagen actual del portal y [vuelva a capturarla desde los discos duros virtuales actuales](virtual-machines-linux-capture-image.md) con la misma configuración que para el sistema operativo (generalizada o especializada).

## Problema: Imagen de galería/marketplace/personalizada; error de asignación
Este error se produce en situaciones en las que la nueva solicitud de máquina virtual está anclada en un clúster que no admite el tamaño de la máquina virtual que se solicita o no tiene espacio libre disponible para alojar la solicitud.

**Causa 1:** el clúster no admite el tamaño de la máquina virtual solicitada.

**Resolución 1:**

- Vuelva a intentar la solicitud con un tamaño de máquina virtual menor.
- Si no se puede cambiar el tamaño de la máquina virtual solicitada:
  - Detenga todas las máquinas virtuales en el conjunto de disponibilidad. Haga clic en **Grupos de recursos** > *su grupo de recursos* > **Recursos** > *su conjunto de disponibilidad* > **Máquinas virtuales** > *su máquina virtual* > **Detener**.
  - Después de detener todas las máquinas virtuales, cree la nueva máquina virtual con el tamaño deseado.
  - Inicie la nueva máquina virtual en primer lugar y luego seleccione cada una de las máquinas virtuales detenidas y haga clic en **Iniciar**.

**Causa 2:** el clúster no tiene recursos disponibles.

**Resolución 2:**

- Vuelva a intentar la solicitud más tarde.
- Si la nueva máquina virtual puede formar parte de un conjunto de disponibilidad diferente
  - Cree una nueva máquina virtual en un conjunto de disponibilidad diferente (en la misma región).
  - Agregue la nueva máquina virtual a la misma red virtual.

<!---HONumber=AcomDC_0601_2016-->