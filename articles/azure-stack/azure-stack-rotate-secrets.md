---
title: Cambio de secretos en Azure Stack | Microsoft Docs
description: Aprenda a cambiar los secretos en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a3dfce6ce1b136e39047cfd47b336b2fb2a35af9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258688"
---
# <a name="rotate-secrets-in-azure-stack"></a>Cambio de secretos en Azure Stack

*Estas instrucciones se aplican solo a sistemas integrados de Azure Stack (versión 1803 y posteriores). No intente el cambio de secretos en versiones de Azure Stack anteriores a la 1802*

Azure Stack usa varios secretos para proteger las comunicaciones entre los recursos y servicios de la infraestructura de Azure Stack.

- **Secretos internos**  
Todos los certificados, contraseñas, cadenas seguras y claves que usa la infraestructura de Azure Stack sin la intervención del operador de este. 

- **Secretos externos**  
Certificados de servicio de infraestructura para servicios de uso externo que proporciona el operador de Azure Stack. Esto incluye los certificados para los siguientes servicios: 
    - Administrator Portal 
    - Public Portal 
    - Administrator Azure Resource Manager 
    - Global Azure Resource Manager 
    - Administrator Keyvault 
    - Keyvault 
    - ACS (incluido Blob Storage, Table Storage y Queue Storage) 
    - ADFS<sup>*</sup>
    - Graph<sup>*</sup>

    > <sup>*</sup> Solo es aplicable si el proveedor de identidades del entorno son los Servicios de federación de Active Directory (AD FS).

> [!NOTE]
> Todas las demás claves y cadenas seguras, incluyendo BMC, las contraseñas de cambio y las contraseñas de cuentas de usuario y administrador aún las actualiza manualmente el administrador. 

Para mantener la integridad de la infraestructura de Azure Stack, los operadores necesitan la capacidad de poder cambiar periódicamente los secretos de la infraestructura con una frecuencia que sea coherente con los requisitos de seguridad de su organización.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Cambio de secretos con certificados externos desde una nueva entidad de certificación

Azure Stack admite el cambio de secretos con certificados externos desde una nueva entidad de certificación (CA) en los contextos siguientes:

|CA del certificado instalado|CA a la que cambiar|Compatible|Versiones compatibles de Azure Stack|
|-----|-----|-----|-----|-----|
|De autofirmado|A empresarial|No compatible||
|De autofirmado|A autofirmado|No compatible||
|De autofirmado|A pública<sup>*</sup>|Compatible|1803 y posterior|
|De empresarial|A empresarial|Compatible siempre y cuando los clientes usen la MISMA entidad de certificación empresarial utilizada en la implementación|1803 y posterior|
|De empresarial|A autofirmado|No compatible||
|De empresarial|A pública<sup>*</sup>|Compatible|1803 y posterior|
|De pública<sup>*</sup>|A empresarial|No compatible|1803 y posterior|
|De pública<sup>*</sup>|A autofirmado|No compatible||
|De pública<sup>*</sup>|A pública<sup>*</sup>|Compatible|1803 y posterior|

