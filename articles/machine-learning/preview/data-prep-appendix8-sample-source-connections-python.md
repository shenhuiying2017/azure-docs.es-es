---
title: "Ejemplos de conexiones de datos de origen adicionales posibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona un conjunto de ejemplos de conexiones de datos de origen que son posibles con la preparación de datos de Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 3ffe0e385f9dd71d8341305f42ceb10e0ea49af4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Ejemplo de conexiones de origen personalizadas (Python) 
Antes de leer este apéndice, vea la [Introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Carga de datos desde data.world

### <a name="prerequisites"></a>requisitos previos

#### <a name="register-yourself-at-dataworld"></a>Registrarse en data.world
Necesita un token de la API desde el sitio web data.world.

#### <a name="install-dataworld-library"></a>Instalar la biblioteca de data.world

Abra la interfaz de línea de comandos de Azure Machine Learning Workbench seleccionando **File** > **Open command-line interface** (Archivo->Abrir interfaz de línea de comandos).

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

A continuación ejecute `dw configure` en la línea de comandos. Se le pedirá el token. Cuando escriba el token, se creará un directorio .dw/ en el directorio particular y el token se almacenará allí.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Ahora ya debería poder importar bibliotecas de data.world.

#### <a name="load-data-into-data-preparation"></a>Cargar datos en la preparación de datos

Cree un nuevo flujo de datos basados en script. A continuación, use el siguiente script para cargar los datos de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Carga de los datos de Azure Cosmos DB en la preparación de datos

Cree un flujo de datos basado en script y use el script siguiente para cargar los datos de Azure Cosmos DB. (Las bibliotecas tienen que instalarse primero. Para más información, consulte el documento de referencia con vínculo anterior).

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

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
