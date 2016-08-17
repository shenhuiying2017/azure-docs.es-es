<properties
   pageTitle="Acceso al panel del Portal de Azure | Microsoft Azure"
   description="En este artículo se explica cómo compartir el acceso a un panel en el Portal de Azure."
   services="azure-portal"
   documentationCenter=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="multiple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="tomfitz"/>

# Uso compartido de paneles de Azure

Después de configurar un panel, puede publicarlo y compartirlo con otros usuarios de su organización. Permita que otros usuarios tengan acceso al panel mediante el [Control de acceso basado en roles](../active-directory/role-based-access-control-configure.md) de Azure. Asigne un usuario o grupo de usuarios a un rol y ese rol definirá si los usuarios pueden ver o modificar el panel publicado.

Todos los paneles publicados se implementan como recursos de Azure, lo que significa que existen como elementos administrables dentro de su suscripción y se encuentran en un grupo de recursos. Desde una perspectiva de control de acceso, los paneles no son distintos de otros recursos como una máquina virtual o una cuenta de almacenamiento.

> [AZURE.TIP] Los iconos individuales del panel tendrán sus propios requisitos de control de acceso en función de los recursos que muestran. Esto significa que puede diseñar un panel que se comparta ampliamente mientras sigue protegiendo los datos de iconos individuales.

## Descripción del control de acceso para los paneles

Mediante el control de acceso basado en roles, puede asignar a usuarios a los roles en tres niveles distintos de ámbito:

- subscription
- resource group
- resource

Los permisos que asigne se heredan desde la suscripción hasta el recurso. El panel publicado es un recurso. Por lo tanto, es posible que ya tenga usuarios asignados a los roles de la suscripción que también funcionen para el panel publicado.

Aquí tiene un ejemplo. Supongamos que tiene una suscripción de Azure y que a varios miembros del equipo se les han asignado los roles de **propietario**, **colaborador** o **lector** de la suscripción. Los usuarios que son propietarios o colaboradores pueden enumerar, ver, crear, modificar o eliminar paneles dentro de la suscripción. Los usuarios que sean lectores pueden enumerar y ver los paneles, pero no pueden modificarlos ni eliminarlos. Los usuarios con acceso de lectura pueden realizar modificaciones locales en un panel publicado (por ejemplo, para solucionar un problema), pero no tienen la opción de publicar esos cambios de vuelta en el servidor. Podrán realizar una copia privada del panel para ellos mismos.

Sin embargo, también puede asignar permisos al grupo de recursos que contiene varios paneles o a un panel individual. Por ejemplo, puede decidir que un grupo de usuarios tenga permisos limitados a través de la suscripción pero un mayor acceso a un panel determinado. Asigne estos usuarios a un rol para ese panel.

## Publicación del panel

Supongamos que ha terminado de configurar un panel que desea compartir con un grupo de usuarios de la suscripción. Los pasos siguientes describen un grupo personalizado denominado Administradores de almacenamiento, pero puede llamar al grupo como desee. Para más información sobre cómo crear un grupo de Active Directory y agregar usuarios a ese grupo, consulte [Administración de grupos en Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

1. En el panel, seleccione **Compartir**.

     ![seleccionar recurso compartido](./media/azure-portal-dashboard-share-access/select-share.png)

2. Antes de asignar el acceso, debe publicar el panel. De forma predeterminada, el panel se publicará en un grupo de recursos denominado **paneles**. Seleccione **Publicar**.

     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Tras esto, el panel ya se ha publicado. Si son adecuados los permisos heredados de la suscripción, ya no es necesario hacer nada más. Otros usuarios de la organización podrán acceder y modificar el panel según su rol de nivel de suscripción. Sin embargo, para este tutorial, vamos a asignar un grupo de usuarios a un rol de ese panel.

## Asignación de acceso a un panel

1. Después de publicar el panel, seleccione **Administrar usuarios**.

     ![administrar usuarios](./media/azure-portal-dashboard-share-access/manage-users.png)

2. Verá una lista de los usuarios existentes que ya están asignados a un rol para este panel. La lista de usuarios existentes será diferente a la de la imagen siguiente. Probablemente, las asignaciones se heredan de la suscripción. Seleccione **Agregar** para agregar un usuario o grupo nuevo.

     ![agregar usuario](./media/azure-portal-dashboard-share-access/existing-users.png)

3. Seleccione el rol que representa los permisos que desea conceder. En este ejemplo, seleccione **Colaborador**.

     ![seleccionar rol](./media/azure-portal-dashboard-share-access/select-role.png)

4. Seleccione el usuario o grupo que desea asignar al rol. Si no ve el usuario o grupo que está buscando en la lista, utilice el cuadro de búsqueda. La lista de grupos disponibles dependerá de los grupos que haya creado en Active Directory.

     ![seleccionar usuario](./media/azure-portal-dashboard-share-access/select-user.png)

5. Cuando haya terminado de agregar usuarios o grupos, seleccione **Aceptar**.

6. La nueva asignación se agrega a la lista de usuarios. Observe que su **acceso** aparece como **Asignado** en lugar de **Heredado**.

     ![roles asignados](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## Pasos siguientes

- Para obtener una lista de roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).
- Para más información acerca de cómo administrar los recursos, consulte [Administración de los recursos de Azure a través del Portal](resource-group-portal.md).

<!---HONumber=AcomDC_0803_2016-->