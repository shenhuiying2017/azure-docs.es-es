---
title: Referencia del cmdlet Start-AzsReadinessChecker | Microsoft Docs
description: Ayuda del cmdlet de PowerShell para el módulo Azure Stack Readiness Checker.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937581"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referencia del cmdlet Start-AzsReadinessChecker

Módulo: Microsoft.AzureStack.ReadinessChecker

Este módulo solo contiene un único cmdlet.  Este cmdlet realiza una o más funciones anteriores a la implementación o al mantenimiento en Azure Stack.

## <a name="syntax"></a>Sintaxis
```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```


```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```





 ## <a name="description"></a>DESCRIPCIÓN
El cmdlet **Start-AzsReadinessChecker** valida los certificados, las cuentas de Azure, las suscripciones de Azure y los directorios de Azure Active Directory. Ejecute la validación antes de implementar Azure Stack o de realizar acciones de servicio de Azure Stack como la rotación de secretos. El cmdlet también puede usarse para generar solicitudes de firma de certificado para certificados de infraestructura y, opcionalmente, para certificados de PaaS.  Por último, el cmdlet puede volver a empaquetar los certificados PFX para solucionar problemas comunes de empaquetado.

## <a name="examples"></a>Ejemplos
**Ejemplo: Generación de solicitudes de firma de certificado**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

En este ejemplo, Start-AzsReadinessChecker genera varias solicitudes de firma de certificados (CSR) para los certificados adecuados para la implementación de Azure Stack ADFS con un nombre de región de "east" y un FQDN externo de "azurestack.contoso.com".

**Ejemplo: Validación de certificados**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

En este ejemplo, se solicita de forma segura la contraseña PFX y Start-AzsReadinessChecker busca en la carpeta relativa "Certificados" los certificados válidos para una implementación de AAD con un nombre de región de "east" y un FQDN externo de "azurestack.contoso.com". 

**Ejemplo: Validación de certificados con datos de implementación (implementación y soporte técnico)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
En este ejemplo de implementación y soporte técnico, se solicita la contraseña PFX de forma segura y Start-AzsReadinessChecker busca en la carpeta relativa "Certificados" los certificados válidos para una implementación donde la identidad, la región y el FQDN externo se leen del archivo JSON de datos de implementación generado para la implementación. 

**Ejemplo: Validación de certificados de PaaS**
```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

En este ejemplo, se crea una tabla hash con las rutas de acceso y las contraseñas de cada certificado de PaaS. Los certificados se pueden omitir. Start-AzsReadinessChecker comprueba que cada ruta de acceso de PFX existe y la valida con la región "east" y el FQDN externo "azurestack.contoso.com".

**Ejemplo: Validación de certificados de PaaS con datos de implementación**
```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

En este ejemplo, se crea una tabla hash con las rutas de acceso y las contraseñas de cada certificado de PaaS. Los certificados se pueden omitir. Start-AzsReadinessChecker comprueba que cada ruta de acceso de PFX existe y la valida con la región y el FQDN externo que se leen del archivo JSON de datos de implementación generado para la implementación. 

**Ejemplo: Validación de la identidad de Azure**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

En este ejemplo, se solicitan las credenciales de cuenta de administrador del servicio de forma segura y Start-AzsReadinessChecker comprueba que la cuenta de Azure y Azure Active Directory son válidos para una implementación de AAD con un nombre de directorio de inquilino de "azurestack.contoso.com".


**Ejemplo: Validación de la identidad de Azure con datos de implementación (compatibilidad de implementación)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

En este ejemplo, se solicitan las credenciales de cuenta de administrador del servicio de forma segura y Start-AzsReadinessChecker comprueba que la cuenta de Azure y Azure Active Directory son válidos para una implementación de AAD donde AzureCloud y TenantName se leen del archivo JSON de datos de implementación generado para la implementación.


**Ejemplo: Validación del registro de Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

