---
title: Enlazar un certificado SSL personalizado a Azure Web Apps | Microsoft Docs
description: "Aprenda a enlazar un certificado SSL personalizado a aplicaciones web, back-ends para aplicaciones móviles o aplicaciones de API en Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5bbdd1db655c080b4372f6728bb47207757209e4
ms.lasthandoff: 04/27/2017


---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Enlazar un certificado SSL personalizado a Azure Web Apps

En este tutorial se muestra cómo enlazar un certificado SSL personalizado adquirido de una entidad de certificación de confianza para [Azure Web Apps](app-service-web-overview.md). Cuando haya terminado, podrá acceder a la aplicación web en el punto de conexión HTTPS de su dominio DNS personalizado.

![Aplicación web con certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!TIP]
> Si necesita un certificado SSL personalizado, puede obtener uno directamente en Azure Portal y enlazarlo a la aplicación web. Siga el tutorial [Incorporación de un certificado SSL a la aplicación App Service](web-sites-purchase-ssl-web-site.md). 
>
> 

## <a name="before-you-begin"></a>Antes de empezar
Antes de seguir este tutorial, asegúrese de haber hecho lo siguiente:

- [Crear una aplicación de App Service](/azure/app-service/)
- [Asignar un nombre DNS personalizado a la aplicación web](web-sites-custom-domain-name.md)
- Adquirir un certificado SSL de una entidad de certificación de confianza

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Requisitos para el certificado SSL

Para usar el certificado en App Service, el certificado debe:

* Estar firmado por una entidad de certificación de confianza
* Haberse exportado como archivo PFX protegido por contraseña
* Contener una clave privada con una longitud de al menos 2048 bits
* Contener todos los certificados intermedios de la cadena de certificados

> [!NOTE]
> **certificados de criptografía de curva elíptica (ECC)** pueden trabajar con el Servicio de aplicaciones, pero están fuera del ámbito de este artículo. Trabaje con la entidad de certificación sobre los pasos exactos para crear certificados ECC.
> 
>

