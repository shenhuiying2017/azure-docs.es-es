---
title: "Dominios personalizados del proxy de aplicación de Azure AD | Microsoft Docs"
description: "Administre los dominios personalizados del proxy de aplicación de Azure AD para que la dirección URL de la aplicación sea la mismo independientemente de dónde accedan los usuarios."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: b8edebd4e7dfdbf85a9beb6d126acaf7ec66dd01


---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Uso de dominios personalizados en el proxy de la aplicación de Azure AD
El uso de un dominio predeterminado permite establecer la misma dirección URL como URL interna y externa para obtener acceso a la aplicación. Los usuarios solo tendrán que recordar una dirección URL para obtener acceso a la aplicación, sin importar desde dónde lo hagan. Esto también le permite crear un acceso directo único en el Panel de acceso para la aplicación. Si usa el dominio predeterminado que proporciona el proxy de la aplicación de Azure AD, no tendrá que realizar ninguna configuración adicional para habilitar el dominio. Si usa un dominio personalizado, hay algunas cosas que debe hacer para asegurarse de que el proxy de la aplicación reconoce su dominio y valida sus certificados.

## <a name="selecting-your-custom-domain"></a>Selección de un dominio personalizado
1. Publique la aplicación según las instrucciones de [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](active-directory-application-proxy-publish.md).
2. Cuando la aplicación aparezca en la lista de aplicaciones, selecciónela y haga clic en **Configurar**.
3. En **Dirección URL externa**, escriba el dominio personalizado.
4. Si la dirección URL externa es https, se le pedirá que cargue un certificado para que Azure pueda validar la dirección URL de la aplicación. También puede cargar un certificado comodín que coincida con la dirección URL externa de la aplicación. Este dominio debe estar en la lista de los [dominios comprobados de Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure debe tener un certificado para la dirección URL del dominio de la aplicación o un certificado comodín que coincida con la dirección URL externa de la aplicación.
5. Agregue un registro DNS que enruta la dirección URL interna a la aplicación. Este registro permite tener la misma dirección URL para el acceso interno y externo a la aplicación, así como un acceso directo único en la lista de aplicaciones del usuario.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Preguntas más frecuentes sobre cómo trabajar con dominios personalizados
P: ¿Puedo seleccionar un certificado ya cargado sin cargarlo de nuevo?  
R: Los certificados cargados previamente se enlazan automáticamente a una aplicación y hay exactamente un certificado que coincide con el nombre de host de la aplicación.  

P: ¿Cómo agrego un certificado y en qué formato debe cargarse el certificado exportado?  
R: El certificado se debe cargar desde la página de configuración de la aplicación. El certificado debe ser un archivo PFX.  

P: ¿Se pueden usar certificados ECC?  
R: No hay ninguna limitación explícita en cuanto a métodos de firma.  

P: ¿Se pueden usar certificados SAN?  
R: Sí.  

P: ¿Se pueden usar certificados comodín?  
R: Sí.  

P: ¿Se puede usar un certificado diferente en cada aplicación?  
R: Sí, a menos que las dos aplicaciones compartan el mismo host externo.  

P: ¿Si registro un nuevo dominio, puedo usar ese dominio?  
R: Sí, la lista de dominios se basa en la lista de dominios comprobados del inquilino.  

P: ¿Qué sucede cuando expira un certificado?  
R: Recibirá una advertencia en la sección de certificados de la página de configuración de la aplicación. Cuando un usuario intente tener acceso a la aplicación, aparecerá una advertencia de seguridad.  

P: ¿Qué debo hacer si quiero reemplazar un certificado para una aplicación determinada?  
R: Cargue un nuevo certificado desde la página de configuración de la aplicación.  

P: ¿Puedo eliminar un certificado y reemplazarlo?  
R: Al cargar un nuevo certificado, si el certificado antiguo no lo está usando otra aplicación, se eliminará automáticamente.  

P: ¿Qué sucede cuando se revoca un certificado?  
R: No se realizan comprobaciones de revocación para certificados. Cuando un usuario intenta acceder a la aplicación, según el explorador, es posible que aparezca una advertencia de seguridad.  

P: ¿Puedo usar un certificado autofirmado?  
R: Sí, se permiten los certificados autofirmados. Tenga en cuenta que, si usa una entidad de certificación privada, el CDP (punto de distribución de lista de revocación de certificados) del certificado debe ser público.  

P: ¿Existe un lugar en donde ver todos los certificados de mi inquilino?  
R: Esta posibilidad no se admite en la versión actual.  

## <a name="next-steps"></a>Pasos siguientes
* [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md) en las aplicaciones publicadas con la autenticación de Azure AD.
* [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md) a las aplicaciones publicadas.
* [Incorporación de su nombre de dominio personalizado a Azure AD](active-directory-add-domain.md)





<!--HONumber=Feb17_HO1-->


