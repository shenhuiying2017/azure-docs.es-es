---
title: "Habilitación de la CLI de Azure para usuarios de Azure Stack | Microsoft Docs"
description: "Obtenga información sobre cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: d184bb9edbe2542d7321d8b9ccc5d23f2401f8d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Habilitación de la CLI de Azure para usuarios de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

No hay ninguna tarea específica del operador de Azure Stack que se pueda realizar con CLI. Pero antes de que los usuarios puedan administrar recursos a través de CLI, los operadores de Azure Stack deben proporcionarles lo siguiente:

* **El certificado raíz de CA de Azure Stack** es necesario si los usuarios usan la CLI desde una estación de trabajo fuera de Azure Stack Development Kit.  

* **El punto de conexión de los alias de máquinas virtuales** proporciona un alias, como "UbuntuLTS" o "Win2012Datacenter", que hace referencia a un publicador de la imagen, una oferta, una SKU y una versión como un parámetro único al implementar máquinas virtuales.  

En las secciones siguientes se explica cómo obtener estos valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportación del certificado raíz de CA de Azure Stack

El certificado raíz de CA de Azure Stack está disponible en el kit de desarrollo y en una máquina virtual del inquilino que se ejecuta en el entorno del kit de desarrollo. Inicie sesión en el kit de desarrollo o la máquina virtual del inquilino y ejecute el script siguiente para exportar el certificado raíz de Azure Stack en formato PEM:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Cerficate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Los operadores de Azure Stack deben configurar un punto de conexión accesible públicamente que hospede un archivo de alias de máquinas virtuales,  que es un archivo JSON que proporciona un nombre común para una imagen, que posteriormente se especifica al implementar una máquina virtual un parámetro de la CLI de Azure.  

Antes de agregar una entrada a un archivo de alias, asegúrese de que [descarga las imágenes de Marketplace]((azure-stack-download-azure-marketplace-item.md) o que ha [descargado su propia imagen personalizada](azure-stack-add-vm-image.md).  Si publica una imagen personalizada, anote la información del publicador, oferta, SKU y versión que especificó en la publicación.  Si es una imagen de Marketplace, la información se puede ver mediante el cmdlet ```Get-AzureVMImage```.  
   
Hay disponible un [archivo de alias de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) con muchos alias de imágenes comunes que se puede usar como punto de partida.  Dicho archivo se debe hospedar en un espacio al que puedan acceder los clientes de la CLI.  Una forma de hacerlo es hospedarlo en una cuenta de almacenamiento de blobs y compartir la dirección URL con los usuarios:

1.  Descargue el [archivo de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) de GitHub.
2.  Cree una cuenta de almacenamiento nueva en Azure Stack.  Cuando finalice, cree un nuevo contenedor de blobs.  Establezca la directiva de acceso en "público".  
3.  Cargue el archivo JSON en el nuevo contenedor.  Una vez que haya finalizado, para ver la dirección URL del blob haga clic en el nombre del blob y, después, seleccione la dirección URL en las propiedades del blob.


## <a name="next-steps"></a>Pasos siguientes

[Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)

[Conexión con PowerShell](azure-stack-connect-powershell.md)

[Administración de permisos de usuario](azure-stack-manage-permissions.md)

