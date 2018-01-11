---
title: "Creación de un área de trabajo de Azure Machine Learning | Microsoft Docs"
description: "Creación de un área de trabajo para Azure Machine Learning Studio"
services: machine-learning
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.author: garye
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview
ms.openlocfilehash: 7aad885b1643ddeb25e5d00a151005b8444315fa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Creación y uso compartido de un área de trabajo de Azure Machine Learning
Este menú vincula a temas en los que se describe cómo configurar los diversos entornos de ciencia de datos usados por el proceso de Cortana Analytics (CAPS).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Para usar Azure Machine Learning Studio, debe tener un área de trabajo de Machine Learning. Esta área de trabajo contiene las herramientas que necesita para crear, administrar y publicar experimentos.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Para crear un área de trabajo
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/)

    > [!NOTE]
    > Para crear un área de trabajo e iniciar sesión en ella, debe ser un administrador de suscripciones de Azure. 
    >
    > 

2. Haga clic en **+ Nuevo**.

3. En el cuadro de búsqueda, escriba **Área de trabajo de Machine Learning Studio** y seleccione el elemento coincidente. A continuación, en la parte inferior de la página, haga clic en **Crear**.

4. Escriba la información de su área de trabajo:

    - El *nombre de área de trabajo* puede tener hasta 260 caracteres, pero no puede terminar con un espacio. El nombre no puede incluir estos caracteres: `< > * % & : \ ? + /`
    - El *plan de servicio web* elegido (o creado), junto con el *plan de tarifa* asociado seleccionado, se usa si implementa servicios web desde esta área de trabajo.

    ![Crear un área de trabajo](./media/create-workspace/create-new-workspace.png)

5. Haga clic en **Crear**.

Una vez implementado el área de trabajo, puede abrirlo en Machine Learning Studio.

1. Examine Machine Learning Studio en [https://studio.azureml.net/](https://studio.azureml.net/).

2. Seleccione el área de trabajo en la esquina superior derecha.

    ![Selección del área de trabajo](./media/create-workspace/open-workspace.png)

3. Haga clic en **my experiments** (Mis experimentos).

    ![Abrir experimentos](./media/create-workspace/my-experiments.png)

Para obtener más información sobre cómo administrar un área de trabajo, consulte [Administración de un área de trabajo de Azure Machine Learning](manage-workspace.md).
Si experimenta algún problema al crear el área de trabajo, vea [Guía de solución de problemas: Creación de un área de trabajo de Machine Learning y conexión a la misma](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Uso compartido de un área de trabajo de Azure Machine Learning
Tras crear un área de trabajo de Machine Learning, puede invitar usuarios a ella, a fin de compartir el acceso a dicha área y todos sus experimentos, conjuntos de datos, bloc de notas, etc. Puede agregar usuarios en uno de estos roles:

* **Usuario**: los usuarios de un área de trabajo pueden crear, abrir, modificar y eliminar experimentos, conjuntos de datos y otros elementos en el área de trabajo.
* **Propietario**: un propietario puede invitar y quitar usuarios en el área de trabajo, además de lo que pueden hacer los usuarios.

> [!NOTE]
> La cuenta de administrador que crea el área de trabajo se agrega automáticamente al área de trabajo como propietario de dicha área. No obstante, a los otros administradores o usuarios de dicha suscripción no se les concede acceso automáticamente al área de trabajo; debe invitarlos de manera explícita.
> 
> 

### <a name="to-share-a-workspace"></a>Para compartir un área de trabajo

1. Inicie sesión en Machine Learning Studio en [https://studio.azureml.net/Home](https://studio.azureml.net/Home).

2. En el panel izquierdo, haga clic en **SETTINGS** (Configuración).

3. Haga clic en la pestaña **USERS** (Usuarios).

4. Haga clic en **INVITE MORE USERS** (Invitar más usuarios) en la parte inferior de la página.

    ![Configuración de Studio](./media/create-workspace/settings.png)

5. Escriba una o varias direcciones de correo electrónico. Los usuarios necesitan una cuenta Microsoft o una cuenta de organización (de Azure Active Directory) válidas.

6. Seleccione si desea agregar los usuarios como propietario o usuario.

7. Haga clic en el botón de marca de verificación **OK** (Aceptar).

Cada usuario agregado recibirá un correo electrónico con instrucciones sobre cómo iniciar sesión en el área de trabajo compartida.

> [!NOTE]
> Para que los usuarios puedan implementar o administrar servicios web en esta área de trabajo, deben ser colaborador o administrador de la suscripción de Azure. 



