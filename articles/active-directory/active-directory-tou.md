---
title: "Términos de uso de Azure Active Directory | Microsoft Docs"
description: "Términos de uso de Azure AD le permitirán a usted y su empresa tener la capacidad para proporcionar los términos de uso a los usuarios de los servicios de Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: billmath
ms.openlocfilehash: c01ea7c6a05c77244ab29fe6eec00ed1e49b6f7e
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Característica Términos de uso de Azure Active Directory (versión preliminar)
Términos de uso de Azure AD proporciona un método sencillo que las organizaciones pueden usar para presentar la información a los usuarios finales.  Esto garantiza que los usuarios ven las declinaciones de responsabilidades pertinentes para los requisitos legales o de cumplimiento.

Términos de uso de Azure Active Directory utilizan el formato pdf para presentar el contenido.   Este pdf puede tener cualquier tipo de contenido, como documentos de contrato existentes, lo que le permite recopilar acuerdos de usuario final durante el inicio de sesión de usuario.  Puede utilizar los términos de uso para aplicaciones, grupos de usuarios, o si tiene varios términos de uso para fines diferentes.

El resto de este documento describe cómo comenzar con Términos de uso de Azure AD.  

## <a name="why-use-azure-ad-terms-of-use"></a>¿Por qué usar Términos de uso de Azure AD?
¿Tiene dificultades para que los empleado o invitados acepten los términos de uso antes de obtener acceso? ¿Necesita ayuda para ver quién ha aceptado y quién no los términos de uso de su empresa?  Términos de uso de Azure AD proporciona un método sencillo que las organizaciones pueden usar para presentar la información a los usuarios finales.  Esto garantiza que estos ven las declinaciones de responsabilidades pertinentes para los requisitos legales o de cumplimiento.

