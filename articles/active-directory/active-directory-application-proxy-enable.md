---
title: "Azure AD App Proxy: introducción a la instalación del conector | Microsoft Docs"
description: "Active el proxy de aplicación en Azure Portal e instale los conectores del proxy inverso."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 09497144ef4047c1191f3c02afccb881d48aaf3c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>Empezar a trabajar con el proxy de aplicación e instalar el conector
En este artículo se explican los pasos para habilitar el proxy de aplicación de Microsoft Azure AD en el directorio en la nube de Azure AD.

Si aún no conoce los beneficios de seguridad y productividad que el proxy de aplicación aporta a su organización, obtenga más información sobre [cómo proporcionar acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Requisitos previos del proxy de la aplicación
Antes de poder habilitar y usar los servicios del proxy de la aplicación, debe tener lo siguiente:

* Una [suscripción Basic o Premium a Microsoft Azure AD](active-directory-editions.md) y un directorio de Azure AD del que sea administrador global.
* Un servidor en el que se ejecute Windows Server 2012 R2 o 2016 y en el que se pueda instalar el conector del proxy de aplicación. El servidor necesita tener la capacidad de conectarse a los servicios del proxy de aplicación en la nube y a las aplicaciones locales que va a publicar.
  * Para habilitar el inicio de sesión único en las aplicaciones publicadas mediante la delegación limitada de kerberos, esta máquina debe estar unida al mismo dominio de AD que las aplicaciones que va a publicar. Para obtener información, vea la información sobre [la delegación limitada de kerberos para el inicio de sesión único con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md).

Si su organización usa servidores proxy para conectarse a Internet, lea [Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md) para más información sobre cómo configurarlos antes de comenzar a usar el proxy de aplicación.

## <a name="open-your-ports"></a>Apertura de los puertos

Para preparar su entorno para el proxy de aplicación de Azure AD, primero debe habilitar la comunicación con los centros de datos de Azure. Si hay un firewall en la ruta de acceso, es preciso asegurarse de que está abierto con el fin de que el conector pueda realizar solicitudes HTTPS (TCP) al proxy de la aplicación.

1. Abra los siguientes puertos al tráfico **saliente**:

   | Número de puerto | Cómo se usa |
   | --- | --- |
   | 80 | Descarga de listas de revocación de certificados (CRL) al validar el certificado SSL |
   | 443 | Toda la comunicación saliente con el servicio del proxy de aplicación |

   Si el firewall fuerza el tráfico según los usuarios que se originan, abra estos puertos para el tráfico de los servicios de Windows que se ejecutan como un servicio de red.

   > [!IMPORTANT]
   > La tabla refleja los requisitos de puerto para la versión 1.5.132.0 y versiones más recientes del conector. Si todavía tiene una versión anterior del conector, también debe habilitar los siguientes puertos aparte del 80 y el 443: 5671, 8080, 9090-9091, 9350, 9352, 10100-10120.
   >
   >Para obtener información sobre cómo actualizar los conectores a la versión más reciente, vea [Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md#automatic-updates).

2. Si el firewall o el proxy permiten la creación de listas blancas con DNS, puede incluir en una de ellas las conexiones en msappproxy.net y servicebus.windows.net. En caso contrario, tiene que permitir el acceso a los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653), que se actualizan cada semana.

3. Microsoft usa cuatro direcciones para verificar certificados. Permita el acceso a las siguientes direcciones URL si aún no se ha hecho para otros productos:
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. El conector necesita tener acceso a login.windows.net y login.microsoftonline.net para el proceso de registro.

5. Use la [herramienta de prueba Connector Ports del proxy de aplicación de Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para comprobar que el conector puede comunicarse con el servicio Proxy de aplicación. Como mínimo, asegúrese de que la región de centro de EE. UU. y la región más cercana tienen todas las marcas de verificación en verde. Además, cuantas más marcas de verificación verde haya, mayor resistencia habrá.

## <a name="install-and-register-a-connector"></a>Instalación y registro de un conector
1. Inicie sesión como administrador en [Azure Portal](https://portal.azure.com/).
2. El directorio actual aparece con el nombre de usuario en la esquina superior derecha. Si necesita cambiar directorios, seleccione ese icono.
3. Vaya a **Azure Active Directory** > **Proxy de aplicación**.

   ![Vaya a Proxy de aplicación](./media/active-directory-application-proxy-enable/app_proxy_navigate.png)

4. Seleccione **Descargar conector**.

   ![Descarga del conector](./media/active-directory-application-proxy-enable/download_connector.png)

5. Ejecute **AADApplicationProxyConnectorInstaller.exe** en el servidor que preparó según los requisitos previos.
6. Siga las instrucciones del asistente para instalar. Durante la instalación se le pedirá que registre el conector en el proxy de aplicación de su inquilino de Azure AD.

   * Proporcione sus credenciales de administrador global de Azure AD. Su inquilino de administrador global puede ser diferente de sus credenciales de Microsoft Azure.
   * Asegúrese de que el administrador que registra el conector está en el mismo directorio donde habilitó el servicio Proxy de aplicación. Por ejemplo, si el dominio del inquilino es contoso.com, el administrador debe ser admin@contoso.com o cualquier otro alias de ese dominio.
   * Si la opción **Configuración de seguridad mejorada de IE** está establecida en **Activado** en el servidor donde va a instalar el conector, la pantalla de registro podría no aparecer. Para acceder, siga las instrucciones del mensaje de error. Asegúrese de que Internet Explorer Enhanced Security está desactivado.

Si desea tener alta disponibilidad, debe implementar al menos dos conectores. Cada conector debe estar registrado de manera independiente.

## <a name="test-that-the-connector-installed-correctly"></a>Prueba de la instalación correcta del conector

Puede comprobar si un nuevo conector se instaló correctamente desde Azure Portal o en el servidor. 

En Azure Portal, inicie sesión en el inquilino y navegue hasta **Azure Active Directory** > **Proxy de aplicación**. Todos los conectores y grupos de conectores aparecen en esta página. Seleccione un conector para ver sus detalles o para moverlo a un grupo de conectores diferente. 

En el servidor, compruebe la lista de servicios activos para el conector y el actualizador del conector. Los dos servicios deben empezar a ejecutarse inmediatamente, pero si no es así, actívelos: 

   * **Conector de Proxy de aplicación de Microsoft AAD** habilita la conectividad

   * **Actualizador del conector del proxy de aplicación de Microsoft AAD** es un servicio de actualización automática. El actualizador busca nuevas versiones del conector y lo actualiza según sea necesario.

   ![Servicios de conector del proxy de la aplicación (captura de pantalla)](./media/active-directory-application-proxy-enable/app_proxy_services.png)

Para obtener información sobre los conectores y cómo actualizarlos, vea [Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md).


## <a name="next-steps"></a>Pasos siguientes
Ya está listo para la [publicación de aplicaciones mediante el proxy de aplicación](application-proxy-publish-azure-portal.md).

Si tiene aplicaciones que están en redes independientes o en diferentes ubicaciones, use grupos de conectores para organizar los distintos conectores en unidades lógicas. Obtenga más información sobre cómo [trabajar con conectores de Proxy de aplicación](active-directory-application-proxy-connectors-azure-portal.md).
