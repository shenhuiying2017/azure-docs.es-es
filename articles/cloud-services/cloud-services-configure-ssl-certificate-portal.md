---
title: "Configuración de SSL para un servicio en la nube | Microsoft Docs"
description: "Aprenda a especificar un punto de conexión HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación. Estos ejemplos usan el Portal de Azure."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5360387816cbbcd631114730fad8b7ce2c8c8aa6


---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configuración de SSL para una aplicación en Azure
> [!div class="op_single_selector"]
> * [Portal de Azure](cloud-services-configure-ssl-certificate-portal.md)
> * [Portal de Azure clásico](cloud-services-configure-ssl-certificate.md)
> 
> 

El cifrado de Capa de sockets seguros (SSL) es el método más usado para proteger los datos que se envían por Internet. Esta tarea común analiza cómo especificar un punto de conexión HTTPS para un rol web y cómo cargar un certificado SSL para proteger su aplicación.

> [!NOTE]
> Los procedimientos de esta tarea se aplican a Servicios en la nube de Azure; para Servicios de aplicaciones consulte [esto](../app-service-web/web-sites-configure-ssl-certificate.md).
> 
> 

Esta tarea usa una implementación de producción; al final de este tema se proporciona información sobre el uso de una implementación de ensayo.

Lea [esto](cloud-services-how-to-create-deploy-portal.md) primero si aún no ha creado un servicio en la nube.

