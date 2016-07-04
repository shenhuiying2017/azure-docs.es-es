<properties
	pageTitle="Uso compartido y control de acceso para los paneles de Azure | Microsoft Azure"
	description="Aprenda a administrar el uso compartido y el control de acceso para los paneles de Azure."
	services="azure-resource-manager,azure-portal"
    keywords="portal,share,access"
	documentationCenter=""
	authors="adamabdelhamed"
	manager="dend"
	editor="dend"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="adamab"/>
    
# Uso compartido y control de acceso para los paneles de Azure

El acceso a la información que muestra la mayoría de los iconos del portal se rige mediante el [Control de acceso basado en roles](./active-directory/role-based-access-control-configure.md) de Azure. Con el fin de integrar perfectamente los paneles en el ecosistema, todos los paneles publicados se implementan como recursos de Azure. Desde una perspectiva de control de acceso, los paneles no son distintos de una máquina virtual o de una cuenta de almacenamiento.

Aquí tiene un ejemplo. Supongamos que tiene una suscripción de Azure y que a varios miembros del equipo se les han asignado los roles de **propietario**, **colaborador** o **lector** de la suscripción. Los usuarios que son propietarios o colaboradores podrán enumerar, ver, crear, modificar o eliminar paneles dentro de la suscripción. Los usuarios que sean lectores podrán enumerar y ver los paneles, pero no podrán modificarlos ni eliminarlos. Los usuarios con acceso de lectura podrán realizar modificaciones locales en un panel publicado (por ejemplo, para solucionar un problema), pero no tendrán la opción de volver a publicar esos cambios en el servidor. Podrán realizar una copia privada del panel para ellos mismos.

Tenga en cuenta que los iconos individuales del panel tendrán sus propios requisitos de control de acceso en función de los recursos para los que están mostrando los datos. Esto significa que puede diseñar un panel que se puede compartir más ampliamente mientras sigue protegiendo los datos de iconos individuales.

<!---HONumber=AcomDC_0622_2016-->