---
title: "Habilitar SSL en un clúster de Azure Machine Learning Compute (MLC) | Microsoft Docs"
description: "Obtener instrucciones para configurar SSL y realizar llamadas de puntuación en un clúster de Azure Machine Learning Compute (MLC)"
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: b76fe7c0caa4a9aca76a9a3f50d1fced0ab67cba
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Habilitar SSL en un clúster de Azure Machine Learning Compute (MLC) 

Estas instrucciones le permitirán configurar SSL para realizar llamadas de puntuación en un clúster de Machine Learning Compute (MLC). 

## <a name="prerequisites"></a>requisitos previos 

1. Decida el valor CNAME (nombre DNS) que se usará al realizar llamadas de puntuación en tiempo real.

2. Cree un certificado *sin contraseña* con ese valor de CNAME.

3. Si el certificado no está en formato PEM, puede convertirlo mediante herramientas como *openssl*.

Después de completar los requisitos previos, tendrá dos archivos:

* Un archivo para el certificado (por ejemplo, `cert.pem`).
* Un archivo para la clave (por ejemplo, `key.pem`).



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Configurar un certificado SSL en un nuevo clúster de ACS

Mediante la CLI de Azure Machine Learning, ejecute el siguiente comando con el modificador `-c` para crear un clúster de ACS que tenga un certificado SSL adjunto:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Configurar un certificado SSL en un clúster de ACS ya existente

Si se dirige a un clúster que se creó sin SSL, puede agregar un certificado mediante los cmdlets de Azure PowerShell: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Asignar el valor CNAME y la dirección IP

Cree una asignación entre el valor CNAME que seleccionó en los requisitos previos y la dirección IP del front-end (FE) en tiempo real. Para conocer la dirección IP del FE, ejecute el siguiente comando. La salida muestra un campo denominado "publicIpAddress", que contiene la dirección IP del front-end del clúster en tiempo real. Consulte las instrucciones de su proveedor de DNS para configurar un registro CNAME.



## <a name="auto-detection-of-a-certificate"></a>Detectar automáticamente un certificado 

Cuando se crea un servicio web en tiempo real mediante el comando "`az ml service create realtime`", Azure Machine Learning detecta automáticamente la SSL configurada en el clúster y configura automáticamente la dirección URL de puntuación con el certificado designado. 

Ejecute el siguiente comando para ver el estado del certificado. Tenga en cuenta el prefijo "https" del URI de puntuación y el valor CNAME en el nombre de host. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