## <a name="prepare-your-web-app"></a>Preparar la aplicación web
Para enlazar un certificado SSL personalizado a la aplicación web, su [plan de App Service](https://azure.microsoft.com/pricing/details/app-service/) debe estar en el nivel **Básico**, **Estándar** o **Premium**. En este paso, asegúrese de que la aplicación web se encuentra en el plan de tarifa compatible.

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Abra Azure Portal. Para ello, inicie sesión en [https://portal.azure.com](https://portal.azure.com) con su cuenta de Azure.

### <a name="navigate-to-your-web-app"></a>Navegar a la aplicación web
En el menú izquierdo, haga clic en **App Services** y luego, en el nombre de la aplicación web.

![Seleccionar la aplicación web](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Ha llegado a la hoja de administración de la aplicación web (_hoja_: página de portal que se abre horizontalmente).  

### <a name="check-the-pricing-tier"></a>Comprobar el plan de tarifa

En el panel de navegación izquierdo de la hoja de la aplicación web, desplácese a la sección **Configuración** y seleccione **Escalar verticalmente (plan de App Service)**.

![Menú Escalar verticalmente](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Asegúrese de que la aplicación web no está en el nivel **Gratis** ni **Compartido**. El nivel actual de la aplicación web aparece resaltado con un cuadro azul oscuro. 

![Comprobar plan de tarifa](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

SSL personalizado no es compatible con los niveles **Gratis** y **Compartido**. Si tiene que escalar verticalmente, pase a la sección siguiente. Si no, cierre la hoja **Elija su plan de tarifa** y vaya directamente a las secciones sobre cómo [cargar y enlazar el certificado SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Escalar verticalmente el plan de App Service

Seleccione uno de los niveles, **Básico**, **Estándar** o **Premium**. 

Haga clic en **Seleccionar**.

![Elegir plan de tarifa](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Cuando aparezca la siguiente notificación, la operación de escalado se habrá completado.

![Notificación de escalado vertical](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Enlazar el certificado SSL

Está listo para cargar el certificado SSL en la aplicación web. 

### <a name="export-certificate-to-pfx"></a>Exportar el certificado a PFX

Debe exportar el certificado SSL personalizado con la clave privada con la que se ha generado la solicitud de certificado.

Si la solicitud de certificado se genera con OpenSSL, se crea una clave privada. Para exportar el certificado a PFX, ejecute el comando siguiente:

```bash
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
```

Si se usa IIS o `Certreq.exe` para generar la solicitud de certificado, instale primero el certificado en la máquina local y luego expórtelo a PFX siguiendo los pasos de [Exportar un certificado con la clave privada](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Cargar el certificado SSL

Para cargar el certificado SSL, haga clic en **Certificados SSL** en el panel de navegación izquierdo de la aplicación web.

Haga clic en **Cargar certificado**.

En **Archivo de certificado PFX**, seleccione el archivo PFX. En **Contraseña del certificado**, escriba la contraseña que creó al exportar el archivo PFX.

Haga clic en **Cargar**.

![Carga del certificado](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

Cuando App Service termina de cargar el certificado, este aparece en la página **Certificados SSL**.

![Certificado cargado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Enlazar el certificado SSL

Ahora debería ver el certificado cargado en la página **Certificado SSL**.

En la sección **Enlaces SSL**, haga clic en **Agregar enlace**.

En la hoja **Agregar enlace SSL**, use las listas desplegables para seleccionar el nombre de dominio que va a proteger, así como el certificado que pretende utilizar. 

En **Tipo de SSL**, seleccione si se va a usar **[Indicación de nombre de servidor (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o SSL basada en IP.
   
- **SSL basada en SNI**: pueden agregarse varios enlaces SSL basados en SNI. Esta opción permite que varios certificados SSL protejan varios dominios en una misma dirección IP. Los exploradores más modernos (como Internet Explorer, Chrome, Firefox y Opera) admiten SNI (encontrará información de compatibilidad con exploradores más completa en [Indicación de nombre de servidor](http://wikipedia.org/wiki/Server_Name_Indication)).
- **SSL basada en IP**: solo pueden agregarse enlaces SSL basados en IP. Esta opción solo permite que un único certificado SSL proteja una dirección IP dedicada. Para proteger varios dominios, debe protegerlos todos con el mismo certificado SSL. Se trata de la opción tradicional para enlaces SSL. 

Haga clic en **Agregar enlace**.

![Enlazar certificado SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Cuando App Service termina de cargar el certificado, este aparece en la sección **Enlaces SSL**.

![Certificado enlazado a la aplicación web](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Reasignar un registro D en SSL de IP

Si no usa SSL basado en IP en la aplicación web, vaya directamente a [Probar HTTPS para el dominio personalizado](#test). 

De manera predeterminada, la aplicación web usa una dirección IP pública compartida. En cuanto se enlaza un certificado con SSL basado en IP, App Service crea otra dirección IP dedicada para la aplicación web.

Si ha asignado un registro D a la aplicación web, actualice el registro de dominio con esta nueva dirección IP dedicada.

La página **Dominio personalizado** de la aplicación web se actualiza con la nueva dirección IP dedicada. [Copie esta dirección IP](app-service-web-tutorial-custom-domain.md#info) y luego [reasigne el registro D](app-service-web-tutorial-custom-domain.md#create-the-a-record) a esta nueva dirección IP.

<a name="test"></a>

## <a name="test-https"></a>Probar HTTPS
Ahora todo lo que queda por hacer es asegurarse de que HTTPS funciona para el dominio personalizado. En varios exploradores, vaya a `https://<your.custom.domain>` para ver que atiende la aplicación web.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Si la aplicación web genera errores de validación del certificado, probablemente se esté usando un certificado autofirmado.
>
> Si no es así, puede que haya olvidado certificados intermedios cuando exportó el certificado al archivo PFX. 
>
>

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Aplicación de HTTPS
Si todavía desea permitir el acceso HTTP a la aplicación web, omita este paso. 

App Service *no* exige HTTPS, por lo que cualquier usuario todavía puede acceder a la aplicación web mediante HTTP. Para exigir HTTPS en su aplicación web, puede definir una regla de reescritura en el archivo `web.config` de la aplicación web. App Service usa este archivo, independientemente de la plataforma del lenguaje de la aplicación web.

> [!NOTE]
> Hay una redirección específica del lenguaje de las solicitudes. ASP.NET MVC puede usar el filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) en lugar de la regla de reescritura en `web.config` (consulte [Implementar una aplicación ASP.NET MVC 5 segura en una aplicación web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).
> 
> 

Si es un desarrollador de .NET, debe estar relativamente familiarizado con este archivo. Se encuentra en la raíz de la solución.

Además, si desarrolla con PHP, Node.js, Python o Java, existe la posibilidad de que generemos este archivo en su nombre en App Service.

Conéctese al punto de conexión FTP de la aplicación web. Para ello, siga las instrucciones que se ofrecen en [Implementación de la aplicación en Azure App Service mediante FTP/S](app-service-deploy-ftp.md). 

Este archivo debe encontrarse en `/home/site/wwwroot`. Si no es así, cree un `web.config` en esta carpeta con el siguiente XML:

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule TAG FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

En el caso de un `web.config` existente, basta con copiar la etiqueta `<rule>` completa en el elemento `configuration/system.webServer/rewrite/rules` de su `web.config`. Si existen otras etiquetas `<rule>` en su `web.config`, coloque la etiqueta `<rule>` copiada antes que las demás etiquetas `<rule>`.

Esta regla devuelve un código HTTP 301 (redirección permanente) al protocolo HTTPS siempre que el usuario realiza una solicitud HTTP en la aplicación web. Por ejemplo, se redirige de `http://contoso.com` a `https://contoso.com`.

Para obtener más información sobre el módulo URL Rewrite de IIS, consulte la documentación de [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="automate-with-scripts"></a>Automatizar con scripts

Puede automatizar enlaces SSL de la aplicación web con scripts, mediante la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI de Azure

El comando siguiente carga un archivo PFX exportado y obtiene la huella digital. 

```bash
thumprint=$(az appservice web config ssl upload --certificate-file <path_to_PFX_file> \
--certificate-password <PFX_password> --name <app_name> --resource-group <resource_group_name> \
--query thumbprint --output tsv)
```

El comando siguiente usa la huella digital del comando anterior para agregar un enlace SSL basado en SNI.

```bash
az appservice web config ssl bind --certificate-thumbprint $thumbprint --ssl-type SNI \
--name <app_name> --resource-group <resource_group_name>
```

### <a name="azure-powershell"></a>Azure PowerShell

El comando siguiente carga un archivo PFX exportado y agrega un enlace SSL basado en SNI.

```PowerShell
New-AzureRmWebAppSSLBinding -WebAppName <app_name> -ResourceGroupName <resource_group_name> -Name <dns_name> `
-CertificateFilePath <path_to_PFX_file> -CertificatePassword <PFX_password> -SslState SniEnabled
```
## <a name="more-resources"></a>Más recursos
* [Centro de confianza de Microsoft Azure](/support/trust-center/security/)
* [Opciones de configuración desbloqueadas en Sitios web Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Activación del registro de diagnósticos](web-sites-enable-diagnostic-log.md)
* [Configuración de Aplicaciones web en Servicio de aplicaciones de Azure](web-sites-configure.md)