<sup>*</sup> Aquí, las entidades de certificación públicas son aquellas que forman parte del programa de raíz de confianza de Windows. Puede encontrar la lista completa de [participantes del programa de certificados de raíz de confianza de Microsoft (al 27 de junio de 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Corrección de alertas

Cuando faltan menos de 30 días para la expiración de los secretos, se generan las siguientes alertas en el portal de administración: 

- Expiración de contraseña de cuenta de servicio pendiente 
- Expiración de certificado interno pendiente 
- Expiración de certificado externo pendiente 

Si ejecuta el cambio de secretos mediante las instrucciones que aparecen a continuación se corregirán estas alertas.

## <a name="pre-steps-for-secret-rotation"></a>Pasos previos para el cambio de secretos

1.  Avise a los usuarios de cualquier operación de mantenimiento. Programe ventanas de mantenimiento normales, en la medida de lo posible, durante horas no laborables. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

    > [!note]  
    > Los pasos siguientes solo se aplican al cambiar secretos externos de Azure Stack.

2.  Prepare un nuevo conjunto de certificados externos de reemplazo. El nuevo conjunto coincide con las especificaciones de certificado que se describen en [Requisitos de certificados de infraestructura de clave pública de Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  Guarde una copia de seguridad de los certificados usados para el cambio en una ubicación segura. Si se ejecuta el cambio y, después, se produce un error, reemplace los certificados del recurso compartido de archivos por las copias de seguridad antes de volver a ejecutar el cambio. Conserve las copias de seguridad en la ubicación segura.
3.  Cree un recurso compartido de archivos al que pueda acceder desde las máquinas virtuales de ERCS. El recurso compartido de archivos debe ser de lectura y escritura para la identidad **CloudAdmin**.
4.  Abra una consola de PowerShell ISE desde un equipo que tenga acceso al recurso compartido de archivos. Vaya al recurso compartido de archivos. 
5.  Ejecute **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** para crear los directorios necesarios para los certificados externos.

## <a name="rotating-external-and-internal-secrets"></a>Cambio de secretos externos e internos

Para cambiar los secretos externos e internos:

1. En el directorio **/Certificates** recién creado en los pasos previos, coloque el nuevo conjunto de certificados externos de reemplazo en la estructura de directorios según el formato descrito en la sección Certificados obligatorios de [Requisitos de certificados de infraestructura de clave pública de Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Cree una sesión de PowerShell con el [punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) mediante la cuenta **CloudAdmin** y almacene las sesiones como una variable. Utilizará esta variable como parámetro en el paso siguiente.

    > [!IMPORTANT]  
    > No escriba la sesión, almacénela como una variable.
    
3. Ejecute **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Pase la variable de sesión de PowerShell del punto de conexión con privilegios como parámetro **Session**. 
4. Ejecute **Start-SecretRotation** con los siguientes parámetros:
    - **PfxFilesPath**  
    Especifique la ruta de acceso de red al directorio de certificados que creó anteriormente.  
    - **PathAccessCredential**  
    Un objeto PSCredential con las credenciales del recurso compartido. 
    - **CertificatePassword**  
    Una cadena segura de la contraseña usada para todos los archivos de certificados pfx creados.
4. Espere mientras cambian los secretos.  
Cuando se complete correctamente el cambio de secretos, la consola mostrará el mensaje **Overall action status: Success** (Estado global de la acción: correcto). 
5. Después de la finalización correcta del cambio de secretos, quite los certificados del recurso compartido creado en el paso anterior y almacénelos en la ubicación segura de copia de seguridad. 

## <a name="walkthrough-of-secret-rotation"></a>Guía detallada sobre cambio de secretos

El siguiente ejemplo de PowerShell muestra los cmdlets y parámetros que debe ejecutar para cambiar los secretos.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Cambio solo de secretos internos

Para cambiar solo los secretos internos de Azure Stack:

1. Cree una sesión de PowerShell con el [punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. En la sesión del punto de conexión con privilegios, ejecute **Start-SecretRotation** sin argumentos.

## <a name="start-secretrotation-reference"></a>Referencia de Start-SecretRotation

Permite cambiar los secretos de un sistema de Azure Stack. Solo se ejecuta en el punto de conexión con privilegios de Azure Stack.

### <a name="syntax"></a>Sintaxis

Ruta de acceso (valor predeterminado)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>DESCRIPCIÓN

El cmdlet Start-SecretRotation permite cambiar los secretos de la infraestructura de un sistema de Azure Stack. De forma predeterminada, cambia todos los secretos expuestos a la infraestructura de red interna y, con la entrada del usuario, también permite cambiar los certificados de todos los puntos de conexión de la infraestructura de red externa. Al cambiar los puntos de conexión de la infraestructura de red externa, Start-SecretRotation se debe ejecutar mediante un bloque de script Invoke-Command con la sesión del punto de conexión con privilegios del entorno de Azure Stack pasada como el parámetro de la sesión.
 
### <a name="parameters"></a>Parámetros

| . | Escriba | Obligatorio | Posición | Valor predeterminado | DESCRIPCIÓN |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | string  | False  | con nombre  | None  | La ruta de acceso del recurso compartido de archivos al directorio **\Certificates** que contiene todos los certificados del punto de conexión de la red externa. Solo se necesita al cambiar secretos internos y externos. El directorio final debe ser **\Certificates**. |
| CertificatePassword | SecureString | False  | con nombre  | None  | La contraseña de todos los certificados que se proporcionan en -PfXFilesPath. Valor obligatorio si se proporciona PfxFilesPath al cambiar secretos internos y externos. |
|

### <a name="examples"></a>Ejemplos
 
**Solo cambio de secretos de infraestructura interna**

```powershell  
PS C:\> Start-SecretRotation  
```

Este comando permite cambiar todos los secretos de la infraestructura expuesta a la red interna de Azure Stack. Start-SecretRotation cambia todos los secretos generados por Stack, pero dado que no se han proporcionado certificados, los certificados de punto de conexión externos no se cambiarán.  

**Cambio de secretos de la infraestructura interna y externa**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Este comando cambia todos los secretos de la infraestructura expuestos a la red interna de Azure Stack, así como los certificados TLS que se usan para los puntos de conexión de la infraestructura de red externa de Azure Stack. Start-SecretRotation cambia todos los secretos generados por Stack, y dado que se han proporcionado certificados, los certificados de punto de conexión externos también se cambiarán.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Actualización de la contraseña del controlador de administración de placa base (BMC)

El controlador de administración de placa base (BMC) supervisa el estado físico de sus servidores. Las especificaciones e instrucciones para actualizar la contraseña de BMC varían según el proveedor de hardware del fabricante de equipos originales (OEM). Debe actualizar regularmente las contraseñas de los componentes de Azure Stack.

1. Actualice el BMC en los servidores físicos de Azure Stack siguiendo las instrucciones del OEM. La contraseña para cada BMC de su entorno debe ser la misma.
2. Abra un punto de conexión con privilegios en sesiones de Azure Stack. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).
3. Después que el símbolo del sistema de PowerShell cambia a **[dirección IP o nombre de máquina virtual de ERCS]: PS>** o a **[azs-ercs01]: PS>**, según el entorno, ejecute `Set-BmcPassword` mediante la ejecución de `invoke-command`. Pase la variable de sesión del punto de conexión con privilegios como parámetro. Por ejemplo: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    También puede usar la versión de PowerShell estática con las contraseñas como líneas de código:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de la seguridad de Azure Stack](azure-stack-security-foundations.md)
