---
title: Generación de certificados de infraestructura de clave pública de Azure Stack para la implementación de sistemas integrados de Azure Stack | Microsoft Docs
description: Describe el proceso de implementación de certificados PKI de Azure Stack para sus sistemas integrados.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 349661352d17b015d4c605b39f1e42aa482949ac
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Generación de solicitudes de firma de certificados de Azure Stack

La herramienta Azure Stack Readiness Checker que se describe en este artículo está disponible [en la Galería de PowerShell](https://aka.ms/AzsReadinessChecker). La herramienta crea solicitudes de firma de certificados (CSR) adecuadas para una implementación de Azure Stack. Los certificados se deben solicitar, generar y validar con suficiente tiempo para probarlos antes de la implementación. 

La herramienta Azure Stack Readiness Checker (AzsReadinessChecker) realiza las siguientes solicitudes de certificado:

 - **Solicitudes de certificados estándar**  
    Realice la solicitud según se indica en [Generación de certificados de infraestructura de clave pública para la implementación de Azure Stack](azure-stack-get-pki-certs.md). 
 - **Tipo de solicitud**  
    Solicite varios SAN comodín, varios certificados de dominio, solicitudes de certificado comodín individuales.
 - **Plataforma como servicio**  
    Opcionalmente, solicite nombres de plataforma como servicio (PaaS) para los certificados como se especifica en [Requisitos de certificados de infraestructura de clave pública de Azure Stack: Certificados PaaS opcionales](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>requisitos previos

El sistema debe cumplir los siguientes requisitos previos antes de generar los CSR para los certificados PKI para una implementación de Azure Stack:

 - Microsoft Azure Stack Readiness Checker
 - Atributos de certificado:
    - Nombre de la región
    - Nombres de dominio completos (FQDN) externos
    - Asunto
 - Windows 10 o Windows Server 2016

## <a name="generate-certificate-signing-requests"></a>Generación de las solicitudes de firma de certificado

Siga estos pasos para preparar y validar los certificados PKI de Azure Stack: 

1.  Instale AzsReadinessChecker desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Declare el **asunto** como un diccionario ordenado. Por ejemplo:  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Si se proporciona un nombre común (CN) esto se sobrescribirá con el primer nombre DNS de la solicitud de certificado.

3.  Declare un directorio de salida que ya exista:

    ````PowerShell  
    $outputDirectory = "$ENV:USERNAME\Documents\AzureStackCSR" 
    ````

4. Declare un **nombre de región** y un **FQDN externo** pensado para la implementación de Azure Stack.

    ```PowerShell  
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` constituye la base en la que se crean todos los nombres DNS externos de Azure Stack. En este ejemplo, el portal sería `portal.east.azurestack.contoso.com`.

5. Para generar una única solicitud de certificado con varios nombres alternativos del firmante incluidos aquellos necesarios para los servicios PaaS:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType MultipleSAN -OutputRequestPath $OutputDirectory -IncludePaaS
    ````

6. Para generar solicitudes de firma de certificados individuales para cada nombre DNS sin servicios PaaS:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleSAN -OutputRequestPath $OutputDirectory
    ````

7. Revise la salida:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

8.  Envíe el archivo **.REQ** generado a la entidad de certificación (puede ser interna o pública).  El directorio de salida de **tart-AzsReadinessChecker** contiene las solicitudes de firma de certificados necesarias para enviarlas a una entidad de certificación.  También tiene un directorio secundario que contiene los archivos INF que se usan durante la generación de una solicitud de certificado, a modo de referencia. Asegúrese de que la entidad de certificación genera certificados mediante la solicitud generada que cumple los [Requisitos de PKI de Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Pasos siguientes

[Preparación de certificados PKI de Azure Stack](azure-stack-prepare-pki-certs.md)

