---
title: "Guía de inicio rápido: Envío de un flujo de trabajo con varias entradas | Microsoft Docs"
titleSuffix: Azure
description: "En la guía de inicio rápido se da por supuesto que tiene instalado el cliente de msgen y que ha ejecutado correctamente los datos de ejemplo mediante el servicio."
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: d410516f807b7914e15bed1fb93ee58d3e340d1e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Envío de un flujo de trabajo con varias entradas del mismo ejemplo

Esta guía de inicio rápido demuestra cómo enviar un flujo de trabajo al servicio de Microsoft Genomics, si la entrada son varios archivos FASTQ o BAM **procedentes del mismo ejemplo**. Tenga en cuenta, sin embargo, que **no puede** mezclar archivos FASTQ y BAM en el mismo envío.

En este tema se da por supuesto que ya ha instalado y ejecutado el cliente `msgen` y está familiarizado con el uso de Azure Storage. Si ha enviado correctamente un flujo de trabajo usando los datos de ejemplo proporcionados, puede continuar con esta guía de inicio rápido. 


## <a name="multiple-bam-files"></a>Varios archivos BAM

### <a name="upload-your-input-files-to-azure-storage"></a>Carga de los archivos de entrada en Azure Storage
Supongamos que tiene varios archivos BAM como entrada, *reads.bam*, *additional_reads.bam* y *yet_more_reads.bam*, y los ha cargado en su cuenta de almacenamiento *myaccount* de Azure. Tiene la dirección URL de la API y la clave de acceso. Desea los resultados en **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Envío del trabajo al cliente `msgen` 

Puede enviar varios archivos BAM pasando todos sus nombres al argumento --input-blob-name-1. Tenga en cuenta que todos los archivos deben provenir del mismo ejemplo, pero su orden no es importante. A continuación se muestran envíos de ejemplo desde una línea de comandos en Windows, en Unix y con un archivo de configuración. Para mayor claridad, se agregan saltos de línea:


Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Para Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Si prefiere usar un archivo de configuración, esto es lo que podría contener:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envíe el archivo `config.txt` con esta invocación: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Varios archivos FASTQ emparejados

### <a name="upload-your-input-files-to-azure-storage"></a>Carga de los archivos de entrada en Azure Storage
Supongamos que tiene varios archivos FASTQ emparejados como entrada, *reads_1.fq.gz* y *reads_2.fq.gz*, *additional_reads_1.fq.gz* y *additional_reads_2.fq.gz*, y *yet_more_reads_1.fq.gz* y *yet_more_reads_2.fq.gz*. Los ha cargado en su cuenta de almacenamiento *myaccount* de Azure y tiene la dirección URL de la API y la clave de acceso. Desea los resultados en **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Envío del trabajo al cliente `msgen` 

Los archivos FASTQ emparejados no solo deben proceder del mismo ejemplo, sino que también deben procesarse de forma conjunta.  Cuando los nombres de archivo se pasan como argumentos --input-blob-name-1 e --input-blob-name-2, su orden es importante. 

A continuación se muestran envíos de ejemplo desde una línea de comandos en Windows, en Unix y con un archivo de configuración. Para mayor claridad, se agregan saltos de línea:


Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Para Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Si prefiere usar un archivo de configuración, esto es lo que podría contener:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envíe el archivo `config.txt` con esta invocación: `msgen submit -f config.txt`

## <a name="next-steps"></a>Pasos siguientes
En este artículo, se cargan varios archivos BAM o FASTQ emparejados en Azure Storage y se envía un flujo de trabajo al servicio Microsoft Genomics mediante el cliente de Python `msgen`. Para obtener información adicional sobre el envío del flujo de trabajo y otros comandos que puede usar con el servicio Microsoft Genomics, vea nuestras [preguntas más frecuentes](frequently-asked-questions-genomics.md). 