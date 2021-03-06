---
title: Automatización del aprovisionamiento de aplicaciones con SCIM en Azure Active Directory | Microsoft Docs
description: Azure Active Directory puede aprovisionar automáticamente los usuarios y grupos a cualquier aplicación o almacén de identidades proporcionado por un servicio web con la interfaz definida en la especificación del protocolo SCIM
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.openlocfilehash: 19a1ae7ae7acc6fe09a529dd174363735343027e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932020"
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Uso de System for Cross-Domain Identity Management para aprovisionar automáticamente a los usuarios y grupos de Azure Active Directory para aplicaciones

## <a name="overview"></a>Información general
Azure Active Directory (Azure AD) puede aprovisionar automáticamente a usuarios y grupos de cualquier aplicación o almacén de identidades dirigidos por un servicio web con la interfaz definida en la [especificación del protocolo System for Cross-Domain Identity Management (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory puede enviar solicitudes para crear, modificar o eliminar los usuarios y grupos asignados al servicio web. El servicio web puede convertir entonces esas solicitudes en operaciones en el almacén de identidades de destino. 

![][0]
*Figura 1: aprovisionamiento de Azure Active Directory en un almacén de identidades a través de un servicio web*

Esta funcionalidad puede usarse junto con la de aportación de la propia aplicación en Azure AD para habilitar el inicio de sesión único y el aprovisionamiento automático de usuarios para aplicaciones que proporcionan un servicio web SCIM o que son dirigidas por uno de estos servicios.

Existen dos casos de uso de SCIM en Azure Active Directory:

* **Aprovisionamiento de usuarios y grupos para aplicaciones que admiten SCIM**: las aplicaciones compatibles con SCIM 2.0 y que usan un token de portador de OAuth para la autenticación funcionan con Azure AD sin tener que configurarse.
* **Creación de una solución de aprovisionamiento propia para aplicaciones que admiten otro aprovisionamiento basado en API**: para las aplicaciones que no son SCIM, puede crear un punto de conexión SCIM para la traducción entre el punto de conexión SCIM de Azure AD y cualquier API que admita la aplicación para el aprovisionamiento de usuarios. Para facilitar el desarrollo de un punto de conexión SCIM, existen bibliotecas de Common Language Infrastructure (CLI) junto con ejemplos de código que muestran cómo proporcionar un punto de conexión SCIM y traducir los mensajes SCIM.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamiento de usuarios y grupos para aplicaciones que admiten SCIM
Azure AD puede configurarse para aprovisionar automáticamente a usuarios y grupos asignados a aplicaciones que implementan un servicio web [System for Cross-Domain Identity Management 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) y que aceptan tokens de portador de OAuth para la autenticación. Dentro de la especificación SCIM 2.0, las aplicaciones deben cumplir estos requisitos:

* Admitir la creación de usuarios o grupos, según la sección 3.3 del protocolo SCIM.  
* Admitir la modificación de usuarios o grupos con solicitudes de revisión, según la sección 3.5.2 del protocolo SCIM.  
* Admitir la recuperación de un recurso conocido, según la sección 3.4.1 del protocolo SCIM.  
* Admitir la consulta de usuarios o grupos, según la sección 3.4.2 del protocolo SCIM.  De forma predeterminada, los usuarios se consultan por externalId y los grupos por displayName.  
* Admitir la consulta de usuarios por Id. y administrador, según la sección 3.4.2 del protocolo SCIM.  
* Admitir la consulta de grupos por Id. y miembro, según la sección 3.4.2 del protocolo SCIM.  
* Aceptar los tokens de portador de OAuth para la autorización. según la sección 2.1 del protocolo SCIM.

Consulte a su proveedor de la aplicación o la documentación que se incluye con la aplicación para ver si existen declaraciones de compatibilidad con estos requisitos.

### <a name="getting-started"></a>Introducción
Las aplicaciones que admiten el perfil SCIM descrito en este artículo se pueden conectar a Azure Active Directory mediante la característica "de aplicación situada fuera de la galería" de la galería de aplicaciones de Azure AD. Una vez conectadas, Azure AD ejecuta un proceso de sincronización cada 20 minutos en el que consulta el punto de conexión SCIM de la aplicación para ver los usuarios y grupos asignados, y los crea o modifica según los detalles de asignación.

**Para conectar una aplicación que admite SCIM:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
2. Vaya a **Azure Active Directory > Aplicaciones empresariales** y seleccione **Nueva aplicación > Todas > Aplicación situada fuera de la galería**.
3. Escriba un nombre para la aplicación y haga clic en el icono **Agregar** para crear un objeto de aplicación.
    
  ![][1]
  *Figura 2: galería de aplicaciones de Azure AD*
    
4. En la pantalla resultante, seleccione la pestaña **Aprovisionamiento** en la columna izquierda.
5. En el menú **Modo de aprovisionamiento**, seleccione **Automático**.
    
  ![][2]
  *Figura 3: configuración del aprovisionamiento en Azure Portal*
    
6. En el campo **Dirección URL del inquilino**, escriba la dirección URL del punto de conexión SCIM de la aplicación. Ejemplo: https://api.contoso.com/scim/v2/
7. Si el punto de conexión SCIM requiere un token de portador OAuth de un emisor que no sea Azure AD, copie el token de portador OAuth necesario en el campo **Token secreto**. Si este campo se deja en blanco, Azure AD incluye un token de portador OAuth emitido desde Azure AD con cada solicitud. Las aplicaciones que usan Azure AD como un proveedor de identidades pueden validar este token emitido por Azure AD.
8. Haga clic en el botón **Probar conexión** para que Azure Active Directory intente conectarse al punto de conexión SCIM. Si se produce un error en los intentos, se muestra información de error.  
9. Si se produce la conexión, a continuación, haga clic en **Guardar** para guardar las credenciales de administrador.
10. En la sección **Asignaciones**, hay dos conjuntos seleccionables de asignaciones de atributos: uno para los objetos de usuario y otro para los objetos de grupo. Seleccione cada uno de ellos para revisar los atributos que se sincronizan desde Azure Active Directory a la aplicación. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los usuarios y grupos de la aplicación con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.

    >[!NOTE]
    >Opcionalmente, puede deshabilitar la sincronización de objetos de grupo deshabilitando la asignación de "grupos". 

11. En **Configuración**, el campo **Ámbito** define qué usuarios y grupos se sincronizan. Al seleccionar "Sync only assigned users and groups" (Sincronizar solo los usuarios y grupos asignados) (recomendado) solo sincronizará los usuarios y grupos asignados en la pestaña **Usuarios y grupos**.
12. Una vez completada la configuración, cambie el **Estado de aprovisionamiento** a **On** (Activo).
13. Haga clic en **Guardar** para iniciar el servicio de aprovisionamiento de Azure AD. 
14. Al sincronizar solo los usuarios y grupos asignados (recomendado), no olvide seleccionar la pestaña **Usuarios y grupos** y asigne los usuarios o grupos que se van a sincronizar.

Una vez que haya iniciado la sincronización inicial, puede utilizar la pestaña **Registros de auditoría** para supervisar el progreso, con lo que se muestran todas las acciones realizadas por el servicio de aprovisionamiento en la aplicación. Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>La sincronización inicial tardará más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 20 minutos, si se está ejecutando el servicio. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Creación de una solución de aprovisionamiento propia para cualquier aplicación
Al crear un servicio web SCIM que interactúa con Azure Active Directory, puede habilitar el inicio de sesión único y el aprovisionamiento automático de usuarios para prácticamente cualquier aplicación que proporciona una API de aprovisionamiento de usuarios REST o SOAP.

Aquí le mostramos cómo funciona:

1. Azure AD proporciona una biblioteca de infraestructura de lenguaje común conocida como [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Los desarrolladores y los integradores de sistemas pueden usar esta biblioteca para crear e implementar un punto de conexión de servicio web basado en SCIM capaz de conectar Azure AD a cualquier almacén de identidades de la aplicación.
2. Las asignaciones se implementan en el servicio web para asignar el esquema de usuario estándar al esquema de usuario y el protocolo requerido por la aplicación.
3. La dirección URL del punto de conexión está registrada en Azure AD como parte de una aplicación personalizada en la galería de aplicaciones.
4. Se asignan usuarios y grupos a esta aplicación en Azure AD. Tras la asignación, se colocan en una cola para sincronizarse con la aplicación de destino. El proceso de sincronización que controla la cola se ejecuta cada 20 minutos.

### <a name="code-samples"></a>Ejemplos de código
Para facilitar este proceso, se proporciona un conjunto de [ejemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) que crean un punto de conexión de servicio web SCIM y demuestran el aprovisionamiento automático. Uno de los ejemplos es de un proveedor que mantiene un archivo con filas de valores separados por comas que representan a usuarios y grupos.  El otro es de un proveedor que funciona en el servicio de identidad y administración de acceso de Amazon Web Services.  

**Requisitos previos**

* Visual Studio 2013 o posterior.
* [SDK de Azure para .NET](https://azure.microsoft.com/downloads/)
* Equipo de Windows que admite el ASP.NET framework 4.5 que se usará como punto de conexión SCIM. Este equipo debe ser accesible desde la nube
* [Una suscripción de Azure con una versión de prueba o con licencia de Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* El ejemplo de Amazon AWS requiere bibliotecas de [AWS Toolkit for Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Para más información, consulte el archivo Léame incluido con el ejemplo.

### <a name="getting-started"></a>Introducción
Es la manera más fácil de implementar un punto de conexión SCIM que puede aceptar las solicitudes de aprovisionamiento de Azure AD para compilar e implementar el ejemplo de código que genera los usuarios aprovisionados en un archivo de valores separados por comas (CSV).

**Para crear un punto de conexión SCIM de ejemplo:**

1. Descargue el paquete de ejemplo de código en [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Descomprima el paquete y colóquelo en un equipo con Windows, en una ubicación como C:\AzureAD-BYOA-Provisioning-Samples\.
3. En esta carpeta, abra la solución FileProvisioningAgent en Visual Studio.
4. Seleccione **Herramientas > Administrador de paquetes de la biblioteca > Consola del administrador de paquetes** y ejecute los siguientes comandos para que el proyecto FileProvisioningAgent resuelva las referencias de la solución:
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Genere el proyecto FileProvisioningAgent.
6. Inicie la aplicación Símbolo del sistema de Windows (como administrador) y use el comando **cd** para cambiar el directorio a la carpeta **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug**.
7. Ejecute el comando siguiente y reemplace <ip-address> por la dirección IP o el nombre de dominio de la máquina Windows:
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. En Windows, en **Configuración de Windows > Configuración de Internet y red**, seleccione **Firewall de Windows > Configuración avanzada** y cree una **regla de entrada** que permita el acceso de entrada al puerto 9000.
9. Si el equipo Windows está detrás de un enrutador, este debe configurarse para realizar la traducción de acceso de red entre su puerto 9000 que está expuesto a Internet y el puerto 9000 en el equipo Windows. Esta configuración es necesaria para que Azure AD tenga acceso a este punto de conexión en la nube.

**Para registrar el punto de conexión SCIM de ejemplo en Azure AD:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
2. Vaya a **Azure Active Directory > Aplicaciones empresariales** y seleccione **Nueva aplicación > Todas > Aplicación situada fuera de la galería**.
3. Escriba un nombre para la aplicación y haga clic en el icono **Agregar** para crear un objeto de aplicación. El objeto de la aplicación creado está diseñado para representar la aplicación de destino a la que va a aprovisionar y para la que va a implementar el inicio de sesión único, no solo el punto de conexión SCIM.
4. En la pantalla resultante, seleccione la pestaña **Aprovisionamiento** en la columna izquierda.
5. En el menú **Modo de aprovisionamiento**, seleccione **Automático**.
    
  ![][2]
  *Figura 4: configuración del aprovisionamiento en Azure Portal*
    
6. En el campo **URL de inquilino**, escriba la dirección URL expuesta a Internet y el puerto del punto de conexión SCIM. La entrada es algo parecido a http://testmachine.contoso.com:9000 o http://<dirección-IP>:9000/, donde <dirección-IP> es la dirección IP expuesta a Internet.  
7. Si el punto de conexión SCIM requiere un token de portador OAuth de un emisor que no sea Azure AD, copie el token de portador OAuth necesario en el campo **Token secreto**. Si se deja este campo en blanco, Azure AD incluirá un token de portador OAuth emitido desde Azure AD con cada solicitud. Las aplicaciones que usan Azure AD como un proveedor de identidades pueden validar este token emitido por Azure AD.
8. Haga clic en el botón **Probar conexión** para que Azure Active Directory intente conectarse al punto de conexión SCIM. Si se produce un error en los intentos, se muestra información de error.  
9. Si se produce la conexión, a continuación, haga clic en **Guardar** para guardar las credenciales de administrador.
10. En la sección **Asignaciones**, hay dos conjuntos seleccionables de asignaciones de atributos: uno para los objetos de usuario y otro para los objetos de grupo. Seleccione cada uno de ellos para revisar los atributos que se sincronizan desde Azure Active Directory a la aplicación. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los usuarios y grupos de la aplicación con el objetivo de realizar operaciones de actualización. Seleccione el botón Guardar para confirmar los cambios.
11. En **Configuración**, el campo **Ámbito** define qué usuarios y grupos se sincronizan. Al seleccionar "Sync only assigned users and groups" (Sincronizar solo los usuarios y grupos asignados) (recomendado) solo sincronizará los usuarios y grupos asignados en la pestaña **Usuarios y grupos**.
12. Una vez completada la configuración, cambie el **Estado de aprovisionamiento** a **On** (Activo).
13. Haga clic en **Guardar** para iniciar el servicio de aprovisionamiento de Azure AD. 
14. Al sincronizar solo los usuarios y grupos asignados (recomendado), no olvide seleccionar la pestaña **Usuarios y grupos** y asigne los usuarios o grupos que se van a sincronizar.

Una vez que haya iniciado la sincronización inicial, puede utilizar la pestaña **Registros de auditoría** para supervisar el progreso, con lo que se muestran todas las acciones realizadas por el servicio de aprovisionamiento en la aplicación. Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

El último paso en la comprobación del ejemplo es abrir el archivo TargetFile.csv en la carpeta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug en el equipo Windows. Una vez que se ejecuta el proceso de aprovisionamiento, este archivo muestra los detalles de todos los usuarios y grupos asignados y aprovisionados.

### <a name="development-libraries"></a>Bibliotecas de desarrollo
Para desarrollar su propio servicio web que cumpla la especificación SCIM, familiarícese primero con las siguientes bibliotecas proporcionadas por Microsoft para ayudar a acelerar el proceso de desarrollo: 

1. Se ofrecen bibliotecas de Common Language Infrastructure (CLI) que se pueden usar con lenguajes basados en dicha infraestructura, como C#. Una de esas bibliotecas, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara una interfaz Microsoft.SystemForCrossDomainIdentityManagement.IProvider, que se muestra en la ilustración siguiente: un desarrollador que utiliza las bibliotecas implementaría esa interfaz con una clase a la que se puede hacer referencia, de forma genérica, como un proveedor. Las bibliotecas permiten al programador implementar un servicio web que se ajusta a la especificación SCIM. El servicio web se puede hospedar en Internet Information Services o en cualquier ensamblado ejecutable de Common Language Infrastructure. La solicitud se traduce en llamadas a los métodos del proveedor, que pueden ser programadas por el desarrollador para operar en un almacén de identidades.
  
  ![][3]
  
2. Los [controladores de ruta Express](http://expressjs.com/guide/routing.html) están disponibles para analizar los objetos de solicitud de node.js que representan llamadas (tal y como se define en la especificación SCIM), realizadas a un servicio web de node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Creación de un punto de conexión SCIM personalizado
Mediante las bibliotecas CLI, los desarrolladores que usan dichas bibliotecas pueden hospedar sus servicios en cualquier ensamblado ejecutable de Common Language Infrastructure, o bien en Internet Information Services. Aquí se incluye un ejemplo de código para hospedar un servicio dentro de un ensamblado ejecutable en la dirección http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Este servicio debe tener una dirección HTTP y un certificado de autenticación de servidor para el que la entidad de certificación raíz tenga uno de los siguientes nombres: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificado de autenticación de servidor puede enlazarse a un puerto en un host de Windows mediante la utilidad de shell de red siguiente: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

En este caso, el valor proporcionado para el argumento certhash es la huella digital del certificado, mientras que el valor proporcionado para el argumento appid es un identificador único global arbitrario.  

Para hospedar el servicio en Internet Information Services, un desarrollador crearía un ensamblado de biblioteca de código CLI con una clase denominada Inicio en el espacio de nombres predeterminado del ensamblado.  Este es un ejemplo de este tipo de clase: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Control de la autenticación de puntos de conexión
Las solicitudes de Azure Active Directory incluyen un token de portador de OAuth 2.0.   Cualquier servicio que reciba la solicitud debe autenticar al emisor como Azure Active Directory en nombre del inquilino de Azure Active Directory esperado, para el acceso al servicio web Graph de Azure Active Directory.  En el token, el emisor se identifica mediante una notificación de iss; por ejemplo, "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  En este ejemplo, la dirección base del valor de notificación, https://sts.windows.net, identifica a Azure Active Directory como el emisor, mientras que el segmento de la dirección relativa, cbb1a5ac f33b 45fa 9bf5 f37db0fed422, es un identificador único del inquilino de Azure Active Directory en cuyo nombre se ha emitido el token.  Si el token se emitió para acceder al servicio web Graph de Azure Active Directory, el identificador de dicho servicio, 00000002-0000-0000-c000-000000000000, debe estar en el valor de notificación aud del token.  

Los desarrolladores que usan las bibliotecas de CLI proporcionadas por Microsoft para crear un servicio SCIM pueden autenticar las solicitudes de Azure Active Directory mediante el paquete Microsoft.Owin.Security.ActiveDirectory siguiendo estos pasos: 

1. En un proveedor, implemente la propiedad Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior; para ello, haga que devuelva un método al que llamar cuando se inicia el servicio: 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Agregue el siguiente código a dicho método para que cualquier solicitud a cualquiera de los puntos de conexión del servicio se autentique como si tuviera un token emitido por Azure Active Directory en nombre de un inquilino especificado, para acceder al servicio web Graph de Azure Active Directory: 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Esquema de grupos y usuarios
Azure Active Directory puede aprovisionar dos tipos de recursos a los servicios web SCIM.  Esos tipos de recursos son los usuarios y grupos.  

Los recursos de usuario se identifican mediante el identificador de esquema "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User", que se incluye en esta especificación del protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  La asignación predeterminada de los atributos de los usuarios en Azure Active Directory a los atributos de los recursos de "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" se proporciona en la tabla 1, a continuación.  

Los recursos del grupo se identifican mediante el identificado de esquema http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  En la Tabla 2, se muestra la asignación predeterminada entre los atributos de los grupos de Azure Active Directory y los atributos de los recursos de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabla 1: Asignación de atributos de usuario predeterminada
| Usuario de Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
| DisplayName |DisplayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabla 2: Asignación de atributos de grupo predeterminada
| Grupo de Azure Active Directory  | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| DisplayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |DisplayName |
| members |members |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="user-provisioning-and-de-provisioning"></a>Aprovisionamiento y cancelación del aprovisionamiento de usuarios
La siguiente ilustración muestra los mensajes que Azure Active Directory enviará a un servicio SCIM para administrar el ciclo de vida de un usuario en otro almacén de identidades. El diagrama también muestra cómo un servicio SCIM implementado mediante las bibliotecas CLI proporcionadas por Microsoft para la creación de dichos servicios traducirá esas solicitudes a llamadas a los métodos de un proveedor.  

![][4]
*Figura 5: secuencia de aprovisionamiento y cancelación de aprovisionamiento de usuarios*

1. Azure Active Directory consulta el servicio para un usuario con un valor de atributo externalId que coincida con el valor de atributo mailNickname de un usuario de Azure AD. La consulta se expresa como una solicitud de Protocolo de transferencia de hipertexto (HTTP) como la de este ejemplo, donde jyoung es un ejemplo de un mailNickname de un usuario en Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Si el servicio se creó mediante las bibliotecas de Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Query del proveedor del servicio.  Esta es la firma de ese método: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Esta es la definición de la interfaz de Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  En el ejemplo anterior de una consulta para un usuario con un valor especificado para el atributo externalId, los valores de los argumentos pasados al método Query serán los siguientes: 
  * parameters.AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Si la respuesta a una consulta al servicio web relativa a un usuario con un valor de atributo externalId que coincide con el valor de atributo mailNickname de un usuario no devuelve ningún usuario, Azure Active Directory solicita al servicio que aprovisione un usuario correspondiente al de Azure Active Directory.  Este es un ejemplo de dicha solicitud: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  Las bibliotecas de Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM traduciría esa solicitud a una llamada al método Create del proveedor del servicio.  El método Create tiene esta firma: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  En el caso de una solicitud de aprovisionamiento de un usuario, el valor del argumento del recurso es una instancia de Microsoft.SystemForCrossDomainIdentityManagement. Clase Core2EnterpriseUser, definida en la biblioteca Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la solicitud para aprovisionar el usuario se realiza correctamente, la implementación del método debería devolver una instancia de Microsoft.SystemForCrossDomainIdentityManagement. Clase Core2EnterpriseUser, con el valor de la propiedad Identifier establecido en el identificador único del usuario recién aprovisionado.  

3. Para actualizar un usuario que se sabe que existe en un almacén de identidades dirigido por un SCIM, Azure Active Directory solicita el estado actual de dicho usuario desde el servicio con una solicitud como esta: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  En un servicio creado mediante las bibliotecas Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce a una llamada al método Retrieve del proveedor del servicio.  Esta es la firma del método Retrieve: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  En el ejemplo anterior de una solicitud para recuperar el estado actual de un usuario, los valores de las propiedades del objeto proporcionados como el valor del argumento parameters son los siguientes: 
  
  * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Si se va a actualizar un atributo de referencia, Azure Active Directory consulta el servicio para determinar si el valor actual del atributo de referencia en el almacén de identidades dirigido por el servicio ya coincide con el valor de dicho atributo en Azure Active Directory. Para los usuarios, el único atributo del que se va a consultar el valor actual de esta manera es el atributo manager. Este es un ejemplo de una solicitud para determinar si el atributo de administrador de un determinado objeto de usuario tiene actualmente un determinado valor: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  El valor del parámetro de consulta de atributos, "id", significa que, si existe un objeto de usuario que cumple la expresión especificada como valor del parámetro de consulta filter, se espera que el servicio responda con un recurso "urn:ietf:params:scim:schemas:core:2.0:User" o "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, que solo incluya el valor del atributo "id" del recurso.  El solicitante conoce el valor del atributo **id**. Se incluye en el valor del parámetro de consulta filter; el propósito de solicitarlo en realidad es solicitar una representación mínima de un recurso que cumpla la expresión de filtro como una indicación de si existe o no tal objeto.   

  Si el servicio se creó mediante las bibliotecas de Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Query del proveedor del servicio. El valor de las propiedades del objeto proporcionado como el valor del argumento parameters es el siguiente: 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  En este caso, el valor del índice x puede ser 0 y el valor del índice y puede ser 1, o bien el valor de x puede ser 1 y el valor de y puede ser 0, en función del orden de las expresiones del parámetro de consulta filter.   

5. A continuación se proporciona un ejemplo de una solicitud de Azure Active Directory a un servicio SCIM para actualizar un usuario: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  Las bibliotecas de Microsoft Common Language Infrastructure para implementar servicios SCIM traducirían la solicitud de una llamada al método Update del proveedor del servicio. Esta es la signatura del método Update: 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    En el ejemplo de una solicitud para actualizar un usuario, el objeto proporcionado como el valor del argumento patch tiene estos valores de propiedad: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest as PatchRequest2).Operations.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. Para cancelar el aprovisionamiento de un usuario de un almacén de identidades dirigido por un servicio SCIM, Azure AD envía una solicitud como esta: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Si el servicio se creó mediante las bibliotecas Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce a una llamada al método Delete del proveedor del servicio.   Este método tiene esta firma: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  El objeto proporcionado como el valor del argumento resourceIdentifier tiene tendrá estos valores de propiedad en el ejemplo anterior de una solicitud de cancelación del aprovisionamiento de un usuario: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Aprovisionamiento y cancelación del aprovisionamiento de grupos
La siguiente ilustración muestra los mensajes que Azure AD envía a un servicio SCIM para administrar el ciclo de vida de un grupo en otro almacén de identidades.  Esos mensajes se diferencian de los mensajes que pertenecen a los usuarios de tres maneras: 

* El esquema de un recurso del grupo se identifica como `http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group`.  
* Las solicitudes para recuperar grupos estipulan que el atributo members se excluya de cualquier recurso proporcionado en respuesta a la solicitud.  
* Las solicitudes para determinar si un atributo de referencia tiene un valor determinado son solicitudes sobre el atributo members.  

![][5]
*Figura 6: secuencia de aprovisionamiento y cancelación del aprovisionamiento de grupos*

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Personalización de asignaciones de atributos para el aprovisionamiento de usuarios](active-directory-saas-customizing-attribute-mappings.md)
* [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
* [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
