

## <a name="azure-cli-20"></a>CLI de Azure 2.0

Cuando haya [instalado la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), use el comando `az vm image list` para ver una lista en caché de imágenes de máquina virtual conocidas. Por ejemplo, el siguiente ejemplo del comando `az vm image list -o table` muestra:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Búsqueda de todas las imágenes actuales

Para obtener la lista actual de todas las imágenes, use el comando `az vm image list` con la opción `--all`. A diferencia de los comandos de la CLI de Azure 1.0, el comando `az vm image list --all` devuelve de forma predeterminada todas las imágenes de **westus** (a menos que especifique un determinado argumento `--location`), por lo que el comando `--all` tarda algún tiempo en completarse. Si desea investigar de forma interactiva, use `az vm image list --all > allImages.json`, que devuelve una lista de todas las imágenes disponibles actualmente en Azure y la almacena como un archivo para su uso local. 

Puede elegir una entre varias opciones para limitar la búsqueda a una ubicación, oferta, publicador o SKU específicos si ya tiene uno o varios en mente. Si no especifica una ubicación, se devuelven los valores para **westus**.

### <a name="find-specific-images"></a>Búsqueda de imágenes específicas

Use `az vm image list` con un [filtro de consulta JMESPATH](https://docs.microsoft.com/cli/azure/query-az-cli2) para buscar información específica. Por ejemplo, a continuación se muestra la **SKU** que está disponible para **Debian** (recuerde que sin el modificador `--all` solo se busca en la caché local de imágenes comunes):

```azurecli
az vm image list --query '[?contains(offer,`Debian`)]' -o table --all
```

El resultado es similar a este: 
```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
  Sku  Publisher    Offer    Urn                       Version    UrnAlias
-----  -----------  -------  ------------------------  ---------  ----------
    8  credativ     Debian   credativ:Debian:8:latest  latest     Debian

<list shortened for the example>
```

Si sabe cuál va a ser la ubicación de implementación, puede usar los resultados de búsqueda de imágenes generales junto con los comandos `az vm image list-skus`, `az vm image list-offers` y `az vm image list-publishers` para encontrar exactamente lo que quiere y dónde se puede implementar. Por ejemplo, si por el ejemplo anterior sabe que `credativ` tiene una oferta Debian, puede usar `--location` y otras opciones para buscar exactamente lo que quiere. En el ejemplo siguiente se busca una imagen de Debian 8 en **westeurope**:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

La salida es la siguiente:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="azure-cli-10"></a>CLI de Azure 1.0 

> [!NOTE]
> En este artículo se describe cómo navegar por las imágenes de máquina virtual y seleccionarlas mediante una instalación de la CLI de Azure 1.0 o Azure PowerShell que admite el modelo de implementación de Azure Resource Manager. Como requisito previo, cambie al modo de Resource Manager. Con la CLI de Azure, especifique el modo escribiendo `azure config mode arm`. 
> 

La forma más rápida de buscar una imagen es llamar al comando `azure vm image list` y pasar la ubicación, el nombre del publicador (no distingue mayúsculas de minúsculas) y una oferta, si la conoce. Por ejemplo, la lista siguiente es solo un ejemplo breve (muchas listas son bastante largas) si sabe que "Canonical" es un publicador de la oferta "UbuntuServer".

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

La columna **Urn** será el formulario que se pase a `azure vm quick-create`.

A menudo, sin embargo, aún no se conoce lo que está disponible. En este caso, puede navegar por las imágenes mediante el comando `azure vm image list-publishers` y especificar una ubicación de centro de datos en el símbolo del sistema. Por ejemplo, a continuación se enumeran todos los publicadores de imágenes en la ubicación Oeste de EE. UU. (pase el argumento de ubicación en minúsculas y sin espacios a partir de las ubicaciones estándar).

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Estas listas pueden ser bastante largas, por lo que la lista del ejemplo anterior es simplemente un fragmento. Supongamos que hemos observado que Canonical es, de hecho, un publicador de imágenes en la ubicación Oeste de EE. UU. Ahora puede encontrar sus ofertas mediante una llamada a `azure vm image list-offers` y pasar la ubicación y el publicador cuando se soliciten, como en el ejemplo siguiente:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Ahora ya sabemos que, en la región oeste de EE. UU., Canonical publica la oferta **UbuntuServer** en Azure. Pero, ¿con qué SKU? Para obtener esos valores, llame a `azure vm image list-skus` y responda a la solicitud con la ubicación, el publicador y la oferta que haya encontrado.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Con esta información, ahora puede buscar con precisión la imagen que desee mediante una llamada a la llamada original en la parte superior.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

Ahora puede elegir con precisión la imagen que desea utilizar. Para crear una máquina virtual rápidamente con la información de URN que acaba de encontrar, o para usar una plantilla con esa información de URN, vea [Uso de la CLI de Azure CLI para Mac, Linux y Windows con el Administrador de recursos de Azure](../articles/xplat-cli-azure-resource-manager.md).

## <a name="powershell"></a>PowerShell
> [!NOTE]
> Instale y configure la [versión más reciente de Azure PowerShell](/powershell/azureps-cmdlets-docs). Si usa módulos de Azure PowerShell anteriores a la versión 1.0, seguirá usando estos comandos, pero primero debe `Switch-AzureMode AzureResourceManager`. 
> 
> 

Al crear una nueva máquina virtual con el Administrador de recursos de Azure, en algunos casos deberá especificar una imagen con la combinación de las propiedades de imagen siguientes:

* Publicador
* Oferta
* SKU

Por ejemplo, estos valores son necesarios para el cmdlet `Set-AzureRMVMSourceImage` de PowerShell o con un archivo de plantilla de grupo de recursos en el que debe especificar el tipo de máquina virtual que se creará.

Si necesita determinar estos valores, puede explorar las imágenes para determinar estos valores:

1. Listado de los publicadores de imágenes.
2. Para un publicador determinado, enumeración de sus ofertas.
3. Para una oferta determinada, enumeración de sus SKU.

En primer lugar, muestre los publicadores con los siguientes comandos:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Rellene el nombre del publicador elegido y ejecute los siguientes comandos:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Rellene el nombre de la oferta elegida y ejecute los siguientes comandos:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

En la pantalla del comando `Get-AzureRMVMImageSku` , tiene toda la información que necesita para especificar la imagen para una nueva máquina virtual.

A continuación se muestra un ejemplo completo:

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Para el publicador de "MicrosoftWindowsServer":

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Para la oferta "WindowsServer":

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

En esta lista, copie el nombre de SKU elegido, y ya tiene toda la información para el cmdlet `Set-AzureRMVMSourceImage` de PowerShell o una plantilla de grupo de recursos.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/