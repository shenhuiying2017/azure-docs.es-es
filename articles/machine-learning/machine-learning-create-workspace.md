---
title: "Creación y uso compartido de un área de trabajo de Aprendizaje automático de Azure | Microsoft Docs"
description: "Creación de un área de trabajo para Estudio de aprendizaje automático de Azure"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: garye;bradsev;ahgyger
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 647398c1a0994da04845672e89d9f82d5e8b6d21


---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Creación y uso compartido de un área de trabajo de Aprendizaje automático de Azure
Este menú vincula a temas en los que se describe cómo configurar los diversos entornos de ciencia de datos usados por el proceso de Cortana Analytics (CAPS).

[!INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Para usar Estudio de aprendizaje automático de Azure, debe tener un área de trabajo de Aprendizaje automático. Esta área de trabajo contiene las herramientas que necesita para crear, administrar y publicar experimentos.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="to-create-a-workspace"></a>Para crear un área de trabajo
1. Inicie sesión en el [Portal de Microsoft Azure clásico].

> [!NOTE]
> Solo los administradores de suscripciones de Azure pueden iniciar sesión. El mero hecho de ser propietario de un área de trabajo de Aprendizaje automático no proporciona acceso al [Portal de Microsoft Azure clásico]. Para obtener más información, consulte [Privilegios de administradores de suscripciones de Azure y de propietarios de áreas de trabajo](#subscriptionvsworkspace) .
> 
> 

1. En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.
   
    ![Servicio de Aprendizaje automático][1]
2. Haga clic en **+NUEVO** en la parte inferior de la página.
3. Haga clic en **SERVICIOS DE DATOS**, en **APRENDIZAJE AUTOMÁTICO** y, después, en **CREACIÓN RÁPIDA**.
   
    ![Creación rápida de una nueva área de trabajo][3]
4. Escriba un **NOMBRE DE ÁREA DE TRABAJO** para el área de trabajo.
5. Especifique la **UBICACIÓN** de Azure y, después, escriba una **CUENTA DE AZURE STORAGE** existente o seleccione **Crear una cuenta de almacenamiento** para crear una cuenta.
6. Haga clic en **CREAR UN ÁREA DE TRABAJO DE ML**.

Después de crear el área de trabajo del Aprendizaje automático, verá que aparece en la página **Aprendizaje automático** .

## <a name="sharing-an-azure-machine-learning-workspace"></a>Uso compartido de un área de trabajo de Aprendizaje automático de Azure
Tras crear un área de trabajo de Aprendizaje automático, puede invitar usuarios a ella, así como compartir el acceso a dicha área y todos sus experimentos. Se admiten dos roles de usuarios:

* **Usuario** : los usuarios de un área de trabajo pueden crear, abrir, modificar y eliminar conjuntos de datos, experimentos y servicios web en el área de trabajo.
* **Propietario** : además de lo que pueden hacer los usuarios, los propietarios pueden invitar, quitar y enumerar usuarios con acceso al área de trabajo. También tienen acceso a varios Notebook.

### <a name="to-share-a-workspace"></a>Para compartir un área de trabajo
1. Inicie sesión en [Estudio de aprendizaje automático]
2. En el panel de Estudio de aprendizaje automático, haga clic en **SETTINGS**
3. Haga clic en **USERS**
4. Haga clic en **INVITE MORE USERS**
   
    ![INVITE MORE USERS][4]
5. Escriba una o varias direcciones de correo electrónico. El usuario solo necesita una cuenta Microsoft (por ejemplo, name@outlook.com)) o una cuenta de organización (de Azure Active Directory) válidas.
6. A continuación, haga clic en el botón Comprobar.

Todos los usuarios que haya agregado recibirán un correo electrónico con instrucciones para iniciar sesión en el área de trabajo compartida.

Para obtener más información sobre cómo administrar un área de trabajo, consulte [Administración de un área de trabajo de Aprendizaje automático de Azure].
Si encuentra un problema al crear el área de trabajo, vea [Guía de solución de problemas: creación y conexión a un área de trabajo de Aprendizaje automático].

## <a name="a-namesubscriptionvsworkspaceaprivileges-of-azure-subscription-administrator-and-of-workspace-owner"></a><a name="subscriptionvsworkspace"></a>Privilegios de administradores de suscripciones de Azure y de propietarios de áreas de trabajo
A continuación se muestra una tabla que clarifica la diferencia entre un administrador de suscripciones y el propietario de un área de trabajo de Azure.

| Acciones | Administrador de suscripciones de Azure | Propietario del espacio de trabajo |
| --- |:---:|:---:|
| Acceder al [Portal de Microsoft Azure clásico] |Sí |No |
| Crear un área de trabajo |Sí |No |
| Eliminar un área de trabajo |Sí |No |
| Agregar un punto de conexión a un servicio web |Sí |No |
| Eliminar un punto de conexión de un servicio web |Sí |No |
| Cambiar la simultaneidad de un servicio web |Sí |No |
| Acceder a [Estudio de aprendizaje automático] |No * |Sí |

> [!NOTE]
> * Se agregará automáticamente un administrador de suscripciones de Azure al área de trabajo que se cree como propietario del área de trabajo. Sin embargo, el mero hecho de ser administrador de suscripciones de Azure no concede acceso a ningún área de trabajo de esa suscripción.
> 
> 

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[Para crear un área de trabajo]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[Administración de un área de trabajo de Aprendizaje automático de Azure]: machine-learning-manage-workspace.md
[Guía de solución de problemas: creación y conexión a un área de trabajo de Aprendizaje automático]: machine-learning-troubleshooting-creating-ml-workspace.md
[Estudio de aprendizaje automático]: https://studio.azureml.net/  
[Portal de Microsoft Azure clásico]: https://manage.windowsazure.com/



<!--HONumber=Nov16_HO3-->


