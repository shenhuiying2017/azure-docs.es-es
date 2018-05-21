---
title: Inicio rápido para crear una aplicación Python que usa Azure Redis Cache | Microsoft Docs
description: En este tutorial, aprenderá a crear una aplicación Python que usa Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/11/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: b66df55043e4fd29f352c6e9ec3b8674800bc4be
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-use-azure-redis-cache-with-python"></a>Inicio rápido: Uso de Azure Redis Cache con Python
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

## <a name="introduction"></a>Introducción

En este tutorial se explica cómo conectarse a Azure Redis Cache con Python para leer una caché y escribir en ella. 

![Prueba completada con Python](./media/cache-python-get-started/cache-python-completed.png)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

* [Entorno de Python 2 o Python 3 ](https://www.python.org/downloads/) instalado con [pip](https://pypi.org/project/pip/). 

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché de Redis en Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalación de redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) es una interfaz de Python para Redis Cache. Use la herramienta de paquetes de Python, *pip*, para instalar el paquete redis-py. 

En el ejemplo siguiente se utiliza *pip3* para que Python 3 instale el paquete redis-py en Windows 10 mediante un símbolo del sistema para desarrolladores de Visual Studio 2017 con privilegios elevados de administrador.

    pip3 install redis

![Instalación de redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Lectura y escritura en la memoria caché

Ejecute Python y pruebe el uso de la memoria caché desde la línea de comandos. Reemplace `<Your Host Name>` y `<Your Access Key>` con los valores de Redis Cache. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

## <a name="create-a-python-script"></a>Creación de un script de Python

Cree un archivo de texto de script denominado *PythonApplication1.py*.

Agregue el siguiente script a *PythonApplication1.py* y guarde el archivo. Este script probará el acceso a la caché. Reemplace `<Your Host Name>` y `<Your Access Key>` con los valores de Redis Cache. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,password=myPassword,ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ") 
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Ejecute el script con Python.

![Prueba completada con Python](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a seguir con otro tutorial, puede mantener los recursos creados en esta guía de inicio rápido y volverlos a utilizar.

En caso contrario, si ya ha terminado con la aplicación de ejemplo de la guía de inicio rápido, puede eliminar los recursos de Azure creados en este tutorial para evitar cargos. 

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible y el grupo de recursos y todos los recursos que contiene se eliminarán de forma permanente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado los recursos para hospedar este ejemplo dentro de un grupo de recursos existente que contiene recursos que desea mantener, puede eliminar cada recurso individualmente de sus hojas respectivas, en lugar de eliminar el grupo de recursos.
>

Inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Grupos de recursos**.

Escriba el nombre del grupo de recursos en el cuadro de texto **Filtrar por nombre...**. En las instrucciones de este artículo se usa un grupo de recursos llamado *TestResources*. En el grupo de recursos de la lista de resultados, haga clic en **...** y, a continuación, en **Eliminar grupo de recursos**.

![Eliminar](./media/cache-web-app-howto/cache-delete-resource-group.png)

Se le pedirá que confirme la eliminación del grupo de recursos. Escriba el nombre del grupo de recursos para confirmar y haga clic en **Eliminar**.

Transcurridos unos instantes, el grupo de recursos y todos los recursos que contiene se eliminan.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de una aplicación web ASP.NET sencilla que use Azure Redis Cache](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
