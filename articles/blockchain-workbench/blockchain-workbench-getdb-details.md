---
title: Obtener los detalles de la base de datos de Azure Blockchain Workbench
description: Vea cómo obtener información de la base de datos de Azure Blockchain Workbench y el servidor de base de datos.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 63b718bcb8722c5fd501891d162eadfae9fb8ec2
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074895"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Obtener información sobre la base de datos de Azure Blockchain Workbench

En este artículo se muestra cómo obtener información detallada sobre la base de datos de Azure Blockchain Workbench.

## <a name="overview"></a>Información general

La información sobre las aplicaciones, los flujos de trabajo y la ejecución de contrato inteligente se proporciona mediante vistas de base de datos en la base de datos de SQL de Blockchain Workbench. Los desarrolladores pueden usar esta información cuando usan herramientas como, por ejemplo, Microsoft Excel, Power BI, Visual Studio y SQL Server Management Studio.

Antes de que un desarrollador pueda conectarse a la base de datos, necesita lo siguiente:

* Acceso de cliente externo permitido en el firewall de base de datos. En este artículo sobre la configuración de un artículo de firewall de base de datos se explica cómo permitir el acceso.
* El nombre del servidor de base de datos y de la base de datos.

## <a name="connect-to-the-blockchain-workbench-database"></a>Conectarse a la base de datos de Blockchain Workbench

Para conectarse a la base de datos:

1. Inicie sesión en Azure Portal con una cuenta que tenga permisos de **Propietario** para los recursos de Azure Blockchain Workbench.
2. En el panel de la izquierda, seleccione **Grupos de recursos**.
3. Elija el nombre del grupo de recursos para la implementación de Blockchain Workbench.
4. Seleccione **Tipo** para ordenar la lista de recursos y luego elija el servidor **SQL server**. En la lista ordenada en la captura de pantalla siguiente se muestran dos bases de datos SQL, "master" y otra que usa "lhgn" como el **prefijo de recurso**.

   ![Lista de recursos de Blockchain Workbench ordenados](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Para ver información detallada sobre la base de datos de Blockchain Workbench, seleccione el vínculo de la base de datos con el **prefijo de recurso** que proporcionó para la implementación de Blockchain Workbench.

   ![Detalles de la base de datos](media/blockchain-workbench-getdb-details/workbench-db-details.png)

El nombre del servidor de base de datos y el nombre de la base de datos permiten conectarse a la base de datos de Blockchain Workbench mediante la herramienta de desarrollo o generación de informes.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Vistas de base de datos de Azure Blockchain Workbench](blockchain-workbench-database-views.md)