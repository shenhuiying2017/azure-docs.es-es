---
title: "Creación de planes de recuperación para conmutación por error y recuperación en Azure Site Recovery | Microsoft Docs"
description: "Se describe cómo crear y personalizar planes de recuperación en Azure Site Recovery para conmutar por error y recuperar máquinas virtuales y servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew
ms.openlocfilehash: 202e0ac8be36e9156ec16fadc1b722f4eb3d1432
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="create-recovery-plans"></a>Creación de planes de recuperación


En este artículo se proporciona información sobre cómo crear y personalizar planes de recuperación en [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Cree planes de recuperación para realizar las siguientes tareas:

* Definen grupos de máquinas que conmutan por error juntas y también se inician juntas.
* Modelan dependencias entre máquinas, agrupándolas en un grupo de planes de recuperación. Por ejemplo, para conmutar por error y abrir una aplicación específica, agrupe todas las máquinas virtuales de dicha aplicación en el mismo grupo de planes de recuperación.
* Ejecutar una conmutación por error. Puede ejecutar una conmutación por error de prueba, planeada o no planeada en un plan de recuperación.


## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

1. Haga clic en **Planes de recuperación** > **Crear plan de recuperación**.
   Especifique un nombre para el plan de recuperación y un origen y destino. La ubicación de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación.

    - Para la replicación de VMM a VMM, seleccione **Tipo de origen** > **VMM** y los servidores VMM de origen y destino. Haga clic en **Hyper-V** para ver las nubes que están protegidas.
    - Para la replicación de VMM en Azure, seleccione **Tipo de origen** > **VMM**.  Seleccione el servidor VMM de origen y **Azure** como destino.
    - Para la replicación de Hyper-V en Azure (sin VMM), seleccione **Tipo de origen** > **Sitio Hyper-V**. Seleccione el sitio como origen y **Azure** como destino.
    - Para la replicación de una máquina virtual de VMware o un servidor local físico en Azure, seleccione un servidor de configuración como origen y **Azure** como destino.
    - Para un plan de recuperación de Azure en Azure, seleccione una región de Azure como origen y otra secundaria de Azure como destino. Las regiones de Azure secundarias son únicamente aquellas en las que se protegen las máquinas virtuales.
2. En **Seleccionar máquinas virtuales**, seleccione las máquinas virtuales (o un grupo de replicación) que desee agregar al grupo predeterminado (Grupo 1) del plan de recuperación.

## <a name="customize-and-extend-recovery-plans"></a>Personalización y extensión de planes de recuperación

Puede personalizar y extender los planes de recuperación:

- **Agregar grupos nuevos**: agregue más grupos de planes de recuperación (hasta siete) al grupo predeterminado y después agregue más máquinas o grupos de replicación a esos grupos de planes de recuperación. Los grupos se numeran en el orden en que se agregan. Solo se puede incluir una máquina virtual o un grupo de replicación en un grupo de planes de recuperación.
- **Agregar una acción manual**: puede agregar acciones manuales que se ejecuten antes o después de un grupo de planes de recuperación. Cuando se ejecuta el plan de recuperación, se detiene en el punto en que se insertó la acción manual. En un cuadro de diálogo, se le pide que especifique que se completó la acción manual.
- **Agregar un script**: puede agregar scripts que se ejecutan antes o después de un grupo de planes recuperación. Cuando agrega un script, este agrega un nuevo conjunto de acciones al grupo. Por ejemplo, un conjunto de pasos previos para el grupo 1 se creará con el nombre: Grupo 1: pasos previos. Dentro de este conjunto se enumerarán todos los pasos previos. Solo puede agregar un script en el sitio principal si hay un servidor VMM implementado.
- **Agregar runbooks de Azure**: puede extender planes de recuperación con runbooks de Azure. Por ejemplo, para automatizar tareas o para crear una recuperación en un solo paso. [Más información](site-recovery-runbook-automation.md).

## <a name="add-scripts"></a>Incorporación de scripts

Puede usar scripts de PowerShell en los planes de recuperación.

 - Asegúrese de que los scripts usan bloques try-catch para que las excepciones se controlen correctamente.
    - Si hay alguna excepción en el script, deja de funcionar y la tarea muestra errores.
    - Si se produce un error, el resto del script no se ejecuta.
    - Si se produce un error al ejecutar una conmutación por error no planeada, el plan de recuperación continúa.
    - Si se produce un error al ejecutar una conmutación por error planeada, el plan de recuperación se detiene. Debe corregir el script, comprobar que se ejecute según lo previsto y volver a ejecutar el plan de recuperación.
- El comando Write-Host no funciona en un script de plan de recuperación y se produce un error en el script. Para crear salida, cree un script de proxy que a su vez se ejecute el script principal. Asegúrese de que toda la salida se canalice mediante el comando >>.
  * Se agota el tiempo de espera del script si no vuelve en 600 segundos.
  * Si se escribe algo en STDERR, el script se clasifica como con errores. La información se muestra en los detalles de ejecución del script.

Si utiliza VMM en la implementación:

* Los scripts de los planes de recuperación se ejecutan en el contexto de la cuenta de servicio de VMM. Asegúrese de que esta cuenta tenga permisos de lectura para el recurso compartido remoto donde se encuentra el script. Pruebe a ejecutar el script en el nivel de privilegios de cuenta del servicio VMM.
* Los cmdlets de VMM se entregan en un módulo de Windows PowerShell. El módulo se instala al mismo tiempo que la consola VMM. Se puede cargar en el script usando este comando en el script:
   - Import-Module -Name virtualmachinemanager. [Más información](https://technet.microsoft.com/library/hh875013.aspx).
* Asegúrese de que tiene al menos un servidor de biblioteca en la implementación de VMM. De forma predeterminada, la ruta de acceso del recurso compartido de biblioteca para un servidor VMM se encuentra localmente en el servidor VMM, con el nombre de carpeta MSCVMMLibrary.
    * Si la ruta de acceso del recurso compartido de biblioteca es remota (o local pero no se comparte con MSCVMMLibrary), configure el recurso compartido como se indica a continuación (por ejemplo, con \\\libserver2.contoso.com\share\):
      * Abra el Editor del Registro y vaya a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Edite el valor **ScriptLibraryPath** y colóquelo como \\libserver2.contoso.com\share\.. Especifique el nombre de dominio completo. Proporcione permisos a la ubicación del recurso compartido. Tenga en cuenta que es el nodo raíz del recurso compartido. **Para comprobarlo, puede examinar la biblioteca en el nodo raíz en VMM. La ruta de acceso que se abre será la raíz de la ruta de acceso, la que tendrá que usar en la variable**.
      * Asegúrese de probar el script con una cuenta de usuario que tenga los mismos permisos que la cuenta del servicio VMM. Esto comprueba que los scripts probados de forma independiente se ejecuten de la misma manera que lo harán en planes de recuperación. En el servidor VMM, establezca que se omita la directiva de ejecución como se indica a continuación:
        * Abra la consola de **Windows PowerShell de 64 bits** con privilegios elevados.
        * Escriba: **Set-executionpolicy bypass**. [Más información](https://technet.microsoft.com/library/ee176961.aspx).

> [!IMPORTANT]
> Debe establecer la directiva de ejecución en Omitir solo en PowerShell de 64 bits. Si se configura para PowerShell de 32 bits, los scripts no se ejecutarán.

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Incorporación de un script o una acción manual a un plan

Puede agregar un script al grupo de planes de recuperación predeterminado después de haber agregado máquinas virtuales o grupos de replicación a él y de haber creado el plan.

1. Abra el plan de recuperación.
2. Haga clic en un elemento en la lista **Paso** y en **Script** o **Acción manual**.
3. Especifique si desea agregar el script o la acción antes o después del elemento seleccionado. Use los botones de comando **Subir** y **Bajar** para subir o bajar el script.
4. Si agrega un script de VMM, seleccione **Failover to VMM script** (Conmutación por error en script de VMM). En **Ruta de acceso del script**, escriba la ruta de acceso relativa al recurso compartido. En el ejemplo siguiente con VMM, especifique la ruta de acceso: **\RPScripts\RPScript.PS1**.
5. Si agrega un runbook de Azure Automation, especifique la cuenta de Azure Automation donde se encuentra el runbook y seleccione el valor de script de runbook de Azure adecuado.
6. Realice una conmutación por error del plan de recuperación para asegurarse de que el script funcione según lo previsto.


### <a name="add-a-vmm-script"></a>Adición de un script de VMM

Si tiene un sitio de origen VMM, puede crear un script en el servidor VMM e incluirlo en el plan de recuperación.

1. Cree una carpeta en el recurso compartido de biblioteca. Por ejemplo, \<nombreDeServidorVMM>\MSSCVMMLibrary\RPScripts. Colóquela en los servidores VMM de origen y destino.
2. Cree el script (por ejemplo, RPScript) y compruebe que funciona según lo previsto.
3. Coloque el script en la ubicación \<nombreDeServidorVMM>\MSSCVMMLibrary de los servidores VMM de origen y destino.


## <a name="next-steps"></a>Pasos siguientes

[Aprenda más](site-recovery-failover.md) sobre la ejecución de conmutaciones por error.
