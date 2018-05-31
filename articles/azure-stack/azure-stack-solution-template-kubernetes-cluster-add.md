---
title: Agregar un clúster de Kubernetes a Marketplace de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo agregar un clúster de Kubernetes a Marketplace de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: f74d378359ec66b76fd53b95ab898f1cec9b07f7
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34009578"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Agregar un clúster de Kubernetes a Marketplace de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!note]  
> Kubernetes de Azure Container Services (ACS) en Azure Stack se encuentra en versión preliminar privada. Para solicitar acceso al elemento Kubernetes de Marketplace necesario para seguir las instrucciones de este artículo, [envíe una solicitud para obtener acceso](https://aka.ms/azsk8).

Puede ofrecer un clúster de Kubernetes como un elemento de Marketplace a los usuarios. Los usuarios pueden implementar Kubernetes en una sola operación coordinada.

En el artículo siguiente explica cómo usar una plantilla de Azure Resource Manager para implementar y aprovisionar los recursos para un clúster de Kubernetes independiente. Antes de empezar, compruebe Azure Stack y la configuración de inquilino de Azure global. Recopile la información necesaria sobre su entorno de Azure Stack. Agregue los recursos necesarios a su inquilino y a Marketplace de Azure Stack. El clúster depende de que los elementos de clúster de Kubernetes, Ubuntu Server y script personalizado se encuentren en Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Crear un plan, una oferta y una suscripción

Cree un plan, una oferta y una suscripción para el elemento de Marketplace de clúster de Kubernetes. También puede usar un plan y una oferta existentes.

1. Inicie sesión en el [portal de administración](https://adminportal.local.azurestack.external).

2. Cree un plan como plan base. Para obtener instrucciones, vea [Creación de un plan en Azure Stack](azure-stack-create-plan.md).

3. Cree una oferta. Para obtener instrucciones, vea [Creación de una oferta en Azure Stack](azure-stack-create-offer.md).

4. Seleccione **Ofertas** y busque la que ha creado.

5. Seleccione **Información general** en la hoja Oferta.

6. Seleccione **Cambiar estado**. Seleccione **Público**.

7. Seleccione **+ Nuevo** > **Offers and Plans (Ofertas y planes)** > **Suscripción** para crear una suscripción.

    a. Especifique un **Nombre para mostrar**.

    b. Especifique un **Usuario**. Use la cuenta de Azure AD asociada con su inquilino.

    c. **Descripción del proveedor**

    d. Establezca el **Inquilino de directorio** en el inquilino de Azure AD de su entorno de Azure Stack. 

    e. Seleccione **Oferta**. Seleccione el nombre de la oferta que ha creado. Tome nota del identificador de suscripción.

## <a name="add-an-ubuntu-server-image"></a>Agregar una imagen de Ubuntu Server

Agregue la siguiente imagen de Ubuntu Server en Marketplace:

1. Inicie sesión en el [portal de administración](https://adminportal.local.azurestack.external).

2. Seleccione **Más servicios** > **Marketplace Management** (Administración de Marketplace).

3. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

4. Escriba `UbuntuServer`.

5. Seleccione el servidor con el perfil siguiente:
    - **Publicador**: Canonical
    - **Oferta**: UbuntuServer
    - **SKU**: 16.04-LTS
    - **Versión**: 16.04.201802220

    > [!Note]  
    > Podría aparecer más de una versión de Ubuntu Server 16.04 LTS. Debe agregar la versión que coincida. El clúster de Kubernetes requiere la versión exacta del elemento.

6. Seleccione **Descargar**.

## <a name="add-a-custom-script-for-linux"></a>Agregar un script personalizado para Linux

Agregue el clúster de Kubernetes desde Marketplace:

1. Abra el [portal de administración](https://adminportal.local.azurestack.external).

2. Seleccione **Más servicios** > **Marketplace Management** (Administración de Marketplace).

3. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

4. Escriba `Custom Script for Linux`.

5. Seleccione el script con el perfil siguiente:
    - **Oferta**: Script personalizado para Linux 2.0
    - **Versión**: 2.0.3
    - **Publicador**: Microsoft Corp.

    > [!Note]  
    > Podría aparecer más de una versión del script personalizado para Linux. Debe agregar la versión que coincida. El clúster de Kubernetes requiere la versión exacta del elemento.

6. Seleccione **Descargar**.


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Agregar el clúster de Kubernetes a Marketplace

1. Abra el [portal de administración](https://adminportal.local.azurestack.external).

2. Seleccione **Más servicios** > **Marketplace Management** (Administración de Marketplace).

3. Seleccione **+ Add from Azure** (+ Agregar desde Azure).

4. Escriba `Kubernetes Cluster`.

5. Seleccione `Kubernetes Cluster`.

6. Seleccione **Descargar**.

    > [!note]  
    > El elemento podría tardar cinco minutos en aparecer en Marketplace.

    ![Clúster de Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Actualizar o quitar el clúster de Kubernetes 

Al actualizar el elemento del clúster de Kubernetes, debe quitar el elemento que se encuentra en Marketplace. Después, siga las instrucciones de este artículo para agregar el clúster de Kubernetes a Marketplace.

Para quitar el elemento del clúster de Kubernetes:

1. Anote el nombre del elemento actual, por ejemplo, `Microsoft.AzureStackKubernetesCluster.0.1.0`.

2. Conéctese a Azure Stack con PowerShell.

3. Use el siguiente cmdlet de PowerShell para quitar el elemento:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Pasos siguientes

[Implementar un clúster de Kubernetes en Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Introducción a la oferta de servicios en Azure Stack](azure-stack-offer-services-overview.md)