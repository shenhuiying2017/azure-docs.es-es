<properties 
	pageTitle="Configuración de un entorno del Servicio de aplicaciones" 
	description="Configuración, administración y supervisión de entornos del Servicio de aplicaciones" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2016" 
	ms.author="ccompy"/>


# Configuración de un entorno del Servicio de aplicaciones #

## Información general ##

A un alto nivel, un entorno del Servicio de aplicaciones consta de varios componentes principales:

- Recursos de proceso que se ejecutan en el servicio hospedado del entorno del Servicio de aplicaciones de Azure
- Almacenamiento
- Base de datos
- Una red virtual clásica "v1" con al menos una subred
- Subred con el servicio hospedado del entorno del Servicio de aplicaciones de Azure que se ejecuta en él

### Recursos de proceso

Se usan los recursos de proceso para los cuatro grupos de recursos. Cada entorno del Servicio de aplicaciones tiene un conjunto de servidores front-end y tres posibles grupos de trabajo. No es necesario usar los tres grupos de trabajo y, si lo desea, puede usar solo uno. Los grupos de recursos, los servidores front-end y los trabajos no son accesibles directamente a los inquilinos. No puede usar RDP en ellos, cambiar su aprovisionamiento o actuar como administrador sobre ellos, pero puede establecer su cantidad y su tamaño. En un ASE tiene 4 opciones de tamaño cuyos nombres van de P1 a P4. Para obtener detalles sobre los tamaños y sus precios, vea [Precios de Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md). Solo puede haber una operación de escalado en curso en un momento determinado.

**Front-end** Los servidores front-end son los puntos de conexión HTTP/HTTPS para las aplicaciones que se mantienen en el ASE. No se ejecutan cargas de trabajo en los servidores front-end.

- Un ASE comienza con (2) P2, lo cual es suficiente para cargas de trabajo de desarrollo y pruebas y cargas de trabajo de producción de bajo nivel. Los P3 están muy recomendados para cargas de trabajo de producción intensas.
- Para las cargas de trabajo de moderadas a intensas, se recomienda contar con al menos 4 x P3 para garantizar que haya suficientes front-end en ejecución cuando se produzca el mantenimiento programado. Las actividades de mantenimiento programado desactivan (1) front-end cada vez, reduciendo de este modo la capacidad global de los front-ends disponibles durante las actividades de mantenimiento.
- No puede agregar una nueva instancia de front-end de manera instantánea. Puede tardar entre 2 y 3 horas en aprovisionarse.
- Para afinar más en el escalado, los clientes deben supervisar el porcentaje de CPU, el porcentaje de memoria y las métricas de solicitudes activas para el grupo de servidores front-end. Si el porcentaje de CPU o memoria está por encima del 70% cuando se ejecutan los P3, agregue más servidores front-end. Si las solicitudes activas están en promedio entre 15.000 y 20.000 solicitudes por front-end, agregue también más servidores front-end. El objetivo general es mantener los porcentajes de CPU y memoria por debajo del 70% y el promedio de solicitudes activas por debajo de 15.000 solicitudes por front-end al ejecutar P3.  

**Trabajo** El trabajo se encuentra donde se ejecutan realmente sus aplicaciones. Al escalar verticalmente sus planes del Servicio de aplicaciones se utiliza el trabajo del grupo de trabajo asociado.

- No es posible agregar trabajo de manera instantánea. Este proceso puede tardar de 2 a 3 horas en aprovisionarse, independientemente de la cantidad que se esté agregando.
- Escalar el tamaño de un recurso de proceso para cualquier grupo tardará de 2 a 3 horas por dominio de actualización. Hay 20 dominios de actualización en un ASE. La escala del tamaño de proceso de un grupo de trabajo con 10 instancias puede tardar entre 20 y 30 horas en completarse. 
- Si cambia el tamaño de los recursos de proceso usados en un grupo de trabajo, provocará arranques en frío de las aplicaciones que se ejecutan en ese grupo de trabajo.

La forma más rápida de cambiar el tamaño de recursos de proceso de un grupo de trabajo que no está ejecutando aplicaciones es el siguiente:

- Reducir el número de instancias a 0. El proceso de desasignación de instancias tardará unos 30 minutos.
- Seleccionar el nuevo tamaño de proceso y el número de instancias. Desde este punto, el proceso tardará entre 2 y 3 horas en completarse.

Si las aplicaciones requieren un tamaño mayor de recursos de proceso, no podrá guiarse por las orientaciones anteriores. En lugar de cambiar el tamaño del grupo de trabajo que hospeda esas aplicaciones, puede rellenar otro grupo de trabajo con instancias del tamaño deseado y trasladar sus aplicaciones a ese grupo.

