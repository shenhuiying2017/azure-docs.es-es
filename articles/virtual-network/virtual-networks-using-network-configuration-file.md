<properties 
	pageTitle="Configuración de una red virtual con un archivo de configuración de red" 
	description="Instrucciones para exportar e importar un archivo de configuración de red en el Portal de administración de Azure para crear o modificar redes virtuales." 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags
	ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services" 
	ms.date="07/09/2015"
	ms.author="cherylmc"/>

# Configuración de una red virtual con un archivo de configuración de red

## Información general

Para configurar la red virtual, puede usar el Asistente del Portal de administración, o puede crear e importar un archivo de configuración de red. Azure usa el archivo de configuración de red para definir la configuración de red virtual.

Es preferible usar el Asistente del Portal de administración para crear inicialmente la configuración de red virtual y, a continuación, realizar cambios posteriores directamente en el archivo de configuración. Por ejemplo, si está configurando varias redes virtuales para suscripciones independientes, puede querer crear primero un archivo de configuración de red con el Asistente del Portal de administración. Puede exportar el archivo para utilizarlo como plantilla, editarlo para especificar una configuración diferente y, a continuación, importar el archivo en el Portal de administración. Puede tratarse de una manera eficaz de crear varias redes virtuales cuando tiene más de una suscripción.

O bien, si desea realizar cambios en los ajustes de configuración de red antes de implementar servicios en la nube o máquinas virtuales en la red, puede exportar el archivo, modificarlo y, a continuación, importarlo de nuevo en Azure. También puede utilizar un archivo de configuración de red para hacer una copia de seguridad de las opciones de configuración de red si desea recrear la red virtual.

## Creación y modificación de un archivo de configuración de red 
La manera más fácil de crear un archivo de configuración de red es exportar la configuración de red desde una configuración de red virtual existente, modificar el archivo para que contenga los ajustes con los que desea configurar las redes virtuales. También puede obtener un archivo de ejemplo y modificarlo.

Para editar el archivo de configuración de red, puede simplemente abrir el archivo, realzar los cambios adecuados y guardarlos. Puede usar cualquier editor *xml* para realizar cambios en el archivo de configuración de red.

Se deben seguir meticulosamente las instrucciones sobre los ajustes del esquema del archivo de configuración de red. Al crear el archivo de configuración de red, la configuración del archivo sobrescribirá la configuración que tenga actualmente para esa suscripción en Azure. Si realiza cambios en los valores en el archivo que no sean compatibles con las instrucciones de configuración, la red virtual podría no configurarse de la manera prevista y, en algunos casos, Azure no le permitirá importar el archivo. Para obtener información acerca de la configuración concreta contenida en un archivo de configuración de red, consulte [Esquema de configuración de redes virtuales de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Azure considera que una subred que tiene algo implementado en ella como "en uso". Cuando una subred está en uso, no puede modificarse. Antes de la modificación, mueva todo lo que haya implementado en la subred a una subred diferente que no se esté modificando. Consulte [Mover una máquina virtual o instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md).



## Exportación e importación de la configuración de red virtual mediante el Portal de administración  
Puede importar y exportar las opciones de configuración de una red contenidas en el archivo de configuración de red mediante PowerShell o el Portal de administración. Las instrucciones siguientes le ayudarán a exportar e importar con el Portal de administración.

### Para exportar la configuración de red
Al exportar, todos los ajustes de las redes virtuales en su suscripción se escribirán en un archivo .xml.

1. Inicie sesión en el **Portal de administración**.
2. En el Portal de administración, en la parte inferior de la página de **redes**, haga clic en **Exportar**. 
3. En la ventana **Exportación de la configuración de red**, compruebe que ha seleccionado la suscripción para la que desea exportar la configuración de red. A continuación, haga clic en la marca de verificación de la esquina inferior derecha. 
4. Cuando se le pida, guarde el archivo *NetworkConfig.xml* en la ubicación que prefiera.


### Para importar la configuración de red

1. En el **Portal de administración**, en el panel de navegación de la parte inferior izquierda, haga clic en **Nuevo**.
2. Haga clic en **Servicios de red** -> **Red virtual** -> **Importar configuración**.
3. En la página **Importación del archivo de configuración de red**, busque el archivo de configuración de red y, a continuación, haga clic en la flecha **Siguiente**.
4. En la página **Creación de la red**, verá información en la pantalla que muestra las secciones de la configuración de red que cambiarán o se crearán. Si está de acuerdo con los cambios, haga clic en la marca de verificación para continuar con la actualización o la creación de la red virtual. 


## Pasos siguientes
Para obtener más información acerca de la configuración de redes virtuales, consulte:

-  [Esquema de configuración de red: configuración opcional de servicios en la nube](https://msdn.microsoft.com/library/azure/jj156091.aspx)
-  [Información acerca de la configuración de red virtual del Portal de administración](https://msdn.microsoft.com/library/azure/jj156074.aspx)
-  [Preguntas frecuentes sobre redes virtuales](https://msdn.microsoft.com/library/azure/dn133803.aspx)





 

<!---HONumber=July15_HO2-->