Términos de uso de Azure AD puede utilizarse en los escenarios siguientes:
-   Términos de uso generales para todos los usuarios de su organización.
-   Términos de uso específicos basados en los atributos de un usuario (por ejemplo médicos por un lado y enfermeras por otro, empleados nacionales por un lado e internacionales por otro, puesto en [grupos dinámicos](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-   Términos de uso basados en el acceso a las aplicaciones de alto impacto empresarial, como Salesforce.


## <a name="prerequisites"></a>Requisitos previos
Siga estos pasos para configurar los Términos de uso de Azure AD:

1. Inicie sesión en Azure AD usando un administrador global, administrador de seguridad o un administrador de acceso condicional para el directorio para el que desea configurar los Términos de uso de Azure AD.
2. Asegúrese de que el directorio tiene una suscripción de Azure AD Premium P1, P2, EMS E3 o EMS E5.  Si no es así [obtenga Azure AD Premium](active-directory-get-started-premium.md) o [inicie una evaluación gratuita](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Ver el panel de Términos de uso de Azure AD en [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>Los controles de la directiva de acceso condicional (incluidos los términos de uso) no admiten el cumplimiento en las cuentas de servicio.  Se recomienda excluir todas las cuentas de servicio de la directiva de acceso condicional.

## <a name="add-company-terms-of-use"></a>Adición de los Términos de uso de la empresa
Una vez que haya finalizado los Términos de uso, utilice el procedimiento siguiente para agregarlas.

### <a name="to-add-terms-of-use"></a>Para agregar Términos de uso
1. Vaya al panel en [https://aka.ms/catou](https://aka.ms/catou)
2. Haga clic en Agregar.</br>
![Agregar condiciones de uso](media/active-directory-tou/tou12.png)
3. Escriba el **Nombre** para los Términos de uso
4. Escriba el **Nombre para mostrar**.  Este encabezado es lo que ven los usuarios cuando inician sesión.
5. Con **Examinar** vaya hasta el pdf de los términos de uso finalizados y selecciónelo.  El tamaño de fuente recomendado es 24.
6. **Seleccionar** un idioma para los términos de uso.  La opción de idioma permite cargar varios términos de uso, cada uno con un idioma diferente.  La versión de los términos de uso que verá un usuario final se basará en sus preferencias del explorador.
7. Seleccione activado o desactivado para **Requerir a los usuarios que expandan las condiciones de uso**.  Si se establece en activado, los usuarios finales deberán consultar las condiciones de uso antes de aceptarlas.
8. En la sección **Acceso condicional**, puede **Forzar** los términos de uso cargados mediante una plantilla o una directiva personalizada de acceso condicional.  Las directivas personalizadas de acceso condicional permiten términos de uso pormenorizados, hasta una aplicación de nube específica o un grupo de usuarios.  Para más información, consulte el artículo sobre [configuración de directivas de acceso condicional](active-directory-conditional-access-best-practices.md)
9. Haga clic en **Crear**.
10. Si ha seleccionado una plantilla personalizada de acceso condicional, a continuación, aparece una nueva pantalla que le permite personalizar la directiva de entidad emisora de certificados.
11. Ahora debería ver los nuevos Términos de uso.</br>

![Agregar condiciones de uso](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Eliminación de Términos de uso
Puede quitar o eliminar los antiguos términos de uso mediante el procedimiento siguiente:

### <a name="to-delete-terms-of-use"></a>Para eliminar Términos de uso
1. Vaya al panel en [https://aka.ms/catou](https://aka.ms/catou)
2. Seleccione los términos de uso que desea quitar.
3. Hacer clic en **Eliminar**.
4. Ahora no debería ver los nuevos términos de uso.


## <a name="audit-terms-of-use"></a>Auditoría de los términos de uso
Términos de uso de Azure AD proporcionan prestaciones de auditoría de uso sencillo para que pueda ver quién ha aceptado los términos de uso y cuándo.  Para empezar a trabajar con auditorías utilice el procedimiento siguiente:

### <a name="to-audit-terms-of-use"></a>Para auditar Términos de uso
1. Vaya al panel en [https://aka.ms/catou](https://aka.ms/catou)
2. Haga clic en Evento de auditoría.</br>
![Evento de auditoría](media/active-directory-tou/tou8.png)
3.  En la pantalla de registros de auditoría de Azure AD, puede filtrar la información utilizando los elementos desplegables para seleccionar información de registro de auditoría específica.
![Evento de auditoría](media/active-directory-tou/tou9.png)
4.  También puede descargar la información en un archivo .csv para su uso de forma local.

## <a name="what-users-see"></a>Qué ven los usuarios
Los usuarios que están dentro del ámbito apropiado, verán lo siguiente una vez que se hayan creado y aplicado los términos de uso.  Verán estas pantallas durante el inicio de sesión.
-   El procedimiento recomendado es tener la fuente en el archivo PDF en tamaño 24.
![Evento de auditoría](media/active-directory-tou/tou10.png)
-   Esta pantalla es como aparece en dispositivos móviles</br></br>
![Evento de auditoría](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>Revisión de los términos de uso
Los usuarios pueden revisar y ver los términos de uso que han aceptado.  Se puede realizar mediante cualquiera de los procedimientos siguientes:

1. Navegue e inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. En la esquina superior derecha, haga clic en su nombre y seleccione **Perfil** en la lista desplegable.
![Perfil](media/active-directory-tou/tou14.png)

3. En Perfil, haga clic en **Revisar los términos de uso**.
![Evento de auditoría](media/active-directory-tou/tou13a.png)

4.  Desde allí puede revisar los términos de uso que ha aceptado. 


## <a name="additional-information"></a>Información adicional
La siguiente información es algo a tener en cuenta y puede ayudarle con los términos de uso.

Los usuarios dentro del ámbito necesitarán cerrar sesión y volver a iniciarla para satisfacer una directiva nueva si:
 - se ha habilitado una directiva de acceso condicional en los términos de uso
 - o se crea una segunda condición de uso

Esto es porque las directivas de acceso condicional surten efecto inmediatamente. Cuando esto sucede el administrador empezará a ver "nubes tristes" o "problemas de token de Azure AD". El administrador tiene que cerrar sesión e iniciarla de nuevo para satisfacer la nueva directiva.





## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**P: ¿Cómo puedo ver si un usuario ha aceptado los términos de uso y cuándo lo ha hecho?**</br>
R: La aceptación de los términos de uso por un usuario se escribe en el registro de auditoría. Puede buscar el registro de auditoría de Azure AD para ver los resultados.  

**P: ¿Si cambian los términos de uso, es necesario para los usuarios aceptar de nuevo?**</br>
R: Sí, un administrador puede cambiar las condiciones de los términos de uso y esto requiere volver a aceptar las nuevas condiciones.

**P: ¿Puede una condición ser compatible con varios idiomas?**</br>
R: No, actualmente no es posible tener varios idiomas en una única condición de uso.  Sin embargo, puede restringir el ámbito a un grupo (por ejemplo, los términos de uso para Francia son diferentes de los términos de uso de Reino Unido). 

**P: ¿Cuándo se desencadenan los términos de uso?**</br>
R: Los términos de uso se desencadenan durante la experiencia de inicio de sesión.

**P: ¿Qué aplicaciones puedo usar como destino de una condición de uso?**</br>
R: Puede crear una directiva de acceso condicional en las aplicaciones empresariales que usan autenticación moderna.  Para más información, consulte [aplicaciones empresariales](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**P: ¿Puedo agregar varios términos de uso a un usuario o aplicación determinados?**</br>
R: Sí, mediante la creación de varias directivas de acceso condicional que tengan como objetivo dichos grupos o aplicaciones. Si un usuario se encuentra en el ámbito de varios términos de uso, aceptará los términos de uso uno por uno.
 
**P: ¿Qué ocurre si un usuario no acepta los términos de uso?**</br>
R: El usuario será bloqueado y no podrá obtener acceso a la aplicación. El usuario tendría que iniciar sesión de nuevo y aceptar las condiciones con el fin de obtener acceso.