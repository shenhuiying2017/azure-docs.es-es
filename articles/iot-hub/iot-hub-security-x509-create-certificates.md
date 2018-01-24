---
title: Uso de PowerShell para crear certificados X.509 | Microsoft Docs
description: "Cómo usar PowerShell para crear certificados X.509 localmente y habilitar la seguridad basada en X.509 en Azure IoT Hub en un entorno simulado."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: b2f78e8debd367f86ee9bb06bf7de50590c61ad7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>Scripts de PowerShell para administrar certificados X.509 firmados por la entidad de certificación

La seguridad basada en certificados X.509 de IoT Hub requiere que comience con un [cadena de certificados X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que incluya el certificado raíz, así como cualquier certificado intermedio hasta el certificado de hoja. Esta guía de *procedimientos* le lleva a través de scripts de PowerShell de ejemplo que usan [OpenSSL](https://www.openssl.org/) para crear y firmar los certificados X.509. Es aconsejable que esta guía se use solo para realizar experimentos, ya que muchos de estos pasos se realizarán durante el proceso de fabricación en el mundo real. Estos certificados se pueden usar para simular seguridad en Azure IoT Hub mediante la *autenticación del certificado X.509*. Los pasos de esta guía crean certificados localmente en una máquina Windows. 

## <a name="prerequisites"></a>requisitos previos
En este tutorial se da por supuesto que ha adquirido los archivos binarios de OpenSSL. Puede
    - descargar el código fuente de OpenSSL y compilar los archivos binarios en su máquina, o bien 
    - descargue e instale cualquier [archivo binario de OpenSSL de terceros](https://wiki.openssl.org/index.php/Binaries), por ejemplo, de [este proyecto en SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>Creación de certificados X.509
Los pasos siguientes muestran un ejemplo de cómo crear los certificados raíz X.509 localmente. 

1. Abra una ventana de PowerShell como *administrador*. 
2. Navegue hasta el directorio de trabajo. Ejecute el siguiente script para establecer las variables globales. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Ejecute el siguiente script, que copia los archivos binarios de OpenSSL en el directorio de trabajo y establece las variables de entorno:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. A continuación, ejecute el siguiente script que busca si ya está instalado un certificado por el *nombre del firmante* especificado, y si OpenSSL está configurado correctamente en su máquina:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Si todo está configurado correctamente, debería ver el mensaje "Correcto". 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>Creación de una cadena de certificados X.509
Cree una cadena de certificados con una CA raíz, por ejemplo, "CN = Azure IoT Root CA" que usa este ejemplo, mediante la ejecución del siguiente script de PowerShell. Este script también actualiza el almacén de certificados del sistema operativo Windows, además de crear archivos de certificado en el directorio de trabajo. 
    1. El siguiente script crea una función de PowerShell para crear un certificado autofirmado para un *nombre de firmante* dado y una autoridad de firma. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. La siguiente función de PowerShell crea certificados X.509 intermedios con la función anterior, así como los archivos binarios de OpenSSL. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. La siguiente función de PowerShell crea la cadena de certificados X.509. Para más información, lea [Cadenas de certificados](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification).
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Este script crea un archivo denominado *RootCA.cer* en su directorio de trabajo. 
    4. Por último, utilice las funciones de PowerShell anteriores para crear la cadena de certificados X.509, para lo que debe ejecutar el comando `New-CACertChain` en la ventana de PowerShell. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Prueba de posesión de su certificado de entidad de certificación X.509

Este script realiza el flujo de la *prueba de posesión* de su certificado X.509. 

En la ventana de PowerShell del escritorio, ejecute el siguiente código:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Este código crea un certificado con el nombre de asunto dado, firmado por la entidad de certificación, en forma de archivo denominado *VerifyCert4.cer* en el directorio de trabajo. Este archivo de certificado servirá de ayuda para validar con su instancia de IoT Hub que tiene el permiso de firma (es decir, la clave privada) de esta entidad de certificación.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Crear certificado X.509 de hoja para el dispositivo

Esta sección muestra que puede usar un script de PowerShell que crea un certificado de dispositivo de hoja y la cadena de certificados correspondiente. 

En la ventana de PowerShell de la máquina local, ejecute el siguiente script para crear un certificado X.509 firmado por una entidad de certificación para este dispositivo:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

A continuación, ejecute `New-CADevice "<yourTestDevice>"` en la ventana de PowerShell y use el nombre descriptivo que usó para crear su dispositivo. Cuando se le solicite la contraseña de la clave privada de la entidad de certificación, escriba, "123". Esto crea un archivo  _<yourTestDevice>.pfx_ en el directorio de trabajo.

## <a name="clean-up-certificates"></a>Limpieza de certificados

En la barra de inicio o la aplicación **Configuración**, busque y seleccione **Administrar certificados de equipo**. Elimine todos los certificados emitidos por **Azure IoT CA TestOnly***. Estos deben encontrarse en las tres ubicaciones siguientes: 

* Certificados - Equipo local > Personal > Certificados
* Certificados - Equipo local > Entidades de certificación raíz de confianza > Certificados
* Certificados - Equipo local > Entidades de emisoras de certificados intermedios > Certificados

   ![Eliminación de certificados Azure IoT CA TestOnly](./media/iot-hub-security-x509-create-certificates/cleanup.png)