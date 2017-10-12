---
title: "Creación y publicación de un elemento de Marketplace en Azure Stack | Microsoft Docs"
description: Cree y publique un elemento de Marketplace en Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: erikje
ms.openlocfilehash: 64203ce186665aada98fbe8daed971164a650399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-publish-a-marketplace-item"></a>Creación y publicación de un producto en Marketplace

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Creación de un elemento para Marketplace
1. [Descargue](http://www.aka.ms/azurestackmarketplaceitem) la herramienta Azure Gallery Packager y el elemento de ejemplo de la plataforma Marketplace de Azure Stack.
2. Abra el elemento de Marketplace de ejemplo y cambie el nombre de la carpeta **SimpleVMTemplate**. (Use el mismo nombre que el del elemento de Marketplace, por ejemplo, **Contoso.TodoList**). Esta carpeta contiene:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Cree una plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) o elija una de GitHub. El elemento de Marketplace usa esta plantilla para crear un recurso.
4. Pruebe la plantilla con las API de Microsoft Azure Stack para asegurarse de que el recurso puede implementarse correctamente.
5. Si la plantilla se basa en una imagen de máquina virtual, siga las instrucciones para [agregar una imagen de máquina virtual a Azure Stack](azure-stack-add-vm-image.md).
6. Guarde la plantilla de Azure Resource Manager en la carpeta **/Contoso.TodoList/DeploymentTemplates/**.
7. Elija los iconos y el texto para el elemento de Marketplace. Agregue iconos a la carpeta **Iconos** y agregue texto al archivo **recursos** de la carpeta **Cadenas**. Use la convención de nomenclatura de pequeño, mediano, grande y ancho para los iconos. Consulte la sección [Referencia de UI del elemento de Marketplace](#reference-marketplace-item-ui) para obtener una descripción detallada.
   
   > [!NOTE]
   > Los tamaños de los cuatro iconos (pequeño, mediano, grande y ancho) son necesarios para compilar correctamente el elemento de Marketplace.
   > 
   > 
8. En el archivo **manifest.json**, cambie **name** por el nombre de su elemento de Marketplace. Cambie también **publisher** por su nombre o al de su empresa.
9. En **artifacts**, cambie **name** y **path** por la información correcta para la plantilla de Azure Resource Manager que incluye.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Reemplace **My Marketplace Items** por una lista de las categorías en donde aparecerá su elemento de Marketplace.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Para cualquier modificación adicional a manifest.json, consulte la sección [Referencia: elemento manifest.json de Marketplace](#reference-marketplace-item-manifestjson).
12. Abra un símbolo del sistema y ejecute el siguiente comando para empaquetar las carpetas en un archivo .azpkg:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Debe existir la ruta de acceso completa para el paquete de salida. Por ejemplo, si la ruta de acceso de salida es C:\MarketPlaceItem\yourpackage.azpkg, debe existir la carpeta C:\MarketPlaceItem.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Publicación de un elemento de Marketplace
1. Use PowerShell o el Explorador de Azure Storage para cargar el elemento de Marketplace (.azpkg) a Azure Blob Storage. Puede cargar en el almacenamiento de Azure Stack local o cargar en Azure Storage. (Se trata de una ubicación temporal para el paquete.) Asegúrese de que el blob es accesible públicamente.
2. En la máquina virtual de cliente del entorno de Microsoft Azure Stack, asegúrese de que la sesión de PowerShell está configurada con sus credenciales de administrador de servicios. Puede encontrar instrucciones para la autenticación de PowerShell en Azure Stack en [Deploy a template with PowerShell](user/azure-stack-deploy-template-powershell.md) (Implementar una plantilla con PowerShell).
3. Utilice el cmdlet **Add-AzureRMGalleryItem** de PowerShell para publicar el elemento de Marketplace en Azure Stack. Por ejemplo:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parámetro | Descripción |
   | --- | --- |
   | SubscriptionID |Identificador de suscripción de administrador de Azure. Puede recuperarlo mediante el uso de PowerShell. Si prefiere obtenerlo en el portal, vaya a la suscripción del proveedor y copie el identificador de suscripción. |
   | GalleryItemUri |URI del blob del paquete de la galería que ya se ha cargado en el almacenamiento. |
   | ApiVersion |Establecido como **2015-04-01**. |
4. Vaya al portal. Ahora puede ver el elemento de Marketplace en el portal, como operador o como usuario.
   
   > [!NOTE]
   > El paquete puede tardar varios minutos en aparecer.
   > 
   > 
5. El elemento de Marketplace ahora se ha guardado en la plataforma Marketplace de Azure Stack. Puede elegir eliminarlo de la ubicación de almacenamiento de blobs.
6. Puede quitar un elemento de Marketplace mediante el cmdlet **Remove-AzureRMGalleryItem**. Ejemplo:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > La interfaz de usuario de Marketplace puede mostrar un error después de quitar un elemento. Para solucionarlo, haga clic en **Configuración** en el portal. A continuación, seleccione **Descartar modificaciones** en **Personalización del portal**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Referencia: manifest.json del elemento de Marketplace
### <a name="identity-information"></a>Información de identidad
| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| Nombre |X |String |[A-Za-z0-9]+ | |
| Publicador |X |String |[A-Za-z0-9]+ | |
| Versión |X |String |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadatos
| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |Se recomiendan 80 caracteres |Es posible que el portal no muestre el nombre del elemento correctamente si tiene más de 80 caracteres. |
| PublisherDisplayName |X |String |Se recomiendan 30 caracteres |Es posible que el portal no muestre el nombre del editor correctamente si tiene más de 30 caracteres. |
| PublisherLegalName |X |String |256 caracteres como máximo | |
| Resumen |X |String |entre 60 y 100 caracteres | |
| LongSummary |X |String |entre 140 y 256 caracteres |Aún no se aplica a Azure Stack. |
| Descripción |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |Entre 500 y 5000 caracteres | |

### <a name="images"></a>Imágenes
Marketplace usa los siguientes iconos:

| Nombre | Ancho | Alto | Notas |
| --- | --- | --- | --- |
| Ancho |255 px |115 px |Siempre se requiere |
| Grande |115 px |115 px |Siempre se requiere |
| Mediano |90 px |90 px |Siempre se requiere |
| Pequeña |40 px |40 px |Siempre se requiere |
| Instantánea |533 px |32 px |Opcional |

### <a name="categories"></a>Categorías
Todos los elementos de Marketplace deben etiquetarse con una categoría que identifique el lugar donde el elemento aparece en la interfaz de usuario del portal. Puede elegir una de las categorías existentes de Azure Stack (Compute, Data + Storage, etc.) o elegir una nueva.

### <a name="links"></a>Vínculos
Cada elemento de Marketplace puede incluir varios vínculos a contenido adicional. Los vínculos se especifican en forma de lista de nombres e identificadores URI.

| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |64 caracteres como máximo | |
| Identificador URI |X |URI | | |

### <a name="additional-properties"></a>Propiedades adicionales
Además de los metadatos anteriores, los creadores de Marketplace pueden proporcionar datos con el par clave-valor personalizado de la forma siguiente:

| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |25 caracteres como máximo | |
| Valor |X |String |30 caracteres como máximo | |

### <a name="html-sanitization"></a>Comprobación del estado de HTML
Todos los campos que permitan HTML pueden tener los siguientes elementos y atributos:

h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i

## <a name="reference-marketplace-item-ui"></a>Referencia: UI del elemento de Marketplace
Los iconos y el texto de los elementos de Marketplace, tal como se muestran en el portal de Azure Stack son los siguientes.

### <a name="create-blade"></a>Hoja Creación
![Hoja Creación](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Hoja de detalles de elemento de Marketplace
![Hoja de detalles de elemento de Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

