---
title: "Azure AD Connect: Actualizar el certificado SSL para una granja de Servicios de federación de Active Directory (AD FS) | Microsoft Docs"
description: En este documento se detallan los pasos para actualizar el certificado SSL de una granja de AD FS mediante Azure AD Connect.
services: active-directory
keywords: "azure ad connect, actualizar ssl de adfs, actualizar certificado de adfs, cambiar certificado de adfs, nuevo certificado de adfs, certificado de adfs, actualizar certificado ssl de adfs, actualizar certificado ssl de adfs, configurar certificado ssl de adfs, adfs, ssl, certificado, certificado de comunicación de servicio de adfs, actualizar federación, configurar federación, aad connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Actualizar el certificado SSL para una granja de Servicios de federación de Active Directory (AD FS)

## <a name="overview"></a>Información general
En este artículo se describe cómo se puede usar Azure AD Connect para actualizar el certificado SSL para la granja de Servicios de federación de Active Directory (AD FS).  Si el método de inicio de sesión en Azure AD Connect se establece en AD FS, la herramienta Azure AD Connect puede usarse para actualizar fácilmente el certificado SSL de la granja de AD FS, a través de todos los servidores de federación y WAP en tres pasos sencillos:

![Tres pasos](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Para más información sobre los certificados usados por AD FS, consulte el artículo [Descripción de los certificados usados por AD FS](https://technet.microsoft.com/library/cc730660.aspx)

##<a name="pre-requisites"></a>Requisitos previos

* **Granja de AD FS**: la granja de AD FS debe ser 2012 R2 o superior
* **Azure AD Connect**: asegúrese de que la versión de Azure AD Connect es 1.1.443.0 o más reciente. Encontrará la tarea "Actualización del certificado SSL de AD FS".

![Tarea de actualización de SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>Paso 1: Proporcionar información de la granja de AD FS

AAD Connect intentará obtener automáticamente la información sobre la granja de AD FS. Para ello, realizará las tareas siguientes:
1. Consultar la información de la granja de AD FS (2016 o posterior)
2. Hacer referencia a la información de las ejecuciones anteriores (que se almacena localmente con Azure AD Connect). 

La lista de servidores que aparecen puede modificarse mediante la adición o eliminación de los servidores a fin de reflejar la configuración actual de la granja de AD FS. Tan pronto como se proporciona la información del servidor, Azure AD Connect muestra la conectividad y el estado actual del certificado SSL.

![Información del servidor de AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Si la lista contiene un servidor que ya no forma parte de la granja de AD FS, entonces haga clic en Quitar para eliminar el servidor de la lista de servidores de la granja de AD FS. 

![Servidor sin conexión en la lista](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> La eliminación de la lista de servidores de la granja de AD FS en Azure AD Connect es una operación local y actualiza la información de la granja de AD FS que Azure AD Connect mantiene localmente. Azure AD Connect no realizará ninguna modificación de configuración en AD FS para reflejar el cambio.    

##<a name="step-2-provide-new-ssl-certificate"></a>Paso 2: Proporcionar el nuevo certificado SSL

Una vez que se confirma la información sobre los servidores de la granja de AD FS, Azure AD Connect solicita el nuevo certificado SSL. Proporcione un certificado PFX protegido con contraseña para continuar con la instalación. 

![Certificado SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
Una vez que se proporciona el certificado, Azure AD Connect realiza una serie de comprobaciones de requisitos previos en el certificado para asegurarse de que es apropiado para la granja de AD FS:
1.    El nombre de sujeto (SN) o el Nombre alternativo de sujeto (SAN) para el certificado es igual que el nombre del servicio de federación o es un certificado comodín.
2.    El certificado es válido durante más de 30 días.
3.    La cadena de confianza del certificado es válida. 
4.    El certificado está protegido con contraseña.

##<a name="step-3-select-servers-for-update"></a>Paso 3: Seleccionar los servidores para la actualización

En el paso siguiente, seleccione los servidores en los que debe actualizarse el certificado SSL. Los servidores que están sin conexión no se pueden seleccionar para la actualización. 

![Seleccionar los servidores para actualizar](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Una vez completada la configuración, Azure AD Connect mostrará el mensaje que indica el estado de la actualización y proporcionará una opción para comprobar el inicio de sesión de AD FS.

![Configuración completada](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>Preguntas más frecuentes

* **¿Cuál debe ser el nombre de sujeto del certificado para el nuevo certificado SSL de AD FS?**

    Azure AD Connect comprobará si el nombre de sujeto del certificado o el nombre alternativo de sujeto contiene el nombre del servicio de federación. Por ejemplo, si el nombre del servicio de federación es fs.contoso.com, el nombre de sujeto alternativo o el nombre de sujeto debe ser fs.contoso.com.  También se admiten certificados comodín. 

* **¿Por qué me piden de nuevo las credenciales en la página del servidor de WAP?**

    Si las credenciales proporcionadas para conectarse a servidores de AD FS no tienen el privilegio para administrar también los servidores de WAP, Azure AD Connect le pedirá las credenciales que tienen privilegios administrativos en los servidores de WAP.

* **El servidor aparece como sin conexión, ¿qué debo hacer?**

    Azure AD Connect no puede realizar ninguna operación si el servidor está sin conexión. Si el servidor forma parte de la granja de AD FS, compruebe la conectividad al servidor y después de resolver el problema, haga clic en el icono de actualización para actualizar el estado en el asistente. Si el servidor formó parte de la granja de anteriormente pero ahora ya no existe, haga clic en Quitar para eliminarlo de la lista de servidores que mantiene Azure AD Connect.  Quitar el servidor de la lista en Azure AD Connect no modifica la configuración propia de AD FS y, si usa AD FS 2016 o una versión posterior, el servidor conservará los valores de configuración y se mostrará de nuevo en la siguiente ejecución de la tarea.

* **¿Puedo actualizar un subconjunto de los servidores de la granja con el nuevo certificado SSL?**

    Sí. Siempre puede ejecutar la tarea "Actualizar certificado SSL" para actualizar el resto de servidores. En la página "Seleccionar servidores para la actualización del certificado SSL", puede ordenar la lista de servidores por "Fecha de expiración de SSL" para tener acceso fácilmente a los servidores que todavía no se han actualizado. 

* **Quité el servidor en la ejecución anterior, pero todavía se muestra como sin conexión y aparece en la página de servidores de AD FS. ¿Por qué sigue estando sin conexión el servidor incluso después de la eliminación?**

    Quitar el servidor de la lista en Azure AD Connect no lo quita de la configuración de AD FS. Azure AD Connect también hace referencia a AD FS (2016 o posterior) para cualquier información sobre la granja y si el servidor sigue estando presente en la configuración de AD FS, se volverá a enumerar en la lista.  

## <a name="next-steps"></a>Pasos siguientes

[Azure AD Connect y federación](active-directory-aadconnectfed-whatis.md)
[Administración y personalización de Servicios de federación de Active Directory con Azure AD Connect](active-directory-aadconnect-federation-management.md)

