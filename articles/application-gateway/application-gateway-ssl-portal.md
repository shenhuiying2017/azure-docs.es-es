---
title: "Configuración de la descarga SSL para Azure Application Gateway mediante Azure Portal | Microsoft Docs"
description: "Este artículo contiene instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante Azure Portal."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](application-gateway-ssl-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
> * [PowerShell clásico de Azure](application-gateway-ssl.md)
> * [CLI de Azure 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway puede configurarse para terminar la sesión Capa de sockets seguros (SSL) en la puerta de enlace para evitar las costosas tareas de descifrado SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="scenario"></a>Escenario

En el siguiente escenario, se explica cómo configurar la descarga SSL en una puerta de enlace de aplicaciones existente. En este escenario se presupone que ya se ha realizado el procedimiento para [crear una puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de empezar

Para configurar la descarga SSL con una puerta de enlace de aplicaciones, se requiere un certificado. Este certificado se carga en la puerta de enlace de aplicaciones y se usa para cifrar y descifrar el tráfico enviado a través de SSL. El certificado debe tener el formato Personal Information Exchange (.pfx). Este formato de archivo permite la exportación de la clave privada, lo que es necesario para que la puerta de enlace de aplicaciones pueda realizar el cifrado y descifrado del tráfico.

## <a name="add-an-https-listener"></a>Incorporación de un agente de escucha HTTPS

El agente de escucha HTTPS busca el tráfico en función de su configuración y ayuda a enrutarlo a los grupos de back-end. Para agregar un agente de escucha HTTPS, siga estos pasos:

   1. Vaya a Azure Portal y seleccione una puerta de enlace de aplicaciones existente.

   2. Para agregar un agente de escucha, seleccione **Agentes de escucha** y el botón **Agregar**.

   ![Panel de información general de Application Gateway][1]


   3. Rellene la información necesaria para el agente de escucha que se indica a continuación y cargue el certificado .pfx:
      - **Nombre**: nombre descriptivo del agente de escucha.

      - **Configuración de IP de front-end**: configuración de IP de front-end que se usa en el agente de escucha.

      - **Puerto de front-end (nombre/puerto)**: nombre descriptivo del puerto que se utiliza en el front-end de la puerta de enlace de aplicaciones y número del puerto real utilizado.

      - **Protocolo**: conmutador que determina si se va a utilizar HTTP o HTTPS en el front-end.

      - **Certificado (nombre/contraseña)**: si se usa la descarga SSL, esta configuración necesita un certificado .pfx. También será necesario especificar un nombre descriptivo y una contraseña.

   4. Seleccione **Aceptar**.

![Panel para agregar un agente de escucha][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Creación de una regla y asociación de la misma al agente de escucha

Ahora se ha creado el agente de escucha. A continuación, debe crear una regla para controlar el tráfico procedente del agente de escucha. Las reglas determinan cómo se enruta el tráfico a los grupos de back-end en función de diversos valores de configuración. Entre estos valores se incluyen el protocolo, el puerto y los sondeos de estado y se indica si se va a utilizar la afinidad de sesiones basada en cookies. Para crear y asociar una regla al agente de escucha, siga estos pasos:


   1. Seleccione la opción **Reglas** de la puerta de enlace de aplicaciones y después **Agregar**.

   ![Panel Reglas de la puerta de enlace de aplicaciones][3]


   2. En **Agregar regla básica**, escriba el nombre descriptivo de la regla en el campo **Nombre** y seleccione el **agente de escucha** que creó en el paso anterior. Elija el **grupo de back-end** y la **configuración de HTTP** adecuados y seleccione **Aceptar**.

   ![Ventana de configuración de HTTPS][4]

Ahora, la configuración está guardada en la puerta de enlace de aplicaciones. El proceso de guardar para esta configuración puede tardar un rato antes de que pueda verse a través del portal o de PowerShell. Una vez guardada, la puerta de enlace de aplicaciones controla el cifrado y descifrado del tráfico. Todo el tráfico entre la puerta de enlace de aplicaciones y los servidores web de back-end se va a controlar mediante HTTP. Todas las comunicaciones dirigidas al cliente, si se inician a través de HTTPS, se devolverán cifradas al cliente.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar un sondeo de estado personalizado con Azure Application Gateway, consulte la sección sobre cómo [crear un sondeo de estado personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

