---
title: Implementación de un clúster de Kubernetes en Azure Stack | Microsoft Docs
description: Aprenda a implementar un clúster de Kubernetes en Azure Stack.
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
ms.openlocfilehash: 7cf865f0ce75d8308d6d42306e8e05852f763cae
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Implementación de un clúster de Kubernetes en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!Note]  
> Kubernetes de Azure Container Services (ACS) en Azure Stack se encuentra en versión preliminar privada. El operador de Azure Stack debe solicitar acceso al elemento Kubernetes de Marketplace necesario para seguir las instrucciones de este artículo.

En el artículo siguiente se explica cómo usar una plantilla de la solución Azure Resource Manager para implementar y aprovisionar los recursos para Kubernetes en una única operación coordinada. Necesita recopilar la información necesaria sobre la instalación de Azure Stack, generar la plantilla y, a continuación, implementar en la nube.

## <a name="kubernetes-and-containers"></a>Kubernetes y contenedores

Puede instalar Kubernetes de Azure Container Services (ACS) en Azure Stack. [Kubernetes](https://kubernetes.io) es un sistema de código abierto para automatizar la implementación, el escalado y la administración de aplicaciones en contenedores. Un [contenedor](https://www.docker.com/what-container) se encuentra en una imagen, similar a una VM. A diferencia de una VM, la imagen de contenedor tan solo incluye los recursos necesarios para ejecutar una aplicación, como el código, el tiempo de ejecución para ejecutar el código, bibliotecas específicas y la configuración.

Puede utilizar Kubernetes para:

- Desarrollar aplicaciones escalables de forma masiva y actualizables que se pueden implementar en segundos. 
- Simplificar el diseño de la aplicación y mejorar la fiabilidad mediante distintas aplicaciones de Helm. [Helm](https://github.com/kubernetes/helm) es una herramienta de empaquetado de código abierto que ayuda a instalar y administrar el ciclo de vida de las aplicaciones de Kubernetes.
- Supervisar y diagnosticar fácilmente el estado de las aplicaciones con una funcionalidad de escalado y actualización.

## <a name="prerequisites"></a>requisitos previos 

Para empezar, asegúrese de tener los permisos adecuados y de que la instancia de Azure Stack está lista.

1. Compruebe que puede crear aplicaciones en el inquilino de Azure Active Directory (Azure AD). Necesitará estos permisos para la implementación de Kubernetes.

    Para obtener instrucciones sobre cómo comprobar sus permisos, consulte [Comprobación de los permisos de Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Genere un par de claves SSH pública y privada para iniciar sesión en la máquina virtual de Linux en Azure Stack. Necesitará la clave pública al crear el clúster.

    Para obtener instrucciones sobre cómo generar una clave, consulte [SSH Key Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation) (Generación de claves SSH).

3. Compruebe que tiene una suscripción válida en el portal del inquilino de Azure Stack y que tiene suficientes direcciones IP públicas disponibles para agregar nuevas aplicaciones.

## <a name="create-a-service-principal-in-azure-ad"></a>Creación de una entidad de servicio en Azure AD

1. Inicie sesión en [Azure Portal](http://www.poartal.azure.com) global.
2. Compruebe que inició sesión con el inquilino de Azure AD asociado con Azure Stack.
3. Crear una aplicación de Azure.

    a. Seleccione **Azure Active Directory** > **+ Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

    b. Escriba un **Nombre** para la aplicación.

    c. Seleccione **Aplicación web/API**.

    d. Escriba `http://localhost` para **URL de inicio de sesión**.

    c. Haga clic en **Crear**

4. Anote el **Id. de la aplicación**. Necesitará el identificador al crear el clúster. Se hace referencia al identificador como **Id. de cliente de entidad de servicio**.

5. Seleccione **Configuración** > **Claves**.

    a. Escriba la **Descripción**.

    b. Seleccione **Never expires** (Nunca expira) para **Expira**.

    c. Seleccione **Guardar**. Tome nota de la cadena de clave. Necesitará la cadena de clave al crear el clúster. Se hace referencia a la clave como **Secreto de cliente de la entidad de servicio**.



## <a name="give-the-service-principal-access"></a>Dar acceso a la entidad de servicio

Dé a la entidad de servicio acceso a su suscripción para que la entidad pueda crear recursos.

1.  Inicie sesión en el [portal de administración](https://adminportal.local.azurestack.external).

2. Seleccione **Más servicios** > **Suscripciones de usuario** > **+ Agregar**.

3. Seleccione la suscripción que ha creado.

4. Seleccione **Control de acceso (IAM)** > seleccione **+ Agregar**.

5. Seleccione el rol **Propietario**.

6. Seleccione el nombre de aplicación creado para la entidad de servicio. Puede que tenga que escribir el nombre en el cuadro de búsqueda.

7. Haga clic en **Save**(Guardar).

## <a name="deploy-a-kubernetes-cluster"></a>Implementación de un clúster de Kubernetes

1. Abra el [portal de Azure Stack](https://portal.local.azurestack.external).

2. Seleccione **+ Nuevo** > **Proceso** > **Clúster de Kubernetes**.

    ![Implementar plantilla de solución](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. Seleccione **Parámetros** en Implementar plantilla de solución.

    ![Implementar plantilla de solución](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. Escriba el **nombre de usuario administrativo de Linux**. Nombre de usuario de las máquinas virtuales de Linux que forman parte del clúster de Kubernetes y DVM.

3. Escriba la **clave pública SSH** utilizada para la autorización en todas las máquinas de Linux creadas como parte del clúster de Kubernetes y DVM.

4. Escriba el **punto de conexión del inquilino**. Este es el punto de conexión de Azure Resource Manager para conectarse para crear el grupo de recursos para el clúster de Kubernetes. Debe obtener el punto de conexión del operador de Azure Stack para un sistema integrado. Para obtener el Kit de desarrollo de Azure Stack (ASDK), puede usar `https://management.local.azurestack.external`.

5. Escriba el **identificador de inquilino** para el inquilino. Si necesita ayuda para encontrar este valor, consulte [Obtención del identificador de inquilino](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

6. Escriba el **prefijo del DNS del perfil maestro** que es único para la región. Debe ser un nombre único para la región, como `k8s-12345`. Como procedimiento recomendado, intente elegirlo de modo que sea el mismo que el nombre del grupo de recursos.

    > [!Note]  
    > Para cada clúster, use un prefijo de DNS del perfil principal nuevo y único.

7. Escriba el número de agentes en el clúster. Este valor se conoce como el **recuento de perfiles del grupo de agentes**. Puede haber de 1 a 32.

8. Escriba el **id. de aplicación de la entidad de servicio**. El proveedor de nube de Kubernetes Azure usa este identificador.

9. Escriba el **secreto de cliente de la entidad de servicio** que creó al crear la aplicación de la entidad de servicio.

10. Escriba la **versión del proveedor de nube de Azure Kubernetes**. Esta es la versión para el proveedor de Kubernetes Azure. Azure Stack publica una compilación de Kubernetes personalizada para cada versión de Azure Stack.

12. Seleccione **Aceptar**.

### <a name="specify-the-solution-values"></a>Especificación de los valores de la solución

1. Seleccione la **Suscripción**.

2. Escriba el nombre del nuevo grupo de recursos o seleccione uno existente. El nombre del recurso debe ser alfanumérico y estar en minúsculas.

3. Escriba la ubicación del grupo de recursos; por ejemplo, **local**.

4. Seleccione **Crear**.

## <a name="connect-to-your-cluster"></a>Conexión al clúster

Ahora está listo para conectarse al clúster. Necesitará el **kubectl**, el cliente de línea de comandos de Kubernetes. En la documentación de Azure Container Service, encontrará instrucciones sobre cómo conectarse al clúster y administrarlo.   

Para obtener instrucciones, consulte [Conexión al clúster](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster).

## <a name="next-steps"></a>Pasos siguientes

[Agregar un clúster de Kubernetes a Marketplace (para el operador de Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes en Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)