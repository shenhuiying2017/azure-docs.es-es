---
title: "Uso del punto de conexión con privilegios en Azure Stack | Microsoft Docs"
description: "Se muestra cómo usar el punto de conexión con privilegios en Azure Stack (para un operador de Azure Stack)."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 9769b12064216680bb1b2db8c1fd7449927c7771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Uso del punto de conexión con privilegios en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Como operador de Azure Stack, debe usar las API del portal de administrador, PowerShell o Azure Resource Manager en la mayor parte de las tareas de administración diarias. Sin embargo, con algunas operaciones menos comunes, deberá usar el *punto de conexión con privilegios*. El punto de conexión es una consola remota de PowerShell preconfigurada que proporciona las funcionalidades suficientes para ayudarle a realizar las tareas necesarias. El punto de conexión aprovecha PowerShell JEA (Just Enough Administration) para exponer únicamente un conjunto restringido de cmdlets. Para acceder al punto de conexión con privilegios e invocar el conjunto restringido de cmdlets, se usa una cuenta sin privilegios. No se necesita una cuenta de administrador. Para mayor seguridad, no se permite scripting.

Puede usar el punto de conexión con privilegios para realizar tareas como las siguientes:

- Tareas de bajo nivel, como la [recopilación de registros de diagnóstico](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Muchas tareas de integración de centros de datos posteriores a la implementación para sistemas integrados, como agregar reenviadores de Sistema de nombres de dominio (DNS) después de la implementación, configurar la integración con Graph y con Active Directory Federation Services (AD FS), la rotación de certificados, etc.
- Trabajar con el soporte técnico para obtener acceso temporal y de alto nivel de cara a la solución de problemas detallada de un sistema integrado. 

El punto de conexión con privilegios registra cada acción (y su salida correspondiente) que se realiza en la sesión de PowerShell. De esta forma las operaciones se realizan con total transparencia y se pueden auditar de forma completa. Puede conservar estos archivos de registro para futuras auditorías.

> [!NOTE]
> En Azure Stack Development Kit (ASDK), puede ejecutar algunos de los comandos disponibles en el punto de conexión con privilegios directamente desde una sesión de PowerShell en el host del kit de desarrollo. No obstante, puede que quiera probar algunas operaciones usando el punto de conexión con privilegios, como la recopilación de registros, dado que es el único método disponible para realizar determinadas operaciones en un entorno de sistemas integrados.

## <a name="access-the-privileged-endpoint"></a>Acceso al punto de conexión con privilegios

El acceso al punto de conexión con privilegios se realiza mediante una sesión remota de PowerShell en la máquina virtual que lo hospeda. En el ASDK, esta máquina virtual se denomina AzS ERCS01. Si va a usar un sistema integrado, hay tres instancias del punto de conexión con privilegios, cada una de las cuales se ejecuta en una máquina virtual (*Prefix*-ERCS01, *Prefix*-ERCS02 o *Prefix*-ERCS03) en diferentes host para proporcionar resistencia. 

Antes de comenzar este procedimiento en un sistema integrado, asegúrese de que puede acceder a un punto de conexión con privilegios bien mediante la dirección IP o a través de DNS. Después de la implementación inicial de Azure Stack, solo puede acceder al punto de conexión con privilegios mediante la dirección IP dado que la integración de DNS no está configurada todavía. El proveedor de hardware OEM le proporcionará un archivo JSON llamado "AzureStackStampDeploymentInfo" que contiene las direcciones IP del punto de conexión con privilegios.

Es recomendable que se conecte al punto de conexión con privilegios solo desde el host de ciclo de vida del hardware o desde un equipo bloqueado dedicado, como una [estación de trabajo de acceso con privilegios](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Realice una de las acciones siguientes en función de su entorno:

    - En un sistema integrado, ejecute el siguiente comando para agregar el punto de conexión con privilegios como un host de confianza en el host de ciclo de vida del hardware o en la estación de trabajo de acceso con privilegios.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

2. En el host de ciclo de vida del hardware o la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell con privilegios elevados. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el punto de conexión con privilegios:
 
    - En un sistema integrado:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      El parámetro `ComputerName` puede ser la dirección IP o el nombre DNS de una de las máquinas virtuales que hospeda un punto de conexión con privilegios. 
    - Si va a ejecutar el ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Cuando se le pida, utilice las siguientes credenciales:

      - **Nombre de usuario**: especifique la cuenta CloudAdmin con el formato **&lt;*dominio de Azure Stack*&gt;\cloudadmin**. (Para ASDK, el nombre de usuario es **azurestack\cloudadmin**).
      - **Contraseña**: escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.
    
3.  Después de conectarse, el símbolo del sistema cambia a  **[*dirección IP o nombre de máquina virtual de ERCS*]: PS > ** o a **[azs ercs01]: PS >**, según el entorno. Desde aquí, ejecute `Get-Command` para ver la lista de los cmdlets disponibles.

    ![Salida del cmdlet Get-Command que muestra la lista de comandos disponibles](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Muchos de estos cmdlets están concebidos únicamente para entornos de sistema integrados (por ejemplo, los cmdlets relacionados con la integración de centros de datos). En el ASDK, se han validado los siguientes cmdlets:

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Measure-Object
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Stop-AzureStack
    - Get-ClusterLog

4.  Como no se permite scripting, no se puede usar la finalización con tabulación en los valores de parámetros. Para obtener la lista de parámetros de un cmdlet dado, ejecute el siguiente comando:

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    Si intenta algún tipo de operación de scripts, se producirá el error **ScriptsNotAllowed**. Este es el comportamiento esperado.

## <a name="close-the-privileged-endpoint-session"></a>Cierre de la sesión del punto de conexión con privilegios

 Como se mencionó anteriormente, el punto de conexión con privilegios registra cada una de las acciones (y su salida correspondiente) que realiza en la sesión de PowerShell. Debe cerrar la sesión mediante el cmdlet `Close-PrivilegedEndpoint`. Este cmdlet cierra correctamente el punto de conexión y transfiere los archivos de registro a un recurso compartido de archivos externo donde se retienen.

Para cerrar la sesión del punto de conexión:

1. Cree un recurso compartido de archivos externo al que se pueda acceder mediante el punto de conexión con privilegios. En un entorno de kit de desarrollo, solo puede crear un recurso compartido de archivos en el host del kit de desarrollo.
2. Ejecute el cmdlet `Close-PrivilegedEndpoint`. 
3. Se le pide una ruta de acceso en la que almacenar el archivo de registro de transcripción. Especifique el recurso compartido de archivos que creó anteriormente, en el formato &#92;&#92;*servername*&#92;*sharename*. Si no se especifica una ruta de acceso, se produce un error en el cmdlet y la sesión permanece abierta. 

    ![Salida del cmdlet Close-PrivilegedEndpoint que muestra dónde se especifica la ruta de acceso del destino de transcripción](media/azure-stack-privileged-endpoint/closeendpoint.png)

Después de que los archivos de registro de transcripción se transfieren correctamente al recurso compartido de archivos, se eliminan automáticamente del punto de conexión con privilegios. Si cierra la sesión de punto de conexión con privilegios mediante los cmdlets `Exit-PSSession` o `Exit`, o simplemente cierra la consola de PowerShell, los archivos de transcripción no se transfieren a un recurso compartido de archivos. Permanecen en el punto de conexión con privilegios. La próxima vez que ejecute `Close-PrivilegedEndpoint` e incluya un recurso compartido de archivos, también se transferirán los registros de transcripción de la sesión anterior.

## <a name="next-steps"></a>Pasos siguientes
[Herramientas de diagnóstico de Azure Stack](azure-stack-diagnostics.md)







