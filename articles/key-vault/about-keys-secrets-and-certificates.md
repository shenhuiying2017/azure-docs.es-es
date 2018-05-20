---
title: Información acerca de claves, secretos y certificados
description: Información general de la interfaz REST y detalles para el desarrollador de Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 2c7dd89d9c2e5d50f2533101499a6e50e52047b3
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="about-keys-secrets-and-certificates"></a>Información acerca de claves, secretos y certificados
Los usuarios pueden usar Azure Key Vault para almacenar y utilizar claves criptográficas en el entorno de Microsoft Azure. Key Vault admite varios tipos de claves y algoritmos y habilita el uso de módulos de seguridad de hardware (HSM) para claves de alto valor. Además, Key Vault permite a los usuarios almacenar secretos de forma segura. Los secretos son objetos de octeto de tamaño limitado sin ninguna semántica específica. Key Vault también admite certificados, que se basan en claves y secretos, y agrega una característica de renovación automática.

Para más información acerca de Azure Key Vault, consulte [¿Qué es Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Información general de Key Vault**

-   [Compatibilidad con estándares](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Tipos de datos](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objetos, identificadores y control de versiones](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Información acerca de las claves**

-   [Claves y tipos de clave](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algoritmos RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algoritmos RSA-HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Protección criptográfica](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Operaciones con claves](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Atributos de la clave](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Etiquetas de la clave](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Información acerca de los secretos** 

-   [Trabajo con secretos](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Atributos del secreto](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Etiquetas del secreto](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Control de acceso al secreto](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Información acerca de los certificados**

-   [Composición de un certificado](#BKMK_CompositionOfCertificate)  
-   [Etiquetas y atributos de certificado](#BKMK_CertificateAttributesAndTags)  
-   [Directiva de certificados](#BKMK_CertificatePolicy)  
-   [Emisor de certificados](#BKMK_CertificateIssuer)  
-   [Contactos de certificados](#BKMK_CertificateContacts)  
-   [Control de acceso al certificado](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Información general de Key Vault

Las secciones siguientes ofrecen información general aplicable a la implementación del servicio de Azure Key Vault.

###  <a name="BKMK_Standards"></a> Compatibilidad con estándares

Las especificaciones de notación de objetos JavaScript (JSON) y de firma y cifrado de objetos JavaScript (JOSE) son fuentes importantes de información general.  

-   [Clave web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Cifrado web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Firma web JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Tipos de datos

Consulte las [especificaciones JOSE](#BKMK_Standards) para tipos de datos relevantes para claves, cifrado y firma.  

-   **algorithm**: un algoritmo admitido para una operación de clave, por ejemplo, RSA1_5  
-   **ciphertext-value**: octetos de texto cifrado codificados con Base64URL  
-   **digest-value**: la salida de un algoritmo de hash codificada con Base64URL  
-   **key-type**: uno de los tipos de clave admitidos; por ejemplo, RSA.  
-   **plaintext-value**: octetos de texto no cifrado codificados con Base64URL  
-   **signature-value**: la salida de un algoritmo de firma codificada con Base64URL  
-   **base64URL**: un valor binario codificado con Base64URL [RFC4648]  
-   **boolean**: true o false  
-   **Identity**: una identidad de Azure Active Directory (AAD).  
-   **IntDate**: un valor decimal JSON que representa el número de segundos desde 1970-01-01T0:0:0Z UTC hasta la fecha y hora UTC especificada. Consulte [RFC3339] para más información acerca de la fecha y hora en general, y la hora UTC en particular.  

###  <a name="BKMK_ObjId"></a> Objetos, identificadores y control de versiones

Los objetos almacenados en Azure Key Vault mantienen las versiones cuando se crea una nueva instancia de un objeto y cada versión tiene una dirección URL y un identificador únicos. Cuando se crea un objeto por primera vez, se le asigna un identificador de versión único y se marca como la versión actual del objeto. La creación de una nueva instancia con el mismo nombre de objeto proporciona al nuevo objeto un identificador de versión único y hace que se convierta en la versión actual.  

Los objetos de Azure Key Vault se pueden tratar con el identificador actual o con un identificador específico de la versión. Por ejemplo, dada una clave con el nombre "MasterKey", realizar operaciones con el identificador actual hace que el sistema use la última versión disponible. Realizar operaciones con el identificador específico de la versión hace que el sistema use esa versión específica del objeto.  

Los objetos se identifican de manera única en Azure Key Vault mediante una dirección URL de tal forma que no haya dos objetos en el sistema, con independencia de la ubicación geográfica, que tengan la misma dirección URL. La dirección URL completa de un objeto se llama Identificador del objeto y se compone de un prefijo que identifica el almacén de claves, el tipo de objeto, un nombre del objeto proporcionado por el usuario y una versión del objeto. El nombre del objeto es inmutable y no distingue entre mayúsculas y minúsculas. Los identificadores que no incluyen la versión del objeto se conocen como identificadores base.  

Para más información, consulte [Autenticación, solicitudes y respuestas](authentication-requests-and-responses.md)

Un identificador de objeto tiene el formato general siguiente:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Donde:  

|||  
|-|-|  
|`keyvault-name`|Nombre de un almacén de claves en el servicio Microsoft Azure Key Vault.<br /><br /> Los nombres de los almacenes de claves los selecciona el usuario y son globalmente únicos.<br /><br /> El nombre de un almacén de claves debe ser una cadena con una longitud de 3 a 24 caracteres que solo contenga (0-9, a-z, A-Z y -).|  
|`object-type`|Tipo de objeto, ya sea "claves" o "secretos".|  
|`object-name`|`object-name` es un nombre proporcionado por el usuario y debe ser único dentro de un almacén de claves. El nombre debe ser una cadena con una longitud de 1 a 127 caracteres que solo contenga 0-9, a-z, A-Z y -.|  
|`object-version`|`object-version` es un identificador de cadena de 32 caracteres generada por el sistema que, opcionalmente, se utiliza para referirse a una versión única de un objeto.|  

## <a name="key-vault-keys"></a>Claves en Key Vault

###  <a name="BKMK_KeyTypes"></a> Claves y tipos de clave

Las claves criptográficas en Azure Key Vault se representan como objetos de clave web JSON [JWK]. Las especificaciones de JWK/JWA de base también se han ampliado para habilitar tipos de clave únicos para la implementación de Azure Key Vault; por ejemplo, la importación de claves en Azure Key Vault mediante el empaquetado específico del proveedor (Thales) de HSM para habilitar el transporte seguro de las claves, de forma que se puedan utilizar únicamente en los HSM de Azure Key Vault.  

La versión inicial de Azure Key Vault admite solo claves RSA; las versiones futuras pueden admitir otros tipos de clave, como la simétrica o la de curva elíptica.  

-   **RSA**: una clave RSA de 2048 bits. Se trata de una clave "débil" que Key Vault procesa en software, aunque se almacena cifrada en reposo con una clave del sistema que se encuentra en un HSM. Los clientes pueden importar una clave RSA existente o solicitar que Azure Key Vault genere una.  
-   **RSA-HSM**: una clave RSA que se procesa en un HSM. Las claves RSA-HSM se protegen en uno de los espacios de seguridad de HSM de Azure Key Vault (hay un espacio de seguridad en cada región geográfica para mantener el aislamiento). Los clientes pueden importar una clave RSA, de forma temporal o exportándola desde un dispositivo HSM compatible o solicitar que Azure Key Vault genere una. Este tipo de clave agrega el atributo T al objeto JWK para transportar el material de la clave de HSM.  

     Para más información acerca de los límites geográficos, consulte [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algoritmos RSA  
 Los siguientes identificadores de algoritmo son compatibles con las claves RSA en Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5**: cifrado de clave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP**: RSAES con relleno de cifrado asimétrico óptimo (OAEP) [RFC3447], con los parámetros predeterminados especificados en RFC 3447 en la sección A.2.1. Estos parámetros predeterminados utilizan una función hash de SHA-1 y una función de generación de máscara de MGF1 con SHA-1.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256**: RSASSA-PKCS-v1_5 con SHA-256. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-256 y debe tener 32 bytes de longitud.  
-   **RS384**: RSASSA-PKCS-v1_5 con SHA-384. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-384 y debe tener 48 bytes de longitud.  
-   **RS512**: RSASSA-PKCS-v1_5 con SHA-512. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-512 y debe tener 64 bytes de longitud.  
-   **RSNULL**: consulte [RFC2437], un caso de uso especializado para habilitar ciertos escenarios de TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algoritmos RSA-HSM  
Los siguientes identificadores de algoritmo son compatibles con las claves RSA-HSM en Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Protección criptográfica

Los módulos criptográficos que utiliza Azure Key Vault, sean HSM o software, tienen validación FIPS. No es necesario hacer nada especial para que se ejecute en el modo FIPS. Si **crean** o **importan** claves como protegidas con HSM, se garantiza que se procesan dentro de los HSM validados de acuerdo con FIPS 140-2 nivel 2 o posterior. Si **crean** o **importan** claves como protegidas por software, se procesan dentro de los módulos criptográficos validados de acuerdo con FIPS 140-2 nivel 1 o posterior. Para más información, consulte [Claves y tipos de clave](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>WRAP/UNWRAP, ENCRYPT/DECRYPT

-   **RSA1_5**: cifrado de clave RSAES-PKCS1-V1_5 [RFC3447].  
-   **RSA-OAEP**: RSAES con relleno de cifrado asimétrico óptimo (OAEP) [RFC3447], con los parámetros predeterminados especificados en RFC 3447 en la sección A.2.1. Estos parámetros predeterminados utilizan una función hash de SHA-1 y una función de generación de máscara de MGF1 con SHA-1.  

 #### <a name="signverify"></a>SIGN/VERIFY  

-   **RS256**: RSASSA-PKCS-v1_5 con SHA-256. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-256 y debe tener 32 bytes de longitud.  
-   **RS384**: RSASSA-PKCS-v1_5 con SHA-384. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-384 y debe tener 48 bytes de longitud.  
-   **RS512**: RSASSA-PKCS-v1_5 con SHA-512. El valor de hash proporcionado por la aplicación debe calcularse mediante SHA-512 y debe tener 64 bytes de longitud.  
-   RSNULL: consulte [RFC2437], un caso de uso especializado para habilitar ciertos escenarios de TLS.  

###  <a name="BKMK_KeyOperations"></a> Operaciones con claves

Azure Key Vault admite las siguientes operaciones en objetos de clave:  

-   **Create (Crear)**: permite a un cliente crear una clave en Azure Key Vault. El valor de la clave es generado y almacenado por Azure Key Vault y no se entrega al cliente. Se pueden crear claves asimétricas en Azure Key Vault (y en el futuro, de curva elíptica y simétricas).  
-   **Import (Importar)**: permite a un cliente importar una clave existente en Azure Key Vault. Se pueden importar claves asimétricas en Azure Key Vault (y en el futuro, de curva elíptica y simétricas) mediante una serie de métodos de empaquetado diferentes dentro de una construcción JWK.  
-   **Update (Actualizar)**: permite a un cliente con los permisos suficientes modificar los metadatos (atributos de la clave) asociados con una clave almacenada previamente en Azure Key Vault.  
-   **Delete (Eliminar)**: permite a un cliente con los permisos suficientes eliminar una clave de Azure Key Vault.  
-   **List (Enumerar)**: permite a un cliente enumerar todas las claves de un determinado almacén de Azure Key Vault.  
-   **List versions (Enumerar versiones)**: permite a un cliente enumerar todas las versiones de una clave determinada en un determinado almacén de Azure Key Vault.  
-   **Get (Obtener)**: permite a un cliente recuperar las partes públicas de una clave determinada en un almacén de Azure Key Vault.  
-   **Backup (Copia de seguridad)**: exporta una clave en un formato protegido.  
-   **Restore (Restaurar)**: importa una clave desde una copia de seguridad previa.  

Para más información, consulte [Operaciones con claves](/rest/api/keyvault/key-operations.md)  

Una vez creada una clave en Azure Key Vault, se pueden realizar las siguientes operaciones criptográficas con la clave:  

-   **Firmar y verificar**: estrictamente, esta operación es "firmar un hash" o "verificar un hash", ya que Azure Key Vault no admite la creación de un hash del contenido como parte de la creación de la firma. Las aplicaciones deben crear el hash de los datos que se van a firmar de modo local y, a continuación, solicitar que Azure Key Vault firme el hash. La verificación de valores de hash firmados se admite por comodidad para las aplicaciones que no pueden tener acceso al material de la clave [pública]; se recomienda que, para mejorar el rendimiento de las aplicaciones, las operaciones de verificación se realicen de forma local.  
-   **Cifrado/Encapsulado de clave**: una clave almacenada en Azure Key Vault se puede utilizar para proteger otra clave, normalmente una clave de cifrado de contenido (CEK) simétrica. Cuando la clave en Azure Key Vault es asimétrica, se usa el cifrado de clave, por ejemplo RSA-OAEP y las operaciones WRAPKEY/UNWRAPKEY (ENCAPSULAR CLAVE/DESENCAPSULAR CLAVE) son equivalentes a ENCRYPT/DECRYPT (CIFRAR/DESCIFRAR). Cuando la clave en Azure Key Vault es simétrica, se usa el encapsulado de clave; por ejemplo, AES-KW. La operación WRAPKEY (ENCAPSULAR CLAVE) se admite por comodidad para las aplicaciones que no pueden tener acceso al material de la clave [pública]; se recomienda que, para mejorar el rendimiento de aplicaciones, las operaciones WRAPKEY se realicen localmente.  
-   **Cifrar y descifrar**: una clave almacenada en Azure Key Vault se puede utilizar para cifrar o descifrar un único bloque de datos, el tamaño de los cuales se determina por el tipo de clave y el algoritmo de cifrado seleccionado. La operación de cifrado se proporciona por comodidad para las aplicaciones que no pueden tener acceso al material de la clave [pública]; se recomienda que, para mejorar el rendimiento de aplicaciones, las operaciones de cifrado se realicen localmente.  

Aunque WRAPKEY/UNWRAPKEY (ENCAPSULAR CLAVE/DESENCAPSULAR CLAVE) con claves asimétricas pueda parecer superfluo porque la operación es equivalente a CIFRAR/DESCIFRAR (ENCRYPT/DECRYPT), el uso de operaciones distintas se considera importante para proporcionar separación semántica y de autorización de estas operaciones, así como coherencia cuando el servicio admita otros tipos de clave.  

Azure Key Vault no admite operaciones de exportación: cuando se aprovisiona una clave en el sistema no se puede extraer ni modificar el material de la clave.  Sin embargo, los usuarios de Azure Key Vault que puedan requerir la clave para otros casos de uso o después de que se ha eliminado en Azure Key Vault, pueden usar las operaciones de copia de seguridad y restauración para importar o para exportar la clave en un formato protegido. Las claves creadas por la operación de copia de seguridad no son utilizables fuera de Azure Key Vault. Como alternativa, la operación de importación se puede utilizar en varias instancias de Azure Key Vault.  

Los usuarios pueden restringir cualquiera de las operaciones criptográficas que admite Azure Key Vault en una base por cada clave mediante la propiedad key_ops del objeto JWK.  

Para más información acerca de los objetos JWK, consulte [Clave web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Atributos de clave

Además del material de la clave, se pueden especificar los siguientes atributos. En una solicitud JSON, la palabra clave attributes y las llaves, "{' '}", son obligatorias aunque no haya ningún atributo especificado.  

- *enabled*: booleano, opcional, el valor predeterminado es **true**. Especifica si la clave está habilitada y se puede utilizar para operaciones criptográficas. El atributo *enabled* se utiliza junto con *nbf* y *exp*. Cuando se produce una operación entre *nbf* y *exp*, solo se permitirá si *enabled* está establecido en **true**. Las operaciones fuera de la franja *nbf* / *exp* se establecen automáticamente como no permitidas, salvo en determinados tipos de operación en [condiciones particulares](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *nbf*: tipo IntDate, opcional, el valor predeterminado es now (ahora). El atributo *nbf* (no antes de) identifica la hora antes de la cual la clave NO DEBE utilizarse para operaciones criptográficas, excepto para ciertos tipos de operación en [condiciones particulares](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). El procesamiento del atributo *nbf* requiere que la fecha y hora actual sea posterior o igual que la fecha y hora enumerada en el atributo *nbf*. Azure Key Vault puede proporcionar un pequeño margen, normalmente no más de unos minutos, para considerar el sesgo del reloj. Su valor debe ser un número que contenga un valor IntDate.  
- *exp*: tipo IntDate, opcional, el valor predeterminado es "forever" (indefinidamente). El atributo *exp* (hora de expiración) identifica la hora después de la cual la clave no debe utilizarse para operaciones criptográficas, excepto para ciertos tipos de operación en las [condiciones particulares](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). El procesamiento del atributo *exp* requiere que la fecha y hora actual sea anterior a la fecha y hora enumerada en el atributo *exp*. Azure Key Vault puede proporcionar un pequeño margen, normalmente no más de unos minutos, para considerar el sesgo del reloj. Su valor debe ser un número que contenga un valor IntDate.  

Existen atributos de solo lectura adicionales que se incluyen en cualquier respuesta que incluya atributos de clave:  

- *created*: tipo IntDate, opcional. El atributo *created* indica cuándo se creó esta versión de la clave. Este valor es NULL para las claves creadas antes de agregar este atributo. Su valor debe ser un número que contenga un valor IntDate.  
- *updated*: tipo IntDate, opcional. El atributo *updated* indica cuándo se modificó esta versión de la clave. Este valor es NULL para las claves modificadas por última vez antes de agregar este atributo. Su valor debe ser un número que contenga un valor IntDate.  

Para más información acerca de IntDate y otros tipos de datos, consulte [Tipos de datos](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Operaciones controladas de fecha y hora

Las claves todavía no válidas y las expiradas, aquellas fuera de la franja *nbf* / *exp*, funcionarán para las operaciones **decrypt**, **unwrap** y **verify** (no devolverán 403, prohibido). La razón de usar el estado "todavía no válido" es permitir que una clave se pruebe antes de su uso en producción. La razón de usar el estado "expirado" es permitir las operaciones de recuperación de datos que se crearon cuando la clave era válida. Además, puede deshabilitar el acceso a una clave mediante directivas de Key Vault o actualizando el atributo de clave *enabled* como **false**.

Para más información acerca de tipos de datos, consulte [Tipos de datos](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Para más información acerca de otros posibles atributos, consulte [Clave web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Etiquetas de la clave

Puede especificar metadatos específicos de la aplicación adicionales en forma de etiquetas. Azure Key Vault admite hasta 15 etiquetas, cada una de las cuales puede tener un nombre de 256 caracteres y un valor de 256 caracteres.  

>[!Note]
>Un llamador puede leer las etiquetas si tiene el permiso *list* o *get* para ese tipo de objeto: claves, secretos o certificados.

###  <a name="BKMK_KeyAccessControl"></a> Control de acceso a claves

El control de acceso para las claves administradas por Key Vault se proporciona en el nivel de un almacén de claves que actúa como contenedor de claves. Hay una directiva de control de acceso para las claves que es distinta de la directiva de control de acceso para los secretos en el mismo almacén de claves. Los usuarios pueden crear uno o varios almacenes para almacenar las claves y están obligados a mantener la segmentación adecuada del escenario y la administración de claves. El control de acceso para claves es independiente del control de acceso para secretos.  

Se pueden conceder los permisos siguientes, por usuario o por entidad de servicio, en la entrada de control de acceso a las claves de un almacén. Estos permisos reflejan fielmente las operaciones permitidas en un objeto de clave:  

-   *create*: crear nuevas claves
-   *get*: leer la parte pública de una clave, además de sus atributos
-   *list*: enumerar las claves o las versiones de una clave almacenada en un almacén de claves
-   *update*: actualizar los atributos de una clave
-   *delete*: eliminar el objeto de clave
-   *sign*: usar la clave para firmar hashes
-   *verify*: usar la clave para verificar hashes
-   *wrapKey*: usar la clave para proteger una clave simétrica
-   *unwrapKey*: usar la clave para desproteger claves simétricas
-   *encrypt*: usar la clave para proteger una secuencia arbitraria de bytes
-   *decrypt*: usar la clave para desproteger una secuencia arbitraria de bytes
-   *import*: importar una clave a un almacén de claves
-   *backup*: copia de seguridad de una clave de un almacén de claves
-   *restore*: restaurar una copia de seguridad de una clave a un almacén de claves
-   *all*: todos los permisos

## <a name="key-vault-secrets"></a>Secretos en Key Vault 

###  <a name="BKMK_WorkingWithSecrets"></a> Trabajo con secretos

Los secretos en Azure Key Vault son secuencias de octetos con un tamaño máximo de 25 Kbytes cada uno. El servicio de Azure Key Vault no proporciona ninguna semántica para los secretos; simplemente acepta los datos, los cifra y los almacena, devolviendo un identificador de secreto, "id", que puede utilizarse para recuperar el secreto en un momento posterior.  

Para datos confidenciales, los clientes deben considerar el uso de capas adicionales de protección de los datos que se almacenan en Azure Key Vault; por ejemplo, mediante el cifrado previo de los datos mediante una clave de protección independiente.  

Azure Key Vault también admite un campo contentType para secretos. Los clientes pueden especificar el tipo de contenido, "contentType", de un secreto para ayudar a interpretar los datos secretos cuando se recuperen. La longitud máxima de este campo es de 255 caracteres. No hay ningún valor predefinido. El uso sugerido es como sugerencia para interpretar los datos secretos. Por ejemplo, una implementación puede almacenar contraseñas y certificados como secretos y, a continuación, utilizar este campo para indicar cuál. No hay ningún valor predefinido.  

###  <a name="BKMK_SecretAttrs"></a> Atributos del secreto

Además los datos secretos, se pueden especificar los siguientes atributos:  

- *exp*: tipo IntDate, opcional, el valor predeterminado es **forever** (indefinidamente). El atributo *exp* (hora de expiración) identifica la hora de expiración después de la cual no se deben recuperar los datos secretos, excepto en las [situaciones particulares](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). El procesamiento del atributo *exp* requiere que la fecha y hora actual sea anterior a la fecha y hora enumerada en el atributo *exp*. Azure Key Vault puede proporcionar un pequeño margen, normalmente no más de unos minutos, para considerar el sesgo del reloj. Su valor debe ser un número que contenga un valor IntDate.  
- *nbf*: tipo IntDate, opcional, el valor predeterminado es **now** (ahora). El atributo *nbf* (no antes de) identifica la hora antes de la cual no se deben recuperar los datos secretos, excepto en las [situaciones particulares](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). El procesamiento del atributo *nbf* requiere que la fecha y hora actual sea posterior o igual que la fecha y hora enumerada en el atributo *nbf*. Azure Key Vault puede proporcionar un pequeño margen, normalmente no más de unos minutos, para considerar el sesgo del reloj. Su valor debe ser un número que contenga un valor IntDate.  
- *enabled*: booleano, opcional, el valor predeterminado es **true**. Este atributo especifica si se pueden recuperar los datos secretos o no. El atributo enabled se usa junto con nbf y *exp* y cuando se produce una operación entre nbf y exp, solo se permitirá si enabled se establece en **true**. Las operaciones fuera de la franja entre *nbf* y *exp* se deniegan automáticamente, excepto en [situaciones particulares](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Existen atributos de solo lectura adicionales que se incluyen en cualquier respuesta que incluya atributos de secreto:  

- *created*: tipo IntDate, opcional. El atributo created indica cuándo se creó esta versión del secreto. Este valor es NULL para los secretos creados antes de agregar este atributo. Su valor debe ser un número que contenga un valor IntDate.  
- *updated*: tipo IntDate, opcional. El atributo updated indica cuándo se modificó esta versión del secreto. Este valor es NULL para los secretos modificados por última vez antes de agregar este atributo. Su valor debe ser un número que contenga un valor IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Operaciones controladas de fecha y hora

Una operación **get** de un secreto funcionará para los secretos todavía no válidos y los expirados, fuera de la franja *nbf* / *exp*. La llamada a la operación **get** de un secreto, para un secreto todavía no válido, se puede usar con fines de prueba. La obtención (**get**) de un secreto caducado se puede utilizar para operaciones de recuperación.

Para más información acerca de tipos de datos, consulte [Tipos de datos](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Control de acceso al secreto

El control de acceso para los secretos administrados por Azure Key Vault se proporciona en el nivel de un almacén de claves que actúa como contenedor de esos secretos. Hay una directiva de control de acceso para los secretos que es distinta de la directiva de control de acceso para las claves en el mismo almacén de claves. Los usuarios pueden crear uno o varios almacenes para almacenar los secretos y están obligados a mantener la segmentación adecuada del escenario y la administración de los secretos. El control de acceso para secretos es independiente del control de acceso para claves.  

Los siguientes permisos se pueden utilizar, en una base por entidad, en la entrada de control de acceso de secretos en un almacén y reflejan fielmente las operaciones permitidas en un objeto de secreto:  

-   *set*: crear nuevos secretos  
-   *get*: leer un secreto  
-   *list*: enumerar los secretos o las versiones de un secreto almacenado en un almacén de claves  
-   *delete*: eliminar el secreto  
-   *all*: todos los permisos  

Para más información acerca de cómo trabajar con secretos, consulte [Operaciones con secretos](/rest/api/keyvault/secret-operations.md).  

###  <a name="BKMK_SecretTags"></a> Etiquetas del secreto  
Puede especificar metadatos específicos de la aplicación adicionales en forma de etiquetas. Azure Key Vault admite hasta 15 etiquetas, cada una de las cuales puede tener un nombre de 256 caracteres y un valor de 256 caracteres.  

>[!Note]
>Un llamador puede leer las etiquetas si tiene el permiso *list* o *get* para ese tipo de objeto: claves, secretos o certificados.

## <a name="key-vault-certificates"></a>Certificados en Key Vault

La compatibilidad con certificados de Key Vault proporciona la administración de sus certificados x509 y los comportamientos siguientes:  

-   Permite que el propietario de un certificado cree un certificado a través de un proceso de creación de Key Vault o a través de la importación de un certificado existente. Esto incluye tanto certificados autofirmados como certificados generados por una entidad de certificación.
-   Permite que el propietario de un certificado de Key Vault implemente un almacenamiento seguro y la administración de certificados X509 sin interacción con material de clave privada.  
-   Permite que el propietario de un certificado cree una directiva que indique a Key Vault cómo administrar el ciclo de vida de un certificado.  
-   Permite que los propietarios de certificados proporcionen información de contacto para la notificación de eventos del ciclo de vida de caducidad y renovación de los certificados.  
-   Admite la renovación automática con emisores seleccionados: proveedores de certificados X509 y entidades de certificación asociados con Key Vault.

>[!Note]
>Los proveedores y entidades no asociados también pueden, pero en este caso no se admitirá la característica de renovación automática.

###  <a name="BKMK_CompositionOfCertificate"></a> Composición de un certificado

Cuando se crea un certificado de Key Vault, se crean también una clave direccionable y un secreto con el mismo nombre. La clave de Key Vault permite las operaciones de clave y el secreto de Key Vault permite la recuperación del valor del certificado como un secreto. Un certificado de Key Vault también contiene metadatos del certificado X.509 público.  

El identificador y la versión de los certificados es similar al de las claves y los secretos. Una versión específica de una clave direccionable y el secreto creados con la versión del certificado de Key Vault está disponible en la respuesta del certificado de Key Vault.
 
![Los certificados son objetos complejos](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Clave exportable o no exportable

Cuando se crea un certificado de Key Vault, este se puede recuperar desde el secreto direccionable con la clave privada en formato PFX o PEM si la directiva utilizada para crear el certificado indica que la clave es exportable. Si la directiva utilizada para crear el certificado de Key Vault indica que la clave no es exportable, la clave privada no es una parte del valor cuando se recupera como un secreto.  

La clave direccionable cobra más relevancia con los certificados de Key Vault no exportables. Las operaciones de la clave de Key Vault direccionable se asignan desde el campo *keyusage* de la directiva del certificado de Key Vault utilizada para crear el certificado de Key Vault.  

Se admiten dos tipos de clave con los certificados: *RSA* o *RSA HSM*. Los exportables solo se permiten con RSA, no se admiten en RSA HSM.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Etiquetas y atributos de certificado

Además de los metadatos del certificado, una clave direccionable y un secreto direccionable, un certificado de Key Vault también contiene atributos y etiquetas.  

#### <a name="attributes"></a>Atributos

Los atributos del certificado se reflejan en los atributos de la clave y el secreto direccionables que se crean cuando se crea el certificado de Key Vault.  

Un certificado de Key Vault tiene los siguientes atributos:  

-   *enabled*: booleano, opcional, el valor predeterminado es **true**. Este atributo se puede especificar para indicar si se pueden recuperar los datos del certificado como secreto o es operativo como una clave. Se usa junto con *nbf* y *exp* y cuando se produce una operación entre *nbf* y exp, solo se permitirá si enabled se establece en true. Las operaciones fuera de la franja entre*nbf* y *exp* se deniegan automáticamente.  

Existen atributos de solo lectura adicionales que se incluyen en la respuesta:

-   *created*: tipo IntDate, indica cuándo se creó esta versión del certificado.  
-   *updated*: tipo IntDate, indica cuándo se modificó esta versión del certificado.  
-   *exp*: tipo IntDate, contiene el valor de la fecha de expiración del certificado X.509.  
-   *nbf*: tipo IntDate, contiene el valor de la fecha del certificado X.509.  

> [!Note] 
> Si expira un certificado de Key Vault, la clave y el secreto direccionables dejan de funcionar.  

#### <a name="tags"></a>Etiquetas

 Diccionario especificado por el cliente de pares de clave y valor, similar a las etiquetas de las claves y los secretos.  

 > [!Note]
> Un llamador puede leer las etiquetas si tiene el permiso *list* o *get* para ese tipo de objeto: claves, secretos o certificados.

###  <a name="BKMK_CertificatePolicy"></a> Directiva de certificados

Una directiva de certificados contiene información acerca de cómo crear y administrar el ciclo de vida de un certificado de Key Vault. Cuando se importa un certificado con clave privada en Key Vault, se crea una directiva predeterminada mediante la lectura del certificado X.509.  

Cuando se crea un certificado de Key Vault desde cero, se debe proporcionar una directiva a Key Vault para informar acerca de cómo crear esta versión del certificado de Key Vault o la próxima versión del certificado de Key Vault. Una vez que se ha establecido una directiva, no se requiere en sucesivas operaciones create para crear las versiones del certificado de Key Vault siguientes.  

Solo hay una instancia de una directiva para todas las versiones de un certificado de Key Vault.  

En un nivel general, una directiva de certificado contiene lo siguiente:  

-   Propiedades del certificado X.509: contiene el nombre del sujeto, los nombres alternativos del sujeto, etc. Se usa para crear una solicitud de certificado X.509.  
-   Propiedades de clave: contiene el tipo de clave, la longitud y los campos de clave exportable y de reutilización. Estos campos indican a Key Vault cómo generar una clave.  
-   Propiedades del secreto: contiene las propiedades del secreto, como el tipo de contenido del secreto direccionable para generar el valor secreto, para recuperar el certificado como un secreto.  
-   Acciones de vigencia: contiene las acciones de vigencia del certificado de Key Vault. Cada acción de vigencia contiene:  

     - Desencadenador: se especifica como días antes de la expiración o como un porcentaje del intervalo de vigencia  

     - Acción: se especifica el tipo de acción: *emailContacts* o *autoRenew*  

-   Emisor: parámetros sobre el emisor del certificado que se utiliza para emitir certificados X.509.  
-   Atributos de directiva: contiene atributos asociados a la directiva  

#### <a name="x509-to-key-vault-usage-mapping"></a>Correspondencia entre el uso de X.509 y Key Vault

La tabla siguiente representa la correspondencia entre la directiva de uso de claves X.509 y las operaciones de clave efectivas de una clave creada como parte de la creación de certificados de Key Vault.

|**Marcas de uso de claves X.509**|**Operaciones de clave de Key Vault**|**Comportamiento predeterminado**|
|----------|--------|--------|
|DataEncipherment|cifrar, descifrar| N/D |
|DecipherOnly|Descifrado| N/D  |
|DigitalSignature|firmar, verificar| Valor predeterminado de Key Vault sin una especificación de uso en el momento de creación del certificado | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|firmar, verificar|N/D|
|KeyEncipherment|encapsular clave, desencapsular clave| Valor predeterminado de Key Vault sin una especificación de uso en el momento de creación del certificado | 
|NonRepudiation|firmar, verificar| N/D |
|crlsign|firmar, verificar| N/D |

###  <a name="BKMK_CertificateIssuer"></a> Emisor de certificados

Un objeto de certificado de Key Vault contiene una configuración que se usa para comunicarse con un proveedor de emisor de certificados seleccionado para solicitar certificados X.509.  

-   Key Vault está asociado con los proveedores de emisión de certificado siguientes para certificados SSL

|**Nombre del proveedor**|**Ubicaciones**|
|----------|--------|
|DigiCert|Compatible con todas las ubicaciones del servicio de Key Vault en la nube pública y Azure Government|
|GlobalSign|Compatible con todas las ubicaciones del servicio de Key Vault en la nube pública y Azure Government|

Para poder crear un emisor de certificados en un almacén de claves, deben realizarse correctamente los pasos de requisitos previos 1 y 2 siguientes.  

1. Incorporación de proveedores de entidad emisora (CA) de certificados  

    -   Un administrador de la organización debe incorporar su empresa (por ejemplo, Contoso) con al menos un proveedor de CA.  

2. El administrador crea las credenciales del solicitante para que Key Vault inscriba (y renueve) certificados SSL  

    -   Proporciona la configuración que se usará para crear un objeto de emisor del proveedor en el almacén de claves  

Para más información acerca de cómo crear objetos de emisor desde el portal de certificados, consulte el [blog de certificados de Key Vault](http://aka.ms/kvcertsblog)  

Key Vault permite la creación de varios objetos de emisor con diferente configuración del proveedor de emisor. Una vez que se crea un objeto de emisor, se puede hacer referencia a su nombre en una o varias directivas de certificado. Al hacer referencia al objeto de emisor se indica a Key Vault que utilice la configuración de acuerdo con lo especificado en el objeto de emisor al solicitar el certificado X.509 al proveedor de la entidad emisora de certificados durante la creación y la renovación de certificados.  

Los objetos de emisor se crean en el almacén y solo se pueden usar con certificados de Key Vault del mismo almacén.  

###  <a name="BKMK_CertificateContacts"></a> Contactos de certificados

Los contactos de certificados contienen información de contacto para enviar notificaciones desencadenadas por los eventos de vigencia del certificado. La información de los contactos es compartida por todos los certificados del almacén de claves. Se envía una notificación a todos los contactos especificados para un evento de cualquier certificado del almacén de claves.  

Si se establece la directiva de un certificado para la renovación automática, se envía una notificación en los siguientes eventos.  

-   Antes de la renovación del certificado
-   Tras la renovación del certificado, indicando si el certificado se renovó correctamente o si se produjo un error que exige la renovación manual del certificado.  

 Si se establece la directiva de certificado en renovación manual (solo correo electrónico), se envía una notificación cuando llega el momento de renovar el certificado.  

###  <a name="BKMK_CertificateAccessControl"></a> Control de acceso al certificado

 El control de acceso para los certificados es administrado por Key Vault y se proporciona en el nivel de un almacén de claves que actúa como contenedor de esos certificados. Hay una directiva de control de acceso para los certificados que es distinta de la directiva de control de acceso para las claves y los secretos en el mismo almacén de claves. Los usuarios pueden crear uno o varios almacenes para almacenar los certificados y están obligados a mantener la segmentación adecuada del escenario y la administración de los certificados.  

 Los siguientes permisos se pueden utilizar, en una base por entidad, en la entrada de control de acceso de secretos en un almacén de claves y reflejan fielmente las operaciones permitidas en un objeto de secreto:  

-   *get*: permite obtener la versión actual del certificado o cualquier versión de un certificado 
-   *list*: permite enumerar los certificados actuales o las versiones de un certificado  
-   *delete*: permite la eliminación de un certificado, su directiva y todas sus versiones  
-   *create*: permite crear un certificado de Key Vault.  
-   *import*: permite la importación de material del certificado en un certificado de Key Vault.  
-   *update*: permite la actualización de un certificado.  
-   *manageconnects*: permite la administración de los contactos del certificado de Key Vault  
-   *getissuers*: permite obtener los emisores de un certificado  
-   *listissuers*: permite enumerar los emisores de un certificado  
-   *setissuers*: permite crear o actualizar los emisores de certificados de Key Vault  
-   *deleteissuers*: permite eliminar los emisores de certificados de Key Vault  
-   *all*: concede todos los permisos  

## <a name="additional-information-for-certificates"></a>Información adicional sobre certificados

- [Certificados y directivas](/rest/api/keyvault/certificates-and-policies.md)
- [Emisores de certificados](/rest/api/keyvault/certificate-issuers.md)
- [Contactos de certificados](/rest/api/keyvault/certificate-contacts.md)

## <a name="see-also"></a>Otras referencias

- [Autenticación, solicitudes y respuestas](authentication-requests-and-responses.md)
- [Versiones de Key Vault](key-vault-versions.md)
- [Guía del desarrollador de Key Vault](/azure/key-vault/key-vault-developers-guide)
