<properties
   pageTitle="Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure en Resource Manager | Microsoft Azure"
   description="Aprenda a crear una puerta de enlace de aplicaciones mediante la CLI de Azure en Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="gwallace" />

# Creación de una puerta de enlace de aplicaciones mediante la CLI de Azure

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesiones basada en cookies, descarga SSL (capa de sockets seguros), sondeos personalizados sobre el estado y compatibilidad con sitios múltiples.

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

## Requisito previo: instalar la CLI de Azure

Para seguir los pasos de este artículo, es preciso [instalar la interfaz de línea de comandos de Azure para Mac, Linux y Windows (CLI de Azure)](../xplat-cli-install.md) e [iniciar sesión en Azure](../xplat-cli-connect.md).

> [AZURE.NOTE] Si no tiene una cuenta de Azure, necesitará una. Regístrese para [obtener una prueba gratuita aquí](../active-directory/sign-up-organization.md).

## Escenario

En este escenario, aprenderá a crear una puerta de enlace de aplicaciones mediante el Portal de Azure.

En este escenario:

- Creará una puerta de enlace de aplicaciones media con dos instancias.
- Creará una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
- Creará una subred denominada Appgatewaysubnet que usa 10.0.0.0/28 como bloque CIDR.
- Configurará un certificado para la descarga SSL.

![Escenario de ejemplo][scenario]

>[AZURE.NOTE] La configuración adicional de la puerta de enlace de aplicaciones, incluidos los sondeos personalizados sobre el estado, las direcciones del grupo de back-end y las reglas se realiza después de que se configura la puerta de enlace de aplicaciones, no durante la implementación inicial.

## Antes de empezar

Puerta de enlace de aplicaciones de Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para que tenga varias subredes. Una vez que se implementa una puerta de enlace de aplicaciones en una subred adicional solo se pueden agregar a ella puertas de enlace de aplicaciones adicionales.

## Creación del grupo de recursos

Antes de crear la puerta de enlace de aplicaciones, se creará un grupo de recursos para que pueda contenerla. A continuación, se muestra el comando.

    azure group create -n AdatumAppGatewayRG -l eastus

## Crear una red virtual

Una vez creado el grupo de recursos, se crea una red virtual para la puerta de enlace de aplicaciones. En el ejemplo siguiente, el espacio de direcciones era 10.0.0.0/16 tal como se definió en las notas del escenario anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## Creación de una subred

Después de crear la red virtual, se agrega una subred para la puerta de enlace de aplicaciones. Si piensa utilizar la puerta de enlace de aplicaciones con una aplicación web hospedada en la misma red virtual que la puerta de enlace de aplicaciones asegúrese de dejar suficiente espacio para otra subred.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## Creación de la puerta de enlace de aplicaciones

Una vez que se crean la red virtual y la subred, los requisitos previos de la puerta de enlace de aplicaciones están completos. Además, se necesita un certificado .pfx exportado previamente y la contraseña de este para el paso siguiente. Las direcciones IP usadas para el back-end son las direcciones IP del servidor back-end. Estos valores pueden ser direcciones IP privadas de la red virtual, direcciones IP públicas o nombres de dominio completos de los servidores back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd

Con este ejemplo sea crea una puerta de enlace de aplicaciones básica con la configuración predeterminada para el agente de escucha, el grupo de back-end, la configuración de http de back-end y las reglas. También configura la descarga SSL. Esta configuración se puede modificar para adaptarse a la implementación una vez que el aprovisionamiento sea correcto. Si ya tiene la aplicación web definida con las direcciones IP del grupo de back-end definido en los pasos anteriores, una vez aprovisionada e iniciada la puerta de enlace de aplicaciones empezará el equilibrio de carga.

## Pasos siguientes

Para aprender a crear sondeos de estado personalizado, visite [Create a custom probe for Application Gateway by using the portal](application-gateway-create-probe-portal.md) (Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal)

Para aprender a configurar la descarga de SSL y eliminar la cara descripción de SSL de los servidores web, visite [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png

<!---HONumber=AcomDC_0907_2016-->