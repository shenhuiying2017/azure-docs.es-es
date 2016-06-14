<properties
	pageTitle="Sustitución de claves de firma en Azure AD | Microsoft Azure"
	description="En este artículo se analizan las prácticas recomendadas de sustitución de claves firma de Azure Active Directory."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

# Sustitución de claves de firma de Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

En este tema se describe lo que necesita saber de las claves públicas que se usan en Azure Active Directory (Azure AD) para firmar los tokens de seguridad. Es importante tener en cuenta que estas claves se sustituyen cada 6 semanas. En caso de emergencia, pueden cambiarse mucho antes. Todas las aplicaciones que usan Azure AD deben poder controlar mediante programación el proceso de sustitución de claves. Siga leyendo este artículo para conocer cómo funcionan las claves y cómo actualizar la aplicación para controlar la sustitución de claves.

## Información general sobre las claves de firma de Azure AD

Azure AD emplea una criptografía de clave pública basada en estándares del sector con el fin de establecer una relación de confianza entre ella y las aplicaciones que la utilizan. En términos prácticos, funciona de la siguiente manera: Azure AD usa una clave de firma que consta de un par de claves pública y privada. Cuando un usuario inicia sesión en una aplicación que utiliza Azure AD para realizar la autenticación, Azure AD crea un token de seguridad que contiene información sobre el usuario. Azure AD firma este token con su clave privada antes de enviarlo a la aplicación. Para comprobar que el token es válido y que realmente se originó en Azure AD, la aplicación debe validar la firma del token usando la clave pública expuesta por Azure AD que se encuentra en el documento de metadatos de federación del inquilino. Esta clave pública, y la clave de firma de la que se deriva, es la misma que se utiliza en todos los inquilinos de Azure AD.

Por motivos de seguridad, las claves pública de Azure AD se sustituyen cada 6 semanas. En caso de emergencia, pueden sustituirse mucho antes. Cualquier aplicación que se integra con Azure AD debe estar preparada para controlar un evento de sustitución de claves, con independencia de la frecuencia con que se produzca. Dependiendo de dónde haya creado la aplicación y con qué biblioteca de autenticación se haya compilado, puede tener o no la lógica necesaria para controlar la sustitución de claves. Si no es así y la aplicación trata de utilizar una clave pública expirada para comprobar la firma de un token, se producirá un error en la solicitud de inicio de sesión.

Dado que una clave se puede sustituir en cualquier momento, siempre hay más de una clave pública válida disponible en el documento de metadatos de federación. La aplicación debe estar preparada para utilizar cualquiera de las claves especificadas del documento, ya que una de ellas puede sustituirse pronto, otra puede ser su reemplazo, etc. Se recomienda que la aplicación almacene estas claves en la memoria caché de una base de datos o un archivo de configuración para aumentar la eficacia de la comunicación con Azure AD durante el proceso de inicio de sesión, así como para validar rápidamente un token con una clave distinta.

## Procedimiento para actualizar la aplicación con la lógica de sustitución de claves

Cómo la aplicación controla la sustitución de claves depende de ciertas variables, por ejemplo, qué marco de identidad se utiliza, la versión de marco de trabajo o el tipo de aplicación. En cada una de las secciones siguientes se mostrará cómo actualizar los tipos de aplicaciones y configuraciones más comunes. También puede seguir los pasos para asegurarse de que la lógica funcione correctamente.

### Aplicaciones web creadas con Visual Studio 2013

Si la aplicación se compiló utilizando una plantilla de aplicación web en Visual Studio 2013 y seleccionó **Cuentas profesionales** en el menú **Cambiar autenticación**, ya tendrá la lógica necesaria para controlar la sustitución de claves. Esta lógica almacena la información de la clave de firma y el identificador único de la organización en dos tablas de base de datos asociadas al proyecto. Puede encontrar la cadena de conexión de la base de datos en el archivo Web.config del proyecto.

Si ha agregado autenticación manualmente a la solución, la aplicación no tendrá la lógica de sustitución de claves necesaria. Tendrá que crearla expresamente, o bien seguir los pasos de la sección Recuperación manual de la clave más reciente y actualización de aplicaciones.

Los siguientes pasos lo ayudará a comprobar que la lógica funcione correctamente en la aplicación.