En este ejemplo, las credenciales del propietario de la suscripción se solicitan de forma segura y Start-AzsReadinessChecker realiza la validación en la cuenta y suscripción indicadas para garantizar que se puede utilizar para el registro de Azure Stack. 


**Ejemplo: Validación del registro de Azure con datos de implementación (equipo de implementación)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

En este ejemplo, las credenciales del propietario de la suscripción se solicitan de forma segura y Start-AzsReadinessChecker realiza la validación en la cuenta y suscripción indicadas para garantizar que puede usarse para el registro de Azure Stack donde los detalles adicionales se leen del archivo JSON de datos de implementación generado para la implementación.

**Ejemplo: Paquete PFX de importación y exportación**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

En este ejemplo, se solicita la contraseña PFX de forma segura. El archivo ssl.pfx se importará al almacén de certificados de la máquina local, se volverá a exportar con la misma contraseña y se guardará como ssl_new.pfx.  Este procedimiento se utiliza cuando la validación del certificado marca que una clave privada no tiene establecido el atributo de máquina local, la cadena del certificado está rota, hay certificados irrelevantes en el PFX o la cadena del certificado está en un orden incorrecto.


**Ejemplo: Vista del informe de validación (implementación y soporte técnico)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

En este ejemplo, el equipo de implementación o de soporte técnico reciben el informe de preparación del cliente (Contoso) y usan Start-AzsReadinessChecker para ver el estado de las ejecuciones de validación que realiza Contoso.

**Ejemplo: Vista del resumen del informe de validación solo de validación de certificados (implementación y soporte técnico)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

En este ejemplo, el equipo de implementación o de soporte técnico reciben el informe de preparación del cliente (Contoso) y usan Start-AzsReadinessChecker para ver un estado resumido de las ejecuciones de validación que realiza Contoso.



## <a name="required-parameters"></a>Parámetros obligatorios
> -RegionName

Especifica el nombre de la región de la implementación de Azure Stack.
|  |  |
|----------------------------|--------------|
|Escriba:                        |string        |
|Posición:                   |con nombre         |
|Valor predeterminado:              |None          |
|Aceptar la entrada de la canalización:      |False         |
|Aceptar caracteres comodín: |False         |

> -FQDN    

Especifica el FQDN externo de la implementación de Azure Stack, con los alias ExternalFQDN y ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Escriba:                        |string        |
|Posición:                   |con nombre         |
|Valor predeterminado:              |ExternalFQDN, ExternalDomainName |
|Aceptar la entrada de la canalización:      |False         |
|Aceptar caracteres comodín: |False         |

 

> -IdentitySystem    

Especifica los valores válidos del sistema de identidades de la implementación de Azure Stack, AAD o ADFS, para Azure Active Directory y Servicios de federación de Active Directory (AD FS) respectivamente.
|  |  |
|----------------------------|--------------|
|Escriba:                        |string        |
|Posición:                   |con nombre         |
|Valor predeterminado:              |None          |
|Valores válidos:                |"AAD", "ADFS"  |
|Aceptar la entrada de la canalización:      |False         |
|Aceptar caracteres comodín: |False         |

> -PfxPassword    

Especifica la contraseña asociada a los archivos de certificado PFX.
|  |  |
|----------------------------|---------|
|Escriba:                        |SecureString |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -PaaSCertificates

Especifica la tabla hash que contiene las rutas de acceso y las contraseñas de los certificados de PaaS.
|  |  |
|----------------------------|---------|
|Escriba:                        |Hashtable |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -DeploymentDataJSONPath

Especifica el archivo de configuración JSON de datos de implementación de Azure Stack. Este archivo se genera para la implementación.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -PfxPath

Especifica la ruta de acceso a un certificado problemático que requiere que se corrija la rutina de importación y exportación, tal y como se indica en la validación de certificados de esta herramienta.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -ExportPFXPath  

Especifica la ruta de acceso de destino para el archivo PFX resultante de la rutina de importación y exportación.  
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -Subject

Especifica un diccionario ordenado del firmante para la generación de solicitudes de certificado.
|  |  |
|----------------------------|---------|
|Escriba:                        |OrderedDictionary   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -RequestType

