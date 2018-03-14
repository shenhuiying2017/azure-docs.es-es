---
title: Uso de la CLI de Databricks desde Azure Cloud Shell | Microsoft Docs
description: Aprenda a usar la CLI de Databricks desde Azure Cloud Shell.
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 212789e8189abf362bb8eaf12e4c551b113b7adb
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Uso de la CLI de Databricks desde Azure Cloud Shell

Aprenda a usar la CLI Databricks desde Azure Cloud Shell para realizar operaciones en Databricks.

## <a name="prerequisites"></a>Requisitos previos

* Un área de trabajo y un clúster de Azure Databricks. Para obtener instrucciones, consulte [Introducción a Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configure un token de acceso personal en Databricks. Para obtener instrucciones, consulte [Administración de tokens](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Uso de Azure Cloud Shell

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
 
2. En la esquina superior derecha, haga clic en el icono **Cloud Shell**.

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Iniciar ODBC desde Excel")

3. Asegúrese de selecciona **Bash** como entorno de Cloud Shell. Puede hacerlo en la lista desplegable, como se muestra en la captura de pantalla siguiente.

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Iniciar ODBC desde Excel") 

4. Cree un entorno virtual en el que pueda instalar la CLI de Databtricks. En el siguiente fragmento de código, se crea un entorno virtual llamado `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Cambie al entorno virtual que ha creado.

       source databrickscli/bin/activate

6. Instale la CLI de Databricks.

       pip install databricks-cli

7. Configure la autenticación con Databricks mediante el token de acceso que debe haber creado, que aparece como parte de los requisitos previos. Use el comando siguiente:

       databricks configure --token

    Recibirá las siguientes solicitudes:

    * Se le solicita que acceda al host de Databricks. Escriba el valor con el formato `https://eastus2.azuredatabricks.net`. En este caso, **Este de EE. UU. 2** es la región de Azure en la que creó el área de trabajo de Azure Databricks.

    * Se le solicita que escriba un nombre de usuario. Escriba **token**.

    * Por último, se le pide que escriba la contraseña. Escriba el token que creó anteriormente.

Una vez completados estos pasos, puede empezar a usar la CLI de Databricks desde Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Uso de la CLI de Databricks

Ya puede empezar a usar la CLI de Databricks. Por ejemplo, ejecute el siguiente comando para enumerar todos los clústeres de Databricks que tenga en el área de trabajo.

    databricks clusters list

También puede usar el siguiente comando para acceder el sistema de archivos de Databricks (DBFS).

    databricks fs ls


Para obtener una referencia completa de los comandos, consulte [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (CLI de Databricks).


## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de la CLI de Azure, consulte [Introducción a Azure Cloud Shell](../cloud-shell/overview.md)
* Para ver una lista de los comandos de la CLI de Azure, consulte la [referencia de la CLI de Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)
* Para ver una lista de comandos de la CLI de Databricks, consulte [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (CLI de Databricks)