1. En Visual Studio 2013, abra la solución y, después, haga clic en la pestaña **Explorador de servidores** de la ventana derecha.
2. Expanda **Conexiones de datos**, **DefaultConnection** y, luego, **Tablas**. Busque la tabla **IssuingAuthorityKeys**, haga clic con el botón derecho en ella y, después, con el botón izquierdo, en **Mostrar datos de tabla**.
3. En la tabla **IssuingAuthorityKeys** tabla, habrá al menos una fila, que se corresponde con el valor de huella digital de la clave. Elimine las filas de la tabla.
4. Haga clic con el botón derecho en la tabla **Inquilinos** de tabla y, después, con el botón izquierdo, en **Mostrar datos de tabla**.
5. En la tabla **Inquilinos**, habrá al menos una fila, que se corresponde con un identificador del inquilino de directorio único. Elimine las filas de la tabla. Si no elimina las filas de las tablas **Inquilinos** y **IssuingAuthorityKeys**, se producirá un error en tiempo de ejecución.
6. Compile y ejecute la aplicación. Una vez que haya iniciado sesión en la cuenta, podrá detener la aplicación.
7. Vuelva a la pestaña **Explorador de servidores** y examine los valores de las tablas **IssuingAuthorityKeys** e **Inquilinos**. Observará que se han vuelto a rellenar automáticamente con la información correspondiente del documento de metadatos de federación.

### Aplicaciones web creadas con Visual Studio 2012

