---
title: Administración de una aplicación de Azure IoT Central | Microsoft Docs
description: Como administrador, ¿cómo administra su aplicación de Azure IoT Central?
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: b60b9e851a3b6612964e67e7764ad8d43d606b4e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199976"
---
# <a name="how-to-administer-your-application"></a>Administración de la aplicación

Después de crear una aplicación Microsoft Azure IoT Central, puede utilizar la sección **Administración** de la interfaz de usuario de Azure IoT Central para administrarla. Para acceder a la sección **Administración**, seleccione **Administración** en el menú de navegación de la izquierda.

La sección **Administración** le permite:

- Administrar usuarios

- Administrar roles

- Ver la información de facturación

- Administrar configuración de la aplicación

- Ampliar una evaluación gratuita

En la sección **Administración**, hay un menú de navegación secundario con vínculos a las distintas tareas de administración.

Para acceder y utilizar la sección **Administración**, debe tener el rol **Administrador** en la aplicación Azure IoT Central. Si crea una aplicación Azure IoT Central, se le asigna automáticamente al rol **Administrator** para esa aplicación. La sección *Administración de usuarios* de este artículo explica más acerca de cómo asignar el rol **Administrador** a otros usuarios.

## <a name="change-application-name"></a>Cambiar el nombre de la aplicación

Para cambiar el nombre de la aplicación, utilice el menú de navegación secundario para acceder a la página **Configuración de la aplicación** de la sección **Administración**.

En la página **Configuración de la aplicación**, introduzca un nombre de su elección en el campo **Nombre de aplicación** y, después, seleccione **Guardar**.

## <a name="change-the-application-url"></a>Cambiar la URL de la aplicación

Para cambiar la dirección URL de la aplicación, utilice el menú de navegación secundario para acceder a la página **Configuración de la aplicación** de la sección **Administración**.

![Página Configuración de la aplicación](media\howto-administer\image0-a.png)

En la página **Configuración de la aplicación**, introduzca la dirección URL de su elección en el campo **URL** y, después, seleccione **Guardar**. La dirección URL debe tener como máximo 200 caracteres de longitud. Si la dirección URL no está disponible, verá un error de validación.

> [!Note]
> Si cambia la dirección URL, la dirección URL anterior la puede tomar otro cliente de Azure IoT Central. En ese caso, ya no estará disponible para su uso por el usuario. Cuando cambie la dirección URL, la dirección antigua ya no funcionará y deberá notificar a los usuarios acerca de la nueva URL que deben utilizar.

## <a name="change-the-application-image"></a>Cambiar la imagen de la aplicación

Para más información sobre el uso de imágenes en una aplicación de Azure IoT Central, consulte [Preparación y carga de imágenes a una aplicación de Azure IoT Central](howto-prepare-images.md).

## <a name="delete-an-application"></a>Eliminar una aplicación

Para eliminar la aplicación, utilice el menú de navegación secundario para acceder a la página **Configuración de la aplicación** de la sección **Administración**.

Elija **Eliminar**.

