<properties 
	pageTitle="Cifrado dinámico:  Configuración de la directiva de autorización de claves de contenido mediante .NET" 
	description="Aprenda a configurar una directiva de autorización para una clave de contenido." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>



# Cifrado dinámico: Configuración de la directiva de autorización de claves de contenido 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)] 

Este artículo forma parte de las series [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](../media-services-video-on-demand-workflow) y [Flujo de trabajo de streaming en vivo](../media-services-live-streaming-workflow). 

## Información general

Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado (dinámicamente) con el Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y DRM de PlayReady. Servicios multimedia también proporciona un servicio para entregar las claves y licencias de PlayReady a los clientes autorizados. 

Actualmente, puede cifrar los siguientes formatos de streaming: HLS, MPEG DASH y Smooth Streaming. No se puede cifrar el formato de streaming HDS o las descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, deberá asociar una clave de cifrado (**CommonEncryption** o **EnvelopeEncryption**) con el recurso (como se describe [aquí](../media-services-dotnet-create-contentkey/)) y configurar directivas de autorización para la clave (como se describe en este artículo). 

Cuando un reproductor solicita una secuencia, Servicios multimedia usa la clave especificada para cifrar dinámicamente el contenido con cifrado AES o PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está autorizado o no para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. La directiva de autorización de claves de contenido podría tener una o más restricciones de autorización: **abierta**, restricción de **token** o restricción de **IP**. La directiva restringida de token debe ir acompañada por un token enviado por servicio de token de seguridad (STS). Servicios multimedia admite tokens en el formato de **tokens de web simple** ([SWT](https://msdn.microsoft.com/es-es/library/gg185950.aspx#BKMK_2)) y **token de web JSON** (JWT).  

Servicios multimedia no proporciona servicios de token de seguridad. Puede crear un STS personalizado o aprovechar el ACS de Microsoft Azure para emitir tokens. El STS debe configurarse para crear un token firmado con la clave especificada y emitir las notificaciones especificadas en la configuración de restricción de tokens (como se describe en este artículo). El servicio de entrega de claves de Servicios multimedia devolverá la clave de cifrado al cliente si el token es válido y las notificaciones del token coinciden con las configuradas para la clave de contenido.

Para obtener más información, consulte 

[Autenticación de token de JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integre las aplicaciones basadas en OWIN MVC de Servicios multimedia de Azure con Active Directory de Azure y restrinja la entrega de claves de contenido basado en notificaciones JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso de ACS de Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

### Se aplican algunas consideraciones:

- Para poder usar el empaquetado dinámico y el cifrado dinámico, debe asegurarse de tener al menos una unidad de escala (también conocida como unidad de streaming). Para obtener más información, consulte [Escalación de un servicio multimedia](../media-services-manage-origins#scale_streaming_endpoints). 
- El recurso debe contener un conjunto de archivos de Smooth Streaming de velocidad de bits múltiple y MP4 de velocidad de bits múltiple. Para obtener más información, consulte [Codificación de un recurso](../media-services-encode-asset/).  
- Cargue y codifique sus recursos con la opción **AssetCreationOptions.StorageEncrypted**.
- Si piensa tener varias claves de contenido que requieren la misma configuración de directiva, se recomienda encarecidamente crear una única directiva de autorización y reutilizarla con varias claves de contenido.
- El servicio de entrega de claves almacena en caché ContentKeyAuthorizationPolicy y sus objetos relacionados (opciones de directivas y restricciones) durante 15 minutos.  Si crea un ContentKeyAuthorizationPolicy y especifica el uso de una restricción "Token", después la prueba y, a continuación, actualiza la directiva a la restricción "Abierta", tardará aproximadamente 15 minutos antes de que la directiva se active en la versión "Abierta" de la directiva.
- Si agrega o actualiza la directiva de entrega de su recurso, debe eliminar un localizador existente (si existe) y crear un nuevo localizador.


##Cifrado dinámico de AES-128 

###Restricción abierta

La restricción abierta significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas.

En el ejemplo siguiente se crea una directiva de autorización abierta y la agrega a la clave de contenido.
	
	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy             
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("Open Authorization Policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	        new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


### Restricción de token

En esta sección se describe cómo crear una directiva de autorización de claves de contenido y la asocia con la clave de contenido. La directiva de autorización describe qué requisitos de autorización se deben cumplir para determinar si el usuario está autorizado a recibir la clave (por ejemplo, si la lista de "clave de verificación" contiene la clave con la que se ha firmado el token).

Para configurar la opción de restricción de token, deberá utilizar un XML para describir los requisitos de autorización del token. El XML de configuración de restricción del token debe cumplir el siguiente esquema XML.

#### <a id="schema"></a>Esquema de restricción de token
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

Al configurar la directiva de restricción de **token**, debe especificar los parámetros de ** clave de verificación principal**, **emisor** y **público**. La **clave de verificación principal **contiene la clave con la que se firmó el token, el **emisor** es el servicio de token de seguridad que emite el token. El **público** (a veces denominado **ámbito**) describe la intención del token o del recurso al que el token autoriza el acceso. El servicio de entrega de claves de Servicios multimedia valida que estos valores del token coinciden con los valores de la plantilla. 

Al usar el **SDK de Servicios multimedia para .NET**, puede utilizar la clase **TokenRestrictionTemplate** para generar el token de restricción.
En el ejemplo siguiente se crea una directiva de autorización con una restricción de token. En este ejemplo, el cliente podría presentar un token que contenga: la clave de firma (VerificationKey), un emisor de tokens y las notificaciones necesarias.
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

#### <a id="test"></a>Token de prueba

Para obtener un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves, haga lo siguiente.
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word "Bearer" in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


## Cifrado dinámico de PlayReady 

Servicios multimedia le permiten configurar los derechos y restricciones que desea para el tiempo de ejecución de DRM de PlayReady para exigirlo cuando un usuario intenta reproducir contenido protegido. 

Al proteger su contenido con PlayReady, una de las cosas que debe especificar en la directiva de autorización es una cadena XML que define la [plantilla de licencia de PlayReady](https://msdn.microsoft.com/es-es/library/azure/dn783459.aspx). En el SDK de Servicios multimedia para. NET, las clases **PlayReadyLicenseResponseTemplate** y **PlayReadyLicenseTemplate** le ayudarán a definir la plantilla de licencia de PlayReady. 

### Restricción abierta
	
La restricción abierta significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas.

En el ejemplo siguiente se crea una directiva de autorización abierta y la agrega a la clave de contenido.

	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy          
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Open", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	            Requirements = null
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

### Restricción de token

Para configurar la opción de restricción de token, deberá utilizar un XML para describir los requisitos de autorización del token. El XML de la configuración de restricción de token debe ajustarse al esquema XML que se muestra en [esta](#schema) sección .
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
	static private string ConfigurePlayReadyLicenseTemplate()
	{
	    // The following code configures PlayReady License Template using .NET classes
	    // and returns the XML string.
	             
	    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	    responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	}

Para obtener un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves, consulte [esta](#test) sección . 

## <a id="types"></a>Tipos usados al definir ContentKeyAuthorizationPolicy

### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }

### <a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



## Pasos siguientes
Ahora que ha configurado la directiva de autorización de la clave de contenido, consulte el tema [Configuración de la directiva de entrega de recursos](../media-services-dotnet-configure-asset-delivery-policy/).

<!--HONumber=45--> 
