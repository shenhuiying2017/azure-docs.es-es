---
title: "Creación y uso compartido de paneles en Azure Portal | Microsoft Docs"
description: "En este artículo se explica cómo crear y editar paneles en el Portal de Azure."
services: azure-portal
documentationcenter: 
author: sewatson
manager: timlt
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 7f90c882285170bac34bfe020831d3ac04010a11
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Creación y uso compartido de paneles en Azure Portal
Puede crear varios paneles y compartirlos con otras personas que tengan acceso a sus suscripciones de Azure.  En este artículo se explican los aspectos básicos de la administración del acceso a los paneles, así como de su creación, edición y publicación.

## <a name="create-a-dashboard"></a>Creación de un panel
Para crear un panel, haga clic en el botón **Panel nuevo** situado junto al nombre del panel actual.  

![crear panel](./media/azure-portal-dashboards/new-dashboard.png)

Esta acción crea un panel nuevo, vacío y privado, y activa el modo de personalización, donde puede asignar un nombre al panel y agregar o reorganizar los iconos.  En este modo, la galería de iconos contraíbles se encuentra encima del menú de navegación izquierdo.  La galería de iconos permite buscar iconos para los recursos de Azure de varias maneras: se puede explorar por [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), por tipo de recurso, por [etiqueta](../azure-resource-manager/resource-group-using-tags.md) o se puede buscar el recurso por su nombre.  

![personalizar panel](./media/azure-portal-dashboards/customize-dashboard.png)

Agregue iconos arrastrándolos y colocándolos en la parte que quiera de la superficie del panel.

Hay una nueva categoría denominada **General** para los iconos que no están asociados a ningún recurso concreto.  En este ejemplo, anclamos el icono de Markdown.  Este icono se utiliza para agregar contenido personalizado al panel.  Además, admite texto sin formato, [sintaxis de Markdown](https://daringfireball.net/projects/markdown/syntax) y un conjunto limitado de HTML.  (por motivos de seguridad, no se pueden realizar tareas como insertar etiquetas `<script>` o utilizar determinados elementos de estilo de CSS que podrían interferir en el portal). 

![agregar Markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Edición de paneles
Después de crear el panel, puede anclar iconos desde la galería de iconos o la representación de icono de las hojas. Vamos a anclar la representación de nuestro grupo de recursos. Puede hacerlo buscando el elemento o desde la hoja del grupo de recursos. Con los dos métodos se ancla la representación de icono del grupo de recursos.

![Anclar al panel](./media/azure-portal-dashboards/pin-to-dashboard.png)

Después de anclar el elemento, se muestra el panel.

![ver panel](./media/azure-portal-dashboards/view-dashboard.png)

Ahora que tenemos un icono de Markdown y un grupo de recursos anclado al panel, podemos cambiar el tamaño de los iconos y reorganizarlos en un diseño más adecuado.

Al pasar el mouse sobre los puntos suspensivos y seleccionarlos, o bien al hacer clic con el botón derecho en un icono, se podrán ver todos los comandos contextuales de dicho icono. De forma predeterminada, hay dos elementos:

1. **Desanclar del panel** : quita el icono de panel.
2. **Personalizar** : se activa el modo de personalización.

![personalizar icono](./media/azure-portal-dashboards/customize-tile.png)

Al seleccionar Personalizar, puede cambiar el tamaño de los iconos y reordenarlos. Para cambiar el tamaño de un icono, seleccione el nuevo tamaño en el menú contextual, tal y como se muestra en la siguiente imagen.

![cambiar tamaño del icono](./media/azure-portal-dashboards/resize-tile.png)

También, si el icono admite cualquier tamaño, puede arrastrar la esquina inferior derecha hasta que tenga el tamaño que quiera.

![cambiar tamaño del icono](./media/azure-portal-dashboards/resize-corner.png)

Después de cambiar el tamaño de los iconos, visualice el panel.

![ver icono](./media/azure-portal-dashboards/view-tile.png)

Cuando haya terminado de personalizar un panel, basta con seleccionar **Personalización finalizada** para salir del modo de personalización o hacer clic con el botón derecho y seleccionar **Personalización finalizada** en el menú contextual.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publicación de un panel y administración del control de acceso
Al crear un panel, será privado de forma predeterminada, lo que significa que usted será la única persona que puede verlo.  Para que puedan verlo otros usuarios, utilice el botón **Compartir** que aparece junto al resto de los comandos del panel.

![compartir panel](./media/azure-portal-dashboards/share-dashboard.png)

Se le pedirá que elija una suscripción y un grupo de recursos en el que se vaya a publicar el panel. Para integrar sin problemas paneles en el ecosistema, hemos implementado los paneles compartidos como recursos de Azure (por lo que no se pueden compartir escribiendo una dirección de correo electrónico).  El acceso a la información que muestra la mayoría de los iconos del portal lo rige el [control de acceso basado en rol de Azure](../active-directory/role-based-access-control-configure.md). Desde una perspectiva de control de acceso, los paneles compartidos no son distintos de una máquina virtual o de una cuenta de almacenamiento.  

Supongamos que tiene una suscripción de Azure y que a los miembros del equipo se les han asignado los roles de **propietario**, **colaborador** o **lector** de la suscripción.  Los usuarios que son propietarios o colaboradores pueden enumerar, ver, crear, modificar o eliminar paneles dentro de la suscripción.  Los usuarios que sean lectores pueden enumerar y ver los paneles, pero no pueden modificarlos ni eliminarlos.  Los usuarios con acceso de lectura pueden realizar modificaciones locales en un panel compartido, pero no tienen la opción de volver a publicar esos cambios en el servidor.  Sin embargo, pueden realizar una copia privada del panel para utilizarlo con fines privados.  Como siempre, los iconos individuales del panel tendrán sus propias reglas de control de acceso en función de los recursos a los que correspondan.  

Para mayor comodidad, la experiencia de publicación del portal lo guiará por un patrón donde colocará paneles en un grupo de recursos denominado " **paneles**".  

![publicar panel](./media/azure-portal-dashboards/publish-dashboard.png)

También puede decidir publicar un panel en un grupo de recursos concreto.  El control de acceso de ese panel coincide con el del grupo de recursos.  Los usuarios que pueden administrar los recursos de ese grupo de recursos también tienen acceso a los paneles.

![publicar panel en grupo de recursos](./media/azure-portal-dashboards/publish-to-resource-group.png)

Cuando se publique el panel, **Uso compartido y control de acceso** se actualice y muestra la información sobre el panel publicado, incluido un vínculo para administrar el acceso de los usuarios al panel.  Este vínculo abre la hoja estándar de control de acceso basado en roles que se utiliza para administrar el acceso a cualquier recurso de Azure.  En cualquier momento se puede volver a esta vista haciendo clic en **Compartir**.

![administrar control de acceso](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Pasos siguientes
* Para administrar los recursos, consulte [Administración de los recursos de Azure a través del Portal](../azure-resource-manager/resource-group-portal.md).
* Para implementar recursos, consulte [Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