Especifica el tipo de SAN de la solicitud de certificado. Valores válidos: MultipleCSR, SingleCSR.
- *MultipleCSR* genera varias solicitudes de certificado, una por servicio.
- *SingleCSR* genera una solicitud de certificado para todos los servicios.   

|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Valores válidos:                |"MultipleCSR","SingleCSR" |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -OutputRequestPath

Especifica la ruta de acceso de destino para los archivos de solicitud de certificado; el directorio debe existir ya.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -AADServiceAdministrator

Especifica el Administrador de servicios de Azure Active Directory que se va a usar para la implementación de Azure Stack.
|  |  |
|----------------------------|---------|
|Escriba:                        |PSCredential   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -AADDirectoryTenantName

Especifica el nombre de Azure Active Directory que se va a usar para la implementación de Azure Stack.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -AzureEnvironment

Especifica la instancia de servicios de Azure que contiene las cuentas, los directorios y las suscripciones que se van a usar para la implementación y el registro de Azure Stack.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Valores válidos:                |'AzureCloud','AzureChinaCloud','AzureGermanCloud' |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -RegistrationAccount

Especifica la cuenta de registro que se va a usar para el registro de Azure Stack.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -RegistrationSubscriptionID

Especifica el identificador de suscripción del registro que se va a usar para el registro de Azure Stack.
|  |  |
|----------------------------|---------|
|Escriba:                        |Guid     |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

> -ReportPath

Especifica la ruta de acceso para el informe de preparación, tiene como valor predeterminado el nombre del informe predeterminado y el directorio actual.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |Todo      |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |



## <a name="optional-parameters"></a>Parámetros opcionales
> -CertificatePath     

Especifica la ruta de acceso en la que solo se encuentran las carpetas de certificados necesarias para el certificado.

Las carpetas necesarias para la implementación de Azure Stack con el sistema de identidades de Azure Active Directory son las siguientes:

ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Las carpetas necesarias para la implementación de Azure Stack con el sistema de identidades de Servicios de federación de Active Directory (AD FS) son las siguientes:

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal


|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |.\Certificates |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |


> -IncludePaaS  

Especifica si se deben agregar servicios y nombres de host de PaaS a las solicitudes de certificado.


|  |  |
|----------------------------|------------------|
|Escriba:                        |SwitchParameter   |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |


> -ReportSections        

Especifica si solo se mostrará el resumen del informe; omite los detalles.
|  |  |
|----------------------------|---------|
|Escriba:                        |string   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |Todo      |
|Valores válidos:                |"Certificate","AzureRegistration","AzureIdentity","Jobs","All" |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |


> -Summary 

Especifica si solo se mostrará el resumen del informe; omite los detalles.
|  |  |
|----------------------------|------------------|
|Escriba:                        |SwitchParameter   |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |


> -CleanReport  

Quita el historial de ejecuciones y validaciones anteriores y escribe las validaciones en un nuevo informe.
|  |  |
|----------------------------|------------------|
|Escriba:                        |SwitchParameter   |
|Alias:                    |cf                |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |


> -OutputPath    

Especifica la ruta de acceso personalizada donde se guarda el informe JSON de preparación y el archivo de registro detallado.  Si la ruta de acceso no existe, la herramienta intentará crear el directorio.
|  |  |
|----------------------------|------------------|
|Escriba:                        |string            |
|Posición:                   |con nombre             |
|Valor predeterminado:              |$ENV:TEMP\AzsReadinessChecker  |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |


> -Confirm  

Solicita confirmación antes de ejecutar el cmdlet.
|  |  |
|----------------------------|------------------|
|Escriba:                        |SwitchParameter   |
|Alias:                    |cf                |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |


> -WhatIf  

Muestra lo que sucedería si se ejecutara el cmdlet. El cmdlet no se ejecuta.
|  |  |
|----------------------------|------------------|
|Escriba:                        |SwitchParameter   |
|Alias:                    |wi                |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |

 
