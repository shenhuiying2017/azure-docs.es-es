<properties
	pageTitle="Adición de propietarios y usuarios a un laboratorio | Microsoft Azure"
	description="Agregue de forma segura un usuario que no esté en su suscripción a Azure DevTest Labs."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="tarcher"/>

# Adición de propietarios y usuarios a un laboratorio

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

## Información general
El acceso a DevTest Labs se controla mediante el control de acceso basado en rol (RBAC) de Azure. Busque [Control de acceso basado en rol (RBAC)](https://azure.microsoft.com/search/?q=role%20based%20access%20control) en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) para más información.


Se concede acceso al laboratorio a través de dos roles:

- **Propietario**: los usuarios asignados al rol **Propietario** en el nivel de laboratorio tienen acceso completo al laboratorio, incluidas las funciones de administración y supervisión. El rol **Propietario** asignado en el nivel de laboratorio no otorga a los usuarios permisos para obtener acceso a los recursos de la suscripción fuera del ámbito del laboratorio. Los usuarios asignados al rol **Propietario** en el nivel de suscripción a Azure tienen automáticamente derechos de **Propietario** en cualquier recurso creado en esa suscripción, lo que incluye laboratorios y máquinas virtuales.

-  **Usuario de DevTest Labs**: los usuarios asignados al rol **Usuario de DevTest Labs** pueden crear máquinas virtuales en el laboratorio especificado, así como ver todos los recursos de laboratorio, como máquinas virtuales, directivas y redes virtuales. Los usuarios pueden ser *internos* (miembro de Azure Active Directory para la suscripción) o *externos* (usuario que no es miembro de Azure AD, como un miembro de una organización asociada).
	-  Un rol **Usuario de DevTest Labs** debe asignarse a través de los iconos **Agregar usuarios** del laboratorio.
	-  Los usuarios del rol **Usuario de DevTest Labs** pueden realizar estas operaciones solo dentro del laboratorio al que están asignados. Por ejemplo, un **usuario de DevTest Labs** no puede crear una máquina virtual mediante el servicio de máquina virtual de la suscripción. La creación de una máquina virtual solo se permite desde la cuenta de DevTest Labs.
	- Un usuario *Externo* es un usuario con una cuenta Microsoft (MSA).
 
Una vez que se crea una máquina virtual, el usuario que la crea se asigna automáticamente al rol de **Propietario** en esa máquina, de forma que puede realizar todas las acciones que se ofrecen en el laboratorio.

## Adición de un propietario al laboratorio

Debido a la manera en que los permisos se propagan desde el ámbito primario al ámbito secundario en Azure, los propietarios de una suscripción de Azure que contenga laboratorios serán automáticamente los propietarios de esos laboratorios, así como de todas las máquinas virtuales y otros recursos que crean los usuarios del laboratorio y el servicio DevTest Labs. Aunque puede agregar más propietarios a un laboratorio mediante la hoja de este en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), su ámbito de administración será más limitado que el de los propietarios de la suscripción, ya que no tendrán acceso completo a algunos de los recursos que el servicio DevTest Labs crea en la suscripción.

Para agregar un propietario a una suscripción de Azure donde tiene laboratorios ya creados o va a crear nuevos laboratorios, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. En el panel de navegación izquierdo, seleccione **Suscripciones**.

	![Vínculo de suscripciones](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Seleccione la suscripción que contendrá los laboratorios.

1. Seleccione el icono **Acceso**.

	![Usuarios de acceso](./media/devtest-lab-add-devtest-user/access-users.png)

1. Seleccione **Agregar** en la hoja **Usuarios**.

	![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. En la hoja **Seleccionar un rol**, seleccione **Propietario**.

1. En el cuadro de texto **Usuario** escriba el correo electrónico del usuario que desea agregar como propietario. Si no se encuentra el usuario, aparecerá un mensaje de error que explica el problema. Si se encuentra el usuario, dicho usuario se mostrará en el cuadro de texto **Usuario**.

1. Seleccione el nombre de usuario encontrado.

1. Elija **Seleccionar**.

1. Seleccione **Aceptar** para cerrar la hoja **Agregar acceso**.

1. Cuando vuelva a la hoja **Usuarios**, verá que el usuario se ha agregado como propietario. Esta persona ahora será un propietario de cualquier laboratorio creado con esta suscripción y, por lo tanto, podrá realizar tareas de propietario.

## Adición de un usuario de DevTest Labs al laboratorio

Para agregar un usuario de DevTest Labs al laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**.

1. Seleccione **DevTest Labs**.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. Seleccione el icono **Acceso**.

	![Acceso de usuario](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Seleccione **Agregar** en la hoja **Usuarios**.

	![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. En la hoja **Seleccionar un rol**, seleccione **DevTest Labs User** (Usuario de DevTest Labs).

1. En la hoja **Agregar usuarios**:

	1. La hoja **Agregar usuarios** mostrará una lista de usuarios integrados. Si el usuario que desea ya está en la lista, basta con seleccionar la fila del usuario para seleccionarlo. Aparecerá una marca de verificación a la izquierda del usuario para indicar que este se ha seleccionado. Para seleccionar varios usuarios, mantenga presionada la tecla **&lt;Ctrl>** mientras selecciona cada usuario. Para anular la selección de un usuario, mantenga presionada la tecla **&lt;Ctrl>** y seleccione el usuario. Un contador en la parte inferior de la hoja indica el número de usuarios seleccionados.

	1. Si el usuario que desea no está en la lista, escriba una cuenta de correo electrónico de Microsoft válida en el cuadro de texto **Usuarios**. Si la dirección de correo electrónico es válida, el usuario se mostrará debajo del cuadro de texto **Usuario**.

	1. Cuando haya seleccionado los usuarios que desea agregar al laboratorio, elija **Seleccionar**.

	1. Seleccione **Aceptar** para cerrar la hoja **Agregar acceso**.

1. La hoja **Usuarios** muestra los roles y usuarios agregados.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

<!---HONumber=AcomDC_0831_2016-->