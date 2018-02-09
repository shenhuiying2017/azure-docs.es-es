---
title: "Ejecución de una prueba de validación en Azure Stack | Microsoft Docs"
description: "Cómo recopilar archivos de registro de diagnósticos en Azure Stack"
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: mabrigg
ms.openlocfilehash: 53ef19628b40c4a008143c867c9e7867ac91854d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Ejecución de una prueba de validación para Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*
 
Puede validar el estado de Azure Stack. Si hay algún problema, póngase en contacto con el Soporte de servicio al cliente de Microsoft. Soporte le pedirá que ejecute Test-AzureStack desde el nodo de administración. La prueba de validación permite aislar el error. A continuación, Soporte puede analizar los registros detallados, centrarse en el área donde se produjo el error y trabajar con usted para resolver el problema.

## <a name="run-test-azurestack"></a>Ejecución de Test-AzureStack

Si hay algún problema, póngase en contacto con el Soporte de servicio al cliente de Microsoft y ejecute **Test-AzureStack**.

1. Tiene un problema.
2. Póngase en contacto con el Soporte de servicio al cliente de Microsoft.
3. Ejecute **Test-AzureStack** desde el punto de conexión con privilegios.
    1. Acceda al punto de conexión con privilegios. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Inicie sesión como **AzureStack\CloudAdmin** en el host de administración.
    3. Abra PowerShell como administrador.
    4. Ejecute: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Ejecute: `Test-AzureStack`
4. Si ninguna de las pruebas notifica error, ejecute: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>`. El cmdlet recopilará los registros de Test-AzureStack. Para más información sobre los registros de diagnóstico, consulte [Registros de diagnóstico de Azure Stack](azure-stack-diagnostics.md).
5. Envíe los registros **SeedRing** al Soporte de servicio de Microsoft. Este le ayudará a resolver el problema.

## <a name="reference-for-test-azurestack"></a>Referencia de Test-AzureStack

Esta sección contiene información general sobre el cmdlet Test-AzureStack y un resumen del informe de validación.

### <a name="test-azurestack"></a>Test-AzureStack

Valida el estado de Azure Stack. El cmdlet notifica el estado del hardware y el software de Azure Stack. El personal de Soporte puede utilizar este informe para reducir el tiempo necesario para resolver casos de soporte técnico de Azure Stack.

> [!Note]  
> Test-AzureStack puede detectar errores que no producen interrupciones en la nube, como un único disco erróneo o un único error en el nodo físico del host.

#### <a name="syntax"></a>Sintaxis

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parámetros

| .               | Valor           | Obligatorio | Valor predeterminado |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Sin        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Sin        | FALSE   |
| AdminCredential         | PSCredential    | Sin        | N/D      |
| StorageConnectionString | string          | Sin        | N/D      |
| Enumerar                    | SwitchParameter | Sin        | FALSE   |
| Ignorar                  | string          | Sin        | N/D      |
| Include                 | string          | Sin        | N/D      |

El cmdlet Test-AzureStack cmdlet admite los parámetros habituales: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable y OutVariable. Para más información, consulte [About Common Parameters](http://go.microsoft.com/fwlink/?LinkID=113216) (Acerca de parámetros habituales). 

### <a name="examples-of-test-azurestack"></a>Ejemplos de Test-AzureStack

En los ejemplos siguientes se supone que ha iniciado sesión como **CloudAdmin** y que va a acceder al punto de conexión con privilegios (PEP). Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Ejecución de Test-AzureStack de forma interactiva sin escenarios en la nube

En una sesión PEP, ejecute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Ejecución de Test-AzureStack con escenarios en la nube

Puede usar Test-AzureStack para ejecutar escenarios en la nube en Azure Stack. Entre los escenarios se incluyen los siguientes:

 - Creación de grupos de recursos
 - Creación de planes
 - Creación de ofertas
 - Creación de cuentas de almacenamiento
 - Creación de una máquina virtual
 - Realización de operaciones de blob mediante la cuenta de almacenamiento que creó en el escenario de prueba
 - Realización de operaciones de cola mediante la cuenta de almacenamiento que creó en el escenario de prueba
 - Realización de operaciones de tabla mediante la cuenta de almacenamiento que creó en el escenario de prueba

Los escenarios en la nube requieren credenciales de administrador en la nube. 
> [!Note]  
> No se pueden ejecutar los escenarios en la nube mediante las credenciales de los Servicios federados de Active Directory (ADFS). Solo se puede acceder al cmdlet **Test-AzureStack** mediante el punto de conexión con privilegios. Sin embargo, este no admite las credenciales de ADFS.

Escriba el nombre de usuario del administrador de la nube en formato UPN serviceadmin@contoso.onmicrosoft.com (AAD). Cuando se le solicite, escriba la contraseña en la cuenta de administrador en la nube.

En una sesión PEP, ejecute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Ejecución de Test-AzureStack sin escenarios en la nube

En una sesión PEP, ejecute:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Enumeración de los escenarios de prueba disponibles:

En una sesión PEP, ejecute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Ejecutar una prueba específica

En una sesión PEP, ejecute:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Para excluir pruebas específicas:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Prueba de validación

En la tabla siguiente se resumen las pruebas de validación ejecutadas por Test-AzureStack.

| NOMBRE                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Resumen de la infraestructura de hospedaje en la nube de Azure Stack                                                                                  |
| Resumen de los servicios de almacenamiento de Azure Stack                                                                                              |
| Resumen de la instancia de rol de la infraestructura de Azure Stack                                                                                  |
| Uso de la infraestructura de hospedaje en la nube de Azure Stack                                                                              |
| Capacidad de la infraestructura de Azure Stack                                                                                               |
| Resumen de API y del portal de Azure Stack                                                                                                |
| Resumen del certificado de Azure Resource Manager de Azure Stack                                                                                               |
| Roles de controlador de administración de la infraestructura, controlador de red, servicios de almacenamiento y de infraestructura de puntos de conexión con privilegios          |
| Instancias de roles de controlador de administración de la infraestructura, controlador de red, servicios de almacenamiento y de infraestructura de puntos de conexión con privilegios |
| Resumen de roles de la infraestructura de Azure Stack                                                                                           |
| Servicios Service Fabric en la nube de Azure Stack                                                                                         |
| Rendimiento de la instancia de rol de la infraestructura de Azure Stack                                                                              |
| Resumen del rendimiento del host en la nube de Azure Stack                                                                                        |
| Resumen del consumo de recursos del servicio Azure Stack                                                                                  |
| Eventos críticos de la unidad de escalado de Azure Stack (últimas 8 horas)                                                                             |
| Resumen de los discos físicos de los servicios de almacenamiento de Azure Stack                                                                               |

## <a name="next-steps"></a>pasos siguientes

 - Para más información sobre las herramientas de diagnóstico de Azure Stack y el registro de problemas, consulte [Herramientas de diagnóstico de Azure Stack](azure-stack-diagnostics.md).
 - Para más información sobre la solución de problemas, consulte [Solución de problemas de Microsoft Azure Stack](azure-stack-troubleshooting.md)