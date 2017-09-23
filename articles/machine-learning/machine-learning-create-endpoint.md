---
title: "Creación de puntos de conexión de servicio web en Machine Learning | Microsoft Docs"
description: "Creación de puntos de conexión de servicio web en Azure Machine Learning"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 9f83ffc9cf7dbe37c1ce9980fd7f5b9133fe78f6
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017

---
# <a name="creating-endpoints"></a>Creación de extremos
> [!NOTE]
>  En este tema se describen técnicas que se aplican a un servicio web Machine Learning **clásico**.
> 
> 

Al crear servicios web que puede vender a sus clientes, necesitará proporcionar modelos entrenados para cada cliente que aún siga vinculado al experimento desde el que se creó el servicio web. Además, las actualizaciones en el experimento pueden aplicarse de manera selectiva a un punto de conexión sin sobrescribir las personalizaciones.

Para ello, Azure Machine Learning permite crear varios puntos de conexión para un servicio web implementado. Cada punto de conexión del servicio web se administra, limita y dirige de forma independiente. Cada punto de conexión es una dirección URL única y clave de autorización que se puede distribuir a sus clientes.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Incorporación de puntos de conexión al servicio web
Hay tres maneras de agregar un punto de conexión a un servicio web.

* De manera programática
* A través del portal de servicios web de Azure Machine Learning
* A través del Portal de Azure clásico

Una vez creado el extremo, puede consumirlo a través de API sincrónicas, API de lotes y hojas de cálculo de Excel. Además de agregar extremos a través de esta interfaz de usuario, también puede usar las API de administración de extremos para agregar extremos mediante programación.

> [!NOTE]
> Si ha agregado más puntos de conexión al servicio web, no podrá eliminar el predeterminado.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Incorporación de un punto de conexión mediante programación
Puede agregar un punto de conexión al servicio web mediante programación utilizando el código de ejemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Incorporación de un punto de conexión mediante el portal de servicios web de Azure Machine Learning
1. En Machine Learning Studio, en la columna de navegación izquierda, haga clic en Servicios web.
2. En la parte inferior del panel de servicios web, haga clic en **Manage endpoints**(Administrar puntos de conexión). El portal de servicios web de Azure Machine Learning se abre en la página de puntos de conexión del servicio web.
3. Haga clic en **Nuevo**.
4. Escriba un nombre y una descripción para el nuevo punto de conexión. Los nombres de los puntos de conexión deben tener 24 caracteres o menos y deben estar formados por letras en minúsculas o números. Seleccione el nivel de registro y si los datos de ejemplo están habilitados. Para obtener más información sobre el registro, consulte [Habilitación del registro para los servicios web Machine Learning](machine-learning-web-services-logging.md).

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>Configuración de puntos de conexión con el Portal de Azure clásico
1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com), haga clic en **Machine Learning** en la columna izquierda. Haga clic en el área de trabajo que contiene el servicio web en el que esté interesado.
   
    ![Navegar a área de trabajo](./media/machine-learning-create-endpoint/figure-1.png)
2. Haga clic en **Servicios web**.
   
    ![Acceso a servicios web](./media/machine-learning-create-endpoint/figure-2.png)
3. Haga clic en el servicio web en el que esté interesado para ver la lista de puntos de conexión disponibles.
   
    ![Navegar a extremo](./media/machine-learning-create-endpoint/figure-3.png)
4. Haga clic en **Agregar extremo**en la parte inferior de la página. Escriba un nombre y una descripción, asegúrese de que no hay ningún otro punto de conexión con el mismo nombre en este servicio web. Deje el nivel de limitación con su valor predeterminado, a menos que tenga requisitos especiales. Para obtener más información sobre las limitaciones, consulte el artículo sobre [escalado de puntos de conexión de API](machine-learning-scaling-webservice.md).
   
    ![Crear extremo](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>Pasos siguientes
[Cómo consumir un servicio web Azure Machine Learning](machine-learning-consume-web-services.md)


