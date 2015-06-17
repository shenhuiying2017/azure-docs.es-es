<properties
   pageTitle="Deshabilitación, habilitación o eliminación de un perfil de Administrador de tráfico"
   description="Este artículo le ayudará a trabajar con sus perfiles del Administrador de tráfico."
   services="traffic-manager"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />

# Deshabilitación, habilitación o eliminación de un perfil

## Deshabilitación o habilitación de un perfil

Puede deshabilitar un perfil de Administrador de tráfico existente para que no envíe solicitudes de usuario a sus extremos configurados. Al deshabilitar un perfil de Administrador de tráfico, dicho perfil y la información contenida en el perfil se mantendrán intactos y se pueden modificar en la interfaz de Administrador de tráfico. Si desea volver a habilitar el perfil, puede hacerlo fácilmente en el Portal de administración y se reanudarán las referencias. Cuando se crea un perfil de Administración de tráfico en el Portal de administración, se habilita automáticamente.

### Para deshabilitar un perfil

1. Modifique el registro de recursos DNS en el servidor DNS de Internet para usar el tipo de registro adecuado y un puntero a otro nombre o dirección IP de una ubicación específica en Internet. En otras palabras, debe cambiar el registro de recursos DNS en el servidor DNS de Internet para que no utilice más un registro de recursos de CNAME que señala al nombre de dominio de su perfil de Administrador de tráfico.
1. El tráfico dejará de dirigirse a los extremos a través de la configuración del perfil de Administrador de tráfico.
1. Seleccione el perfil que desea deshabilitar. Para seleccionar el perfil en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre de perfil. No haga clic en el nombre del perfil o en la flecha junto al nombre, ya que le llevará a la página de configuración del perfil.
1. Después de seleccionar el perfil, haga clic en Deshabilitar en la parte inferior de la página.

### Para habilitar un perfil

1. Seleccione el perfil que desea habilitar. Para seleccionar el perfil en la página Administrador de tráfico, resalte el perfil haciendo clic en la columna situada junto al nombre de perfil. No haga clic en el nombre del perfil o en la flecha junto al nombre, ya que le llevará a la página de configuración del perfil.
1. Después de seleccionar el perfil, haga clic en Habilitar en la parte inferior de la página.
1. Modifique el registro de recursos DNS en el servidor DNS de Internet para usar el tipo de registro CNAME, que asigna el nombre de dominio de la compañía en el nombre de dominio de su perfil de Administrador de tráfico. Para obtener más información, consulte [Redireccionamiento de un dominio de Internet de la compañía a un dominio del Administrador de tráfico](../point-a-company-internet-domain-to-a-traffic-manager-domain.md).
1. El tráfico comenzará a dirigirse de nuevo a los extremos.

## Eliminación de un perfil


### Para eliminar un perfil

1. Asegúrese de que el registro de recursos de DNS en el servidor DNS de Internet ya no utiliza un registro de recursos de CNAME que señala al nombre de dominio de su perfil de Administrador de tráfico.
1. Seleccione el perfil que desea eliminar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil 
1. haciendo clic en la columna situada junto al perfil. No haga clic en el nombre del perfil o en la flecha junto al nombre, ya que le llevará a la página de configuración del perfil.
1. Después de seleccionar el perfil, haga clic en Eliminar en la parte inferior de la página.

## Otras referencias

[Administrador de tráfico](../traffic-manager.md)

[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)



<!--HONumber=49--> 