Si la aplicación se compiló en Visual Studio 2012, probablemente ha utilizado la herramienta de identidad y acceso para configurar la aplicación. También es probable que esté utilizando el [registro de nombres de emisor de validación (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). El VINR se encarga de mantener la información sobre los proveedores de identidad de confianza (Azure AD) y las claves utilizadas para validar los tokens que emiten. El VINR también facilita la tarea de actualizar automáticamente la información de claves almacenada en un archivo Web.config descargando el documento de metadatos de federación más reciente asociado a su directorio, comprobando si la configuración está actualizada con respecto al último documento y actualizando la aplicación para usar la nueva clave según sea necesario.

Si ha creado la aplicación utilizando cualquiera de las muestras código o la documentación de tutorial que proporciona Microsoft, la lógica de sustitución de claves ya estará incluida en el proyecto. Observará que el código siguiente ya existe en el proyecto. Si la aplicación aún no tiene esta lógica, siga estos pasos para agregarla y comprobar que funciona correctamente.

1. En **Explorador de soluciones**, agregue una referencia al ensamblado **System.IdentityModel** del proyecto correspondiente.
2. Abra el archivo **Global.asax.cs** y agregue lo siguiente utilizando directivas:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Agregue el método siguiente al archivo **Global.asax.cs**:
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Invoque el método **RefreshValidationSettings()** en el método **Application\_Start ()** de **Global.asax.cs**, tal y como se muestra:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Una vez que haya seguido estos pasos, el archivo Web.config de la aplicación se actualizará con la información más reciente del documento de metadatos de federación, incluidas las últimas claves. Esta actualización se producirá cada vez que recicle el grupo de aplicaciones de IIS; de forma predeterminada, esta acción se realizará cada 29 horas.

Siga los pasos que figuran a continuación para comprobar que la lógica de sustitución de claves funciona correctamente.

1. Una vez que haya comprobado que la aplicación está utilizando el código anterior, abra el archivo **Web.config** y desplácese al bloque **<issuerNameRegistry>**; busque expresamente las siguientes líneas:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. En la configuración de **<add thumbprint=””>**, cambie el valor de huella digital reemplazando cualquier carácter por otro diferente. Guarde el archivo **Web.config**.

3. Compile la aplicación y, después, ejecútela. Si puede completar el proceso de inicio de sesión, la aplicación actualizará correctamente la clave descargando la información necesaria del documento de metadatos de federación de su directorio. Si tiene problemas para iniciar sesión, asegúrese de que los cambios en la aplicación sean correctos consultando el tema [Adding Sign-On to Your Web Application Using Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) (Incorporación del inicio de sesión único en aplicaciones web mediante Azure AD), o bien descargarlos e inspeccionando el siguiente código de muestra: [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Aplicación multiempresa en la nube para Azure Active Directory).


### Las aplicaciones web creadas con Visual Studio 2008 o 2010 y Windows Identity Foundation (WIF) 1.0 para .NET 3.5

Si ha compilado una aplicación en la versión 1.0 de WIF, no habrá ningún mecanismo para actualizar automáticamente la configuración de la aplicación con el fin de usar una nueva clave. La manera más sencilla de actualizar la clave es usar las herramientas de FedUtil incluidas en el SDK de WIF, que pueden recuperar el documento de metadatos más recientes y actualizar la configuración. Estas instrucciones se incluyen a continuación. Como alternativa, puede realizar uno de los siguientes pasos:

- Siga las instrucciones de la sección Recuperación manual de la clave más reciente y actualización de aplicaciones y cree la lógica para realizar los pasos mediante programación.
- Actualice la aplicación a .NET 4.5, que incluye la versión más reciente de WIF ubicada en el espacio de nombres del sistema. Después, podrá utilizar el [registro de nombres de emisor de validación (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para realizar las actualizaciones automáticas de la configuración de la aplicación.


1. Compruebe que tiene instalado el SDK de la versión 1.0 de WIF en la máquina de desarrollo de Visual Studio 2008 o 2010. También puede [descargarlo desde aquí](https://www.microsoft.com/es-ES/download/details.aspx?id=4451) si aún no lo ha instalado.
2. En Visual Studio, abra la solución y, luego, haga clic con el botón derecho en el proyecto correspondiente y seleccione **Actualizar metadatos de federación**. Si esta opción no está disponible, significa que no se ha instalado FedUtil o el SDK de la versión 1.0 de WIF.
3. En el símbolo del sistema, seleccione **Actualizar** para iniciar la actualización de los metadatos de federación. Si tiene acceso al entorno de servidor donde está hospedada la aplicación, puede utilizar, si lo desea, el [Programador de actualización automática de metadatos](https://msdn.microsoft.com/library/ee517272.aspx) de FedUtil.
4. Haga clic en **Finalizar** para completar el proceso de actualización.

### API web que usan tokens web JSON (JWT)

Si tiene una aplicación que llama a una API web mediante un token JWT emitido mediante Azure AD para autorizar la solicitud, el token JWT se valida de la misma manera que una solicitud de inicio de sesión: utilizando la clave pública de Azure AD para comprobar la firma. Las aplicaciones API web tienen que estar preparadas para controlar la sustitución de claves, ya que, básicamente, usan el mismo certificado X509 para firmar el token.

Si creó una aplicación API web en Visual Studio 2013 con la plantilla de API web y, a después, seleccionó **Cuentas profesionales** en el menú **Cambiar autenticación**, la aplicación ya tendrá la lógica necesaria. Si configura manualmente la autenticación, siga estas instrucciones para aprender a configurar la API web con el fin de actualizar automáticamente la información de claves.

El fragmento de código siguiente muestra cómo obtener las claves más recientes del documento de metadatos de federación y, luego, utilizar el [controlador de token JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar el token. En el fragmento de código se da por hecho que va a utilizar su propio mecanismo de almacenamiento en caché para conservar la clave con el fin de validar los tokens futuros de Azure AD, ya sea en una base de datos, un archivo de configuración o en otro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### Recuperación manual de la clave más reciente y actualización de aplicaciones

Si el tipo de aplicación o plataforma no admite en estos momentos un mecanismo automático para actualizar la clave, puede realizar los pasos siguientes:

1. En un explorador web, vaya a https://manage.windowsazure.com, inicie sesión en su cuenta y, luego, haga clic en el icono de Active Directory del menú izquierdo.
2. Haga clic en el directorio donde se registra la aplicación y, luego, haga clic en el vínculo **Ver extremos** de la barra de comandos.
3. En la lista de puntos de conexión de directorio y de inicio de sesión único, copie el vínculo **Documento de metadatos de federación**.
4. Abra una nueva pestaña en el explorador y vaya a la dirección URL que acaba de copiar. Verá el contenido del documento XML de metadatos de federación. Para obtener más información sobre este documento, consulte el tema de metadatos de federación.
5. Para actualizar una aplicación que utilice una nueva clave, localice cada bloque **<RoleDescriptor>** y, luego, copie el valor de cada uno de ellos en el elemento **<X509Certificate>** del bloque. Por ejemplo:
```
<RoleDescriptor xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType">
      <KeyDescriptor use="signing">
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
                <X509Data>
                    <X509Certificate>MIIDPjC…BcXWLAIarZ</X509Certificate>
```
6. Después de copiar el valor del elemento **<X509Certificate>**, abra un editor de texto sin formato y pegue dicho valor. No se olvide de quitar los espacios en blanco finales y, luego, de guardar el archivo con una extensión **.cer**.

Acaba de crear el certificado X509 que se utiliza como la clave pública de Azure AD. Con los detalles del certificado, como su huella digital y fecha de expiración, puede comprobar manualmente o mediante programación que la huella digital y el certificado que utiliza actualmente la aplicación son válidos.

<!---HONumber=AcomDC_0601_2016-->