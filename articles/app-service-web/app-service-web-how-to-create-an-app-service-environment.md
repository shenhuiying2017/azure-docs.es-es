<properties 
	pageTitle="Creación de un entorno del Servicio de aplicaciones" 
	description="Descripción de flujo de creación de entornos de servicio de aplicación" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Creación de un entorno del Servicio de aplicaciones #

Los entornos del Servicio de aplicaciones (ASE) son una opción del servicio Premium del Servicio de aplicaciones de Azure que se encuentra actualmente en vista previa. Ofrece una capacidad de configuración mejorada que no está disponible en las marcas multiempresa. Para comprender mejor las capacidades ofrecidas por los entornos del Servicio de aplicaciones lea la [¿Qué es un entorno del Servicio de aplicaciones][WhatisASE].

### Información general ###

La característica de ASE esencialmente implementa el Servicio de aplicaciones de Azure en la red virtual de un cliente. Para ello, el cliente necesita:

- Una red virtual regional con 512 (/23) direcciones o más
- Una subred en esta red virtual con 256 (/ 24) direcciones o más
- La subred ** no debe contener otros recursos informáticos**. Solo se puede implementar un entorno del Servicio de aplicaciones en una subred. El intento de creación dará error si hay otros recursos informáticos que ya residan en la subred.

Si aún no tiene una red virtual que desea usar para hospedar el entorno del Servicio de aplicaciones, puede crear una durante la creación del entorno del Servicio de aplicaciones.

Cada implementación de ASE es un servicio hospedado que Azure administra y mantiene. Los recursos de proceso que hospedan los roles del sistema ASE no son accesibles para el cliente aunque este administre la cantidad de instancias y sus tamaños.

## Creación del entorno del Servicio de aplicaciones ##

Hay dos maneras de obtener acceso a la IU de creación de ASE. Puede encontrarlo buscando ***entorno del Servicio de aplicaciones*** en Azure Marketplace o a través de Nuevo -> Web + Móvil.

### Creación rápida ###
Después de entrar en la IU de creación puede crear rápidamente un ASE simplemente escribiendo un nombre para la implementación. A su vez se creará una red virtual con 512 direcciones, una subred con 256 direcciones en esa red virtual y un entorno ASE con 2 servidores front-end y lo 2 trabajos en el grupo de trabajo 1. Asegúrese de seleccionar la ubicación donde desea que se encuentre el sistema y la suscripción en la que desee que esté. Las únicas cuentas que puede usar el ASE para hospedar contenido deben encontrarse en la suscripción utilizada para crearlo.

El nombre especificado para el ASE se utilizará para las aplicaciones web creadas en el ASE. Si el nombre del ASE es appsvcenvdemo, el nombre de dominio sería .*appsvcenvdemo.p.azurewebsites.net*. Si creó una aplicación web llamada mytestapp, sería podría obtener acceso a ella a través de la dirección *mytestapp.appsvcenvdemo.p.azurewebsites.net*. No puede usar espacios en blanco en el nombre. Si utiliza caracteres en mayúsculas en el nombre, el nombre de dominio será la versión en minúsculas total de ese nombre.


![][1]

### Grupos de recursos de proceso ###

Los recursos de proceso que se usan para el entorno del Servicio de aplicaciones se administran en grupos de recurso de proceso, lo que permite configurar las instancias de recurso de proceso que tiene en el grupo además de su tamaño. Un entorno del Servicio de aplicaciones consta de servidores front-end y trabajos. Los servidores front-end manejan la carga de conexiones de la aplicación y los trabajos ejecutan el código de aplicación. Los servidores front-end se administran en un grupo de recursos de proceso dedicado. A su vez, los trabajos se administran en 3 grupos de recursos de proceso independientes llamados

- Grupo de trabajo 1
- Grupo de trabajo 2
- Grupo de trabajo 3

Si tiene un gran número de solicitudes para las aplicaciones web sencillas probablemente escalaría los servidores front-end y tendría menos trabajos. Si tuviera aplicaciones web de uso intensivo de CPU o de memoria con tráfico ligero, entonces no necesitaría muchos servidores front-end, pero es probable que necesitara más trabajos o trabajos más grandes.

Independientemente del tamaño de los recursos de proceso, el espacio mínimo tiene 2 servidores front-end y 2 trabajos. Un entorno del Servicio de aplicaciones se puede configurar para usar hasta 55 recursos de proceso en total. De esos 55 recursos de proceso, solamente 50 se pueden usar para hospedar cargas de trabajo. El motivo de esto es doble. Hay un mínimo de 2 recursos de proceso front-end. Esto deja hasta 53 para admitir asignación de grupos de trabajo. Para proporcionar tolerancia a errores, necesita tener un recurso de proceso adicional asignado según las reglas siguientes:

- cada grupo de trabajo necesita al menos un recurso de proceso adicional que no se pueden asignar a cargas de trabajo
- cuando la cantidad de recursos de proceso de un grupo supera un determinado valor, se necesita otro recurso de proceso

Dentro de cualquier grupo de trabajo los requisitos de tolerancia a errores son que para un determinado valor de X recursos asignados a un grupo de trabajo:

- Si el valor de X está comprendido entre 2 y 20, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-1
- Si el valor de X está comprendido entre 21 y 40, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-2
- Si el valor de X está comprendido entre 41 y 53, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-3

Además de ser capaz de administrar la cantidad de recursos de proceso que se pueden asignar a un grupo determinado, también tiene control sobre el tamaño. Con entornos del Servicio de aplicaciones puede elegir 4 tamaños diferentes (etiqueta P1 a P4). Para obtener detalles sobre los tamaños y sus precios, vea [Precios de Servicio de aplicaciones][AppServicePricing]. Los tamaños de los recursos de proceso P1 a P3 son los mismos que los que están disponibles en los entornos multiempresa. El recurso de proceso P4 proporciona 8 núcleos con 14 GB de RAM y solamente está disponible en un entorno del Servicio de aplicaciones.

El precio de los entornos del Servicio de aplicaciones está en contra de los recursos de proceso asignados. Se paga por los recursos de proceso asignados a su entorno del Servicio de aplicaciones independientemente de si hospedan cargas de trabajo o no.



### Creación de una red virtual ###
Aunque existe la capacidad de creación rápida que creará automáticamente una nueva red virtual, la característica también admite la selección de una red virtual existente y la creación manual de una red virtual. Puede seleccionar una red virtual existente si es lo suficientemente grande como para admitir la implementación de un entorno del Servicio de aplicaciones. La red virtual debe tener 512 direcciones o más. Si selecciona una red virtual que ya existía también tendrá que especificar una subred para usar o crear una nuevo. La subred debe tener 256 direcciones o más.

Si usa la IU de creación de red virtual, tiene que proporcionar lo siguiente:

- Nombre de red virtual
- Intervalo de direcciones de red virtual en la notación CIDR
- Nombre de subred
- Intervalo de subred en la notación CIDR

Si no está familiarizado con la notación CIDR, sepa que adopta la forma 10.0.0.0/22 donde /22 especifica el intervalo. En este ejemplo, /22 significa un intervalo de 1.024 direcciones o desde 10.0.0.0-10.0.3.255. /23 significa 512 direcciones, y así sucesivamente.

![][2]

### Definición de tamaño del entorno del Servicio de aplicaciones ###

El siguiente elemento para configurar es la escala del sistema. De forma predeterminada, hay 2 recursos de proceso de P2 de front-end, 2 trabajos de P1 y 1 dirección IP. Existen 2 servidores front-end con el fin de proporcionar alta disponibilidad y distribuir la carga. El tamaño mínimo para los front-end es P2 para garantizar que tienen suficiente capacidad para admitir un sistema modesto. Si sabe que el sistema necesita admitir un gran número de solicitudes, puede ajustar la cantidad de servidores front-end y el tamaño de servidor usado.

Como se indicó anteriormente, dentro de un ASE hay 3 grupos de trabajo que puede definir un cliente. El tamaño de los recursos de proceso puede ser de P1 a P4. De forma predeterminada solo hay 2 trabajos de P1 configurados en el grupo de recursos 1. Esto es suficiente para admitir un único plan del Servicio de aplicaciones con 1 instancia.

Los controles deslizantes se ajustan automáticamente para reflejar la capacidad de proceso total disponible en el entorno del Servicio de aplicaciones. Como los controles deslizantes se ajustan dentro de cualquier grupo, los otros controles deslizantes cambian para reflejar la cantidad disponible de recursos de proceso que quedan antes de llegar a 55.
 
![][3]

La adición de nuevas instancias para que estén disponibles no es un proceso rápido. Si sabe que va a necesita recursos de proceso adicionales, debe aprovisionarlos con antelación. El tiempo de aprovisionamiento pueden tardar varias horas dependiendo de la cantidad que se va a agregar al sistema. Recuerde que para asegurarse de que el sistema cumplir los requisitos de tolerancia a errores, cada ASE debe tener una instancia de reserva disponible en cada grupo de trabajo.

De forma predeterminada, un ASE viene con 1 dirección IP que está disponible para SSL de IP. Si sabe que va a necesitar más puede especificarlo aquí o administrarlo después de la creación.
  
### Después de la creación del entorno del Servicio de aplicaciones ###

Después de la creación de ASE puede ajustar:

- La cantidad de servidores front-end (mínimo: 2)
- La cantidad de trabajos (mínimo: 2)
- La cantidad de direcciones IP
- Los tamaños de recursos de proceso usados por los servidores front-end o los trabajos (el tamaño mínimo de front-end es P2)

No puede cambiar:

- La ubicación
- La suscripción
- El grupos de recursos
- La red virtual usada
- La subred usada

Aquí encontrará más detalles sobre la administración y supervisión de entornos del Servicio de aplicaciones: [Configuración de un entorno del Servicio de aplicaciones][ASEConfig].

Existen dependencias adicionales que no están disponibles para personalización, como la base de datos y el almacenamiento. Estas son controlados por Azure y se incluyen con el sistema. El almacenamiento del sistema admite hasta 500 GB para todo el entorno del Servicio de aplicaciones.


## Introducción

Para empezar a trabajar con entornos del Servicio de aplicaciones, vea [Introducción a los entornos del Servicio de aplicaciones][WhatisASE].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/

<!---HONumber=July15_HO3-->