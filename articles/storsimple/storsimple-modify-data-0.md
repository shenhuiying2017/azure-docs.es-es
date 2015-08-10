<properties 
   pageTitle="Modificación de las opciones de configuración de la interfaz de red DATA 0 en el dispositivo StorSimple"
   description="Aprenda a volver a configurar la interfaz de red DATA 0 en el dispositivo StorSimple"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/30/2015"
   ms.author="alkohli" />

# Modificación de las opciones de configuración de la interfaz de red DATA 0 en el dispositivo StorSimple

## Información general
El dispositivo Microsoft Azure StorSimple tiene seis interfaces de red: de DATA de 0 a DATA 5. La interfaz DATA 0 siempre se configura a través de la interfaz de Windows PowerShell o la consola de serie, y se habilitada para la nube automáticamente. La interfaz DATA 0 se configura por primera vez a través del asistente para la instalación durante la implementación inicial del dispositivo StorSimple. Cuando el dispositivo está en un modo de funcionamiento, tal vez tenga que volver a establecer la configuración de DATA 0. En este tutorial se proporcionan dos métodos para modificar la configuración de red 0 de DATA 0, ambos a través de Windows PowerShell para StorSimple.

Después de leer el tutorial, podrá:

- Modificar la configuración de DATA 0 mediante el asistente para la instalación
- Modificar la configuración de red de DATA 0 mediante el cmdlet `Set-HcsNetInterface`


## Modificar la configuración de red de DATA 0 mediante el asistente para la instalación
Puede volver a establecer la configuración de red de DATA 0 conectándose a la interfaz de Windows PowerShell del dispositivo StorSimple e iniciando una sesión del asistente para la instalación. Siga estos pasos para modificar la configuración de DATA 0.

#### Para modificar la configuración de red de DATA 0 mediante el asistente para la instalación

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.

1. En el símbolo del sistema, escriba:


	`Invoke-HcsSetupWizard`

1. Aparecerá un asistente para instalación que le ayudará a configurar la interfaz DATA 0 del dispositivo. Proporcione nuevos valores para la dirección IP, la pasarela y la máscara de red.

> [AZURE.NOTE]Las IP fijas de controladores deberán volverse a configurar a través de la página Configurar del dispositivo StorSimple en el Portal de administración de Azure. Para obtener más información, vaya a [Modificar la interfaz de red a través de la página Configurar (dispositivo)](storsimple-modify-device-config.md#modify-network-interfaces).


## Modificación de la configuración de red de DATA 0 mediante el cmdlet Set-HcsNetInterface
Una alternativa para volver a configurar la interfaz de red DATA 0 es a través del uso del cmdlet `Set-HcsNetInterface`. El cmdlet se ejecuta desde la interfaz de Windows PowerShell del dispositivo StorSimple. Siga estos pasos para modificar la configuración de DATA 0:

#### Para modificar la configuración de red de DATA 0 mediante el cmdlet Set-HcsNetInterface

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador del dispositivo**. La contraseña predeterminada es `Password1`.

1. En el símbolo del sistema, escriba:

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

	Si está usando direcciones IPv6, use el comando siguiente:

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv6Address <> -IPv6Netmask <> -IPv6Gateway <> -Controller0IPv6Address <> -Controller1IPv6Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`

1. Escriba valores para DATA 0 en los corchetes angulares (<>) para los elementos siguientes:
											
	- Dirección IPv4/IPv6
	
	- Pasarela IPv4/IPv6
	
	- Máscara de subred IPv4/IPv6
	
	- Dirección IPv4/IPv6 fija para el Controlador 0

	- Dirección IPv4/IPv6 fija para el Controlador 1

## Pasos siguientes
Si experimenta problemas al configurar las interfaces de red, consulte [Solucionar problemas de implementación](storsimple-troubleshoot-deployment.md).

<!---HONumber=July15_HO5-->