- Crear las instancias adicionales del tamaño de proceso necesario en otro grupo de trabajo. Esto tardará de 2 a 3 horas en completarse.
- Volver a asignar sus planes del Servicio de aplicaciones que hospedan las aplicaciones y que necesitan un tamaño mayor al grupo de trabajo recién configurado. Se trata de una operación rápida que debería tardar menos de un minuto en completarse.  
- Reducir el primer grupo de trabajo si ya no necesita esas instancias no utilizadas. Esta operación tarda unos 30 minutos en completarse.

**Escalado automático** Una de las herramientas que pueden ayudar a administrar el consumo de recursos de proceso es el escalado automático, que se puede aplicar a grupos de trabajo o servidores front-end. Puede, por ejemplo, aumentar las instancias de cualquier tipo de grupo de la mañana y reducirlo por la noche, o incluso agregar instancias cuando la cantidad de trabajo disponible en un grupo caiga por debajo de un umbral determinado. Si desea establecer reglas de escalado automático alrededor de métricas del grupo de recursos de proceso, tenga en cuenta el tiempo necesario para el aprovisionamiento. Para obtener más detalles sobre el escalado automático de los entornos del Servicio de aplicaciones, vea [Escalado automático y entorno del Servicio de aplicaciones][ASEAutoscale].

### Almacenamiento

Cada ASE se configura con 500 Gb de almacenamiento. Este espacio se usa en todas las aplicaciones del ASE. Este espacio de almacenamiento es parte del ASE y actualmente no se puede cambiar para usar el espacio de almacenamiento del cliente. Si realiza ajustes en el enrutamiento o la seguridad de la red virtual, es necesario seguir permitiendo el acceso a Almacenamiento de Azure; de lo contrario ASE no funcionará.

### Base de datos

La base de datos contiene la información que define el entorno, así como los detalles de las aplicaciones que se ejecutan en él. También es parte de la suscripción de Azure y no es algo que puedan manipular directamente los clientes. Si realiza ajustes en el enrutamiento o la seguridad de la red virtual, es necesario seguir permitiendo el acceso a SQL Azure; de lo contrario ASE no funcionará.

### Red

La red virtual que se usa con el ASE puede ser una de las que hizo al crear el ASE o alguna que ya tuviera de antes. Si quiere que la red virtual (VNET) se encuentre en un grupo de recursos distinto del usado para el ASE, deberá crearla por separado del flujo de creación del ASE. Si genera la subred durante la creación del ASE, obligará al ASE a permanecer en el mismo grupo de recursos que la red virtual.

Existen algunas restricciones en la red virtual que se aplican a un ASE:

- Actualmente solo se admiten las redes virtuales "clásicas" V1.
- la red virtual debe ser una red virtual regional
- Las redes virtuales que se utilizan para hospedar un ASE deben utilizar direcciones RFC1918 (es decir, direcciones privadas).
- Es necesario que haya una subred con 8 o más direcciones donde se implementa el ASE.
- Una vez que una subred se utilice para hospedar un ASE, no se puede cambiar el intervalo de direcciones de la subred. Por este motivo, se recomienda que la subred contenga al menos 64 direcciones para tener en cuenta el crecimiento futuro del ASE. 

A diferencia del servicio hospedado que contiene el ASE, la [red virtual][virtualnetwork] y la subred están todas bajo el control del usuario. La administración de la red virtual se realiza a través de la interfaz de usuario de la red virtual o Powershell.

Dado que esta funcionalidad coloca el Servicio de aplicaciones de Azure en la red virtual, significa que las aplicaciones hospedadas en su ASE ahora pueden tener acceso a los recursos disponibles a través de ExpressRoute o VPN de sitio a sitio directamente. Las aplicaciones dentro de los entornos del Servicio de aplicaciones no requieren características de red adicionales para tener acceso a los recursos disponibles a la red virtual que hospeda el entorno del Servicio de aplicaciones. Esto significa que no es necesario usar la integración de red virtual o conexiones híbridas para acceder a los recursos o para conectarse a la red virtual. Sin embargo, puede usar ambas características para acceder a los recursos en redes que no están conectadas a la red virtual. Por ejemplo, puede usar la integración de red virtual para integrarse con una red virtual que está en su suscripción, pero que no está conectada a la red virtual en la que se encuentra su ASE. También puede usar las conexiones híbridas para tener acceso a los recursos de otras redes, de la manera habitual.

Si la red virtual está configurada con una VPN de ExpressRoute, debe tener en cuenta las necesidades de enrutamiento que tenga un ASE. Existen algunas configuraciones de rutas definida por el usuario (UDR) que son incompatibles con un ASE. Para obtener información detallada respecto a la ejecución de un ASE en una red virtual con ExpressRoute, consulte el siguiente documento: [Detalles de configuración de red para entornos del Servicio de aplicaciones con ExpressRoute][ExpressRoute].

