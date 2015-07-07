<properties
   pageTitle="Implementación de una plantilla mediante la CLI de Azure para Mac, Linux y Windows | Microsoft Azure"
   description="Describe los pasos básicos para implementar o actualizar cualquier plantilla."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Implementación de una plantilla mediante la CLI de Azure para Mac, Linux y Windows

## Instalar curl, wget u otra herramienta de descarga
Este tema usa **curl**. Utilice el Administrador de paquetes del sistema operativo o descárguelo [aquí](http://curl.haxx.se/download.html).

## Descargar el archivo de parámetros de plantilla (o la plantilla si es necesario)

Los pasos siguientes le permitirán implementar una plantilla de Azure, incluso si es una plantilla compleja. En este tema se utiliza la plantilla que crea un servidor de Ubuntu básico con la extensión de la máquina virtual de customscript instalada como ejemplo. Los archivos se incluyen también al final del tema como referencia.

### Descargar el archivo azuredeploy-parameters.json

Descargue el archivo de azuredeploy-parameters.json si existe uno para la plantilla que desea implementar.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json

## Escriba la información de implementación del grupo de recursos.

Abra este archivo con su editor favorito. Verá que necesita especificar un valor para varias de las claves, especialmente **adminUsername**, **adminPassword** (recuerde las reglas de complejidad) y el nombre de la cuenta de almacenamiento y los nombres DNS que desee.

    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-es-es-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }

Agregue nuevos valores (Azure creará un nuevo almacenamiento y recursos de DNS si puede) o utilice los recursos que ya ha creado. El archivo siguiente azuredeploy-parameters.json muestra un ejemplo:




la dirección url siguiente toma el archivo de parámetros de azuredeploy-parameters.json "vacío", que funcionará si utiliza el método interactivo. Si usa un enfoque de archivos de parámetros descargados y personalizados, deberá usar en cambio la opción --template-file <template-file>. También tengo scripts escritos que extraen secciones individuales de cualquier parte de estos archivos, dependiendo de lo que desee hacer. Puede que desee mencionar que para realizar análisis de json prefieren jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq


### Implementación de los archivos de plantilla y parámetros


[AZURE.NOTE]Verá que es posible que algunas plantillas no tienen ningún archivo azuredeploy-parameters.json correspondiente.

Parámetros para establecer, o bien pueden formar ya una parte de la plantilla, dependiendo de las plantillas que esté utilizando. En estos casos, es posible

Si la plantilla contiene sus parámetros directamente o desea extraer los datos de parámetros usted mismo. Para obtener más información acerca de la estructura de plantillas, consulte [Idioma de la plantilla del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json (o simplemente el archivo azuredeploy-parameters.json) O bien puede extraer la sección de parámetros de la plantilla, en cuyo caso deberá guardarla dentro de la propia plantilla o como un archivo independiente azuredeploy-parameters.json. Deberá obtener los valores que se van a colocar en los parámetros.

## Modificación del archivo azuredeploy templates.json

Recopile los valores que necesita.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Vestibul ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis: [Vínculo 3 a otro tema de documentación de azure.microsoft.com](../storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png
 

<!---HONumber=62-->