---
title: "Adición de un dominio personalizado y SSL a una aplicación web de Azure | Microsoft Docs"
description: "Aprenda a preparar la aplicación web de Azure para el entorno de producción agregándole la marca de su empresa. Asigne el nombre de dominio personalizado (dominio personal) a la aplicación web y protéjala con un certificado SSL personalizado."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017


---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>Adición de un dominio personalizado y SSL a una aplicación web de Azure

En este tutorial se muestra cómo asignar rápidamente un nombre de dominio personalizado a la aplicación web de Azure para, a continuación, protegerla con un certificado SSL personalizado. 

## <a name="before-you-begin"></a>Antes de empezar

Antes de ejecutar este ejemplo, instale la [CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) localmente.

También necesitará acceso administrativo a la página de configuración de DNS del proveedor de dominios respectivo. Por ejemplo, para agregar `www.contoso.com`, deberá configurar entradas DNS para `contoso.com`.

Por último, necesita un archivo .PFX válido _y_ su contraseña para el certificado SSL que desea cargar y enlazar. Este certificado SSL debe configurarse para proteger el nombre de dominio personalizado que desee. En el ejemplo anterior, su certificado SSL debe proteger a `www.contoso.com`. 

## <a name="step-1---create-an-azure-web-app"></a>Paso 1: Creación de una aplicación web de Azure

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Ahora vamos a usar la CLI de Azure 2.0 en una ventana de terminal para crear los recursos necesarios para hospedar nuestra aplicación de Node.js en Azure.  Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Crear un grupo de recursos   
Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran recursos de Azure como aplicaciones web, bases de datos y cuentas de almacenamiento. 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

Para ver los posibles valores que se pueden usar para `---location`, utilice el comando `az appservice list-locations` de la CLI de Azure.

## <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones

Cree un plan de App Service con el comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

En el siguiente ejemplo se crea un plan de App Service denominado `myAppServicePlan` que usa el plan de tarifa **Básico**.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

Cuando se ha creado el plan de App Service, la CLI de Azure muestra información similar al ejemplo siguiente. 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>Creación de una aplicación web

Ahora que ha creado un plan de App Service, cree una aplicación web dentro del plan de App Service `myAppServicePlan`. La aplicación web ofrece un espacio de hospedaje para implementar el código, así como una dirección URL para que pueda ver la aplicación implementada. Use el comando [az appservice web create](/cli/azure/appservice/web#create) para crear la aplicación web. 

En el comando siguiente, sustituya su nombre de aplicación único donde vea el marcador de posición `<app_name>`. Este nombre único se usará como parte del nombre de dominio predeterminado para la aplicación web, por lo que el nombre debe ser único en todas las aplicaciones de Azure. Más adelante puede asignar cualquier entrada de DNS personalizada a la aplicación web antes de exponerla a los usuarios. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

Cuando se ha creado la aplicación web, la CLI de Azure muestra información similar al siguiente ejemplo. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

En la salida JSON, `defaultHostName` muestra el nombre de dominio predeterminado de la aplicación web. En su explorador, navegue hacia esta dirección.

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>Paso 2: Configuración de la asignación de DNS

En este paso, agregará una asignación de un dominio personalizado al nombre de dominio predeterminado de la aplicación web, `<app_name>.azurewebsites.net`. Normalmente, este paso se realiza en el sitio web de su proveedor de dominios. El sitio web de cada registrador de dominios es ligeramente diferente, por lo que debe consultar la documentación de su proveedor. Sin embargo, estas son algunas directrices generales. 

### <a name="navigate-to-to-dns-management-page"></a>Navegación a la página de administración de DNS

En primer lugar, inicie sesión en el sitio web de su registrador de dominios.  

A continuación, busque la página para administrar registros DNS. Busque los vínculos o áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**. A menudo, puede encontrar el vínculo al consultar la información de la cuenta y al buscar un vínculo como **Mis dominios**.

Una vez que haya encontrado esta página, busque un vínculo que le permita agregar o editar registros DNS. Podría aparecer como **Archivo de zona** o **Registros DNS**, o bien como un vínculo de **Configuración avanzada**.

### <a name="create-a-cname-record"></a>Creación de un registro CNAME

Agregue un registro CNAME que asigne el nombre de subdominio deseado al nombre de dominio predeterminado de la aplicación web (`<app_name>.azurewebsites.net`, donde `<app_name>` es el nombre único de la aplicación).

Para el ejemplo `www.contoso.com`, cree un CNAME que asigne el nombre de host `www` a `<app_name>.azurewebsites.net`.

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>Paso 3: Configuración del dominio personalizado en la aplicación web

Cuando finalice la configuración de la asignación del nombre de host en el sitio web de su proveedor de dominios, estará listo para configurar el dominio personalizado en su aplicación web. Use el comando [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) para agregar esta configuración. 

En el comando siguiente, sustituya `<app_name>` por su nombre de aplicación único y <your_custom_domain> por el nombre de dominio personalizado completo (p. ej., `www.contoso.com`). 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

El dominio personalizado ahora está completamente asignado a su aplicación web. En su explorador, navegue hasta el nombre de dominio personalizado. Por ejemplo:

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>Paso 4: Enlace de un certificado SSL personalizado a su aplicación web

Ahora tiene una aplicación web de Azure, con el nombre de dominio que desea en la barra de direcciones del explorador. Sin embargo, si navega a `https://<your_custom_domain>` ahora, obtendrá un error de certificado. 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

Este error se produce porque la aplicación web no tiene todavía un enlace de certificado SSL que coincida con el nombre de dominio personalizado. Sin embargo, no obtendrá ningún error si navega a `https://<app_name>.azurewebsites.net`. Esto se debe a que su aplicación, así como todas las aplicaciones de Azure App Service, están protegidas de forma predeterminada con el certificado SSL para el dominio con comodín `*.azurewebsites.net`. 

Para acceder a la aplicación web mediante su nombre de dominio personalizado, debe enlazar el certificado SSL de su dominio personalizado a la aplicación web. Esto lo hará en este paso. 

### <a name="upload-the-ssl-certificate"></a>Carga del certificado SSL

Cargue el certificado SSL del dominio personalizado en la aplicación web mediante el comando [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload).

En el siguiente comando, sustituya `<app_name>` por el nombre de aplicación único, `<path_to_ptx_file>` con la ruta de acceso al archivo PFX, y `<password>` por la contraseña del certificado. 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

Cuando se carga el certificado, la CLI de Azure muestra información similar al ejemplo siguiente:

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

En la salida JSON, `thumbprint` muestra la huella digital del certificado cargado. Copie su valor para el siguiente paso.

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>Enlace del certificado SSL cargado a la aplicación web

La aplicación web tiene ahora el nombre de dominio personalizado que desea, y también tiene un certificado SSL que protege ese dominio personalizado. Lo único que queda por hacer es enlazar el certificado cargado a la aplicación web. Para ello se usa el comando [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind).

En el siguiente comando, sustituya `<app_name>` por el nombre de aplicación único y `<thumbprint-from-previous-output>` por la huella digital del certificado que obtuvo con el comando anterior. 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

Cuando el certificado está enlazado a su aplicación web, la CLI de Azure muestra información similar al ejemplo siguiente:

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

En el explorador, navegue hasta el punto de conexión HTTPS del nombre de dominio personalizado. Por ejemplo:

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>Más recursos

[Comprar y configurar un nombre de dominio personalizado en Azure App Service](custom-dns-web-site-buydomains-web-app.md)
[Compra y configuración de un certificado SSL para Azure App Service](web-sites-purchase-ssl-web-site.md)

