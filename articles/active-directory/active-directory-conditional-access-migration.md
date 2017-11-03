---
title: "Migración de directivas clásicas en Azure Portal | Microsoft Docs"
description: "Migración de directivas clásicas en Azure Portal."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migración de directivas clásicas en Azure Portal 


El [acceso condicional](active-directory-conditional-access-azure-portal.md) es una funcionalidad de Azure Active Directory (Azure AD) que le permite controlar cómo acceden los usuarios autorizados a las aplicaciones en la nube. Aunque el propósito sigue siendo el mismo, la nueva versión de Azure Portal también presenta mejoras importantes acerca de cómo funciona el acceso condicional. Las directivas de acceso condicional que se configuraron fuera de Azure Portal pueden coexistir con las nuevas directivas que también se crean en dicho portal. Siempre y cuando no las deshabilite o elimine, estas seguirán aplicándose a su entorno. Sin embargo, se recomienda que migre las directivas clásicas a las nuevas directivas de acceso condicional de Azure AD porque:

- Las nuevas directivas le permiten dirigirse a escenarios que no podría controlar con directivas clásicas.

- Puede reducir el número de directivas que tiene que administrar mediante su consolidación.   

En este tema se le ayuda con la migración de las directivas clásicas existentes a las nuevas directivas de acceso condicional de Azure AD.


## <a name="classic-policies"></a>Directivas clásicas

Las directivas de acceso condicional para Azure AD e Intune que no creó en Azure Portal también se denominan **directivas clásicas**. Para migrar las directivas clásicas, no es necesario tener acceso al portal clásico de Azure. Azure Portal ofrece una vista de [**Directivas clásicas (versión preliminar)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) que le permite revisar las directivas clásicas.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Abrir una directiva clásica

**Para abrir una directiva clásica:**

1. En [Azure Portal](https://portal.azure.com), en la barra de navegación izquierda, haga clic en **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/02.png)
 
2. En la página **Conditional access - Policies** (Acceso condicional - Directivas), en la sección **Administrar**, haga clic en **Directivas clásicas (versión preliminar)**.

3. En la lista de directivas clásicas, seleccione la directiva que le interesa.   

    ![Acceso condicional](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Directivas de acceso condicional de Azure AD

En este tema se proporcionan pasos detallados que permiten migrar las directivas clásicas sin estar familiarizado con las directivas de acceso condicional de Azure AD. Sin embargo, estar familiarizado con los conceptos básicos y la terminología del acceso condicional de Azure AD ayuda a mejorar su experiencia de migración.

Consulte:

- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md) para conocer la terminología y los conceptos básicos.

- [Introducción al acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) para familiarizarse con la interfaz de usuario de Azure Portal.


 





## <a name="multi-factor-authentication-policy"></a>Directiva de autenticación multifactor 

En este ejemplo se muestra cómo migrar una directiva clásica que requiere autenticación multifactor** para una aplicación en la nube. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**Para migrar una directiva clásica:**

1. [Abra la directiva clásica](#open-a-classic-policy) para obtener las opciones de configuración.
2. Cree una nueva directiva de acceso condicional de Azure AD para reemplazar la directiva clásica. 


### <a name="create-a-new-conditional-access-policy"></a>Crear una nueva directiva de acceso condicional


1. En [Azure Portal](https://portal.azure.com), en la barra de navegación izquierda, haga clic en **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/02.png)



3. En la página **Acceso condicional**, en la barra de herramientas de la parte superior, haga clic en **Agregar** para abrir la página **Nuevo**.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/03.png)

4. En la página **Nuevo**, en el cuadro de texto **Nombre**, escriba un nombre para la directiva.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/29.png)

5. En la sección **Asignaciones**, haga clic en **Usuarios y grupos**.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/05.png)

    a. Si tiene todos los usuarios seleccionados en la directiva clásica, haga clic en **Todos los usuarios**. 

    ![Acceso condicional](./media/active-directory-conditional-access-migration/35.png)

    b. Si tiene grupos seleccionados en la directiva clásica, haga clic en **Seleccionar usuarios y grupos** y, después, seleccione los grupos y usuarios necesarios.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/36.png)

    c. Si tiene los grupos excluidos, haga clic en la pestaña **Excluir** y, después, seleccione los grupos y usuarios necesarios. 

    ![Acceso condicional](./media/active-directory-conditional-access-migration/37.png)

6. En la página **Nuevo**, en la sección **Asignación**, haga clic en **Aplicaciones en la nube** para abrir la página **Aplicaciones en la nube**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. En la página **Aplicaciones en la nube**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-migration/08.png)

    a. Haga clic en **Seleccionar aplicaciones**.

    b. Haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, seleccione la aplicación en la nube y haga clic en **Seleccionar**.

    d. En la página **Aplicaciones en la nube**, haga clic en **Listo**.



9. Si seleccionó **Requerir autenticación multifactor**:

    ![Acceso condicional](./media/active-directory-conditional-access-migration/26.png)

    a. En la sección **Controles de acceso**, haga clic en **Conceder**.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/27.png)

    b. En la página **Conceder**, haga clic en **Conceder acceso** y, después, haga clic en **Requerir autenticación multifactor**.

    c. Haga clic en **Seleccionar**.


10. Haga clic en **On** (Activar) para habilitar la directiva.

    ![Acceso condicional](./media/active-directory-conditional-access-migration/30.png)

11. Deshabilite la directiva clásica. 

    ![Acceso condicional](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
