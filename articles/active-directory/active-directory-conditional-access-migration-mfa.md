---
title: "Migración de una directiva clásica que requiere autenticación multifactor en Azure Portal | Microsoft Docs"
description: "En este artículo se muestra cómo migrar una directiva clásica que requiere autenticación multifactor en Azure Portal."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 77484dc3773736ea15c39ede5f9d49b6b694d960
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migración de una directiva clásica que requiere autenticación multifactor en Azure Portal 

En este ejemplo se muestra cómo migrar una directiva clásica que requiere **autenticación multifactor** para una aplicación en la nube. Aunque no es un requisito previo, le recomendamos que lea [Migración de directivas clásicas en Azure Portal](active-directory-conditional-access-migration.md) antes de iniciar la migración de las directivas clásicas.


 
## <a name="overview"></a>Información general 

En el escenario de este artículo se muestra cómo migrar una directiva clásica que requiere **autenticación multifactor** para una aplicación en la nube. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


El proceso de migración consta de los pasos siguientes:

1. [Abra la directiva clásica](#open-a-classic-policy) para obtener las opciones de configuración.
2. Cree una nueva directiva de acceso condicional de Azure AD para reemplazar la directiva clásica. 
3. Deshabilite la directiva clásica.



## <a name="open-a-classic-policy"></a>Abrir una directiva clásica

1. En [Azure Portal](https://portal.azure.com), en la barra de navegación izquierda, haga clic en **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Acceso condicional](./media/active-directory-conditional-access-migration-mfa/02.png)

3. En la sección **Administrar**, haga clic en **Directivas clásicas (vista previa)**.

    ![Directivas clásicas](./media/active-directory-conditional-access-migration-mfa/12.png)

4. En la lista de directivas clásicas, haga clic en la directiva que requiera **autenticación multifactor** para una aplicación en la nube.

    ![Directivas clásicas](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Crear una nueva directiva de acceso condicional


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



## <a name="disable-the-classic-policy"></a>Deshabilitar la directiva clásica

Para deshabilitar la directiva clásica, haga clic en **Deshabilitar** en la vista **Detalles**.

![Directivas clásicas](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre la migración de directivas clásicas, consulte [Migración de directivas clásicas en Azure Portal](active-directory-conditional-access-migration.md).


- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introducción al acceso condicional en Azure Active Directory).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
