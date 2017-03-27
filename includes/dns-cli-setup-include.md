## <a name="set-up-azure-cli-for-azure-dns"></a>Configuración de la CLI de Azure para DNS de Azure

### <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes.

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instale la versión más reciente de la CLI de Azure, disponible para Windows, Linux o Mac. Puede consultar mas información disponible en [Instalación de la CLI de Azure](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure.

Abra una ventana de consola y autentíquese con sus credenciales. Para más información, consulte [Inicio de sesión en Azure desde la CLI de Azure](../articles/xplat-cli-connect.md).

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Cambio del modo de CLI

La DNS de Azure usa el Administrador de recursos de Azure. Asegúrese de cambiar al modo de CLI para usar los comandos de Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Selección de la suscripción

Compruebe las suscripciones para la cuenta.

```azurecli
azure account list
```

Elección de la suscripción de Azure que se va a usar.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

Puede omitir este paso si utiliza un grupo de recursos existente.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registro del proveedor de recursos

El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar Azure DNS. Se trata de una operación única para cada suscripción.

```azurecli
azure provider register --namespace Microsoft.Network
```