También puede controlar ahora el acceso a las aplicaciones mediante grupos de seguridad de red. Esta capacidad le permite bloquear el entorno del Servicio de aplicaciones a solamente las direcciones IP a las que desea restringirlo. Para obtener más información sobre cómo hacerlo, vea el documento [Control del tráfico de entrada en un entorno del Servicio de aplicaciones](app-service-app-service-environment-control-inbound-traffic.md).

## Portal

La interfaz de usuario para administrar y supervisar el entorno del Servicio de aplicaciones está disponible en el Portal de Azure. Si tiene un entorno del Servicio de aplicaciones, es probable que vea el símbolo del Servicio de aplicaciones en la barra lateral. Este símbolo se usa para representar entornos del Servicio de aplicaciones en el Portal de Azure.

![][1]

Puede usar el icono o seleccionar el botón de contenido adicional (símbolo mayor que) en la parte inferior de la barra lateral y seleccionar entornos del Servicio de aplicaciones. Ambos hacen lo mismo, es decir, abren la IU que muestra todos los entornos del Servicio de aplicaciones. Al seleccionar uno de los ASE mostrados, se abre la interfaz de usuario usada para supervisarlo y administrarlo.

![][2]

La primera hoja muestra algunas de las propiedades del ASE junto con un gráfico de métrica por grupo de recursos. Algunas de las propiedades que aparecen en el bloque Essentials también son hipervínculos que abrirán la hoja asociada. Por ejemplo puede seleccionar el nombre de red virtual para abrir la interfaz de usuario asociada a la red virtual en la que se ejecuta el entorno del Servicio de aplicaciones. Los planes del Servicio de aplicaciones y las aplicaciones abren hojas que muestran los elementos que se encuentran en su entorno del Servicio de aplicaciones.

### Supervisión

Los gráficos ofrecen la capacidad de ver una variedad de estadísticas de rendimiento de cada grupo de recursos. Para el grupo de servidores front-end, supervise la CPU y la memoria promedio. Para los grupos de trabajo, supervise la cantidad utilizada y la cantidad disponible. Varios planes del Servicio de aplicaciones pueden usar los trabajos en un grupo de trabajo. La carga de trabajo no se distribuye de la misma manera que en los servidores front-end; por tanto, el uso de CPU y memoria no proporcionan una gran cantidad de información útil. Es más importante realizar el seguimiento de cuántos trabajos usó y están disponibles, sobre todo si va a administrar este sistema para que otros lo usen.

Todas las métricas que se pueden seguir en los gráficos también pueden usarse para configurar alertas. La configuración de alertas funciona de la misma forma que en cualquier otro lugar del Servicio de aplicaciones. Puede establecer una alerta de la parte de la interfaz de usuario de alertas o a partir de la exploración en profundidad de la interfaz de usuario de métricas y de hacer clic en Agregar alerta.
 
![][3]

Las métricas que acabamos de analizar son las métricas el entorno del Servicio de aplicaciones. También hay métricas disponibles en el nivel del Plan del Servicio de aplicaciones (ASP). Aquí es dónde la supervisión de la CPU y de la memoria tiene más sentido. En un ASE, todos los ASP son ASP dedicados. Es decir que las únicas aplicaciones que se ejecutan en los hosts asignados a dicho ASP son las aplicaciones de ese ASP. Para ver los detalles del ASP, solo muéstrelo desde cualquiera de las listas de la interfaz de usuario del ASE o incluso examine los planes del Servicio de aplicaciones que enumeran todos ellos.

### Settings

En la hoja de ASE hay una sección de configuración que contiene varias funciones importantes.

**Configuración > Propiedades** La hoja Configuración se abrirá automáticamente cuando active la hoja ASE. En la parte superior se encuentra Propiedades. Hay una serie de elementos aquí que son redundantes con lo que ve en Essentials, aunque lo que es muy útil es la dirección VIP, así como la dirección IP saliente.

![][4]

**Configuración > Direcciones IP** Al crear una aplicación SSL de IP en su ASE, necesita una dirección SSL de IP. Para hacerlo, el ASE debe tener algunas direcciones SSL de IP que se puedan asignar. Cuando se crea un ASE, este tiene 1 dirección SSL de IP para este propósito, pero puede agregar más. Las direcciones de SSL de IP adicionales conllevan un cargo, como se muestra en los [Precios de Servicio de aplicaciones][AppServicePricing], en la sección sobre las conexiones SSL. El precio adicional es el precio de SSL de IP.

**Configuración > Grupo de servidores front-end / Grupos de trabajo** Cada una de estas hojas del grupo de recursos ofrece la posibilidad de ver información únicamente acerca de ese grupo de recursos, además de proporcionar controles para escalar completamente ese grupo de recursos.

