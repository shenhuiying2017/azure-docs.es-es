---
title: "Incorporación de un script a un plan de recuperación en Azure Site Recovery | Microsoft Docs"
description: "Obtenga información sobre los requisitos previos para agregar un script nuevo de System Center Virtual Machine Manager (VMM) a un plan de recuperación en Azure."
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
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Incorporación de un script de VMM a un plan de recuperación

En este artículo se describe cómo crear un script de System Center Virtual Machine Manager (VMM) y agregarlo a un plan de recuperación en [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>requisitos previos

Puede usar scripts de PowerShell en los planes de recuperación. Para que sea posible acceder al script desde el plan de recuperación, debe crearlo y colocarlo en la biblioteca de VMM. Cuando escriba el script, tenga en mente las consideraciones siguientes:

* Asegúrese de que los scripts usan bloques try-catch, para que las excepciones se controlen correctamente.
    - Si se produce una excepción en el script, este dejará de ejecutarse y la tarea se mostrará con errores.
    - Si se produce un error, el resto del script no se ejecuta.
    - Si se produce un error al ejecutar una conmutación por error no planeada, el plan de recuperación continúa.
    - Si se produce un error al ejecutar una conmutación por error planeada, el plan de recuperación se detiene. Corrija el script, compruebe que se ejecuta según lo previsto y, luego, vuelva a ejecutar el plan de recuperación.
        - El comando `Write-Host` no funciona en un script de plan de recuperación. Si usa el comando `Write-Host` en un script, el script presenta un error. Para crear salida, cree un script de proxy que a su vez se ejecute el script principal. Use el comando **\>\>** para asegurarse de que toda la salida se canalice.
        - Se agota el tiempo de espera del script si no vuelve en 600 segundos.
        - Si se escribe algo en STDERR, el script se clasifica como con errores. La información se muestra en los detalles de ejecución del script.

* Los scripts de los planes de recuperación se ejecutan en el contexto de la cuenta de servicio de VMM. Asegúrese de que esta cuenta tiene permisos de lectura para el recurso compartido remoto donde se encuentra el script. Pruebe el script para ejecutarlo con el mismo nivel de derechos de usuario que la cuenta de servicio de VMM.
* Los cmdlets de VMM se entregan en un módulo de Windows PowerShell. El módulo se instala al mismo tiempo que la consola VMM. Para cargar el módulo en el script, use el comando siguiente en el script: 

    `Import-Module -Name virtualmachinemanager`

    Para más información, consulte [Introducción a Windows PowerShell y VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Asegúrese de que tiene al menos un servidor de biblioteca en la implementación de VMM. De manera predeterminada, la ruta de acceso del recurso compartido de biblioteca para un servidor VMM se encuentra localmente en el servidor VMM. El nombre de la carpeta es MSCVMMLibrary.

  Si la ruta de acceso del recurso compartido de biblioteca es remota (o si es local, pero no se comparte con MSCVMMLibrary), configure el recurso compartido como se indica a continuación, con \\libserver2.contoso.com\share\ como ejemplo:
  
  1. Abra el Editor del Registro y vaya a **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  2. Cambie el valor de **ScriptLibraryPath** a **\\\libserver2.contoso.com\share\\**. Especifique el nombre de dominio completo. Proporcione permisos a la ubicación del recurso compartido. Este es el nodo raíz del recurso compartido. Para comprobar el nodo raíz, en VMM, vaya al nodo raíz en la biblioteca. La ruta de acceso que se abre es la raíz de la ruta de acceso. Esta es la ruta de acceso que debe usar en la variable.

  3. Pruebe el script con una cuenta de usuario que tenga el mismo nivel de derechos de usuario que la cuenta de servicio de VMM. Al usar estos derechos de usuario se comprueba que los scripts independientes y probados se ejecutan del mismo modo que se ejecutan en los planes de recuperación. En el servidor VMM, establezca que se omita la directiva de ejecución como se indica a continuación:

     a. Abra la consola de **Windows PowerShell de 64 bits** como administrador.
     
     b. Escriba **Set-executionpolicy bypass**. Para más información, consulte [Uso del cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Establezca **Set-executionpolicy bypass** solo en la consola PowerShell de 64 bits. Si lo establece en la consola PowerShell de 32 bits, los scripts no se ejecutarán.

## <a name="add-the-script-to-the-vmm-library"></a>Adición del script a la biblioteca de VMM

Si tiene un sitio de origen de VMM, puede crear un script en el servidor VMM. Luego, incluya el script en el plan de recuperación.

1. En el recurso compartido de biblioteca, cree una carpeta nueva. Por ejemplo, \<Nombre del servidor VMM>\MSSCVMMLibrary\RPScripts. Coloque la carpeta en los servidores VMM de origen y de destino.
2. Cree el script. Por ejemplo, asígnele al script el nombre RPScript. Compruebe que el script funciona según lo previsto.
3. Coloque el script en la carpeta \<Nombre del servidor VMM>\MSSCVMMLibrary en los servidores VMM de origen y de destino.

## <a name="add-the-script-to-a-recovery-plan"></a>Adición del script a un plan de recuperación

Después de agregar las máquinas virtuales o los grupos de replicación a un plan de recuperación y crear el plan, puede agregar el script al grupo.

1. Abra el plan de recuperación.
2. En la lista **Paso**, seleccione un elemento. Luego, seleccione **Script** o **Acción manual**.
3. Especifique si agregar el script o la acción antes o después del elemento seleccionado. Para subir o bajar la posición del script, seleccione los botones **Subir** o **Bajar**.
4. Si agrega un script de VMM, seleccione **Failover to VMM script** (Conmutación por error en script de VMM). En **Ruta de acceso del script**, escriba la ruta de acceso relativa al recurso compartido. Por ejemplo, escriba **\RPScripts\RPScript.PS1**.
5. Si agrega un runbook de Azure Automation, especifique la cuenta de Automation en la que se encuentra el runbook. A continuación, seleccione el script del runbook de Azure que quiere usar.
6. Para asegurarse de que el script funciona según lo previsto, realice una conmutación por error de prueba del plan de recuperación.


## <a name="next-steps"></a>pasos siguientes
* Aprenda más sobre la [ejecución de conmutaciones por error](site-recovery-failover.md).

