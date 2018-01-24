---
title: "Generación de certificados de infraestructura de clave pública de Azure Stack para la implementación de sistemas integrados de Azure Stack | Microsoft Docs"
description: "Describe el proceso de implementación de certificados PKI de Azure Stack para sus sistemas integrados."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e7edbc1c4aa6e3cb1026d493886ef7ca704b9131
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Generación de certificados de infraestructura de clave pública para la implementación de Azure Stack
Ahora que conoce [los requisitos de los certificados PKI](azure-stack-pki-certs.md) para las implementaciones de Azure Stack, debe obtener dichos certificados de la entidad de certificación (CA) que prefiera. 

## <a name="request-certificates-using-an-inf-file"></a>Solicitud de certificados con un archivo INF
Una manera de solicitar certificados de una entidad de certificación pública o interna es mediante un archivo INF. La utilidad certreq.exe integrada de Windows puede usar un archivo INF que especifique los detalles de los certificados, a fin de generar un archivo de solicitud tal como se describe en esta sección. 

### <a name="sample-inf-file"></a>Ejemplo de archivo INF 
El ejemplo de archivo INF de solicitud del certificado puede usarse para crear un archivo de solicitud de certificados desconectada y enviarlo a una entidad de certificación (interna o pública). El archivo INF cubre todos los puntos de conexión necesarios (incluidos los servicios de PaaS opcionales) en un único certificado comodín. 

El ejemplo de archivo INF supone que la región es igual a **sea** y el valor de nombre de dominio completo externo es **sea&#46;contoso&#46;com**. Cambie esos valores de modo que coincidan con su entorno antes de generar un ejemplo de archivo INF para su implementación. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Generación y envío de la solicitud a la entidad de certificación
El siguiente proceso describe cómo puede personalizar y usar el ejemplo de archivo INF generado anteriormente para solicitar un certificado de una entidad de certificación:

1. **Edite y guarde el archivo INF**. Copie el ejemplo proporcionado y guárdelo en un archivo de texto nuevo. Reemplace el nombre del firmante y el nombre de dominio completo externo con los valores que correspondan a la implementación y guarde el archivo como .INF.
2. **Genere una solicitud con certreq**. Con un equipo Windows, inicie un símbolo del sistema como administrador y ejecute el siguiente comando para generar un archivo de solicitud (.req): `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Envíelo a la entidad de certificación**. Envíe el archivo .REQ generado a la entidad de certificación (puede ser interna o pública).
4. **Importe el archivo .CER**. La entidad de certificación devuelve un archivo .CER. Con el mismo equipo Windows desde el que ha generado el archivo de solicitud, importe el archivo .CER que se devuelve en el almacén de equipo o personal. 
5. **Exporte y copie el archivo . PFX en las carpetas de implementación**. Exporte el certificado (incluida la clave privada) como un archivo .PFX y cópielo en las carpetas de implementación descritas en los [requisitos de PKI de la implementación de Azure Stack](azure-stack-pki-certs.md).

