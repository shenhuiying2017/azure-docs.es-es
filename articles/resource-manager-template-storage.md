<properties
   pageTitle="Plantilla del Administrador de recursos para almacenamiento | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para las cuentas de almacenamiento."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2015"
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

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sí | **Microsoft.Storage/storageAccounts** | El tipo de recurso que se creará. |
| apiVersion | enum | Sí | **2015-06-15** <br /> **2015-05-01-preview** | La versión de la API que se usará para crear el recurso. | 
| name | cadena | Sí | Entre 3 y 24 caracteres, solo números y letras minúsculas | El nombre de la cuenta de almacenamiento que se creará. El nombre debe ser único en todo Azure. Considere usar la función [uniqueString](resource-group-template-functions.md#uniquestring) junto con su convención de nomenclatura, tal como se indica en el ejemplo que aparece a continuación. |
| location | cadena | Sí | Para determinar las regiones válidas, consulte [Regiones admitidas](resource-manager-supported-services.md#supported-regions). | La región donde se hospedará la cuenta de almacenamiento. |
| propiedades | objeto | Sí | (se muestra a continuación) | Un objeto que especifica el tipo de cuenta de almacenamiento que se creará.

### properties object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | cadena | Sí | **Standard\_LRS**<br />**Standard\_ZRS**<br />**Standard\_GRS**<br />**Standard\_RAGRS**<br />**Premium\_LRS** | El tipo de cuenta de almacenamiento. Los valores permitidos corresponden a Almacenamiento con redundancia local estándar, Almacenamiento con redundancia de zona estándar, Almacenamiento con redundancia geográfica estándar, Almacenamiento con redundancia geográfica con acceso de lectura estándar y Redundancia local premium. Para obtener información sobre estos tipos de cuenta, consulte [Replicación de almacenamiento de Azure](./storage/storage-redundancy.md). |

	
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

## Pasos siguientes

- Para obtener información general sobre el almacenamiento, consulte [Introducción a Almacenamiento de Microsoft Azure](./storage/storage-introduction.md).
- Si desea obtener ejemplos de plantillas que usan una cuenta de almacenamiento nueva con una máquina virtual, consulte [Implementación de una máquina virtual simple de Linux](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) o [Implementación de una máquina virtual simple de Windows](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=Nov15_HO1-->