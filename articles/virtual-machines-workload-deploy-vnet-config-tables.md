<properties 
	pageTitle="Crear una red virtual entre locales usando las tablas de configuración" 
	description="En este tema se describe cómo configurar una red virtual entre locales usando las tablas de configuración predeterminadas." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="josephd"/>

# Crear una red virtual entre locales usando las tablas de configuración

Este tema le guiará en el proceso de creación de una red virtual entre locales usando la configuración previamente especificada en el siguiente conjunto de tablas de configuración:

- Tabla V: Configuración de red virtual entre locales
- Tabla S: Subredes de la red virtual
- Tabla D: Servidores DNS locales
- Tabla L: Prefijos de dirección de la red local

Estas tablas suelen rellenarse en un tema que describe la configuración de una carga de trabajo de TI en Azure e incluye una red virtual entre locales. Consulte un ejemplo en [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

El procedimiento siguiente hace referencia a la información contenida en dichas tablas para guiarle a lo largo del proceso de configuración de la red virtual. Si todavía no especificó la configuración de estas tablas en otro tema, pero desea configurar igualmente una red virtual entre locales, vea [Configurar una conexión de sitio a sitio entre locales a una red virtual de Azure](https://msdn.microsoft.com/library/dn133795.aspx).

> [AZURE.NOTE]Este procedimiento le guiará en el proceso de creación de una red virtual que usa una conexión VPN de sitio a sitio. Para obtener información sobre cómo usar ExpressRoute para la conexión de sitio a sitio, vea [Introducción técnica a ExpressRoute](https://msdn.microsoft.com/library/dn606309.aspx).
 
## Crear una nueva red virtual de Azure entre locales con los valores de las tablas de configuración

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2. En la barra de tareas, haga clic en **Nuevo > Servicios de red > Red virtual > Creación personalizada**.
3. En la página Detalles de redes virtuales:
- En **Nombre**, escriba el nombre del Elemento 1 de la tabla V.
- En **Ubicación**, seleccione la región del Elemento 2 en la tabla V. 
4. Haga clic en la flecha de avance para continuar.
5. En la página Servidores DNS y conectividad VPN:
- En **Servidores DNS**, para cada entrada de la tabla D, configure el nombre descriptivo y la dirección IP de los servidores DNS locales.
- En **Conectividad de sitio a sitio**, seleccione **Configurar una VPN de sitio a sitio**.
- Si ya configuró una red local y desea usarla, seleccione su nombre en **Red local**. Si desea crear una nueva red local, seleccione **Especificar una nueva red local** en **Red local**.
- Si no configuró una red local para la suscripción, no verá el campo **Red local**. 
6. Haga clic en la flecha de avance para continuar.
7. En la página Conectividad de sitio a sitio (si seleccionó **Especificar una nueva red local** en el paso 5):
- En **Nombre**, escriba el nombre del Elemento 3 de la tabla V (el nombre de la red local).
- En **Dirección IP del dispositivo VPN**, escriba la dirección del Elemento 4 de la tabla V.
- En **Espacio de direcciones**, para cada entrada de tabla L, escriba los espacios de direcciones IP de la red de su organización en lo que hace referencia al prefijo (en **IP de inicio**) y la longitud del prefijo (en **CIDR [Recuento de direcciones]**).
8. Haga clic en la flecha de avance para continuar.
9. En la página Espacios de direcciones de redes virtuales:
- En **Espacio de direcciones**, escriba el espacio de direcciones IP privado de la red virtual del Elemento 5 de la tabla V, en lo que hace referencia al prefijo (en **IP de inicio**) y la longitud del prefijo (en **CIDR [Recuento de direcciones]**).
- En **Subredes**, para cada entrada de tabla S:
	- Escriba el nombre de la subred en la columna **Subredes**, sobrescribiendo el nombre predeterminado si es necesario.
	- Escriba el espacio de direcciones IP privado de la subred, en lo que hace referencia al prefijo (en **IP de inicio**) y la longitud del prefijo (en **CIDR [Recuento de direcciones]**).
- Haga clic en **Agregar subred de puerta de enlace**.
10. Haga clic en la marca de verificación para completar la configuración.

## Recursos adicionales

[Información general sobre redes virtuales](https://msdn.microsoft.com/library/jj156007.aspx)

[Tareas de configuración de red virtual](https://msdn.microsoft.com/library/jj156206.aspx)

[Configurar una conexión sitio a sitio entre locales a una red virtual de Azure](https://msdn.microsoft.com/library/dn133795.aspx)

<!--HONumber=54-->