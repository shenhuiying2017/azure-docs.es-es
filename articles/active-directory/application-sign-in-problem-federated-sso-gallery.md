---
title: "Problemas al iniciar sesión en una aplicación de la galería configurada para inicio de sesión único federado | Microsoft Docs"
description: "Instrucciones para resolver errores específicos al iniciar sesión en una aplicación que se ha configurado para un inicio de sesión único federado basado en SAML con Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: b6d998575a3bf21ada200cfedeed61fe67e3b9d7
ms.lasthandoff: 04/17/2017


---

# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problemas al iniciar sesión en una aplicación de la galería configurada para inicio de sesión único federado

Para solucionar el problema, debe comprobar la configuración de la aplicación en Azure AD de la forma siguiente:

-   Ha seguido todos los pasos de configuración de la aplicación de la galería de Azure AD.

-   El identificador y la dirección URL de respuesta configurados en AAD coinciden con los valores previstos en la aplicación.

-   Ha asignado usuarios a la aplicación.

## <a name="application-not-found-in-directory"></a>No se encontró la aplicación en el directorio

*Error: No se encontró la aplicación con el identificador "https://contoso.com" en el directorio*.

**Causa posible**

El atributo Issuer que se envía de la aplicación a Azure AD en la solicitud SAML no coincide con el valor de identificador configurado en la aplicación Azure AD.

**Resolución**

Asegúrese de que el atributo Issuer de la solicitud SAML coincide con el valor del identificador configurado en Azure AD:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Vaya a la sección **Dominio y direcciones URL**. Compruebe que el valor del cuadro de texto Identificador coincide con el valor del identificador mostrado en el error.

Después de actualizar el valor del identificador de Azure AD y de comprobar que coincide con el valor enviado por la aplicación en la solicitud SAML, podrá iniciar sesión en la aplicación.

## <a name="user-not-assigned-a-role"></a>Usuario no asignado a un rol

*Error: El usuario con sesión iniciada 'brian@contoso.com' no está asignado a un rol de la aplicación*.

**Causa posible**

El usuario no tiene acceso a la aplicación en Azure AD.

**Resolución**

Para asignar uno o varios usuarios a una aplicación directamente, siga los pasos siguientes:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea asignar a un usuario de la lista.

7.  Cuando se cargue la aplicación, haga clic en **Usuarios y grupos** desde el menú de navegación izquierdo de la aplicación.

8.  Haga clic en el botón **Agregar** en la parte superior de la lista **Usuarios y grupos** para abrir la hoja **Agregar asignación**.

9.  Haga clic en el selector **Usuarios y grupos** de la hoja **Agregar asignación**.

10. Escriba el **nombre completo** o la **dirección de correo electrónico** del usuario al que quiere asignar en el cuadro de búsqueda **Buscar por nombre o dirección de correo**.

11. Mantenga el puntero sobre el **usuario** en la lista para que aparezca una **casilla**. Haga clic en la casilla situada junto a la foto o el logotipo del perfil del usuario para agregar ese usuario a la lista de **seleccionados**.

12. **Opcional:** si desea **agregar más de un usuario**, escriba otro **nombre completo** o **dirección de correo electrónico** en el cuadro de búsqueda **Buscar por nombre o dirección de correo** y haga clic en la casilla para agregar ese usuario a la lista de **seleccionados**.

13. Cuando haya terminado de seleccionar usuarios, haga clic en el botón **Seleccionar** para agregarlos a la lista de usuarios y grupos que se asignarán a la aplicación.

14. **Opcional:** haga clic en el selector **Seleccionar rol** de la hoja **Agregar asignación** para seleccionar un rol que se asignará a los usuarios que ha seleccionado.

15. Haga clic en el botón **Asignar** para asignar la aplicación a los usuarios seleccionados.

Tras un breve período de tiempo, los usuarios seleccionados podrán iniciar estas aplicaciones mediante los métodos descritos en la sección de descripción de la solución.

## <a name="not-a-valid-saml-request"></a>No es una solicitud SAML válida

*Error: La solicitud no es un mensaje de protocolo Saml2 válido.*

**Causa posible**

Azure AD no admite la solicitud SAML enviada por la aplicación para el inicio de sesión único. Algunos de los problemas más comunes son:

-   Faltan campos obligatorios en la solicitud SAML

-   Método codificado de la solicitud SAML

**Resolución**

1.  Capture la solicitud SAML. Siga el tutorial de [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) para obtener información sobre cómo capturar la solicitud SAML.

2.  Póngase en contacto con el proveedor de la aplicación y comparta:

   -   La solicitud SAML

   -   [Los requisitos del protocolo SAML de inicio de sesión único de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Deben validar que admiten la implementación de SAML de Azure AD para inicio de sesión único.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Ningún recurso en la lista requiredResourceAccess

*Error: La aplicación cliente ha solicitado el acceso al recurso '00000002-0000-0000-c000-000000000000'. Se produjo un error en esta solicitud porque el cliente no ha especificado este recurso en su lista requiredResourceAccess*.

**Causa posible**

El objeto de aplicación está dañado.

**Resolución**

Para solucionar el problema, quite la aplicación del directorio. Después, agregue y vuelva a configurar la aplicación, y siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Haga clic en **Eliminar** en la parte superior izquierda de la hoja **Información general** de la aplicación.

8.  Actualice Azure AD y agregue la aplicación en la galería de Azure AD. Después, configure la aplicación.

<span id="_Hlk477190176" class="anchor"></span>Tras configurar la aplicación, debe poder iniciar sesión en la aplicación.

## <a name="certificate-or-key-not-configured"></a>Certificado o clave no configurados

*Error: No hay configurada ninguna clave de firma.*

**Causa posible**

El objeto de aplicación está dañado y Azure AD no reconoce el certificado configurado para la aplicación.

**Resolución**

Para eliminar y crear un nuevo certificado, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

 * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Haga clic en **Crear nuevo certificado** en la sección **Certificado de firma de SAML**.

9.  Seleccione la fecha de expiración. A continuación, haga clic en **Guardar**.

10. Active **Activar el certificado nuevo** para reemplazar el certificado activo. Después, haga clic en **Guardar** en la parte superior de la hoja y en Aceptar para activar el certificado de sustitución.

11. En la sección **Certificado de firma de SAML**, haga clic en **Quitar** para quitar el certificado **no usado**.

## <a name="next-steps"></a>Pasos siguientes
[Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