La hoja base de cada grupo de recursos proporciona un gráfico con métricas de dicho grupo de recursos. Al igual que con los gráficos de la hoja de ASE, puede acceder al gráfico y configurar las alertas como desee. Configurar una alerta desde la hoja del ASE para un grupo de recursos específico es igual que hacerlo desde el grupo de recursos. Desde la hoja de configuración del grupo de trabajo tendrá acceso a la lista de todas las aplicaciones o planes del Servicio de aplicaciones que se ejecutan en este grupo de trabajo.

![][5]

### Capacidades de escalado del portal  

Hay tres operaciones de escala:

- cambio del número de direcciones IP en el ASE que están disponibles para el uso de SSL de IP
- cambio de tamaño del recurso de proceso usado en un grupo de recursos
- cambio de la cantidad de recursos de proceso que se usa en un grupo de recursos, bien manualmente o mediante el escalado automático

En el portal existen tres formas de controlar el número de servidores de los grupos de recursos:

- Operación de escalado desde la hoja de ASE principal de la parte superior. Puede realizar varios cambios en la configuración de escalado de los grupos de servidores front-end y trabajo y todos se aplican como una única operación.
- Realizando una operación de escalado manual desde la hoja Escala de grupos de recursos individuales (debajo de Configuración)
- Realizando una operación de escalado automático (se configura desde la hoja Escala de grupos de recursos individuales)

Para usar la operación de escala en la hoja del ASE, arrastre el control deslizante a la cantidad deseada y guarde. Esta interfaz de usuario también admite el cambio del tamaño.

![][6]

Para usar las funciones de escalado manual o automático en un grupo de recursos específico, vaya a *Configuración > Grupo de servidores front-end / Grupos de trabajo* según corresponda y abra el grupo que desea cambiar. Vaya a *Configuración > Escalar horizontalmente o Configuración > Escalar verticalmente*. La hoja *Escalar horizontalmente* le permite controlar la cantidad de la instancia. *Escalar verticalmente* le permite controlar el tamaño del recurso.

![][7]

## Consideraciones sobre la tolerancia a errores

Un entorno del Servicio de aplicaciones se puede configurar para usar hasta 55 recursos de proceso en total. De esos 55 recursos de proceso, solamente 50 se pueden usar para hospedar cargas de trabajo. El motivo de esto es doble. Hay un mínimo de 2 recursos de proceso front-end. Esto deja hasta 53 para admitir asignación de grupos de trabajo. Para proporcionar tolerancia a errores, necesita tener un recurso de proceso adicional asignado según las reglas siguientes:

- cada grupo de trabajo necesita al menos un recurso de proceso adicional que no se puede asignar a cargas de trabajo
- cuando la cantidad de recursos de proceso de un grupo de trabajo supera un determinado valor, se necesita otro recurso de proceso para tolerancia a errores Esto no ocurre en el grupo de servidores front-end.

Dentro de cualquier grupo de trabajo los requisitos de tolerancia a errores son que para un determinado valor de X recursos asignados a un grupo de trabajo:

- Si el valor de X está comprendido entre 2 y 20, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-1
- Si el valor de X está comprendido entre 21 y 40, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-2
- Si el valor de X está comprendido entre 41 y 53, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-3

La superficie mínima tiene dos servidores front-end y dos trabajos. Con las instrucciones anteriores, se muestran a continuación algunos ejemplos que sirven de aclaración.

- Si tiene 30 trabajos en un solo grupo, 28 de ellos pueden usarse para hospedar las cargas de trabajo. 
- Si tiene dos trabajos en un solo grupo, en este caso solo se puede usar uno para hospedar las cargas de trabajo.
- Si tiene 20 trabajos en un solo grupo, en este caso solo se pueden usar 19 para hospedar las cargas de trabajo.  
- Si tiene 21 trabajos en un solo grupo, en este caso solo se pueden usar 19 para hospedar las cargas de trabajo.  

El aspecto de la tolerancia a errores es importante, pero debe tenerlo en cuenta cuando la escala supera determinados umbrales. Si desea agregar más capacidad a partir de 20 instancias, en este caso 22 o un valor superior a 21 no agregará más capacidad. Lo mismo puede decirse por encima de 40, donde el número siguiente que agrega capacidad es 42.

## Eliminación de un entorno del Servicio de aplicaciones ##

Si desea eliminar un entorno del Servicio de aplicaciones, simplemente utilice la acción Eliminar que se encuentra en la parte superior de la hoja Entorno del Servicio de aplicaciones. Al hacerlo, se le pedirá que escriba el nombre del entorno del Servicio de aplicaciones para confirmar que realmente quiere hacerlo. NOTA: Cuando se elimina un entorno del Servicio de aplicaciones, se elimina también todo su contenido.

![][9]

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones](app-service-web-how-to-create-an-app-service-environment.md).

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0413_2016-->