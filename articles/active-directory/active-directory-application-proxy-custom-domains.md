<properties
	pageTitle="Uso de dominios personalizados en el proxy de la aplicación de Azure AD"
	description="Explica cómo trabajar con dominios personalizados en el proxy de la aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="rkarlin"/>

# Uso de dominios personalizados en el proxy de la aplicación de Azure AD
> [AZURE.NOTE]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn532272.aspx).

El uso de un dominio predeterminado permite establecer la misma dirección URL como URL interna y externa de acceso a la aplicación a fin de que los usuarios solo tengan que recordar una dirección URL para tener acceso a la aplicación, sin importar desde dónde lo hagan. Además, permite crear un acceso directo único en el Panel de acceso para la aplicación. Si usa el dominio predeterminado que proporciona el proxy de la aplicación de Azure AD, no tendrá que realizar ninguna configuración adicional para habilitar el dominio. En caso de que use un dominio personalizado, hay algunas cosas que debe hacer para asegurarse de que el proxy de la aplicación reconoce su dominio y valida sus certificados.

## Selección de un dominio personalizado

1. Publique la aplicación según las instrucciones de publicación de aplicaciones con el proxy de la aplicación.
2. Cuando la aplicación aparezca en la lista de aplicaciones, selecciónela y haga clic en **Configurar**.
3. En **Dirección URL externa**, escriba el dominio personalizado.
4. Si la dirección URL externa es https, se le pedirá que cargue un certificado para que Azure pueda validar la dirección URL de la aplicación. También puede cargar un certificado comodín que coincida con la dirección URL externa de la aplicación. Este dominio debe estar en la lista de los [dominios comprobados de Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure debe tener un certificado para la dirección URL del dominio de la aplicación o un certificado comodín que coincida con la dirección URL externa de la aplicación.
5. Asegúrese de agregar un registro DNS que enrute la dirección URL interna a la aplicación que le permita tener la misma dirección URL para el acceso interno y externo y un solo acceso directo a la aplicación en la lista de aplicaciones de usuario.

## Preguntas más frecuentes sobre cómo trabajar con dominios personalizados

P: ¿Puedo seleccionar un certificado ya cargado sin cargarlo de nuevo? <br> R: Los certificados cargados previamente se enlazan automáticamente a una aplicación y existe exactamente un certificado que coincida con el nombre de host de la aplicación. <br> …<br> P: ¿Cómo puedo agregar un certificado y con qué formato debe cargarse el certificado exportado? <br> R: El certificado se debe cargar desde la página de configuración de la aplicación. El certificado debe ser un archivo PFX. <br> …<br> P: ¿Se pueden usar certificados ECC? <br> R: No hay ninguna limitación explícita en cuanto a métodos de firma. <br> …<br> P: ¿Se pueden usar certificados de SAN? <br> R: Sí.<br> …<br> P: ¿Se pueden usar certificados comodín? <br> R: Sí. <br> …<br> P: ¿Se puede usar un certificado diferente en cada aplicación? <br> R: Sí, a menos que las dos aplicaciones compartan el mismo host externo. <br> …..<br> P: ¿Si registro un nuevo dominio, puedo usar ese dominio? <br> R: Sí, la lista de dominios se basa en la lista de dominios comprobados del inquilino. <br> …<br> P: ¿Qué sucede cuando un certificado expira? <br> R: Recibirá una advertencia en la sección de certificados de la página de configuración de la aplicación. Cuando un usuario intenta tener acceso a la aplicación, aparece una advertencia de seguridad. <br> …<br> P: ¿Qué he de hacer si quiero reemplazar un certificado para una aplicación determinada? <br> R: Cargar un nuevo certificado desde la página de configuración de la aplicación.<br> ...<br> P: ¿Puedo eliminar un certificado y reemplazarlo? <br> R: Al cargar un nuevo certificado, si el certificado antiguo no se usa en otra aplicación, se eliminará automáticamente.<br> ...<br> P: ¿Qué ocurre cuando se revoca un certificado?<br> R: No se realizan comprobaciones de revocación de los certificados. Cuando un usuario intenta obtener acceso a la aplicación, dependiendo del explorador, es posible que aparezca una advertencia de seguridad.<br> …<br> P: ¿Puedo usar un certificado autofirmado? <br> R: Sí, los certificados autofirmados están permitidos. Tenga en cuenta que si usa una entidad de certificación privada, el CDP (punto de distribución de lista de revocación de certificados) del certificado debe ser público. <br> ...<br> P: ¿Existe un lugar en donde ver todos los certificados de mi inquilino? <br> R: Esta posibilidad no se admite en la versión actual.<br>



## Recursos adicionales

* [Registro en Azure como una organización](..sign-up-organization.md)
* [Identidad de Azure](..fundamentals-identity.md)

<!---HONumber=August15_HO8-->