---
title: RDG y Servidor Azure MFA mediante RADIUS | Microsoft Docs
description: "Se trata de la página de Azure Multi-Factor Authentication que le ayudará en la implementación de la puerta de enlace de escritorio remoto (RD) y el servidor Azure Multi-Factor Authentication mediante RADIUS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/27/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 3b4181701c5df03a3df7e0446b313eac201ad99e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Puerta de enlace de Escritorio remoto y Servidor Azure Multi-Factor Authentication con RADIUS
A menudo, Puerta de enlace de Escritorio remoto utiliza los servicios de directivas de red (NPS) local para autenticar a los usuarios. En este artículo se describe cómo enrutar la solicitud RADIUS desde Puerta de enlace de Escritorio remoto (a través del NPS local) a Servidor Multi-Factor Authentication. La combinación de Azure MFA y Puerta de enlace de Escritorio remoto significa que los usuarios pueden tener acceso a sus entornos de trabajo desde cualquier lugar mientras realicen una autenticación segura. 

Puesto que no se admite la autenticación de Windows para los servicios de terminal en Server 2012 R2, utilice Puerta de enlace de Escritorio remoto y RADIUS para integrar con el servidor MFA. 

Instale el servidor Azure Multi-Factor Authentication en un servidor independiente, que luego entrega la solicitud RADIUS a NPS en el servidor de Puerta de enlace de Escritorio remoto. Una vez que NPS valida el nombre de usuario y la contraseña, devuelve una respuesta al servidor Microsoft Azure Multi-Factor Authentication. Luego, el servidor MFA ejecuta el segundo factor de autenticación y devuelve un resultado a la puerta de enlace.

## <a name="prerequisites"></a>Requisitos previos

- Un servidor Azure MFA unido a un dominio. Si no tiene uno ya instalado, siga los pasos de [Introducción a Servidor Azure Multi-factor Authentication](multi-factor-authentication-get-started-server.md).
- Una Puerta de enlace de Escritorio remoto que se autentica con los servicios de la directiva de red.

## <a name="configure-the-remote-desktop-gateway"></a>Configuración de Puerta de enlace de Escritorio remoto
Configure Puerta de enlace de Escritorio remoto para enviar la autenticación RADIUS a una instancia de Servidor Azure Multi-Factor Authentication. 

1. En Administrador de puerta de enlace de Escritorio remoto, haga clic en el nombre del servidor y seleccione **Propiedades**.
2. Vaya a la pestaña **Almacén de CAP de RD** y seleccione **Servidor central que ejecuta NPS**. 
3. Agregue una o más instancias de Servidor Azure Multi-Factor Authentication como servidores RADIUS al especificar una dirección IP en cada servidor. 
4. Cree un secreto compartido para cada servidor.

## <a name="configure-nps"></a>Configuración de NPS
La puerta de enlace de escritorio remoto usa NPS para enviar la solicitud de RADIUS a la Azure Multi-Factor Authentication. Para configurar NPS, primero cambie la configuración de tiempo de espera para evitar que Puerta de enlace de Escritorio remoto agote el tiempo de espera antes de que se complete la verificación en dos pasos. Después, actualice NPS para recibir autenticaciones RADIUS desde el servidor MFA. Use el procedimiento siguiente para configurar NPS:

### <a name="modify-the-timeout-policy"></a>Modificación de la directiva de tiempo de espera

1. En NPS, abra el menú **Clientes y servidores RADIUS** de la columna izquierda y seleccione **Grupos de servidores RADIUS remotos**. 
2. Seleccione el **GRUPO DE SERVIDORES DE PUERTA DE ENLACE DE TS**. 
3. Vaya a la pestaña **Equilibrio de carga**. 
4. Cambie tanto **Segundos transcurridos sin respuesta antes de que la solicitud se considere anulada** como **Segundos transcurridos entre solicitudes cuando el servidor se identifica como no disponible** a un valor que oscile entre 30 y 60 segundos. (Si encuentra que el servidor sigue agotando el tiempo de espera durante la autenticación, puede regresar a este punto y aumentar el número de segundos).
5. Vaya a la pestaña **Autenticación/Cuenta** y compruebe de que los puertos RADIUS especificados coincidan con los puertos en los que está escuchando la instancia de Servidor Multi-Factor Authentication.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Preparación de NPS para recibir autenticaciones del servidor MFA

