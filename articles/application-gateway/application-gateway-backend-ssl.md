<properties
   pageTitle="Habilitación de directiva de SSL y SSL de extremo a extremo en Application Gateway | Microsoft Azure"
   description="En esta página se proporciona información general sobre la compatibilidad de Application Gateway con el protocolo SSL de extremo a extremo."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amsriva"/>

# Habilitación de directiva de SSL y SSL de extremo a extremo en Application Gateway

## Información general

Application Gateway es compatible con la terminación SSL en la puerta de enlace; después, el tráfico fluye normalmente sin cifrar a los servidores back-end. De esta forma, los servidores web se libran de la costosa sobrecarga de cifrado y descifrado. Sin embargo, para algunos clientes, la comunicación sin cifrar en los servidores back-end no es una opción aceptable. Esto podría deberse a los requisitos de seguridad y cumplimiento normativo, o bien a la posibilidad de que la aplicación solo acepte una conexión segura. Para tales aplicaciones, Application Gateway ahora admite el cifrado SSL de extremo a extremo.

El protocolo SSL de extremo a extremo permite transmitir de forma segura información confidencial cifrada al back-end, al mismo tiempo que se aprovechan las ventajas del equilibrio de carga de capa 7 que proporciona Application Gateway, como la afinidad de la cookie, el enrutamiento basado en URL, la compatibilidad con el enrutamiento basado en sitios o la posibilidad de insertar encabezados X-Forwarded-*.

Cuando se configura con el modo de comunicación de SSL de extremo a extremo, Application Gateway finaliza las sesiones SSL del usuario en la puerta de enlace y descifra el tráfico de usuario. Después, aplica las reglas configuradas para seleccionar una instancia de grupo de back-end adecuada en la que enrutar el tráfico. Posteriormente, Application Gateway inicia una nueva conexión SSL al servidor back-end y vuelve a cifrar los datos mediante el certificado de clave pública del servidor back-end antes de transmitir la solicitud al back-end. El protocolo SSL de extremo a extremo se habilita estableciendo la configuración del protocolo de BackendHTTPSetting en Https, que, luego, se aplica a un grupo de back-end. Cada servidor back-end del grupo de back-end con SSL de extremo a extremo habilitado debe configurarse con un certificado para permitir la comunicación segura.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

En este ejemplo, las solicitudes de https://contoso.com puede enrutarse a ContosoServerPool a través de HTTP, y https://fabrikam.com se redirigen a FabrikamServerPool a través de HTTPS con SSL de extremo a extremo.

## SSL de extremo a extremo y adición a listas blancas de certificados

Application Gateway solo se comunica con instancias de back-end conocidas, que tienen en la lista blanca su certificado con Application Gateway. Para habilitar la creación de listas blancas de certificados, debe cargar la clave pública de los certificados de servidor back-end en Application Gateway. Solo se permiten conexiones con back-ends conocidos y agregados a la lista blanca; el resto obtendrán un error de puerta de enlace. Los certificados autofirmados son para fines de prueba que y no se recomiendan para cargas de trabajo de producción. Antes de poder usar estos certificados, también deben estar en la lista con Application Gateway, tal y como se describió anteriormente.

## Directiva SSL de Application Gateway

Application Gateway también admite directivas de negociación SSL configurables, que permiten a los clientes controlar de forma más precisa las conexiones SSL en Application Gateway.

1. SSL 2.0 y 3.0 están desactivados en todas las instancias de Application Gateway. No pueden configurarse.
2. La definición de directivas SSL proporciona la opción de deshabilitar cualquiera de los siguientes 3 protocolos: TLSv1_0, TLSv1_1 y TLSv1\_2.
3. Si no se define ninguna directiva SSL, las 3 (TLSv1_0, TLSv1_1 y TLSv1\_2) se habilitarían.

## Pasos siguientes

Después de obtener información sobre la directiva SSL y SSL de extremo a extremo, vaya a la página sobre [habilitación d SSL de extremo a extremo en Application Gateway](application-gateway-end-to-end-ssl-powershell.md) para crear una puerta de enlace de aplicaciones con capacidad para enviar tráfico al back-end de forma cifrada.

<!---HONumber=AcomDC_0928_2016-->