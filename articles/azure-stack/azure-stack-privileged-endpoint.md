---
title: "Uso del punto de conexión con privilegios en Azure Stack | Microsoft Docs"
description: "Se muestra cómo usar el punto de conexión con privilegios (PEP) en Azure Stack (para un operador de Azure Stack)."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: mabrigg
ms.openlocfilehash: 34ad4d7038202bd5efa2b3c210571268a39bf278
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Uso del punto de conexión con privilegios en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Como operador de Azure Stack, debe usar las API del portal de administrador, PowerShell o Azure Resource Manager en la mayor parte de las tareas de administración diarias. Sin embargo, con algunas operaciones menos comunes, deberá usar el *punto de conexión con privilegios* (PEP). El PEP es una consola remota de PowerShell preconfigurada que proporciona las funcionalidades suficientes para ayudarle a realizar una tarea necesaria. El punto de conexión usa [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/en-us/powershell/jea/overview) para exponer únicamente un conjunto restringido de cmdlets. Para acceder al PEP e invocar el conjunto restringido de cmdlets, se usa una cuenta sin privilegios. No se necesita una cuenta de administrador. Para mayor seguridad, no se permite scripting.

Puede usar el PEP para realizar tareas como las siguientes:

- Tareas de bajo nivel, como la [recopilación de registros de diagnóstico](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Muchas tareas de integración de centros de datos posteriores a la implementación para sistemas integrados, como agregar reenviadores de Sistema de nombres de dominio (DNS) después de la implementación, configurar la integración con Microsoft Graph y con Active Directory Federation Services (AD FS), la rotación de certificados, etc.
- Trabajar con el soporte técnico para obtener acceso temporal y de alto nivel de cara a la solución de problemas detallada de un sistema integrado.

El PEP registra cada acción (y su salida correspondiente) que se realiza en la sesión de PowerShell. De esta forma las operaciones se realizan con total transparencia y se pueden auditar de forma completa. Puede conservar estos archivos de registro para futuras auditorías.

> [!NOTE]
> En el Kit de desarrollo de Azure Stack (ASDK), puede ejecutar algunos de los comandos disponibles en el PEP directamente desde una sesión de PowerShell en el host del Kit de desarrollo. No obstante, puede que quiera probar algunas operaciones usando el PEP, como la recopilación de registros, dado que es el único método disponible para realizar determinadas operaciones en un entorno de sistemas integrados.

## <a name="access-the-privileged-endpoint"></a>Acceso al punto de conexión con privilegios

El acceso al PEP se realiza mediante una sesión remota de PowerShell en la máquina virtual que lo hospeda. En el ASDK, esta máquina virtual se denomina **AzS-ERCS01**. Si va a usar un sistema integrado, hay tres instancias del PEP, cada una de las cuales se ejecuta en una máquina virtual (*Prefix*-ERCS01, *Prefix*-ERCS02 o *Prefix*-ERCS03) en diferentes hosts para proporcionar resistencia. 

Antes de comenzar este procedimiento en un sistema integrado, asegúrese de que puede acceder a un PEP bien mediante la dirección IP o a través de DNS. Después de la implementación inicial de Azure Stack, solo puede acceder al PEP mediante la dirección IP, dado que la integración de DNS no está configurada todavía. El proveedor de hardware OEM le proporcionará un archivo JSON denominado **AzureStackStampDeploymentInfo** que contiene las direcciones IP del PEP.

Es recomendable que se conecte al PEP solo desde el host de ciclo de vida del hardware o desde un equipo seguro dedicado, como una [estación de trabajo de acceso con privilegios](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Acceda a la estación de trabajo de acceso con privilegios.

    - En un sistema integrado, ejecute el siguiente comando para agregar el PEP como un host de confianza en el host de ciclo de vida del hardware o en la estación de trabajo de acceso con privilegios.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

2. En el host de ciclo de vida del hardware o la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell con privilegios elevados. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el PEP:
 
    - En un sistema integrado:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      El parámetro `ComputerName` puede ser la dirección IP o el nombre DNS de una de las máquinas virtuales que hospeda el PEP. 
    - Si va a ejecutar el ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Cuando se le pida, utilice las siguientes credenciales:

      - **Nombre de usuario**: especifique la cuenta CloudAdmin con el formato **&lt;*dominio de Azure Stack*&gt;\cloudadmin**. (Para ASDK, el nombre de usuario es **azurestack\cloudadmin**).
      - **Contraseña**: escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.
    
3.  Después de conectarse, el símbolo del sistema cambia a **[*dirección IP o nombre de máquina virtual de ERCS*]: PS>** o a **[azs-ercs01]: PS>**, según el entorno. Desde aquí, ejecute `Get-Command` para ver la lista de los cmdlets disponibles.

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
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Sugerencias para el uso del punto de conexión con privilegios 

Tal y como se mencionó anteriormente, el PEP es un punto de conexión de [PowerShell JEA](https://docs.microsoft.com/en-us/powershell/jea/overview). Al proporcionar una capa de seguridad sólida, un punto de conexión de JEA reduce algunas de las funcionalidades básicas de PowerShell, como la finalización con tabulación o de scripting. Si intenta algún tipo de operación de scripts, se producirá el error **ScriptsNotAllowed**. Este es el comportamiento esperado.

Por lo tanto, para obtener la lista de parámetros de un cmdlet determinado, debe ejecutar el siguiente comando:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Como alternativa, puede usar el cmdlet [Import-PSSession](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) para importar todos los cmdlets de PEP a la sesión actual en al equipo local. De esta forma, todos los cmdlets y las funciones del PEP ahora están disponibles en el equipo local, junto con la finalización con tabulación y, más en general, de scripting. 

Lleve a cabo los siguientes pasos para importar la sesión del PEP al equipo local:

1. Acceda a la estación de trabajo de acceso con privilegios.

    - En un sistema integrado, ejecute el siguiente comando para agregar el PEP como un host de confianza en el host de ciclo de vida del hardware o en la estación de trabajo de acceso con privilegios.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

2. En el host de ciclo de vida del hardware o la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell con privilegios elevados. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el PEP:
 
    - En un sistema integrado:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      El parámetro `ComputerName` puede ser la dirección IP o el nombre DNS de una de las máquinas virtuales que hospeda el PEP. 
    - Si va a ejecutar el ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Cuando se le pida, utilice las siguientes credenciales:

      - **Nombre de usuario**: especifique la cuenta CloudAdmin con el formato **&lt;*dominio de Azure Stack*&gt;\cloudadmin**. (Para ASDK, el nombre de usuario es **azurestack\cloudadmin**).
      - **Contraseña**: escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.

3. Importe la sesión PEP al equipo local.
    ````PowerShell 
        Import-PSSession $session
    ````
4. Ahora, puede usar la finalización con tabulación y realizar scripting como de costumbre en la sesión de PowerShell local con todas las funciones y cmdlets del PEP sin reducir la posición de seguridad de Azure Stack. ¡Disfrute!


## <a name="close-the-privileged-endpoint-session"></a>Cierre de la sesión del punto de conexión con privilegios

 Como se mencionó anteriormente, el PEP registra cada una de las acciones (y su salida correspondiente) que realiza en la sesión de PowerShell. Debe cerrar la sesión mediante el cmdlet `Close-PrivilegedEndpoint`. Este cmdlet cierra correctamente el punto de conexión y transfiere los archivos de registro a un recurso compartido de archivos externo donde se retienen.

Para cerrar la sesión del punto de conexión:

1. Cree un recurso compartido de archivos externo al que se pueda acceder mediante el PEP. En un entorno de kit de desarrollo, solo puede crear un recurso compartido de archivos en el host del kit de desarrollo.
2. Ejecute el cmdlet `Close-PrivilegedEndpoint`. 
3. Se le pide una ruta de acceso en la que almacenar el archivo de registro de transcripción. Especifique el recurso compartido de archivos que creó anteriormente, en el formato &#92;&#92;*servername*&#92;*sharename*. Si no se especifica una ruta de acceso, se produce un error en el cmdlet y la sesión permanece abierta. 

    ![Salida del cmdlet Close-PrivilegedEndpoint que muestra dónde se especifica la ruta de acceso del destino de transcripción](media/azure-stack-privileged-endpoint/closeendpoint.png)

Una vez los archivos de registro de transcripción se transfieren correctamente al recurso compartido de archivos, se eliminan automáticamente del PEP. Si cierra la sesión del PEP mediante los cmdlets `Exit-PSSession` o `Exit`, o simplemente cierra la consola de PowerShell, los archivos de transcripción no se transfieren a un recurso compartido de archivos. Permanecen en el PEP. La próxima vez que ejecute `Close-PrivilegedEndpoint` e incluya un recurso compartido de archivos, también se transferirán los registros de transcripción de la sesión anterior.

## <a name="next-steps"></a>pasos siguientes
[Herramientas de diagnóstico de Azure Stack](azure-stack-diagnostics.md)