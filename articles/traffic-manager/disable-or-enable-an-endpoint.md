<properties
   pageTitle="Deshabilitación o habilitación de un extremo de Administrador de tráfico"
   description="Este artículo le ayudará a deshabilitar o habilitar los extremos del perfil de Administrador de tráfico."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/10/2015"
   ms.author="joaoma" />

# Deshabilitación o habilitación de un extremo de Administrador de tráfico

También puede deshabilitar los extremos individuales que forman parte de un perfil del Administrador de tráfico. Los extremos incluyen servicios en la nube y sitios web. Al deshabilitar un extremo, se deja como parte del perfil, pero el perfil actúa como si el extremo no estuviera incluido en él. Esta acción es muy útil para quitar temporalmente un extremo que se encuentre en modo de mantenimiento o que se vaya a implementar. Cuando el extremo esté activo y ejecutándose, se puede habilitar

>[AZURE.NOTE]**Deshabilitar un extremo no tiene nada que ver con su estado de implementación en Azure. Seguirá activo un extremo correcto que podrá recibir tráfico incluso cuando se deshabilite en el Administrador de tráfico. Además, al deshabilitar un extremo de un perfil, no se afecta al estado en otro perfil.**

## Para deshabilitar un extremo

1. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración del extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
1. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que se incluyen en la configuración. 
1. Haga clic en el extremo que desea deshabilitar y, a continuación, haga clic en **Deshabilitar** en la parte inferior de la página.
1. El tráfico dejará de fluir hacia el extremo basado en el período de vida (TTL) de DNS configurado para el nombre de dominio del Administrador de tráfico. Puede cambiar el valor de TTL desde la página de configuración del perfil del Administrador de tráfico.

## Para habilitar un extremo


1. En el panel Administrador de tráfico del Portal de administración, localice el perfil del Administrador de tráfico que contiene la configuración del extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
1. En la parte superior de la página, haga clic en **Extremos** para ver los extremos que se incluyen en la configuración.
1. Haga clic en el extremo que desea habilitar y, a continuación, haga clic en **Habilitar** en la parte inferior de la página.
1. El tráfico comenzará a fluir al servicio de nuevo como indica el perfil.

## Pasos siguientes

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Información general sobre el Administrador de tráfico](../traffic-manager.md)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)


[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)
 

<!---HONumber=July15_HO4-->