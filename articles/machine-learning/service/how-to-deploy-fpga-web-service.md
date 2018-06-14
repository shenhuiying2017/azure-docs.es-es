---
title: Implementación de un modelo como servicio web en una FPGA con Azure Machine Learning
description: Obtenga información sobre cómo implementar un servicio web con un modelo que se ejecuta en una FPGA con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33784474"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Implementación de un modelo como servicio web en una FPGA con Azure Machine Learning

En este documento, obtendrá información sobre cómo configurar el entorno de su estación de trabajo e implementar un modelo como servicio web en las [matrices de puertas programables por campos (FPGA)](concept-accelerate-with-fpgas.md). El servicio web usa Project Brainwave para ejecutar el modelo en FPGA.

El uso de las FPGA brinda una inferencia de latencia muy baja, incluso con un tamaño de lote único.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Creación de una cuenta de Administración de modelos de Azure Machine Learning

1. Vaya a la página de creación de cuentas de Administración de modelos en [Azure Portal](https://aka.ms/aml-create-mma).

2. En el portal, cree una cuenta de Administración de modelos en la región **Este de EE. UU. 2**.

   ![Imagen de la pantalla de creación de cuenta de Administración de modelos](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Asigne un nombre a la cuenta de Administración de modelos, elija una suscripción y un grupo de recursos.

   >[!IMPORTANT]
   >En la ubicación, DEBE elegir **Este de EE. UU. 2** como la región.  Actualmente no hay otras regiones disponibles.

4. Elija un plan de tarifa (S1 es suficiente, pero también podría usar S2 y S3).  No se admite el plan DevTest.  

5. Haga clic en **Seleccionar** para confirmar el plan de tarifa.

6. Haga clic en **Crear** en la Administración de modelos de Machine Learning que aparece a la izquierda.

## <a name="get-model-management-account-information"></a>Obtención de información de la cuenta de Administración de modelos

Para información sobre la cuenta de Administración de modelos (MMA), haga clic en la __cuenta de Administración de modelos__ en Azure Portal.

Copie los valores de los elementos siguientes:

+ Nombre de la cuenta de Administración de modelos (en la esquina superior izquierda)
+ Definición de un nombre de grupo de recursos
+ Id. de suscripción
+ Ubicación (use "eastus2")

![Información de la cuenta de Administración de modelos](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Configuración de la máquina

Para configurar la estación de trabajo para la implementación de FPGA, use estos pasos:

1. Descargue e instale la versión más reciente de [Git](https://git-scm.com/downloads).

2. Instale [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Para descargar el entorno Anaconda, use el comando siguiente desde un símbolo del sistema de Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Para crear el entorno, abra un **símbolo del sistema de Anaconda** (no un símbolo del sistema de Azure Machine Learning Workbench) y ejecute el comando siguiente:

    > [!IMPORTANT]
    > El archivo `environment.yml` está en el repositorio Git que clonó en el paso anterior. Cambie la ruta de acceso según sea necesario para señalar el archivo en su estación de trabajo.

    ```
    conda env create -f environment.yml
    ```

5. Para activar el entorno, use el comando siguiente:

    ```
    conda activate amlrealtimeai
    ```

6. Para iniciar el servidor Jupyter Notebook, use el comando siguiente:

    ```
    jupyter notebook
    ```

    La salida de este comando es similar al texto siguiente:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Obtendrá un token distinto cada vez que ejecute el comando.

    Si el explorador no se abre automáticamente en Jupyter Notebook, abra la página con la dirección URL HTTP que el comando anterior devolvió.

    ![Imagen de la página web de Jupyter Notebook](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Implementación del modelo

En Jupyter Notebook, abra el Notebook `00_QuickStart.ipynb` desde el directorio `notebooks/resnet50`. Siga las instrucciones del Notebook para lo siguiente:

* Definición del servicio
* Implementación del modelo
* Consumo del modelo implementado
* Eliminación de los servicios implementados

> [!IMPORTANT]
> Para optimizar la latencia y el rendimiento, la estación de trabajo debe estar en la misma región de Azure que el punto de conexión.  Actualmente, las API se crean en la región de Azure Este de EE. UU.

## <a name="ssltls-and-authentication"></a>Protocolo SSL/TSL y autenticación

Azure Machine Learning brinda compatibilidad con el protocolo SSL y autenticación basada en claves. Esto le permite restringir el acceso a su servicio y proteger los datos que envían los clientes.

> [!NOTE]
> Los pasos de esta sección solo se aplican a los modelos acelerados de hardware de Azure Machine Learning. Para los servicios estándar de Azure Machine Learning, consulte el documento [Habilitar SSL en un clúster de Azure Machine Learning Compute](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc).

> [!IMPORTANT]
> La autenticación solo está habilitada para los servicios que han proporcionado un certificado SSL y una clave. 
>
> Si no habilita SSL, cualquier usuario de Internet podrá hacer llamadas al servicio.
>
> Si habilita SSL, se requiere una clave de autenticación cuando se accede al servicio.

El protocolo SSL cifra los datos que se envían entre el cliente y el servicio. También lo usa el cliente para comprobar la identidad del servidor.

Puede implementar un servicio con SSL habilitado, o bien actualizar un servicio ya implementado para habilitarlo. Los pasos son los mismos:

1. Adquirir un nombre de dominio.

2. Adquirir un certificado SSL.

3. Implementar o actualizar el servicio con SSL habilitado.

4. Actualizar el DNS para que apunte al servicio.

### <a name="acquire-a-domain-name"></a>Adquisición de un nombre de dominio

Si todavía no tiene un nombre de dominio, puede comprar uno en un __registrador de nombres de dominio__. El proceso varía entre los distintos registradores, al igual que el costo. El registrador también proporciona herramientas para administrar el nombre de dominio. Estas herramientas se usan para asignar un nombre de dominio completo (como www.contoso.com) a la dirección IP donde se hospeda el servicio.

### <a name="acquire-an-ssl-certificate"></a>Adquisición de un certificado SSL

Hay muchas maneras de obtener un certificado SSL. La más común es comprar uno en una __entidad de certificación__ (CA). Independientemente de dónde se obtiene el certificado, se necesitan estos archivos:

* Un __certificado__. El certificado debe incluir la cadena de certificados completa y debe estar codificado en PEM.
* Una __clave__. La clave debe estar codificada en PEM.

> [!TIP]
> Si la entidad de certificación no puede proporcionar el certificado y la clave como archivos codificados en PEM, puede usar una utilidad como [OpenSSL](https://www.openssl.org/) para cambiar el formato.

> [!IMPORTANT]
> Los certificados autofirmados solo se deben usar para desarrollo. No se deben usar en el entorno de producción.
>
> Si usa un certificado autofirmado, consulte la sección [Consumo de servicios con certificados autofirmados](#self-signed) para instrucciones específicas.

> [!WARNING]
> Cuando solicite un certificado, debe proporcionar el nombre de dominio completo (FQDN) de la dirección que pretende usar para el servicio. Por ejemplo, www.contoso.com. La dirección que aparece en el certificado y la dirección que usan los clientes se comparan cuando se valida la identidad del servicio.
>
> Si no coinciden, se generará un error. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Implementación o actualización del servicio con el protocolo SSL habilitado

Para implementar el servicio con el protocolo SSL habilitado, establezca el parámetro `ssl_enabled` en `True`. Establezca el parámetro `ssl_certificate` en el valor del archivo __certificate__ y `ssl_key` en el valor del archivo __key__. En el ejemplo siguiente se muestra cómo implementar un servicio con el protocolo SSL habilitado:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

La respuesta de la operación `create_service` incluye la dirección IP del servicio. La dirección IP se usa al asignar el nombre DNS a la dirección IP del servicio.

La respuesta también incluye una __clave principal__ y una __clave secundaria__ que se usan para consumir el servicio.

### <a name="update-your-dns-to-point-to-the-service"></a>Actualización del DNS para que apunte al servicio

Use las herramientas que proporciona el registrador de nombres de dominio para actualizar el registro de DNS de su nombre de dominio. El registro debe apuntar a la dirección IP del servicio.

> [!NOTE]
> En función del registrador y del período de vida (TTL) configurado para el nombre de dominio, pueden pasar de varios minutos a varias horas antes de que los clientes puedan resolver el nombre de dominio.

### <a name="consuming-authenticated-services"></a>Consumo de servicios autenticados

En los ejemplos siguientes se muestra cómo consumir un servicio autenticado con Python y C#:

> [!NOTE]
> Reemplace `authkey` por la clave principal o secundaria que se devolvió al crear el servicio.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Otros clientes gRPC pueden autenticar las solicitudes al establecer un encabezado de autorización. El enfoque general es crear un objeto `ChannelCredentials` que combina `SslCredentials` con `CallCredentials`. Esto se agrega al encabezado de autorización de la solicitud. Para más información sobre cómo implementar la compatibilidad con los encabezados específicos, consulte [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

En los ejemplos siguientes se muestra cómo establecer el encabezado en C# y Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Consumo de servicios con certificados autofirmados

Hay dos maneras de permitir que el cliente se autentique en un servicio protegido con un certificado autofirmado:

* En el sistema cliente, establezca la variable de entorno `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` en el sistema cliente para que apunte al archivo de certificado.

* Cuando construya un objeto `SslCredentials`, pase el contenido del archivo de certificado al constructor.

Usar cualquiera de los métodos hace que gRPC use el certificado como el certificado raíz.

> [!IMPORTANT]
> gRPC no aceptará certificados que no son de confianza. Si se usa un certificado que no es de confianza, se generará un error con el código de estado `Unavailable`. Los detalles del error incluyen `Connection Failed`.
