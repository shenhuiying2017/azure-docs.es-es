---
title: "Autenticación RADIUS y Servidor Azure MFA | Microsoft Docs"
description: "Se trata de la página Azure Multi-Factor Authentication que ayudará a implementar la autenticación RADIUS y el servidor Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/26/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: e696b95c9db86b062440f0c4fd788bf97223317a
ms.lasthandoff: 02/28/2017

---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Integración de la autenticación RADIUS con Servidor Azure Multi-Factor Authentication
Use la sección Autenticación RADIUS de Servidor Azure MFA para habilitar y configurar la autenticación RADIUS. RADIUS es un protocolo estándar para aceptar las solicitudes de autenticación y procesar estas solicitudes. Servidor Azure Multi-Factor Authentication actúa como servidor RADIUS. Insértelo entre el cliente RADIUS (dispositivo VPN) y el destino de autenticación, que puede ser Active Directory (AD), un directorio LDAP u otro servidor RADIUS, para agregar Azure Multi-Factor Authentication. Para que Azure Multi-Factor Authentication (MFA) funcione, debe configurar Servidor Azure MFA para que pueda comunicarse con los servidores de cliente y el destino de autenticación. El Servidor Azure MFA acepta solicitudes de un cliente RADIUS, valida las credenciales en el destino de autenticación, agrega Azure Multi-Factor Authentication y envía una respuesta al cliente RADIUS. La solicitud de autenticación se realizará correctamente solo si la autenticación principal y Azure Multi-Factor Authentication se ejecutan correctamente.

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

  El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.

  El secreto compartido debe ser el mismo en el servidor de Azure Multi-Factor Authentication y en el dispositivo/servidor. 

6. Active la casilla **Requerir coincidencia de usuario de Multi-Factor Authentication** si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al servidor un número significativo de usuarios o se van a excluir de la verificación en dos pasos, deje la casilla desactivada. 
7. Active la casilla **Habilitar token OATH de reserva** si desea usar códigos de acceso OATH desde aplicaciones de comprobación por móvil como reserva para la llamada de teléfono fuera de banda, el SMS o la notificación push.
8. Haga clic en **Aceptar**.

Repita los pasos del 4 al 8 para agregar todos los clientes RADIUS adicionales necesarios.

## <a name="configure-your-radius-client"></a>Configuración del cliente RADIUS

1. Haga clic en la pestaña **Destino**.
2. Si el Servidor Azure MFA está instalado en un servidor unido a un dominio de un entorno de Active Directory, seleccione el dominio de Windows.
3. Si los usuarios deben autenticarse en un directorio LDAP, seleccione **Enlace LDAP**. 

  Para usar un enlace LDAP, haga clic en el icono de integración de directorios y edite la configuración de LDAP en la pestaña Configuración para que el servidor pueda enlazar con su directorio. Puede encontrar instrucciones para configurar LDAP en la [Guía de configuración de proxy LDAP](multi-factor-authentication-get-started-server-ldap.md).

4. Si los usuarios deben autenticarse en otro servidor RADIUS, seleccione servidores RADIUS.
5. Haga clic en **Agregar** para configurar el servidor al que el Servidor Azure MFA entregará las solicitudes RADIUS.
6. En el cuadro de diálogo Agregar servidor RADIUS, escriba la dirección IP del servidor RADIUS y un secreto compartido. 

  El secreto compartido debe ser el mismo en el Servidor Azure Multi-Factor Authentication y en el servidor RADIUS. Cambie el puerto de autenticación y el de cuentas si el servidor RADIUS usa puertos diferentes.

7. Haga clic en **Aceptar**.
8. Agregue el Servidor Azure MFA como cliente RADIUS en el otro servidor RADIUS para que procese las solicitudes de acceso que se le envían desde el Servidor Azure MFA. Use el mismo secreto compartido configurado en el Servidor Azure Multi-Factor Authentication.

Repita estos pasos para agregar más servidores RADIUS y configure el orden en el que el Servidor Azure MFA debe llamarlos con los botones **Subir** y **Bajar**. 

Esto completa la configuración del servidor Azure Multi-Factor Authentication. Ahora, el servidor está escuchando en los puertos configurados las solicitudes de acceso RADIUS de los clientes configurados.   

## <a name="radius-client-configuration"></a>Configuración del cliente RADIUS
Para configurar el cliente RADIUS, siga las siguientes instrucciones:

* Configure el dispositivo/servidor para autenticarse mediante RADIUS en la dirección IP del servidor Azure Multi-Factor Authentication, que actuará como servidor RADIUS.
* Use el mismo secreto compartido que se configuró anteriormente.
* Configure el tiempo de espera de RADIUS en entre 30 y 60 segundos para que haya tiempo para validar las credenciales del usuario, realizar la verificación en dos pasos, recibir su respuesta y, a continuación, responder a la solicitud de acceso RADIUS.