> [!Note]
> Al eliminar una aplicación de manera permanente, se eliminan todos los datos asociados con la aplicación. Para eliminar una aplicación, también debe tener el derecho de eliminar recursos en la suscripción de Azure que haya elegido al crear la aplicación. Para aprender más, consulte [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Roles en Azure IoT Central

Los roles le permiten controlar quién, dentro de su organización, puede realizar varias tareas de Azure IoT Central. Azure IoT Central tiene tres roles que puede asignar a los usuarios de la aplicación. Los roles se asignan por cada aplicación. El mismo usuario puede tener diferentes roles en diferentes aplicaciones. Puede asignar el misma usuario a varios roles dentro de una aplicación.

### <a name="administrator"></a>Administrador

Los usuarios del rol **Administrador** tienen acceso a toda la funcionalidad de una aplicación de Azure IoT Central.

El usuario que crea una aplicación se asigna automáticamente al rol **Administrador**. Siempre debe haber al menos un usuario en el rol **Administrador**.

### <a name="application-builder"></a>Generador de aplicaciones

Los usuarios con el rol **Application Builder** (Generador de aplicaciones) pueden hacer todo en una aplicación Azure IoT Central excepto administrar la aplicación.

### <a name="application-operator"></a>Operador de aplicaciones

Los usuarios con el rol **Application Operator** (Operador de aplicaciones) no tienen acceso a la página **Application Builder** (Generador de aplicaciones). No pueden administrar la aplicación.

## <a name="manage-users"></a>Administrar usuarios

Los administradores de aplicaciones pueden asignar usuarios a los roles de la aplicación.

### <a name="add-users"></a>Agregar usuarios

Cada usuario debe tener una cuenta de usuario antes de poder iniciar sesión y acceder a la aplicación Azure IoT Central. Se admiten las cuentas de Microsoft (MSA) y las de Azure Active Directory (AAD) en Azure IoT Central. Actualmente no se admiten los grupos de Azure Active Directory en Azure IoT Central.

Para más información, consulte la [ayuda para cuentas de Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Inicio rápido: incorporación de nuevos usuarios a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para agregar una cuenta de usuario a una aplicación de Azure IoT Central, utilice el menú de navegación secundario para acceder a la página **Usuarios** de la sección **Administración**.

    ![Lista de usuarios](media\howto-administer\image1.png)

1. En la página **Usuarios**, elija **+ Agregar usuario** para agregar uno.

    ![Agregar usuario](media\howto-administer\image2.png)

1. Cuando agregue un usuario a la aplicación Azure IoT Central, elija un rol para el usuario en el menú desplegable **Rol**. Aprenda más sobre los roles en la sección *Roles en Azure IoT Central* de este artículo.

    ![Selección de roles](media\howto-administer\image3.png)

    > [!NOTE]
    >  Para agregar usuarios de forma masiva, indique los ID de usuario de todos los usuarios que desee agregar, separados por punto y coma. Elija un rol en la lista desplegable **Rol** y elija **Guardar**.

1. Después de agregar un usuario, aparece una entrada para ese usuario en la página **Usuarios**.

    ![Lista de usuarios](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Modificar los roles asignados a usuarios

Los roles no se pueden modificar una vez asignados. Para modificar el rol asignado a un usuario, elimine el usuario y agréguelo de nuevo con un rol diferente.

### <a name="delete-users"></a>Eliminación de usuarios

Para eliminar usuarios, active una o varias casillas en la página **Usuarios** y, a continuación, seleccione **Eliminar**.

## <a name="view-your-bill"></a>Ver la factura

Para ver la factura, vaya a la página **Facturación** en la sección **Administración** y elija **Facturación**. La página de facturación de Azure se abre en una nueva pestaña y puede ver la factura de cada una de las aplicaciones de Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Convertir la evaluación gratuita en una aplicación de pagada

Cuando haya evaluado IoT Central, puede convertir la evaluación gratuita en una aplicación pagada. Para completar el proceso de autoservicio, siga estos pasos:

1. Utilice el menú de navegación secundario para ir a la página **Facturación** en la sección **Administración**. Si no ha extendido la evaluación gratuita, la página tiene el siguiente aspecto:

    ![Estado de la evaluación gratuita](media/howto-administer/freetrial.png)

2. Haga clic en **Convert to Paid** (Convertir en pagada). Si no ha extendido la evaluación gratuita, el elemento emergente tiene el siguiente aspecto:

    En el elemento emergente, seleccione el inquilino apropiado de Azure Active Directory y, después, la suscripción a Azure que desea utilizar para la aplicación IoT Central.

    ![Extender la evaluación gratuita](media/howto-administer/extend.png)

3. Después de hacer clic en **Convertir**, la evaluación gratuita se convierte en una aplicación de pago y comienza a facturarse.

## <a name="extend-your-free-trial"></a>Ampliar la evaluación gratuita

De forma predeterminada, todas las evaluaciones gratuitas están disponibles durante 7 días. Si desea aumentar la evaluación gratuita a 30 días, siga estos pasos:

1. Utilice el menú de navegación secundario para ir a la página **Facturación** en la sección **Administración**.

1. Haga clic en **Ampliar prueba**. En el elemento emergente, seleccione el inquilino apropiado de Azure Active Directory y, después, la suscripción a Azure para utilizar en la aplicación IoT Central.

1. Después, haga clic en **Ampliar**. La versión de prueba ahora es válida durante 30 días.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar una aplicación de Azure IoT Central, este es el paso siguiente sugerido:

> [!div class="nextstepaction"]
> [Configuración de la plantilla de dispositivo](howto-set-up-template.md)