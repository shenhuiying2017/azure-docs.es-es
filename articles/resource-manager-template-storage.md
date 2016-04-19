<properties
   pageTitle="Plantilla del Administrador de recursos para almacenamiento | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para implementar las cuentas de almacenamiento mediante una plantilla."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Cuenta de almacenamiento: esquema de plantilla

Crea una cuenta de almacenamiento.

## Formato de esquema

Para crear una cuenta de almacenamiento, agregue el siguiente esquema a la sección de recursos de la plantilla.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
        	"accountType": string
        }
    }

## Valores

Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Valor |
| ---- | ---- |
| type | Enum<br />Obligatorio<br />**Microsoft.Storage/storageAccounts**<br /><br />Tipo de recurso que se creará. |
| apiVersion | Enum<br />Obligatorio<br />**2015-06-15** o **2015-05-01-preview**<br /><br />Versión de la API que se usará para crear el recurso. | 
| name | Cadena<br />Obligatorio<br />Entre 3 y 24 caracteres; solo números y letras minúsculas.<br /><br />Nombre de la cuenta de almacenamiento que se creará. El nombre debe ser único en todo Azure. Considere usar la función [uniqueString](resource-group-template-functions.md#uniquestring) junto con su convención de nomenclatura, tal como se indica en el ejemplo que aparece a continuación. |
| location | Cadena<br />Obligatorio<br />Región compatible con cuentas de almacenamiento. Para determinar las regiones válidas, consulte las [regiones admitidas](resource-manager-supported-services.md#supported-regions).<br /><br />Región para hospedar la cuenta de almacenamiento. |
| propiedades | Objeto<br />Obligatorio<br />[Objeto de propiedades](#properties)<br /><br />Objeto que especifica el tipo de cuenta de almacenamiento que se creará. |

<a id="properties" />
### properties object

| Nombre | Valor |
| ---- | ---- | 
| accountType | Cadena<br />Obligatorio<br />**Standard\_LRS**, **Standard\_ZRS**, **Standard\_GRS**, **Standard\_RAGRS** o **Premium\_LRS**<br /><br />Tipo de cuenta de almacenamiento. Los valores permitidos corresponden a Almacenamiento con redundancia local estándar, Almacenamiento con redundancia de zona estándar, Almacenamiento con redundancia geográfica estándar, Almacenamiento con redundancia geográfica con acceso de lectura estándar y Redundancia local premium. Para obtener información sobre estos tipos de cuenta, consulte [Replicación de almacenamiento de Azure](./storage/storage-redundancy.md). |

	
## Ejemplos

En el ejemplo siguiente, se implementa una cuenta de almacenamiento con redundancia local estándar con un nombre único basado en el Id. del grupo de recursos

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
                "location": "[resourceGroup().location]",
                "properties": 
        	{
                    "accountType": "Standard_LRS"
        	}
            }
        ],
        "outputs": {}
    }

## Plantillas de inicio rápido

Hay muchas plantillas de inicio rápido que incluyen una cuenta de almacenamiento. Las plantillas siguientes muestran algunos escenarios comunes:

- [Create a Standard Storage Account (Crear una cuenta de almacenamiento estándar)](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Simple deployment of an Windows VM](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Simple deployment of an Linux VM](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [Create a CDN Profile, a CDN Endpoint with a Storage Account as origin (Crear un perfil de CDN, un punto de conexión de CDN con una cuenta de almacenamiento como origen)](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [Create a High Availabilty SharePoint Farm with 9 VMs using the Powershell DSC Extension](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [Simple deployment of a 5 Node secure Service Fabric Cluster with WAD enabled](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [Create a Virtual Machine from a Windows Image with 4 Empty Data Disks (Crear una máquina virtual a partir de una imagen de Windows con cuatro discos de datos vacíos)](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## Pasos siguientes

- Para obtener información general sobre el almacenamiento, consulte [Introducción a Almacenamiento de Microsoft Azure](./storage/storage-introduction.md).
- Si desea obtener ejemplos de plantillas que usan una cuenta de almacenamiento nueva con una máquina virtual, consulte [Implementación de una máquina virtual simple de Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) o [Implementación de una máquina virtual simple de Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=AcomDC_0406_2016-->