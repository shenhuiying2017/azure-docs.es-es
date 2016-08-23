<properties
   pageTitle="Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el portal | Microsoft Azure"
   description="En esta página se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante el portal"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>

# Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el portal

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Puerta de enlace de aplicaciones de Azure puede configurarse para terminar la sesión SSL (Capa de sockets seguros) en la puerta de enlace para evitar las costosas tareas de descifrado de SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## Escenario

El siguiente escenario pasa por la configuración de la descarga SSL en una puerta de enlace de aplicaciones existente. El escenario supone que ya ha seguido los pasos para [crear una Puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md).

## Antes de empezar

Para configurar la descarga SSL con una puerta de enlace de aplicaciones, se requiere un certificado. Este certificado se carga en la puerta de enlace de aplicaciones y se usa para cifrar y descifrar el tráfico enviado a través de SSL. El certificado debe estar en formato Intercambio de información personal (.pfx). Este formato de archivo permite la exportación de la clave privada, necesaria para que la puerta de enlace de aplicaciones realice el cifrado y descifrado del tráfico.

## Incorporación de un agente de escucha HTTPS

El agente de escucha HTTPS busca tráfico en función de su configuración y ayuda a enrutar el tráfico a los grupos de back-end.

### Paso 1

Vaya al Portal de Azure y seleccione una puerta de enlace de aplicaciones existente.

![hoja de información general de puerta de enlace de aplicaciones][1]

### Paso 2

Haga clic en Agentes de escucha y en el botón Agregar para agregar un nuevo agente de escucha.

### Paso 3

Rellene la información necesaria para el agente de escucha y cargue el certificado .pfx. Cuando haya terminado, haga clic en Aceptar.

**Nombre**: se trata de un nombre descriptivo del agente de escucha. **Configuración de direcciones IP de front-end**: se trata de la configuración de direcciones IP de front-end que se usa para el agente de escucha. **Puerto front-end (nombre/puerto)**: un nombre descriptivo para el puerto usado en el front-end de la puerta de enlace de aplicaciones y el puerto real usado. **Protocolo**: un modificador para determinar si se usa https o http para el front-end. **Certificado (nombre/contraseña)**: si se usa la descarga SSL, un certificado .pfx se requiere para esta configuración, al igual que un nombre y contraseña descriptivos.

![agregar hoja del agente de escucha][2]

## Creación de una regla y asociación de la misma al agente de escucha

Ahora se ha creado el agente de escucha. Es hora de crear una regla para controlar el tráfico desde el agente de escucha.

### Paso 1

Haga clic en las **reglas** de la puerta de enlace de aplicaciones y, a continuación, en Agregar.

![hoja de reglas appgateway][3]

### Paso 2

En la hoja **Agregar regla básica**, escriba el nombre descriptivo para la regla y elija el agente de escucha creado en el paso anterior. Elija el grupo de back-end y la configuración HTTP adecuados y haga clic en **Aceptar**.

![ventana de configuración de HTTPS][4]

Ahora, la configuración está guardada en la puerta de enlace de aplicaciones. El proceso de guardar para esta configuración puede tardar un rato antes de que pueda verse a través del portal o de PowerShell. Una vez guardada, la puerta de enlace de aplicaciones controla el cifrado y descifrado del tráfico. Todo el tráfico entre la puerta de enlace de aplicaciones y los servidores web de back-end se controlará mediante http. Cualquier comunicación dirigida de nuevo al cliente iniciada mediante https se devolverá al cliente cifrado.

## Pasos siguientes

Para aprender a configurar un sondeo de estado personalizado con Puerta de enlace de aplicaciones de Azure, consulte la sección sobre cómo [crear un sondeo de estado personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

<!---HONumber=AcomDC_0810_2016-->