---
title: Uso de perfiles de la versión de la API con Ruby en Azure Stack | Microsoft Docs
description: Información acerca de los perfiles de la versión de la API con Ruby en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd8130ac12f9c7c2095f9329dc4ce8a34187cf62
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011226"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Uso de los perfiles de la versión de la API con Ruby en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Perfiles de la versión de API y Ruby

El SDK de Ruby para Resource Manager de Azure Stack proporciona herramientas que le ayudarán a crear y administrar su infraestructura. Proveedores de recursos en el SDK incluye Compute, Redes virtuales y opciones de almacenamiento con el lenguaje Ruby. Los perfiles de API en el SDK de Ruby habilitan el desarrollo en la nube híbrida ayudándole a cambiar entre los recursos de Azure globales y los recursos de Azure Stack.

Un perfil de API es una combinación de los proveedores de recursos y las versiones del servicio. Puede usar un perfil de API para combinar los diferentes tipos de recursos.

 - Para usar las últimas versiones de todos los servicios, use el perfil **más reciente** de la gema de acumulación del SDK de Azure.
 - Para usar los servicios compatibles con Azure Stack, utilice el perfil **V2017_03_09** de la gema de acumulación del SDK de Azure.
 - Para usar la última versión de API de un servicio, utilice el perfil **más reciente** de la gema específica. Por ejemplo, si desea usar solo la última versión de API del servicio de proceso, utilice el perfil **más reciente** de la gema **Compute**.
 - Para usar la versión de API de un servicio, utilice las versiones de API específicas definidas dentro de la gema.

> [!Note]   
> Puede combinar todas las opciones en la misma aplicación.

