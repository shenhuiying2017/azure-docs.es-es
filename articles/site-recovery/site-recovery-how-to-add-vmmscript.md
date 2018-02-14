---
title: "Cómo agregar scripts a un plan de recuperación en Azure Site Recovery | Microsoft Docs"
description: "Describe los requisitos previos para agregar un script nuevo a un plan de recuperación de VMM en Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>Adición de scripts de VMM a un plan de recuperación


En este artículo se proporciona información sobre cómo crear y agregar scripts de VMM a planes de recuperación en [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>Requisitos previos para agregar un script a un plan de recuperación

Puede usar scripts de PowerShell en los planes de recuperación. Debe crearlos y colocarlos en la biblioteca de VMM para que sean accesibles desde el plan de recuperación. A continuación, presentamos algunos aspectos que debe tener en cuenta mientras escribe el script.

* Asegúrese de que los scripts usan bloques try-catch para que las excepciones se controlen correctamente.
    - Si hay alguna excepción en el script, deja de funcionar y la tarea muestra errores.
    - Si se produce un error, el resto del script no se ejecuta.
    - Si se produce un error al ejecutar una conmutación por error no planeada, el plan de recuperación continúa.
    - Si se produce un error al ejecutar una conmutación por error planeada, el plan de recuperación se detiene. Debe corregir el script, comprobar que se ejecute según lo previsto y volver a ejecutar el plan de recuperación.
        - El comando Write-Host no funciona en un script de plan de recuperación y se produce un error en el script. Para crear salida, cree un script de proxy que a su vez se ejecute el script principal. Asegúrese de que toda la salida se canalice mediante el comando >>.
        - Se agota el tiempo de espera del script si no vuelve en 600 segundos.
        - Si se escribe algo en STDERR, el script se clasifica como con errores. La información se muestra en los detalles de ejecución del script.

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
> Debe establecer la directiva de ejecución en Omitir solo en PowerShell de 64 bits. Si se establece para PowerShell de 32 bits, los scripts no se ejecutarán.

## <a name="add-the-script-to-the-vmm-library"></a>Adición del script a la biblioteca de VMM

Si tiene un sitio de origen VMM, puede crear un script en el servidor VMM e incluirlo en el plan de recuperación.

1. Cree una carpeta en el recurso compartido de biblioteca. Por ejemplo, \<nombreDeServidorVMM>\MSSCVMMLibrary\RPScripts. Colóquela en los servidores VMM de origen y destino.
2. Cree el script (por ejemplo, RPScript) y compruebe que funciona según lo previsto.
3. Coloque el script en la ubicación \<nombreDeServidorVMM>\MSSCVMMLibrary de los servidores VMM de origen y destino.

## <a name="add-the-script-to-a-recovery-plan"></a>Adición del script a un plan de recuperación

Puede agregar el script al grupo después de haberle agregado máquinas virtuales o grupos de replicación y de haber creado el plan.

1. Abra el plan de recuperación.
2. Haga clic en un elemento en la lista **Paso** y en **Script** o **Acción manual**.
3. Especifique si desea agregar el script o la acción antes o después del elemento seleccionado. Use los botones de comando **Subir** y **Bajar** para subir o bajar el script.
4. Si agrega un script de VMM, seleccione **Failover to VMM script** (Conmutación por error en script de VMM). En **Ruta de acceso del script**, escriba la ruta de acceso relativa al recurso compartido. En el ejemplo siguiente con VMM, especifique la ruta de acceso: **\RPScripts\RPScript.PS1**.
5. Si agrega un runbook de Azure Automation, especifique la cuenta de Azure Automation donde se encuentra el runbook y seleccione el valor de script de runbook de Azure adecuado.
6. Realice una conmutación por error de prueba del plan de recuperación para asegurarse de que el script funcione según lo previsto.


## <a name="next-steps"></a>pasos siguientes

[Aprenda más](site-recovery-failover.md) sobre la ejecución de conmutaciones por error.
