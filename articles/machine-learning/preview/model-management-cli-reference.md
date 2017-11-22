---
title: "Referencia de la interfaz de línea de comandos de Administración de modelos de Azure Machine Learning | Microsoft Docs"
description: "Referencia de la interfaz de línea de comandos de Administración de modelos de Azure Machine Learning."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 373abb8f40a8acf557b7cd4a0d0b3fb55f4a545c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="model-management-command-line-interface-reference"></a>Referencia de la interfaz de línea de comandos de Administración de modelos

## <a name="base-cli-concepts"></a>Conceptos básicos de la CLI:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Comandos de cuenta
Se requiere una cuenta de administración de modelos para utilizar los servicios que le permiten implementar y administrar modelos. Use `az ml account modelmanagement -h` para ver la lista siguiente:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Creación de una cuenta de administración de modelos**

Cree una cuenta de administración de modelos mediante el siguiente comando. Esta cuenta se utilizará para la facturación.

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Argumentos locales:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Comandos de entorno

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Configuración del entorno de desarrollo**

El comando de configuración requiere que tenga acceso de colaborador a la suscripción. Si no lo tiene, al menos necesita tener acceso de colaborador al grupo de recursos en el que se está realizando la implementación. Para hacer esto último, es preciso especificar el nombre del grupo de recursos como parte del comando de configuración mediante la marca `-g`. 

Hay dos opciones para la implementación: *local* y *clúster*. Si establece la marca `--cluster` (o `-c`) se habilitará la implementación del clúster que aprovisiona un clúster de ACS. Esta es la sintaxis de configuración básica:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Esto inicializa el entorno de Azure Machine Learning con una cuenta de almacenamiento, un registro de ACR y un servicio App Insights que se crea en la suscripción. De forma predeterminada, se inicializará el entorno solo para las implementaciones locales (ninguna en ACS) si no se especifica ninguna marca. Si desea escalar el servicio, especifique la marca `--cluster` (o `-c`) para crear un clúster de ACS.

Detalles del comando:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Will be created if it does not exist.
                                     If not provided, resource group will be created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command will fail if user is not logged in.

Argumentos globales
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Comandos del modelo

    list
    register
    show

**Registro de un modelo**

Comando para registrar el modelo.

`az ml model register --model [path to model file] --name [model name]`

Detalles del comando:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Argumentos globales

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Comandos de manifiesto

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Creación del manifiesto**

Crea un archivo de manifiesto para el modelo. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Detalles del comando:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Argumentos del modelo registrados

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Argumentos del modelo no registrados

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Argumentos globales

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Comandos de imagen

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Creación de imagen**

Puede crear una imagen con la opción de haber creado su manifiesto antes. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

O bien, puede crear el manifiesto y la imagen con un único comando. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Detalles del comando:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Argumentos del manifiesto registrados

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Argumentos del manifiesto no registrados

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Comandos de servicio
Se admiten los siguientes comandos para el servicio. Para ver los parámetros para cada comando, use la opción -h. Por ejemplo, use `az ml service create realtime -h` para ver los detalles del comando create.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Creación de un servicio**

Para crear un servicio con una imagen creada anteriormente, use el comando siguiente:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Para crear un servicio, manifiesto e imagen con un único comando, use el comando siguiente:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Detalles de comandos:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Argumentos de imagen registrados

    --image-id                        : [Required] Image to deploy to the service.

Argumentos de imagen no registrados

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Argumentos globales

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Observe la marca `-d` para asociar dependencias: si se pasa el nombre de un directorio que no está ya empaquetado (zip, tar, etc.), ese directorio obtendrá automáticamente el formato tar y se pasará y, posteriormente, se desempaquetará automáticamente en el otro extremo. 

Si se pasa en un directorio que ya está empaquetado, se tratará como un archivo y se pasará tal y como está. No se desempaquetará automáticamente, sino que se espera que el usuario incorpore ese paso en el código.

**Obtención de los detalles del servicio**

Obtenga los detalles del servicio incluidos la dirección URL y el uso (incluidos a su vez los datos de ejemplo si se ha creado un esquema).

`az ml service show realtime --name [service name]`

Detalles del comando:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argumentos globales

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**Ejecución del servicio**

`az ml service run realtime -n [service name] -d [input_data]`

Detalles del comando:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Argumentos globales

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Comando

    az ml service run realtime

Arguments --id -i    : [obligatorio] El identificador del servicio con el que puntuar.
-d         : los datos usados para llamar al servicio web.
-v         : marca de nivel de detalle.

Argumentos globales

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
