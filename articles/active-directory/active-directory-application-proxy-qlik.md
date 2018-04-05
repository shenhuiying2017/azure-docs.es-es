---
title: Proxy de aplicación de Azure AD y Qlik Sense | Microsoft Docs
description: Active el proxy de aplicación en Azure Portal e instale los conectores del proxy inverso.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/26/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 331f8ed2e77a076dd8969dc37add1cdeafc852dc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy de aplicación y Qlik Sense 
Azure Active Directory Application Proxy y Qlik Sense se han asociado para garantizar que pueda usar fácilmente el proxy de aplicación para proporcionar acceso remoto para la implementación de Qlik Sense.  

## <a name="prerequisites"></a>requisitos previos 
Para el resto de este escenario, se da por sentado que ha hecho lo siguiente:
 
- Configurar [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- Instalar un conector de proxy de aplicación 

## <a name="install-an-application-proxy-connector"></a>Instalación de un conector del proxy de la aplicación 
Si ya tiene habilitado el proxy de aplicación y tiene instalado un conector, puede omitir esta sección y pasar a [agregar la aplicación a Azure AD con el proxy de aplicación](application-proxy-ping-access.md). 

El conector del proxy de la aplicación es un servicio de Windows Server que dirige el tráfico de los empleados remotos a las aplicaciones publicadas. Para más información acerca de las instrucciones de instalación, consulte [Habilitación del proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md). 


1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global. 
2. Seleccione Azure Active Directory > Proxy de aplicación 
3. Seleccione Descargar conector para iniciar la descarga del conector de proxy de la aplicación. Siga las instrucciones de instalación. 
 
>[!NOTE]
>La descarga del conector debería habilitar automáticamente el proxy de la aplicación en el directorio, pero si no lo hace, puede seleccionar **Habilitar el proxy de la aplicación**. 
 
## <a name="publish-your-applications-in-azure"></a>Publicación de aplicaciones en Azure 
Para publicar QlikSense, debe publicar dos aplicaciones en Azure.  

### <a name="application-1"></a>Aplicación n.º 1: 
Siga estos pasos para publicar la aplicación. Para obtener un tutorial más detallado de los pasos 1 a 8, consulte [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](application-proxy-publish-azure-portal.md). 


1. Si no lo hizo en la sección anterior, inicie sesión en Azure Portal como administrador global. 
2. Seleccione **Azure Active Directory** > **Aplicaciones empresariales**. 
3. Seleccione **Agregar** en la parte superior de la hoja. 
4. Seleccione **Aplicación local**. 
5.       Rellene los campos requeridos con la información de la aplicación nueva. Utilice las siguientes instrucciones para realizar la configuración: 
    - **Dirección URL interna**: esta aplicación debe tener una dirección URL interna que es la propia dirección URL de QlikSense. Por ejemplo, **https&#58;//demo.qlikemm.com** 
    - **Método de autenticación previa**: Azure Active Directory (se recomienda, pero no es obligatorio) 
1.       Seleccione **Agregar** en la parte inferior de la hoja. Se agrega la aplicación y se abre el menú de inicio rápido. 
2.       En dicho menú, seleccione **Asignar un usuario para las pruebas**  y agregue al menos un usuario a la aplicación. Asegúrese de que esta cuenta de prueba tiene acceso a la aplicación local. 
3.       Seleccione **Asignar** para guardar la asignación del usuario de prueba. 
4.       (Opcional) En la hoja de administración de la aplicación, seleccione Inicio de sesión único. Elija **Kerberos Constrained Delegation** (Delegación limitada de Kerberos) en el menú desplegable y rellene los campos obligatorios según la configuración de Qlik. Seleccione **Guardar**. 

### <a name="application-2"></a>Aplicación n.º 2: 
Siga los mismos pasos que para la aplicación n.º 1, con las siguientes excepciones: 

**Paso n.º 5**: la dirección URL interna ahora debe ser la dirección URL de QlikSense con el puerto de autenticación que utiliza la aplicación. El valor predeterminado es **4244** para HTTPS y 4248 para HTTP. Ej.: **https&#58;//demo.qlik.com:4244** 
**Paso n.º 10:** no configure SSO y deje el **inicio de sesión único deshabilitado**
 
 
## <a name="testing"></a>Prueba 
La aplicación ya se puede probar. Acceda a la dirección URL externa que usó para publicar QlikSense en la aplicación n.º 1 e inicie sesión como usuario asignado a ambas aplicaciones.  

## <a name="next-steps"></a>Pasos siguientes

- [Publicar aplicaciones con Proxy de aplicación](application-proxy-publish-azure-portal.md)
- [Trabajar con conectores de Proxy de aplicación](active-directory-application-proxy-connectors-azure-portal.md).
