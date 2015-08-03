<properties 
   pageTitle="Instalar la actualización 1 en el dispositivo StorSimple"
   description="Explica cómo instalar la actualización 1 de la serie StorSimple 8000 Series en un dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# Instalar la actualización 1 en el dispositivo StorSimple

## Información general

En este tutorial se explica cómo instalar la actualización 1 en un dispositivo StorSimple con una versión de software anterior a la actualización 1. El dispositivo puede ejecutar la versión disponible con carácter general (GA), la actualización 0.1, la actualización 0.2 o la actualización 0.3 del software.

En la instalación, si el dispositivo utiliza una versión anterior a la actualización 1, se realizan varias comprobaciones en el mismo. Estas comprobaciones determinan el estado del dispositivo en cuanto a la conectividad de red y al estado del hardware.

Se le solicitará que realice una comprobación previa manual para asegurarse de que:

- Las direcciones IP fijas del controlador son enrutables y se pueden conectar a Internet. Estas direcciones IP se usan para atender las actualizaciones de los dispositivos de StorSimple. Esto se puede comprobar ejecutando el siguiente cmdlet en cada controlador:

    `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**Salida de ejemplo para la conexión de prueba cuando los IP fijos pueden conectarse a Internet**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    
	    


- Antes de actualizar el dispositivo, se recomienda tomar una instantánea en la nube de los datos del dispositivo.

Tras comprobar y confirmar las comprobaciones manuales (arriba), se realizará una serie de comprobaciones automáticas previas a la actualización. Entre ellos se incluyen los siguientes:

- **Comprobaciones del estado del controlador** para comprobar que los controladores del dispositivo están en buen estado y en línea.

- **Comprobaciones de mantenimiento de componentes de hardware** para comprobar que todos los componentes de hardware del dispositivo StorSimple están en buen estado.

- **Comprobaciones de DATA 0** para comprobar DATA 0 está habilitado en el dispositivo. Si la interfaz no está habilitada, tendrá que habilitarla y, a continuación, volver a intentarlo.

- **Comprobaciones de DATA 2 y DATA 3** para comprobar que las interfaces de red de DATA 2 y DATA 3 no están habilitadas. Si estas interfaces están habilitadas, tendrá que deshabilitarlas y, a continuación, intentar actualizar el dispositivo. Esta comprobación solo se realiza se va a realizar una actualización de un dispositivo que ejecuta software de disponibilidad general. Los dispositivos que ejecuten las versiones 0.1, 0.2, o 0.3 no necesitarán esta comprobación.

- **Comprobación de la puerta de enlace** de todos los dispositivos que ejecuten una versión anterior a la actualización 1. Esta comprobación se realiza únicamente en los dispositivos que tienen una puerta de enlace configurada para una interfaz de red que no sea DATA 0.
 
La actualización 1 sólo se aplicará si se ha completado satisfactoriamente todas las comprobaciones anteriores a la actualización. Después de haber aplicado la actualización 1 en el dispositivo StorSimple, las actualizaciones futuras no tendrán las comprobaciones de Data 2 y Data 3, y la comprobación de la puerta de enlace. Se realizarán las restantes comprobaciones previas.

## Uso del portal de administración para instalar la actualización 1.

Para actualizar los dispositivos que ejecutan la versión GA, se recomienda utilizar el Portal de administración de Azure. Realice los pasos siguientes para actualizar el dispositivo.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## Solución de errores en actualización

**¿Qué ocurre si ve una notificación que indica que se ha producido un error en las comprobaciones previas a la actualización?**

Si se produce un error en la comprobación previa, asegúrese de que ha examinado la barra de notificación detallada en la parte inferior de la página. Esto proporciona una guía para saber en qué comprobación previa se ha producido el error. La siguiente ilustración muestra una instancia en la que aparece dicha notificación. En este caso, ni la comprobación de mantenimiento del controlador ni la comprobación de mantenimiento de los componentes de hardware han resultado satisfactorias. En la sección **Estado del hardware**, puede ver que los componentes de Controlador 0 y Controlador 1 necesitan atención.
 
  ![Error de comprobación previa](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

Es preciso asegurarse de que ambos controladores funcionan correctamente y están en línea. También necesitará asegurarse de que en la página de mantenimiento se muestra que todos los componentes de hardware del dispositivo StorSimple están en buen estado. A continuación, puede intentar instalar las actualizaciones. Si no puede corregir los problemas de los componentes de hardware, deberá ponerse en contacto con el servicio de soporte técnico de Microsoft para los pasos siguientes.

**¿Qué sucede si recibe el mensaje de error "Las actualizaciones no se pudieron instalar" y la recomendación es hacer referencia a la guía de solución de problemas de actualización para determinar la causa del error?**

Una causa probable podría ser que no tiene conectividad con los servidores de Microsoft Update. Esta es una comprobación manual que se debe realizar. Si se pierde la conexión con servidor de actualizaciones, se producirá un error en el trabajo de actualización. Para comprobar la conectividad, ejecute el siguiente cmdlet desde la interfaz de Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Ejecute el cmdlet en ambos controladores.
 
Si ha comprobado que existe conectividad y sigue apareciendo este problema, póngase en contacto con servicio de soporte técnico de Microsoft para los pasos siguientes.

## Pasos siguientes

Más información acerca de [Microsoft Azure StorSimple](storsimple-overview.md)

<!---HONumber=July15_HO4-->