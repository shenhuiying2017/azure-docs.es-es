---
title: "Organización de los recursos con grupos de administración de Azure: Azure | Microsoft Docs"
description: "Obtenga información sobre los grupos de administración y cómo utilizarlos."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organización de los recursos con grupos de administración de Azure 

Si tiene varias suscripciones, puede organizarlas en contenedores llamados "grupos de administración" para ayudarle a administrar el acceso, la directiva, los costos y el cumplimiento en las suscripciones. A modo de ejemplo, puede aplicar directivas a un grupo de administración que limiten qué tipos de recursos se pueden crear.

> [!Note]
> Esta característica actualmente está en versión preliminar privada. [Regístrese aquí](https://aka.ms/MGPreviewSignup) para tener su inscripción unida a la versión preliminar.   
 


Los grupos de administración se pueden organizar en una jerarquía. La estructura que se muestra es una representación de ejemplo de una jerarquía de grupos de administración que puede existir:


![Árbol de jerarquía](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>Cómo se relacionan los grupos de administración para la inscripción de Azure Enterprise

La introducción de grupos de administración es un paso del recorrido más grande de la transición de las características de [Enterprise Portal](https://ea.azure.com) a [Azure Portal](https://portal.azure.com).

Tal y como se definió en Enterprise Portal, se crea la estructura de grupos de administración. Toda la jerarquía, que consta de la inscripción, los departamentos y las cuentas, se asigna a los grupos de administración correspondientes. 

Así es como la estructura actual de EA se asigna a la jerarquía de grupos de administración. 

![Árbol de jerarquía](media/billing-enterprise-mgmt-groups/tree2.png)

En la tabla siguiente se muestra cómo se asignan los usuarios de Enterprise Portal a la jerarquía de grupos de administración.

|    Rol de EA                                       |    Rol en el nodo de grupos de administración asignado    |    Permisos en el nodo de grupos de administración                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    Administrador de EA                              |    Colaborador de directivas de recursos                   |    Puede ver los costos, administrar las directivas de recursos y ver la jerarquía en el nodo de inscripción y debajo de dicho nodo    |
|    Administrador de EA en modo de solo lectura            |    Lector de facturación                                |    Puede leer los costos y ver la jerarquía en el nodo de inscripción y debajo de dicho nodo                              |
|    Administrador de departamentos                      |    Lector de facturación                                |    Puede leer los costos y ver la jerarquía en el nodo de departamentos y debajo de dicho nodo                                 |
|    Administrador de departamentos en modo de solo lectura    |    Lector de facturación                                |    Puede leer los costos y ver la jerarquía en el nodo de departamentos y debajo de dicho nodo                                 |
|    Propietario de cuenta                                 |    Colaborador de directivas de recursos                   |    Puede ver los costos, administrar la directiva de recursos y ver la jerarquía en el nodo de cuentas y debajo de dicho nodo       |




## <a name="view-management-groups-in-the-azure-portal"></a>Visualización de grupos de administración en Azure Portal

Para ver una inscripción, un departamento o una cuenta dentro de la versión preliminar, inicie sesión en Azure Portal con el vínculo del correo electrónico de bienvenida.   

![Jerarquía](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>Visualización de costos 
En las pantallas de detalles de grupos de administración, verá los costos mensuales actuales hasta la fecha. Estos costos se basan en el uso y no tienen en cuenta los importes de prepago, uso por encima del límite, cantidades incluidas, ajustes e impuestos. Para el grupo de administración correspondiente a la inscripción, la sección de costos muestra el compromiso restante.  

![detalles de inscripción](media/billing-enterprise-mgmt-groups/enrollment.png)

 "Los costos facturados por separado" son la acumulación mensual de los cambios independientes como Marketplace, usos por encima del límite y otros costos que no contravienen el compromiso de la inscripción.  Para más información sobre el desglose de costos, consulte [Enterprise Portal](https://ea.azure.com). 

### <a name="enabling-access-to-costs"></a>Habilitación del acceso a los costos
Si no ve los costos, vea nuestro documento [Troubleshoot enterprise cost views](https://aka.ms/enableazurecosts) (Solución de problemas de las vistas de costos empresariales) para obtener ayuda.  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>Retrasos entre Enterprise Portal y Azure Portal 
* Durante la versión preliminar, los importes que se muestran en Azure Portal podrían estar retrasados con respecto a los valores de Enterprise Portal. Este problema es temporal y se está trabajando en él.
* La configuración actualizada en Enterprise Portal tiene un retraso de varios minutos antes de que las actualizaciones se reflejen en Azure Portal. 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>Los grupos de administración tienen una relación con el directorio   
Al igual que las suscripciones, los grupos de administración también tienen una relación de confianza con Azure AD. Una jerarquía de grupos de administración confía en un único directorio para autenticar a los usuarios. Todos los administradores asociados a una jerarquía de grupos de administración deben pertenecer al mismo directorio. 

Dado que la jerarquía de inscripción está asignada a grupos de administración, se establece una relación de confianza con un único directorio. Siempre que sea posible, se selecciona un directorio existente asociado a las cuentas de usuario de la inscripción. En algunos casos, se crea un nuevo directorio y se invita a todos los usuarios existentes de la inscripción a ese directorio. Los directorios asociados a las suscripciones de la inscripción no se ven afectados. Por lo tanto, la jerarquía podría crearse en un directorio diferente de las suscripciones. [Obtenga más información](billing-enterprise-mgmt-grp-find.md) sobre cómo afecta este proceso a la experiencia de navegar entre la jerarquía y sus suscripciones.

## <a name="administering-your-management-groups"></a>Administración de los grupos de administración
Los grupos de administración de Azure Portal se encuentran en versión preliminar y son de solo lectura en esta versión inicial. Para realizar actualizaciones, vaya a Enterprise Portal. Las actualizaciones se reflejan automáticamente en Azure Portal. Consulte la documentación de Enterprise Portal para obtener ayuda sobre cómo realizar modificaciones e incorporaciones.   

## <a name="policy-management"></a>Administración de directivas
El Administrador de recursos permite crear directivas personalizadas para administrar los recursos. Con grupos de administración, las directivas se pueden asignar en cualquier nivel de la jerarquía y los recursos que heredan esas directivas.  [Más información](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> La directiva no se aplica a través de los directorios. 


