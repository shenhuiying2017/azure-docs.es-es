---
title: "CLI de Azure: creación y consulta de instancias únicas de SQL Database | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos con la CLI de Azure."
keywords: "tutorial de base de datos SQL, creación de una base de datos SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Creación y consulta de instancias únicas de Azure SQL Database con la CLI de Azure

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se detalla la implementación de una instancia de Azure SQL Database con la CLI de Azure.

Antes de empezar, asegúrese de que se ha instalado la CLI de Azure. Para más información, consulte la [guía de instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>un servidor lógico

Cree un servidor lógico con el comando [az sql server create](/cli/azure/sql/server#create). En el ejemplo siguiente se crea un servidor denominado aleatoriamente en el grupo de recursos con un inicio de sesión de administrador denominado `ServerAdmin` y una contraseña `ChangeYourAdminPassword1`. Cambie estos valores predefinidos por los que prefiera.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Cree una regla de firewall de nivel de servidor con el comando[az sql server create](/cli/azure/sql/server/firewall-rule#create). Una regla de firewall de nivel de servidor permite a una aplicación externa, como SQL Server Management Studio o la utilidad SQLCMD conectarse a una instancia de SQL Database a través del firewall del servicio de SQL Database. En el ejemplo siguiente se crea una regla de firewall para un intervalo de direcciones predefinidas que, en este ejemplo, es todo el intervalo de direcciones IP posible. Cambie estos valores predefinidos por los valores de su dirección IP o intervalo de direcciones IP externas. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Creación de una base de datos en el servidor

Cree una base de datos en el servidor con el comando[az sql db create](/cli/azure/sql/db#create). En el ejemplo siguiente se crea una base de datos en blanco denominada `mySampleDatabase`. Cambie este valor predefinido por el que prefiera.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar todos los recursos creados por este tutorial de inicio rápido, ejecute el siguiente comando:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

- Para conectarse y consultar mediante SQL Server Management Studio, consulte el artículo de [Conexión y consultas con SSMS](sql-database-connect-query-ssms.md).
- Para conectarse con Visual Studio, consulte el artículo de [Conexión y consultas con Visual Studio](sql-database-connect-query.md).
- Para información técnica general de SQL Database, consulte el artículo [Acerca del servicio SQL Database](sql-database-technical-overview.md).