1. Haga clic en **Clientes RADIUS** bajo Clientes y servidores RADIUS en la columna izquierda y seleccione **Nuevo**.
2. Agregue el servidor de Azure Multi-Factor Authentication como cliente RADIUS. Elija un nombre descriptivo y especifique un secreto compartido.
3. Abra el menú **Directivas** en la columna izquierda y seleccione **Directivas de solicitud de conexión**. Debe ver una directiva denominada DIRECTIVA DE AUTORIZACIÓN DE PUERTA DE ENLACE TS creada al configurar la puerta de enlace RD. Esta directiva reenvía las solicitudes RADIUS al servidor Multi-Factor Authentication.
4. Haga clic en **DIRECTIVA DE AUTORIZACIÓN DE PUERTA DE ENLACE TS** y seleccione **Duplicar directiva**. 
5. Abra la nueva directiva y vaya a la pestaña **Condiciones**.
6. Agregue una condición que haga coincidir el nombre descriptivo del cliente con el nombre descriptivo establecido en el paso 2 para el cliente RADIUS de Servidor Azure Multi-Factor Authentication. 
7. Vaya a la pestaña **Configuración** y seleccione **Autenticación**.
8. Cambie el proveedor de autenticación a **Autenticar solicitudes en este servidor**. Esta directiva garantiza que cuando NPS recibe una solicitud RADIUS desde el servidor Azure MFA, la autenticación se produce localmente en lugar de enviar una solicitud RADIUS a Servidor Azure Multi-Factor Authentication que daría lugar a una condición de bucle. 
9. Para evitar una condición de bucle, asegúrese de que la nueva directiva se ordena encima de la directiva original, en el panel **Directivas de solicitud de conexión**.

## <a name="configure-azure-multi-factor-authentication"></a>Configuración de Azure Multi-Factor Authentication

El servidor Azure Multi-Factor Authentication se configura como un proxy RADIUS entre la puerta de enlace de escritorio remoto y NPS.  Debe instalarse en un servidor unido a un dominio independiente del servidor de puerta de enlace de escritorio remoto. Use el procedimiento siguiente para configurar el servidor Azure Multi-Factor Authentication.

1. Abra Servidor Azure Multi-Factor Authentication y seleccione el icono de autenticación RADIUS. 
2. Active la casilla **Habilitar autenticación RADIUS**.
3. En la pestaña Clientes, asegúrese de que los puertos coincidan con lo que se configura en NPS y seleccione **Agregar**.
4. Agregue la dirección IP del servidor de Puerta de enlace de Escritorio remoto, el nombre de la aplicación (opcional) y un secreto compartido. El secreto compartido debe ser el mismo en Servidor Azure Multi-Factor Authentication y en Puerta de enlace de Escritorio remoto.
3. Vaya a la pestaña **Destino** y seleccione el botón de opción **Servidores RADIUS**.
4. Seleccione **Agregar** y escriba la dirección IP, el secreto compartido y los puertos del servidor NPS. A menos que use un NPS central, el cliente RADIUS y el destino RADIUS coincidirán. El secreto compartido debe coincidir con el configurado en la sección de cliente RADIUS del servidor NPS.

![Autenticación Radius](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="next-steps"></a>Pasos siguientes

- Integración de Azure MFA y [aplicaciones web de IIS](multi-factor-authentication-get-started-server-iis.md)

- Obtenga respuestas en las [P+F sobre Azure Multi-Factor Authentication](multi-factor-authentication-faq.md).
