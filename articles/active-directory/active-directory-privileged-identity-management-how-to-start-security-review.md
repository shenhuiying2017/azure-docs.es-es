<properties
   pageTitle="Inicio de una revisión de acceso | Microsoft Azure"
   description="Aprenda a crear una revisión de acceso para identidades con privilegios con la aplicación Privileged Identity Management de Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# Inicio de una revisión de acceso en Privileged Identity Management de Azure AD

Las asignaciones de roles se convierten en "obsoletas" cuando los usuarios tienen acceso con privilegios que ya no necesitan. Con el fin de reducir el riesgo asociado a estas asignaciones de roles obsoletos, los administradores de roles con privilegios deben revisar regularmente los roles que se han concedido a los usuarios. En este documento se describen los pasos para iniciar una revisión de acceso en Azure AD Privileged Identity Management (PIM).

## Inicio de una revisión de acceso
> [AZURE.NOTE] Si no ha agregado la aplicación PIM al panel del Portal de Azure, consulte los pasos que se indican en [Introducción a Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

En la página principal de la aplicación PIM, hay tres formas de comenzar una revisión de acceso:

- **Revisiones de acceso** > **Agregar**
- **Roles** > botón **Revisar**
- Seleccionar el rol específico que se va a revisar en la lista de roles > botón **Revisar**

Al hacer clic en el botón **Revisar**, aparece la hoja **Iniciar una revisión de acceso**. En esta hoja, va a configurar la revisión con un nombre y un límite de tiempo, elija un rol para revisar y decida quién llevará a cabo la revisión.

![Inicio de una revisión de acceso: captura de pantalla][1]

### Configuración de la revisión

Para crear una revisión de acceso, necesitará nombrarla y establecer una fecha de inicio y finalización.

![Configuración de una revisión: captura de pantalla][2]

Asegúrese de que la longitud de la revisión sea lo suficientemente larga para que los usuarios la completen. Si finaliza antes de la fecha de finalización, siempre puede detener pronto la revisión.

### Elija un rol para su revisión

Cada revisión se centra solo en un rol. A menos que iniciara la revisión de acceso desde una hoja de rol específica, deberá elegir un rol ahora.

1. Vaya a **Revisar la pertenencia al rol**

    ![Revisión de pertenencia al rol: captura de pantalla][3]

2. Elija un rol de la lista.

### Decida quién llevará a cabo la revisión

Hay tres opciones para realizar una revisión. Puede asignar la revisión a otra persona para que la complete, puede hacerlo usted mismo o hacer que cada usuario revise su propio acceso.

1. Vaya a **Seleccionar revisores**

    ![Selección de revisores: captura de pantalla][4]

2. Elija una de las opciones:
    - **Seleccionar revisor**: utilice esta opción si no sabe quién requiere acceso. Con esta opción, puede asignar la revisión a un propietario de recursos o al administrador de grupos.
    - **Yo**: resulta útil si desea obtener una vista previa de cómo funcionan las revisiones de acceso o desea revisar en nombre de personas que no pueden hacerlo.
    - **Revisión de los propios miembros**: utilice esta opción para que los usuarios revisen ellos mismos sus propias asignaciones de roles.

### Inicio de la revisión

Por último, tiene la opción de requerir que los usuarios proporcionen un motivo si aprueban su acceso. Agregue una descripción de la revisión si lo desea y seleccione **Iniciar**.

Asegúrese de que permiten a los usuarios saber que hay una revisión de acceso esperando para ellos y [cómo realizar una revisión de acceso](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## Administración de la revisión de acceso

Puede seguir el progreso de las revisiones a medida que los revisores las realizan en el panel de PIM de Azure AD, en la sección de revisiones de acceso. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](active-directory-privileged-identity-management-how-to-complete-review.md).

Hasta que termine el período de revisión, puede recordar a los usuarios completar su revisión o detener la revisión antes desde la sección de revisiones de acceso.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Tabla de contenido de PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png

<!---HONumber=AcomDC_0907_2016-->