## <a name="install-the-azure-ruby-sdk"></a>Instalación del SDK de Ruby de Azure

 - Siga las instrucciones oficiales para instalar [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Siga las instrucciones oficiales para instalar [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Durante la instalación, elija la opción para **agregar Ruby a la variable PATH**
    - Instale el kit de desarrollo durante la instalación de Ruby cuando se le pida.
    - A continuación, instale el software que instala varios programas con el siguiente comando:  
      `Gem install bundler`
 - Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Las instrucciones para crear una suscripción están [aquí](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Cree una entidad de servicio y guarde su identificador y su secreto. Las instrucciones para crear una entidad de servicio de Azure Stack están [aquí](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Las instrucciones sobre cómo asignar un rol a la entidad de servicio están [aquí](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Instalación de los paquetes rubygem

Puede instalar los paquetes rubygem de Azure directamente.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Tenga en cuenta que el SDK de Ruby de Azure Resource Manager está en versión preliminar y probablemente tendrá cambios de interfaz importantes en próximas versiones. Un número mayor en la versión secundaria puede indicar cambios importantes.

## <a name="usage-of-the-azuresdk-gem"></a>Uso de la gema azure_sdk

La gema, azure_sdk, es una acumulación de todas las gemas admitidas en el SDK de Ruby. Esta gema consta de un perfil **más reciente**, que admite la última versión de todos los servicios. Presente un perfil con versión **V2017_03_09**, que se crea para Azure Stack.

Puede instalar la gema de acumulación azure_sdk con el siguiente comando:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Requisito previo

Para usar el SDK de Azure de Ruby con Azure Stack, debe proporcionar los siguientes valores y, a continuación, establecer valores con variables de entorno. Consulte las instrucciones bajo la tabla de su sistema operativo sobre cómo configurar las variables de entorno. 

| Valor | Variables de entorno | DESCRIPCIÓN | 
| --- | --- | --- | --- |
| Id. de inquilino | AZURE_TENANT_ID | El valor de su [identificador de inquilino](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview) de Azure Stack. |
| Id. de cliente | AZURE_CLIENT_ID | El identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este documento.  |
| Id. de suscripción | AZURE_SUBSCRIPTION_ID | El [identificador de suscripción](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) es su forma de tener acceso a las ofertas de Azure Stack. |
| Secreto del cliente | AZURE_CLIENT_SECRET | El secreto de aplicación de la entidad de servicio que guardó al crear esta última. |
| Punto de conexión de Resource Manager | ARM_ENDPOINT | Consulte [Punto de conexión de Resource Manager de Azure Stack](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punto de conexión de Resource Manager de Azure Stack

Microsoft Azure Resource Manager es un marco de administración que permite a los administradores implementar, administrar y supervisar recursos de Azure. Azure Resource Manager puede controlar estas tareas como grupo, en vez de individualmente, en una sola operación.

Puede obtener la información de metadatos del punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código.

  > [!Note]  
  > El valor de **ResourceManagerUrl** del Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external/`  
  > El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Para recuperar los metadatos necesarios: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Archivo JSON de ejemplo:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>Establecimiento de variables de entorno

**Microsoft Windows**  
Para establecer las variables de entorno, en el símbolo del sistema de Windows, use el siguiente formato:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**Sistemas basados en macOS, Linux y Unix**  
En los sistemas basados en Unix, podría usar el comando como:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Perfiles de API existentes

La gema de acumulación azure_sdk tiene los dos perfiles siguientes:

1. **V2017_03_09**  
  Perfil creado para Azure Stack. Use este perfil para que los servicios sean lo más compatibles posible con Azure Stack.
2. **Más reciente**  
  El perfil consta de las versiones más recientes de todos los servicios. Use las versiones más recientes de todos los servicios.

Para obtener más información sobre los perfiles de API y Azure Stack, consulte [Resumen de perfiles de API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Uso de perfil de API del SDK de Ruby de Azure

Las siguientes líneas se deben usar para crear una instancia de un cliente del perfil. Este parámetro solo es necesario para Azure Stack u otras nubes privadas. Azure global ya tiene esta configuración de forma predeterminada.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

El cliente del perfil se puede usar para tener acceso a proveedores de recursos individuales, como proceso, almacenamiento y red.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Definición de las funciones de configuración del entorno de Azure Stack

Para autenticar la entidad de servicio en el entorno de Azure Stack, defina los puntos de conexión con **get_active_directory_settings()**. Este método usa la variable de entorno **ARM_Endpoint** que definió al establecer sus variables de entorno.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Ejemplos donde se usan perfiles de API

Puede usar los siguientes ejemplos encontrados en repositorios de GitHub como referencia creando soluciones con perfiles de API de Azure Stack y Ruby:

 - [Administración de recursos y grupos de recursos de Azure con Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Administración de máquinas virtuales con Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Grupos y Resource Manager de ejemplo

Para ejecutar el ejemplo, asegúrese de haber instalado Ruby. Si usa Visual Studio Code, descargue el SDK de Ruby como extensión también. 

> [!Note]  
> Puede obtener el repositorio del ejemplo en "[Administración de recursos y grupos de recursos de Azure con Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Clone el repositorio.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Instale las dependencias con una agrupación de trabajos.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Cree una entidad de servicio de Azure con PowerShell y recupere los valores necesarios. 

  Para obtener instrucciones sobre cómo crear una entidad de servicio, consulte [Uso de Azure PowerShell para crear una entidad de servicio con un certificado](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Los valores necesarios son:
  - Id. de inquilino
  - Id. de cliente
  - Secreto del cliente
  - Id. de suscripción
  - Punto de conexión de Resource Manager

  Establezca las siguientes variables de entorno con la información que recuperó de la entidad de servicio que creó.

  - export AZURE_TENANT_ID={your tenant id}
  - export AZURE_CLIENT_ID={your client id}
  - export AZURE_CLIENT_SECRET={your client secret}
  - export AZURE_SUBSCRIPTION_ID={your subscription id}
  - export ARM_ENDPOINT={your AzureStack Resource manager url}

  > [!Note]  
  > En Windows, use set en lugar de export.

4. Asegúrese de que la variable de ubicación se establece en su ubicación de Azure Stack. Por ejemplo, LOCAL="local"

5. Agregue la siguiente línea de código si usa Azure Stack u otras nubes privadas para dirigir los puntos de conexión de Active Directory adecuados.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. Dentro de la variable de opciones, agregue la configuración de Active Directory y la URL base para trabajar con Azure Stack. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Cree el cliente del perfil que dirige el perfil de Azure Stack:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Para autenticar la entidad de servicio con Azure Stack, los puntos de conexión deben definirse con **get_active_directory_settings()**. Este método usa la variable de entorno **ARM_Endpoint** que definió al establecer sus variables de entorno.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Ejecute el ejemplo.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Pasos siguientes

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
