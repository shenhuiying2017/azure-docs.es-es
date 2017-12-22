---
title: "Integración de Key Vault con SQL Server en máquinas virtuales Windows en Azure (clásicas) | Microsoft Docs"
description: "Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con Azure Key Vault. En este tema se explica cómo usar la integración de Azure Key Vault con la creación de máquinas virtuales de SQL Server en el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a9ac5763bb934bd0646e47c3936f7bdd0d603b1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Configurar la integración de Azure Key Vault para SQL Server en Azure Virtual Machines (implementación clásica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Clásico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Información general
SQL Server tiene varias características de cifrado, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) y el [cifrado de copia de seguridad](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves criptográficas que se usan para el cifrado. El servicio Azure Key Vault (AKV) está diseñado para mejorar la seguridad y la administración de estas claves en una ubicación segura y con gran disponibilidad. El [conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use estas claves de Azure Key Vault.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

Si ejecuta SQL Server con máquinas locales, hay una serie de [pasos que puede seguir para acceder a Azure Key Vault desde la máquina de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero para SQL Server en las máquinas virtuales de Azure, puede ahorrar tiempo usando la característica *Integración de Azure Key Vault* . Con algunos cmdlets de Azure PowerShell para habilitar esta característica, puede automatizar la configuración necesaria para que una máquina virtual de SQL tenga acceso a su Almacén de claves.

Cuando se habilita esta característica, automáticamente se instala el conector de SQL Server, se configura el proveedor EKM para obtener acceso a Azure Key Vault y se crea la credencial para que pueda tener acceso a su almacén. Si examinamos los pasos descritos en la documentación local que se mencionó anteriormente, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que aún tiene que hacer manualmente es crear el Almacén de claves y las claves. Desde allí, se automatiza toda la configuración de la máquina virtual de SQL. Cuando esta característica haya completado el programa de instalación, puede ejecutar instrucciones de T-SQL para empezar a cifrar sus bases de datos o copias de seguridad como lo haría normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Configuración de la integración de AKV
Use PowerShell para configurar la integración de Azure Key Vault. Las secciones siguientes proporcionan una visión general de los parámetros necesarios y, a continuación, un script de PowerShell de ejemplo.

### <a name="install-the-sql-server-iaas-extension"></a>Instalación de la extensión IaaS de SQL Server
Primero, [instale la extensión IaaS de SQL Server](../classic/sql-server-agent-extension.md).

### <a name="understand-the-input-parameters"></a>Descripción de los parámetros de entrada
En la tabla siguiente se enumeran los parámetros necesarios para ejecutar el script de PowerShell en la sección siguiente.

| Parámetro | Description | Ejemplo |
| --- | --- | --- |
| **$akvURL** |**La dirección URL del Almacén de claves** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**Nombre de entidad de servicio** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Secreto de entidad de servicio** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Nombre de credencial**: la integración de AKV crea una credencial en SQL Server, permitiendo el acceso de la máquina virtual al Almacén de claves. Elija un nombre para esta credencial. |"mycred1" |
| **$vmName** |**Nombre de la máquina virtual**: el nombre de una máquina virtual de SQL creada anteriormente. |"myvmname" |
| **$serviceName** |**Nombre de servicio**: nombre del servicio en la nube que está asociado a la máquina virtual de SQL. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Habilitación de la integración de AKV con PowerShell
El cmdlet **New-AzureVMSqlServerKeyVaultCredentialConfig** crea un objeto de configuración para la característica de integración de Azure Key Vault. **Set-AzureVMSqlServerExtension** configura esta integración con el parámetro **KeyVaultCredentialSettings**. Los pasos siguientes muestran cómo usar estos comandos.

1. En Azure PowerShell, configure primero los parámetros de entrada con los valores específicos como se describe en las secciones anteriores de este tema. El siguiente script es un ejemplo:
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. A continuación, use el siguiente script para configurar y habilitar la integración de AKV.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

La extensión del agente de Iaas de SQL actualizará la máquina virtual de SQL con esta nueva configuración.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

