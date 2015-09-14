<properties 
   pageTitle="Proveedor de recursos de red"
	description="Proveedor de recursos de red"
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="adinah"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/23/2015"
	ms.author="telmos"/>

# Proveedor de recursos de red
Una necesidad que sustenta el éxito de un negocio hoy en día es la capacidad de crear y administrar aplicaciones compatibles con redes de gran escala de una forma ágil, flexible, segura y repetible. El Administrador de recursos de Azure (ARM) permite crear aplicaciones, como una única colección de recursos en grupos de recursos. Estos recursos se administran a través de diversos proveedores de recursos en el Administrador de recursos de Azure.

El Administrador de recursos de Azure permite crear dichas aplicaciones y la colección asociada de recursos de red como una única colección de recursos en un grupo de recursos. La aplicación y los recursos de red se ejecutan como una sola unidad en un grupo de recursos del Administrador de recursos de Azure.

Puede administrar los recursos de red mediante cualquiera de las siguientes interfaces de administración:

- API basadas en REST
- PowerShell
- .NET SDK
- SDK de Node.JS
- SDK de Java
- CLI de Azure
- Portal de Azure
- Lenguaje de la plantilla del Administrador de recursos de Azure

Con la incorporación de proveedores de recursos de red, puede disfrutar de las ventajas siguientes:

- **Metadatos**: puede agregar información a los recursos mediante etiquetas. Estas etiquetas pueden utilizarse para realizar el seguimiento de la utilización de recursos en todas las suscripciones y los grupos de recursos.
- **Mayor control de la red**: los recursos de red están acoplados holgadamente y los puede controlar de forma más detallada. Esto significa que tendrá una mayor flexibilidad en la administración de los recursos de red.
- **Configuración más rápida**: dado que los recursos de red están acoplados holgadamente, puede crear y organizar los recursos de red en paralelo. Esto ha reducido drásticamente el tiempo de configuración.
- **Control de acceso basado en rol (RBAC)**: RBAC proporciona roles predeterminados, con un ámbito de seguridad específico, además de permitir la creación de roles personalizados para una administración segura. 
- **Administración e implementación más sencillas**: resulta más fácil implementar y administrar aplicaciones, ya que puede crear una pila de toda la aplicación como una única colección de recursos en un grupo de recursos. Y más rápido de implementar, ya que es posible hacerlo con solo proporcionar una carga JSON de la plantilla.
- **Personalización rápida**: puede utilizar las plantillas de estilo declarativo para habilitar la personalización repetible y rápida de las implementaciones. 
- **Personalización repetible**: puede utilizar las plantillas de estilo declarativo para habilitar la personalización repetible y rápida de las implementaciones.

## Recursos de red 
Ahora puede administrar los recursos de red de forma independiente, en lugar de tener hacerlo a través de un recurso de proceso único (una máquina virtual). Esto garantiza un mayor grado de flexibilidad y agilidad a la hora de componer una infraestructura compleja y de gran escala en un grupo de recursos.
 
El diagrama siguiente muestra una visión general del modelo de recursos de red y sus asociaciones. Los recursos de nivel superior están coloreados con un contorno azul. Además de los recursos de nivel superior, puede ver los recursos secundarios coloreados con un contorno gris. Puede administrar individualmente cada recurso.

![Modelo de recursos de red](./media/resource-groups-networking/Figure1.png)

A continuación se presenta una vista conceptual de una implementación de ejemplo que implica una aplicación de varios niveles. Todos los recursos de red están coloreados con un contorno azul.

![Modelo de recursos de red](./media/resource-groups-networking/Figure2.png)

## API de REST 
Como se mencionó anteriormente, se pueden administrar los recursos de red a través de una variedad de interfaces, incluidas API de REST, .NET SDK, SDK de Node.JS, SDK de Java, PowerShell, CLI, Portal de Azure y plantillas.

Las API de REST se ajustan a la especificación del protocolo HTTP 1.1. A continuación se presenta la estructura general de URI de la API:

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Y los parámetros entre llaves representan los elementos siguientes:

- **subscription-id**: identificador de su suscripción a Azure.
- **resource-provider-namespace**: espacio de nombres para el proveedor que se utiliza. El valor para el proveedor de recursos de red es *Microsoft.Network*.
- **region-name**: el nombre de la región de Azure.

Se admiten los siguientes métodos HTTP cuando se realizan llamadas a la API de REST:

- **PUT**: se utiliza para crear un recurso de un tipo determinado, modificar una propiedad de recurso o cambiar una asociación entre recursos. 
- **GET**: se utiliza para recuperar información para un recurso aprovisionado.
- **DELETE**: se utiliza para eliminar un recurso existente.

La solicitud y la respuesta se ajustan a un formato de carga JSON. Para obtener más información, consulte [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

## Lenguaje de la plantilla del Administrador de recursos de Azure
Además de administrar recursos de forma imperativa (a través de API o SDK), también puede utilizar un estilo de programación declarativo para crear y administrar recursos de red mediante el lenguaje de la plantilla del Administrador de recursos de Azure.

A continuación se proporciona una representación de una plantilla de ejemplo:

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

La plantilla es principalmente una descripción de JSON de los recursos y los valores de instancia insertados a través de parámetros. El ejemplo siguiente puede utilizarse para crear una red virtual con dos subredes.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Tiene la opción de proporcionar los valores de los parámetros manualmente cuando se utiliza una plantilla, o bien puede usar un archivo de parámetros. El ejemplo siguiente muestra un posible conjunto de valores de parámetros para usar con la plantilla anterior:

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


Las principales ventajas derivadas del uso de plantillas son las siguientes:

- Puede crear una infraestructura compleja en un grupo de recursos de estilo declarativo. La coordinación de la creación de recursos, incluida la administración de dependencias, se controla mediante el Administrador de recursos de Azure. 
- La infraestructura se puede crear de una manera repetible en diferentes regiones y dentro de una misma región cambiando simplemente parámetros. 
- El estilo declarativo permite reducir el tiempo necesario para generar las plantillas y distribuir la infraestructura. 

Para ver plantillas de ejemplo, consulte [Plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).

Para obtener más información sobre el lenguaje de la plantilla del Administrador de recursos de Azure, vea [Idioma de la plantilla del administrador de recursos de Azure](../resource-group-authoring-templates.md).

La plantilla del ejemplo anterior utiliza la red virtual y los recursos de la subred. Hay otros recursos de red que se pueden utilizar, como se muestra a continuación:

## NIC
La tarjeta de interfaz de red, o NIC, representa una interfaz de red que se puede asociar a una máquina virtual (VM). Una máquina virtual puede tener una o varias NIC.

![NIC en una sola máquina virtual](./media/resource-groups-networking/Figure3.png)

Entre las principales propiedades de un recurso de NIC se incluyen las siguientes:

- Configuración de IP
- Nombre DNS interno
- Servidores DNS

Una NIC también se puede asociar con los siguientes recursos de red:

- Grupo de seguridad de red (NSG) 
- Equilibrador de carga

## Red virtual y subred
Las redes virtuales (VNET) y las subredes ayudan a definir un límite de seguridad para las cargas de trabajo que se ejecutan en Azure. Una red virtual se caracteriza por un espacio de direcciones, también denominado bloque CIDR.

Una subred es un recurso secundario de una red virtual, y ayuda a definir segmentos de espacios de direcciones dentro de un bloque CIDR usando prefijos de direcciones IP. Las máquinas virtuales que ejecutan varias cargas de trabajo funcionan básicamente en un límite de subred.

![NIC en una sola máquina virtual](./media/resource-groups-networking/Figure4.png)

Entre las principales propiedades de un recurso de red virtual se incluyen las siguientes:

- Espacio de direcciones IP (bloque CIDR) 
- Nombre de red virtual
- subredes
- Servidores DNS

Una red virtual puede asociarse también con los siguientes recursos de red:

- Puerta de enlace de VPN

Entre las principales propiedades de una subred se incluyen las siguientes:

- Prefijo de dirección IP
- Nombre de subred

Una subred puede asociarse también con los siguientes recursos de red:

- Grupo de seguridad de red

## Equilibrador de carga
Se utiliza un equilibrador de carga cuando desea escalar sus aplicaciones. Entre los escenarios de implementación habituales se encuentran las aplicaciones que se ejecutan en varias instancias de máquina virtual. Las instancias de máquina virtual están representadas por un equilibrador de carga que ayuda a distribuir el tráfico de red a las diversas instancias.

![NIC en una sola máquina virtual](./media/resource-groups-networking/Figure5.png)

Los equilibradores de carga contienen los siguientes recursos secundarios:

- **Configuración de dirección IP de front-end**: un equilibrador de carga puede incluir una o varias direcciones IP de front-end, conocidas también como IP virtuales (VIP). Estas direcciones IP sirven como entrada para el tráfico. 
- **Grupo de direcciones de back-end**: se trata de direcciones IP asociadas a las NIC de máquina virtual a la que se distribuirá la carga.
- **Reglas de equilibrio de carga**: una propiedad de regla asigna una combinación de una determinada dirección IP de front-end y puerto a un conjunto compuesto por una combinación de direcciones IP de back-end y puerto. Con una única definición de un recurso de equilibrador de carga, puede definir varias reglas de equilibrio de carga, donde cada regla refleja una combinación de dirección IP de front-end y puerto y de dirección IP de back-end y puerto asociada con las máquinas virtuales. 
- **Sondeos**: los sondeos permiten realizar un seguimiento del estado de las instancias de máquina virtual. Si se produce un error en un sondeo de estado, la instancia de VM se sacará automáticamente de la rotación.
- **Reglas NAT entrantes**: las reglas NAT que definen el tráfico entrante que fluye a través de la dirección IP de front-end y se distribuye a la dirección IP de back-end.

## Puerta de enlace de aplicaciones

La puerta de enlace de aplicaciones ofrece una solución de equilibrio de carga HTTP administrada de Azure basada en el equilibrio de carga de nivel 7. El equilibrio de carga de aplicaciones permite el uso de reglas de enrutamiento para el tráfico de red basado en HTTP.

La puerta de enlace de aplicaciones contiene los siguientes recursos secundarios:

- **Grupo de servidores back-end:** la lista de direcciones IP de los servidores back-end. Las direcciones IP mostradas deben pertenecer a la subred de la red virtual o ser una VIP/IP pública. 
- **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este puerto es el puerto público abierto en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha**: la escucha tiene un puerto front-end, un protocolo (http o https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). 
- **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto. Actualmente, solo se admite la regla básica. La regla básica es la distribución de carga round robin.


## Dirección IP pública
Un recurso de dirección IP pública proporciona una dirección IP pública reservada o dinámica. Una dirección IP pública puede asignarse a un equilibrador de carga, NAT, o asociarse a una dirección IP privada en una tarjeta NIC de una máquina virtual.

Entre las principales propiedades de un recurso de dirección IP pública se incluyen las siguientes:

- **Método de asignación de IP**: reservado o dinámico. 

## Grupo de seguridad de red (NSG)
Un recurso de grupo de seguridad de red permite la creación de un límite de seguridad para las cargas de trabajo mediante la implementación de reglas de permiso y denegación. Estas reglas se pueden aplicar en el nivel de NIC (nivel de instancia de máquina virtual) o en el nivel de subred (grupo de máquinas virtuales).

Entre las principales propiedades de un recurso de grupo de seguridad de red se incluyen las siguientes:

- **Regla de seguridad**: un grupo de seguridad de red puede contener varias reglas de seguridad definidas. Cada regla puede permitir o denegar distintos tipos de tráfico.

## Regla de seguridad
Una regla de seguridad es un recurso secundario de un grupo de seguridad de red.

Entre las principales propiedades de una regla de seguridad se incluyen las siguientes:

- **Protocolo**: protocolo de red al que se aplica esta regla.
- **Intervalo de puerto de origen**: puerto de origen, o intervalo, desde 0 a 65535. Se puede utilizar un carácter comodín para que coincida con todos los puertos. 
- **Intervalo de puertos de destino**: puerto de destino, o intervalo, desde 0 a 65535. Se puede utilizar un carácter comodín para que coincida con todos los puertos.
- **Prefijo de dirección de origen**: intervalo de direcciones IP de origen. 
- **Prefijo de dirección de destino**: intervalo de direcciones IP de destino.
- **Acceso** – *permitir* o *denegar* tráfico.
- **Prioridad**: un valor entre 100 y 4096. El número de prioridad debe ser único para cada regla de la colección de reglas de seguridad. Cuanto menor sea el número de prioridad, mayor será la prioridad de la regla.
- **Dirección**: especifica si la regla se aplicará al tráfico en la dirección *entrante* o *saliente*. 

## Puerta de enlace de VPN 
Un recurso de puerta de enlace de VPN le permite crear una conexión segura entre su centro de datos local y Azure. Un recurso de puerta de enlace de VPN se puede configurar de tres maneras diferentes:
 
- **De punto a sitio**: puede obtener acceso seguro a los recursos de Azure hospedados en una red virtual con un cliente VPN desde cualquier equipo. 
- **Conexión de varios sitios**: puede conectarse de forma segura desde los centros de datos locales a los recursos que se ejecutan en una red virtual. 
- **Red virtual a red virtual**: puede conectarse de forma segura en todas las redes virtuales de Azure de la misma región, o de diferentes regiones, para generar cargas de trabajo con redundancia geográfica.

Entre las principales propiedades de una puerta de enlace de VPN se incluyen las siguientes:
 
- **Tipo de puerta de enlace**: puerta de enlace de ruta dinámica o estática. 
- **Prefijo del grupo de direcciones de cliente VPN**: direcciones IP que se asignarán a los clientes que se conectan en una configuración de punto a sitio.



## Perfil del Administrador de tráfico
El Administrador de tráfico y su recurso de extremo secundario permiten la distribución del tráfico a los extremos de Azure y fuera de Azure. Esta distribución de tráfico se rige por las directivas. El Administrador de tráfico también permite supervisar el estado del extremo y desviar el tráfico adecuadamente según el estado de un extremo.

Entre las principales propiedades de un perfil de administrador de tráfico se incluyen las siguientes:

- **Método de enrutamiento de tráfico**: los valores posibles son *Rendimiento*, *Ponderado* y *Prioridad*.
- **Configuración de DNS**: nombre de dominio completo del perfil.
- **Protocolo**: protocolo de supervisión, los valores posibles son *HTTP* y *HTTPS*.
- **Puerto**: puerto de supervisión. 
- **Ruta**: ruta de acceso de supervisión.
- **Extremos**: contenedor de recursos de extremo.

## Extremo 
Un extremo es un recurso secundario de un perfil de Administrador de tráfico. Representa un servicio o extremo web al que se distribuye el tráfico del usuario según la directiva configurada en el recurso de perfil del Administrador de tráfico.

Entre las principales propiedades de un extremo se incluyen las siguientes:
 
- **Tipo**: el tipo de extremo, los posibles valores son *extremo de Azure*, *extremo externo* y *extremo anidado*. 
- **Identificador de recurso de destino**: dirección IP pública de servicio o extremo web. Puede tratarse de un extremo de Azure o uno externo.
- **Ponderación**: ponderación del extremo utilizada en la administración del tráfico. 
- **Prioridad**: prioridad del extremo, utilizada para definir una acción de conmutación por error. 

## DNS de Azure

DNS de Azure es un servicio de hospedaje para los dominios DNS, que permite resolver nombres mediante la infraestructura de Microsoft Azure.

Entre las principales propiedades del DNS de Azure se incluyen las siguientes:

- ** Zonas DNS **: información de la zona de dominio para almacenar los registros de DNS de un dominio determinado.
- ** Conjunto de registros de DNS**: una colección de registros de un tipo específico. Los tipos compatibles son A, AAAA, CNAME, MX, NS, SOA, SRV y TXT.


## Uso de una plantilla

Puede implementar servicios en Azure desde una plantilla mediante PowerShell, CLI de Azure o mediante un clic para implementar desde GitHub. Para implementar servicios de una plantilla en GitHub, ejecute los siguientes pasos:

1. Abra el archivo de plantilla 3 desde GitHub. Como ejemplo, abra [Red virtual con dos subredes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Haga clic en **Implementación en Azure**, y, a continuación, inicie sesión en el Portal de Azure con sus credenciales.
3. Compruebe la plantilla y, a continuación, haga clic en **Guardar**.
4. Haga clic en **Editar parámetros** y seleccione una ubicación, como *West US*, para la red virtual y las subredes.
5. Si es necesario, cambie los parámetros **ADDRESSPREFIX** y **SUBNETPREFIX** y, a continuación, haga clic en **Aceptar**.
6. Haga clic en **Seleccionar un grupo de recursos** y, a continuación, haga clic en el grupo de recursos al que desea agregar la red virtual y las subredes. Como alternativa, puede crear un nuevo grupo de recursos haciendo clic en **Crear nuevo**.
3. Haga clic en **Crear**. Observe el icono que muestra **Implementación de plantillas de aprovisionamiento**. Una vez que se realiza la implementación, verá una pantalla similar a la siguiente.

![Implementación de plantilla de ejemplo](./media/resource-groups-networking/Figure6.png)


## Otras referencias

[Referencia de API de redes de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referencia de Azure PowerShell para redes](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md)

[Redes de Azure: plantillas utilizadas habitualmente](https://github.com/Azure/azure-quickstart-templates)

[Proveedor de recursos de proceso](../virtual-machines-azurerm-versus-azuresm)

[Información general del Administrador de recursos de Azure](../resource-group-overview)

[Control de acceso basado en rol en el Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Uso de etiquetas en el Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Implementaciones de plantilla](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=September15_HO1-->