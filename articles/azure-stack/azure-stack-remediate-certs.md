---
title: Corrección de problemas de certificados en Azure Stack | Microsoft Docs
description: Use Azure Stack Readiness Checker para revisar y corregir problemas de certificados.
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
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937575"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Corrección de problemas comunes con certificados de PKI en Azure Stack
La información de este artículo puede ayudarle a reconocer y resolver problemas comunes con los certificados de PKI en Azure Stack. Puede detectar problemas cuando usa la herramienta Azure Stack Readiness Checker para [validar certificados de PKI en Azure Stack](azure-stack-validate-pki-certs.md). La herramienta comprueba que los certificados cumplen los requisitos de PKI de una implementación de Azure Stack y una rotación de secretos de Azure Stack, y registra los resultados en un archivo [report.json file](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Lectura de PFX
**Advertencia**: la contraseña sólo protege la información privada del certificado.  
**Corrección**: se recomienda exportar archivos PFX con la configuración opcional de **Habilitar privacidad de certificado**.  

**Error**archivo PFX no válido.  
**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo de firma
**Error**: el algoritmo de firma es SHA1.    
**Corrección**: siga los pasos de generación de solicitudes de firma de certificado de Azure Stack para volver a generar la solicitud de firma de certificados (CSR) con el algoritmo de firma SHA256. Después, reenvíe la CSR a la entidad de certificación para que vuelva a emitir el certificado.

## <a name="private-key"></a>Clave privada
**Error**: falta la clave privada o no contiene el atributo de máquina Local.  
**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en Preparación de certificados PKI de Azure Stack para la implementación. Estos pasos incluyen la exportación desde el almacén de certificados de la máquina local.

## <a name="certificate-chain"></a>Cadena de certificados
**Error**: la cadena de certificados no está completa.  
**Corrección**: los -certificados deben contener una cadena de certificados completa.  Vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md) y seleccione la opción **Incluir todos los certificados en la ruta de certificación si es posible**.

## <a name="dns-names"></a>Nombres DNS
**Error** -el objeto DNSNameList del certificado no contiene el nombre del punto de conexión de servicio de Azure Stack o una coincidencia válida de caracteres comodín.  Las coincidencias de caracteres comodín solo son válidas para el espacio de nombres del extremo izquierdo del nombre DNS. Por ejemplo, _*.region.domain.com_ solo es válida para *portal.region.domain.com*, no para _*.table.region.domain.com_.  
**Corrección**: siga los pasos de Generación de solicitudes de firma de certificados de Azure Stack para volver a generar la CSR con los nombres DNS correctos para admitir puntos de conexión de Azure Stack. Reenvíe la CSR a una entidad de certificación y luego siga los pasos de [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md) para exportar el certificado de la máquina que genera la CSR.  

## <a name="key-usage"></a>Uso de claves
**Error**: falta el uso de claves en la firma digital o el cifrado de clave, o falta el uso mejorado de clave en la autenticación de servidor o la autenticación de cliente.  
**Corrección**: siga los pasos de [Generación de solicitudes de firma de certificados de Azure Stack](azure-stack-get-pki-certs.md) para volver a generar la CSR con los atributos de uso de clave correctos.  Vuelva a enviar la CSR a la entidad de certificación y confirme que ninguna plantilla de certificado sobrescribe el uso de clave en la solicitud.

## <a name="key-size"></a>Tamaño de clave
**Error**: el tamaño de clave es inferior a 2048    
**Corrección**: siga los pasos de [Generación de solicitudes de firma de certificados de Azure Stack](azure-stack-get-pki-certs.md) para volver a generar la CSR con la longitud de clave correcta (2048) y reenvíe la CSR a la entidad de certificación.

## <a name="chain-order"></a>Orden de la cadena
**Error**: el orden de la cadena de certificados es incorrecto.  
**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md) y seleccione la opción **Incluir todos los certificados en la ruta de certificación si es posible**. Asegúrese de que solo se selecciona el certificado de hoja para la exportación. 

## <a name="other-certificates"></a>Otros certificados
**Error**: el paquete PFX contiene certificados que no son el certificado de hoja ni parte de la cadena de certificados.  
**Corrección**: vuelva a exportar el certificado mediante los pasos descritos en [Preparación de certificados PKI de Azure Stack para la implementación](azure-stack-prepare-pki-certs.md) y seleccione la opción **Incluir todos los certificados en la ruta de certificación si es posible**. Asegúrese de que solo se selecciona el certificado de hoja para la exportación.

## <a name="fix-common-packaging-issues"></a>Solución de problemas comunes de empaquetado
AzsReadinessChecker puede importar y luego exportar un archivo PFX para solucionar problemas comunes de empaquetado, por ejemplo: 
 - Falta la *clave privada* en el atributo de máquina Local.
 - La *cadena de certificados* es incorrecta o está incompleta. (La máquina local debe contener la cadena de certificados si no lo hace el paquete PFX). 
 - *Otros certificados*.
Aunque AzsReadinessChecker no sirve de ayuda si hay que generar una nueva CSR y volver a emitir un certificado. 

### <a name="prerequisites"></a>requisitos previos
Deben cumplirse los siguientes requisitos previos en el equipo donde se ejecuta la herramienta: 
 - Windows 10 o Windows Server 2016, con conectividad a internet.
 - PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmd de PowerShell y luego revise la versión *principal* y las versiones *secundarias*.

   > `$PSVersionTable.PSVersion`
 - Configure [PowerShell para Azure Stack](azure-stack-powershell-install.md). 
 - Descargue la versión más reciente de la herramienta [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

### <a name="import-and-export-an-existing-pfx-file"></a>Importación y exportación de una archivo PFX existente
1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para establecer la contraseña PFX. Reemplace *PFXpassword* por la contraseña real. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para exportar un nuevo archivo PFX.
   - En *-PfxPath*, especifique la ruta de acceso al archivo PFX con el que trabaja.  En el ejemplo siguiente, la ruta de acceso es *.\certificates\ssl.pfx*.
   - En *-ExportPFXPath*, especifique la ubicación y el nombre del archivo PFX que se va a exportar.  En el ejemplo siguiente, la ruta de acceso es *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. Cuando se haya completado la herramienta, compruebe el éxito en el resultado: ![resultados](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de la seguridad de Azure Stack](azure-stack-rotate-secrets.md)