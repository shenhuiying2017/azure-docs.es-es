---
title: Establecimiento de directivas de seguridad en Azure Security Center | Microsoft Docs
description: Este documento le ha ayudado a configurar directivas de seguridad en el Centro de seguridad de Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Establecimiento de directivas de seguridad en Azure Security Center
Este documento le ayuda a configurar las directivas de seguridad en Security Center guiándole por los pasos necesarios para realizar esta tarea.


## <a name="how-security-policies-work"></a>¿Cómo funcionan las directivas de seguridad?
Security Center crea automáticamente una directiva de seguridad predeterminada para cada una de las suscripciones de Azure. Puede modificar la directiva en Security Center o usar [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para crear nuevas definiciones, definir directivas adicionales y asignar directivas entre grupos de administración (que pueden representar una organización entera, una unidad de negocio de ella, etc.) y supervisar el cumplimiento de estas directivas en todos estos ámbitos.

> [!NOTE]
> Azure Policy se encuentra en versión preliminar limitada. Haga clic [aquí](https://aka.ms/getpolicy) para unirse. Para más información sobre las directivas de Azure, lea [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy). (Creación y administración de directivas para exigir cumplimiento).

## <a name="how-to-change-security-policies-in-security-center"></a>¿Cómo se cambian las directivas de seguridad en Security Center?
Puede editar la directiva de seguridad predeterminada de cada una de las suscripciones de Azure en Security Center. Para modificar una directiva de seguridad, debe ser propietario, colaborador o administrador de seguridad de esa suscripción o del grupo de administración que la contiene. Inicie sesión en Azure Portal y siga estos pasos para ver las directivas de seguridad en Security Center:

1. En el panel **Security Center**, en **General**, haga clic en **Directiva de seguridad**.
2. Seleccione la suscripción en la que desea habilitar la directiva de seguridad.

    ![Administración de directivas](./media/security-center-policies/security-center-policies-fig10.png)

3. En la sección **COMPONENTES DE LA DIRECTIVA**, haga clic en **Directiva de seguridad**.

    ![Componentes de la directiva](./media/security-center-policies/security-center-policies-fig12.png)

4. Esta es la directiva predeterminada asignada a Security Center mediante Azure Policy. Puede eliminar elementos de la sección **POLICIES AND PARAMETERS** (DIRECTIVAS Y PARÁMETROS) o puede agregar otras definiciones de directiva que se encuentren en **AVAILABLE OPTIONS** (OPCIONES DISPONIBLES). Para ello, simplemente haga clic en el signo más junto al nombre de la definición.

    ![Definiciones de directiva](./media/security-center-policies/security-center-policies-fig11.png)

5. Para una explicación más detallada sobre la directiva, haga clic en ella y se abrirá otra página con los detalles y el código JSON que tiene la estructura [definición de directiva(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. Cuando termine de realizar las modificaciones, haga clic en **Guardar**.

## <a name="see-also"></a>Otras referencias
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md). Aprenda a planificar y entender las consideraciones de diseño para adoptar Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md). Aprenda a administrar y responder a las alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md). Aprenda cómo supervisar el estado de mantenimiento de las soluciones de sus asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.
