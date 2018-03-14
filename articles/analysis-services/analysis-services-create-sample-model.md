---
title: "Incorporación de un modelo tabular de ejemplo para el servidor de Azure Analysis Services | Microsoft Docs"
description: "Obtenga información sobre cómo agregar un modelo de ejemplo en Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/28/2018
ms.author: owend
ms.openlocfilehash: df83f5dd86d1edf857378ae69b16a86b57f9a2fe
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-add-a-sample-model"></a>Tutorial: Incorporación de un modelo de ejemplo

En este tutorial, agregará un modelo de ejemplo de Adventure Works al servidor. El modelo de ejemplo es una versión completa del tutorial de modelado de datos de Adventure Works Internet Sales (1200). Un modelo de ejemplo es útil para probar la administración de modelos, la conexión con las herramientas y las aplicaciones cliente, y para consultar datos de modelos.

## <a name="before-you-begin"></a>Antes de empezar

Para completar este tutorial, necesita:

- Un servidor de Azure Analysis Services.
- Permisos de administrador del servidor

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="create-a-sample-model"></a>Creación de un modelo de ejemplo

1. En **Información general** del servidor, haga clic en **Nuevo modelo**.

    ![Creación de un modelo de ejemplo](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. En **Nuevo modelo** > **Choose a datasource (Elegir un origen de datos)**, compruebe que la opción **Datos de ejemplo** está seleccionada y, a continuación, haga clic en **Agregar**.

    ![Seleccionar datos de ejemplo](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. En **Información general**, compruebe que se crea el ejemplo `adventureworks`.

    ![Seleccionar datos de ejemplo](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

El modelo de ejemplo usa los recursos de memoria caché. Si no va a usar el modelo de ejemplo para realizar pruebas, debe quitarlo del servidor.

> [!NOTE]
> Estos pasos describen cómo eliminar un modelo de un servidor mediante el uso de SSMS. También puede eliminar un modelo mediante la versión preliminar de la característica Diseñador web.

1. En SSMS > **Explorador de objetos**, haga clic en **Conectar** > **Analysis Services**.

2. En **Conectar con el servidor**, pegue el nombre del servidor y, a continuación, en **Autenticación**, elija **Active Directory - Universal compatible con MFA**, escriba el nombre de usuario y, a continuación, haga clic en **Conectar**.

    ![Iniciar sesión](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. En **Explorador de objetos**, haga clic con el botón derecho en la base de datos de ejemplo `adventureworks` y, después, haga clic en **Eliminar**.

    ![Eliminar la base de datos de ejemplo](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Pasos siguientes 

[Conexión en Power BI Desktop](analysis-services-connect-pbi.md)   
[Administración de usuarios y roles de base de datos](analysis-services-database-users.md)


