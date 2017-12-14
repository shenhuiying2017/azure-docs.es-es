---
title: "Autenticación RADIUS y Servidor Azure MFA | Microsoft Docs"
description: "Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la autenticación RADIUS y el servidor Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: H1Hack27Feb2017, it-pro
ms.openlocfilehash: 07b242699e016550c82f491c1678c6dbc9f7234d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integración de la autenticación RADIUS con Servidor Azure Multi-Factor Authentication

RADIUS es un protocolo estándar para aceptar las solicitudes de autenticación y procesar estas solicitudes. Servidor Azure Multi-Factor Authentication puede actuar como servidor RADIUS. Insértelo entre el cliente RADIUS (aplicación VPN) y el destino de autenticación para agregar la verificación en dos pasos. El destino de autenticación puede ser Active Directory, un directorio LDAP u otro servidor RADIUS. Para que Azure Multi-Factor Authentication (MFA) funcione, debe configurar Servidor Azure MFA para que pueda comunicarse con los servidores de cliente y el destino de autenticación. El Servidor Azure MFA acepta solicitudes de un cliente RADIUS, valida las credenciales en el destino de autenticación, agrega Azure Multi-Factor Authentication y envía una respuesta al cliente RADIUS. La solicitud de autenticación se realizará correctamente solo si la autenticación principal y Azure Multi-Factor Authentication se ejecutan correctamente.

> [!NOTE]
> El servidor de MFA solo admite los protocolos RADIUS de PAP (protocolo de autenticación de contraseña) y MSCHAPv2 (protocolo de autenticación por desafío mutuo de Microsoft) cuando actúa como servidor RADIUS.  Otros protocolos, como EAP (protocolo de autenticación extensible), se pueden usar cuando el servidor MFA actúa como un proxy RADIUS para otro servidor RADIUS que admite dicho protocolo.
>
> En esta configuración, los tokens SMS y OATH unidireccionales no funcionarán, ya que Servidor MFA no puede iniciar una respuesta de desafío de RADIUS correcta con protocolos alternativos.

![Autenticación Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>Incorporación de un cliente RADIUS
Para configurar la autenticación RADIUS, instale el servidor Azure Multi-Factor Authentication en un servidor Windows. Si tiene un entorno de Active Directory, el servidor debe estar unido al dominio de dentro de la red. Use el procedimiento siguiente para configurar el servidor Azure Multi-Factor Authentication.

1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de RADIUS en el menú izquierdo.
2. Active la casilla **Habilitar autenticación RADIUS**.
3. En la pestaña Clientes, cambie los puertos de autenticación y control si el servicio RADIUS de Azure MFA debe escuchar las solicitudes RADIUS en puertos no estándar.
4. Haga clic en **Agregar**.
5. Escriba la dirección IP del dispositivo o el servidor que se autenticará en el Servidor de Azure Multi-Factor Authentication, un nombre de aplicación (opcional) y un secreto compartido.

  El nombre de la aplicación aparece en los informes puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.

  El secreto compartido debe ser el mismo en el servidor de Azure Multi-Factor Authentication y en el dispositivo/servidor.

6. Active la casilla **Requerir coincidencia de usuario de Multi-Factor Authentication** si todos los usuarios se importaron al servidor y están sujetos a la autenticación multifactor. Si aún no se importó al servidor un número significativo de usuarios o se van a excluir de la verificación en dos pasos, deje la casilla desactivada.
7. Active la casilla **Habilitar token OATH de reserva** si desea usar códigos de acceso OATH desde aplicaciones de comprobación por móvil como método de copia de seguridad.
8. Haga clic en **Aceptar**.

Repita los pasos del 4 al 8 para agregar todos los clientes RADIUS adicionales necesarios.

## <a name="configure-your-radius-client"></a>Configuración del cliente RADIUS

1. Haga clic en la pestaña **Destino**.
2. Si el Servidor Azure MFA está instalado en un servidor unido a un dominio de un entorno de Active Directory, seleccione el dominio de Windows.
3. Si los usuarios deben autenticarse en un directorio LDAP, seleccione **Enlace LDAP**.

  Seleccione el icono de integración de directorios y edite la configuración de LDAP en la pestaña Configuración para que el servidor pueda enlazar con su directorio. Puede encontrar instrucciones para configurar LDAP en la [Guía de configuración de proxy LDAP](multi-factor-authentication-get-started-server-ldap.md).

4. Si los usuarios deben autenticarse en otro servidor RADIUS, seleccione servidores RADIUS.
5. Haga clic en **Agregar** para configurar el servidor al que el Servidor Azure MFA entregará las solicitudes RADIUS.
6. En el cuadro de diálogo Agregar servidor RADIUS, escriba la dirección IP del servidor RADIUS y un secreto compartido.

  El secreto compartido debe ser el mismo en el Servidor Azure Multi-Factor Authentication y en el servidor RADIUS. Cambie el puerto de autenticación y el de cuentas si el servidor RADIUS usa puertos diferentes.

7. Haga clic en **Aceptar**.
8. Agregue el Servidor Azure MFA como cliente RADIUS en el otro servidor RADIUS para que procese las solicitudes de acceso que se le envían desde el Servidor Azure MFA. Use el mismo secreto compartido configurado en el Servidor Azure Multi-Factor Authentication.

Repita estos pasos para agregar más servidores RADIUS. Configure el orden en el que el Servidor Azure MFA debe llamarlos con los botones **Subir** y **Bajar**.

Ha configurado correctamente Servidor Azure Multi-Factor Authentication. Ahora, el servidor está escuchando en los puertos configurados las solicitudes de acceso RADIUS de los clientes configurados.   

## <a name="radius-client-configuration"></a>Configuración del cliente RADIUS
Para configurar el cliente RADIUS, siga las siguientes instrucciones:

* Configure el dispositivo/servidor para autenticarse mediante RADIUS en la dirección IP de Servidor Azure Multi-Factor Authentication, que actúa como servidor RADIUS.
* Use el mismo secreto compartido que se configuró anteriormente.
* Configure el tiempo de espera de RADIUS en entre 30 y 60 segundos para que haya tiempo para validar las credenciales del usuario, realizar la verificación en dos pasos, recibir su respuesta y, a continuación, responder a la solicitud de acceso RADIUS.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [integrar con la autenticación RADIUS](multi-factor-authentication-nps-extension.md) si tiene Azure Multi-Factor Authentication en la nube. 