[!INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## <a name="step-1-get-an-ssl-certificate"></a>Paso 1: Obtener un certificado SSL
Para configurar SSL para una aplicación, necesita primero obtener un certificado SSL que haya sido firmado por una entidad de certificación (CA), un tercero de confianza que emite certificados para este propósito. Si todavía no tiene uno de estos certificados, deberá obtenerla mediante una compañía que venda certificados SSL.

El certificado debe cumplir los siguientes requisitos de certificados SSL en Azure:

* El certificado debe contener una clave privada.
* El certificado debe crearse para el intercambio de claves, que se puedan exportar a un archivo Personal Information Exchange (.pfx).
* El nombre de sujeto del certificado debe coincidir con el dominio usado para tener acceso al servicio en la nube. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio cloudapp.net. Debe adquirir un nombre de dominio personalizado para usarlo cuando obtenga acceso a su servicio. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre de dominio personalizado que se usó para tener acceso a su aplicación. Por ejemplo, si su nombre de dominio personalizado es **contoso.com** debe solicitar un certificado de su CA para ***.contoso.com** o **www.contoso.com**.
* Este certificado debe usar un cifrado de 2048 bits como mínimo.

Para propósitos de prueba, puede [crear](cloud-services-certs-create.md) y usar un certificado autofirmado. Un certificado autofirmado no está autenticado por una CA y puede usar el dominio cloudapp.net como la dirección URL del sitio web. Por ejemplo, la tarea siguiente usa un certificado autofirmado en el que el nombre común (CN) usado en el certificado es **sslexample.cloudapp.net**.

A continuación, debe incluir información sobre el certificado en su definición de servicio y los archivos de configuración del servicio.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Paso 2: Modificar los archivos de definición y configuración del servicio
Su aplicación debe estar configurada para usar el certificado y se debe agregar un punto de conexión HTTPS. Como resultado, se deben actualizar la definición de servicio y los archivos de configuración del servicio.

1. En su entorno de desarrollo, abra el archivo de definición de servicio (CSDEF), agregue una sección **Certificates** dentro de la sección **WebRole** e incluya la siguiente información sobre el certificado (y los certificados intermedios):
   
       <WebRole name="CertificateTesting" vmsize="Small">
       ...
           <Certificates>
               <Certificate name="SampleCertificate" 
                            storeLocation="LocalMachine" 
                            storeName="My"
                            permissionLevel="limitedOrElevated" />
               <!-- IMPORTANT! Unless your certificate is either
               self-signed or signed directly by the CA root, you
               must include all the intermediate certificates
               here. You must list them here, even if they are
               not bound to any endpoints. Failing to list any of
               the intermediate certificates may cause hard-to-reproduce
               interoperability problems on some clients.-->
               <Certificate name="CAForSampleCertificate"
                            storeLocation="LocalMachine"
                            storeName="CA"
                            permissionLevel="limitedOrElevated" />
           </Certificates>
       ...
       </WebRole>
   
   La sección **Certificates** define el nombre de nuestro certificado, su ubicación y el nombre de la tienda donde se encuentra.
   
   Se pueden establecer permisos (atributo`permisionLevel` ) en uno de los siguientes casos:
   
   | Valor del permiso | Descripción |
   | --- | --- |
   | limitedOrElevated |**(Predeterminado)** todos los procesos de rol pueden tener acceso a la clave privada. |
   | elevated |Solo los procesos elevados pueden tener acceso a la clave privada. |
2. En el archivo de definición de servicio, agregue un elemento **InputEndpoint** en la sección **Endpoints** para habilitar HTTPS:
   
       <WebRole name="CertificateTesting" vmsize="Small">
       ...
           <Endpoints>
               <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                   certificate="SampleCertificate" />
           </Endpoints>
       ...
       </WebRole>
3. En el archivo de definición de servicio, agregue un elemento **Binding** en la sección **Sites**. Esto agrega un enlace de HTTPS para asignar el punto de conexión a su sitio:
   
       <WebRole name="CertificateTesting" vmsize="Small">
       ...
           <Sites>
               <Site name="Web">
                   <Bindings>
                       <Binding name="HttpsIn" endpointName="HttpsIn" />
                   </Bindings>
               </Site>
           </Sites>
       ...
       </WebRole>
   
   Se han efectuado todos los cambios necesarios en el archivo de definición de servicio; sin embargo, todavía necesita agregar la información del certificado en el archivo de configuración del servicio.
4. En el archivo de configuración de servicio (CSCFG), ServiceConfiguration.Cloud.cscfg, agregue una sección **Certificates** en la sección **Role** y reemplace el valor de la huella digital de muestra que se indica a continuación por el de su certificado:
   
       <Role name="Deployment">
       ...
           <Certificates>
               <Certificate name="SampleCertificate" 
                   thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                   thumbprintAlgorithm="sha1" />
               <Certificate name="CAForSampleCertificate"
                   thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                   thumbprintAlgorithm="sha1" />
           </Certificates>
       ...
       </Role>

(El ejemplo anterior usa **sha1** para el algoritmo de huella digital. Especifique el valor adecuado para el algoritmo de huella digital de su certificado).

Ahora que se actualizaron los archivos de definición del servicio y configuración del servicio, prepare su implementación para cargarla en Azure. Si va a usar **cspack**, no utilice la marca **/generateConfigurationFile**, puesto que así se sobrescribe la información del certificado que acaba de insertar.

## <a name="step-3-upload-a-certificate"></a>Paso 3: Cargar un certificado
Conéctese al portal y...

1. Para seleccionar el servicio en la nube en el portal, haga clic en **Servicio en la nube**. (Que aparece en la sección **Todos los recursos**). 
   
    ![Publicación del servicio en la nube](media/cloud-services-configure-ssl-certificate-portal/browse.png)
2. Haga clic en **Certificados**.
   
    ![Haga clic en el icono de certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)
3. Proporcione el **Archivo**, la **Contraseña** y luego haga clic en **Cargar**.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Paso 4: Conectarse a la instancia de rol con HTTPS
Ahora que su implementación está funcionando en Azure, puede conectarse a ella con HTTPS.

1. Haga clic en la **dirección URL del sitio** para abrir el explorador web.
   
   ![Haga clic en la dirección URL del sitio](media/cloud-services-configure-ssl-certificate-portal/navigate.png)
2. En el explorador web, modifique el vínculo para usar **https** en lugar de **http** y, a continuación, visite la página.
   
   > [!NOTE]
   > Si va a usar un certificado autofirmado, cuando vaya a un punto de conexión HTTPS que está asociado al certificado autofirmado, aparecerá un error de certificado en el explorador. El uso de un certificado firmado por una entidad de certificación de confianza elimina el problema; mientras tanto, puede ignorar el error. (Otra opción es agregar el certificado autofirmado a la tienda de certificados de la entidad de certificación de confianza para el usuario).
   > 
   > 
   
   ![Vista previa del sitio](media/cloud-services-configure-ssl-certificate-portal/show-site.png)
   
   > [!TIP]
   > Si desea usar SSL para una implementación de ensayo en vez de una implementación de producción, tendrá que determinar primero la dirección URL que se usó para la implementación de ensayo. Una vez que se ha implementado el servicio en la nube, la dirección URL del entorno de ensayo viene determinada por el GUID del **identificador de implementación** en este formato: `https://deployment-id.cloudapp.net/`  
   > 
   > Cree un certificado con el nombre común (CN) igual a la dirección URL basada en GUID (por ejemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Use el portal para agregar el certificado a su servicio en la nube de ensayo. A continuación, agregue la información del certificado a los archivos CSDEF y CSCFG, vuelva a empaquetar la aplicación y actualice la implementación de ensayo para que use el nuevo paquete.
   > 
   > 

## <a name="next-steps"></a>Pasos siguientes
* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administración del servicio en la nube](cloud-services-how-to-manage-portal.md).




<!--HONumber=Nov16_HO3-->


