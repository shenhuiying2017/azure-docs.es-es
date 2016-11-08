---
title: Adición del nombre de dominio personalizado a la versión preliminar de Azure Active Directory | Microsoft Docs
description: Cómo agregar nombres de dominio de la empresa a Azure Active Directory y comprobar el nombre de dominio.
services: active-directory
documentationcenter: ''
author: jeffsta
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Adición de un nombre de dominio personalizado a la versión preliminar de Azure Active Directory
Dispone de uno o varios nombres de dominio que su organización usa para su actividad empresarial, y los usuarios inician sesión en la red corporativa mediante su nombre de dominio corporativo. Ahora que usa la versión preliminar de Azure Active Directory (Azure AD), también puede agregar el nombre de dominio corporativo a Azure AD. [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md) Esto permite asignar en el directorio nombres de usuario que resultan familiares a los usuarios, como ‘alice@contoso.com’. El proceso es simple:

1. Incorporación de nombres de dominio personalizados al directorio
2. Incorporación de la entrada DNS para el nombre de dominio en el registrador de nombres de dominio
3. Comprobación del nombre de dominio personalizado en Azure AD

## ¿Cómo se agrega un nombre de dominio?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, después, presione **Entrar**.
   
   ![Apertura de Administración de usuarios](./media/active-directory-domains-add-azure-portal/user-management.png)
3. En la hoja ***nombre-directorio***, seleccione **Nombres de dominio**.
4. En la hoja ***nombre-directorio* - Nombres de dominio**, seleccione el comando **Agregar**.
   
   ![Selección del comando Agregar](./media/active-directory-domains-add-azure-portal/add-command.png)
5. En la hoja **Nombre de dominio**, escriba el nombre del dominio personalizado en el cuadro, como 'contoso.com', y luego seleccione **Agregar dominio**. Asegúrese de incluir .com, .net u otra extensión de nivel superior.
6. En la hoja ***nombre de dominio*** (es decir, la hoja que se abre con el nuevo nombre de dominio en el título), obtenga la información de entrada de DNS que usará Azure AD para comprobar que su organización posee el nombre de dominio personalizado.
   
   ![Obtención de información de entrada de DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Ahora que ha agregado el nombre de dominio, Azure AD debe comprobar que su organización posea el nombre de dominio. Para que Azure AD pueda realizar esta comprobación, tiene que agregar una entrada DNS en el archivo de zona DNS para el nombre de dominio. Esta tarea se lleva a cabo en el sitio web del registrador de nombres de dominio para el nombre de dominio.

## Incorporación de la entrada DNS en el registrador de nombres de dominio para el dominio
El siguiente paso para usar el nombre de dominio personalizado con Azure AD consiste en actualizar el archivo de zona DNS para el dominio. Esto permite a Azure AD comprobar que su organización posee el nombre de dominio personalizado.

1. Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene acceso para actualizar la entrada DNS, pida a la persona o equipo que tenga acceso que complete el paso 2 y que le informe cuando esté completo.
2. Actualice el archivo de zona DNS para el dominio. Para ello, agregue la entrada DNS que le proporcione Azure AD. Esta entrada DNS permite a Azure AD comprobar la propiedad del dominio. No cambiará ningún comportamiento, como el enrutamiento de correo o el hospedaje web.

Para obtener ayuda con la incorporación de la entrada DNS, lea [Instrucciones para agregar entradas DNS en los registradores DNS más habituales](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

## Comprobación del nombre de dominio con Azure AD
Una vez agregada la entrada DNS, ya puede comprobar el nombre de dominio con Azure AD.

Solo se puede comprobar un nombre de dominio después de que se hayan propagado los registros de DNS. Esta propagación a suele tardar unos segundos, pero a veces puede tardar una hora o incluso más. Si la comprobación no funciona la primera vez, inténtelo de nuevo más tarde.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Examinar**, escriba Administración de usuarios en el cuadro de texto y, a continuación, seleccione **Entrar**.
   
   ![Apertura de Administración de usuarios](./media/active-directory-domains-add-azure-portal/user-management.png)
3. En la hoja **Administración de usuarios - Nombres de dominio**, seleccione el nombre de dominio sin comprobar que quiere comprobar.
4. En la hoja ***nombre de dominio*** (es decir, la hoja que se abre con el nuevo nombre de dominio en el título), seleccione **Comprobar** para finalizar la comprobación.

Ya puede [asignar nombres de usuario a un dominio personalizado](active-directory-create-users-azure-portal.md).

## Solución de problemas
Si no puede comprobar un nombre de dominio personalizado, intente lo siguiente. Comenzaremos con las más comunes hasta llegar a las menos frecuentes.

1. **Espere una hora**. Los registros de DNS deben propagarse antes de que Azure AD pueda comprobar el dominio. Esto puede tardar una hora o más.
2. **Asegúrese de que se especificó el registro de DNS y que es correcto**. Complete este paso en el sitio web del registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio si la entrada DNS no está presente en el archivo de zona DNS, o si no es una coincidencia exacta con la entrada DNS que Azure AD le ha proporcionado. Si no tiene acceso para actualizar los registros de DNS para el dominio en el nombre de dominio, comparta la entrada DNS con la persona o el equipo de su organización que cuente con este acceso y solicítele que la agregue.
3. **Elimine el nombre de dominio de otro directorio en Azure AD**. Se puede comprobar el nombre de dominio en solo un único directorio. Si previamente se ha comprobado un nombre de dominio en otro directorio, se debe eliminar allí antes de que se puede comprobar en el nuevo directorio. Para más información sobre cómo eliminar los nombres de dominio, consulte [Administración de nombres de dominio personalizados](active-directory-domains-manage-azure-portal.md).

## Incorporación de más nombres de dominio personalizados
Si su organización usa varios nombres de dominio personalizados, como 'contoso.com' y 'contosobank.com', se puede agregar un máximo de 900 nombres de dominio. Siga los mismos pasos de este artículo para agregar cada nombre de dominio.

## Pasos siguientes
[Managing custom domain names (Administración de nombres de dominio).](active-directory-domains-manage-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->