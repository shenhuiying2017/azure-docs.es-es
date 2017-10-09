---
title: "Conexiones de datos de origen adicionales de ejemplo posibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona un conjunto de ejemplos de conexiones de datos de origen posibles con la preparación de datos de Azure ML."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="sample-of-custom-source-connections-python"></a>Ejemplo de conexiones de origen personalizadas (Python) 
Antes de leer este apéndice, vea la [introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).

## <a name="loading-data-from-dataworld"></a>Cargar datos desde data.world

### <a name="prerequisites"></a>Requisitos previos

#### <a name="register-yourself-at-dataworld"></a>Registrarse en data.world
Se necesita un token de API que se obtiene en el sitio web de data.world.

#### <a name="install-dataworld-library"></a>Instalar la biblioteca de data.world

Abra la interfaz de línea de comandos de Azure Machine Learning Workbench desde _File->Open command-line interface_ (Archivo->Abrir interfaz de línea de comandos).

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Ejecute `dw configure` en la línea de comandos. Se le pedirá el token. Cuando escriba el token, se creará un directorio .dw/ en el directorio particular y el token se almacenará allí.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Ahora ya debería poder importar bibliotecas de data.world.

#### <a name="load-data-into-data-preparation"></a>Cargar datos en la preparación de datos

Cree un flujo de datos basado en script y use el script siguiente para cargar los datos de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>Cargar datos de CosmosDB en la preparación de datos

Cree un flujo de datos basado en script y use el script siguiente para cargar los datos de CosmosDB (primero deberá instalar las bibliotecas, vea el documento de referencia indicado anteriormente).

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

