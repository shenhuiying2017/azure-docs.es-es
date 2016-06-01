<properties
   	pageTitle="Creación de clústeres Hadoop, HBase o Storm en Linux en HDInsight con cURL y la API de REST de Azure | Microsoft Azure"
   	description="Aprenda a crear clústeres de HDInsight basados en Linux mediante cURL, plantillas del Administrador de recursos de Azure y la API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o Storm) o usar scripts para instalar componentes personalizados."
   	services="hdinsight"
   	documentationCenter=""
   	authors="Blackmist"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/16/2016"
   	ms.author="larryfr"/>

#Creación de clústeres basados en Linux en HDInsight con cURL y la API de REST de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

La API de REST de Azure permite realizar operaciones de administración de servicios hospedados en la plataforma Azure, incluida la creación de recursos como, por ejemplo, clústeres de HDInsight basados en Linux. En este documento, obtendrá información sobre cómo crear plantillas del Administrador de recursos de Azure para configurar un clúster de HDInsight y el almacenamiento asociado y luego usar cURL para implementar la plantilla en la API de REST de Azure para crear un clúster de HDInsight.

> [AZURE.IMPORTANT] Los pasos de este documento usan el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o cambiando el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Requisitos previos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI de Azure__ La CLI de Azure se usa para crear una entidad de servicio, que luego se emplea para generar tokens de autenticación para solicitudes a la API de REST de Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __cURL__. Esta utilidad está disponible a través del sistema de administración de paquetes, o se puede descargar desde [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Si usa PowerShell para ejecutar los comandos de este documento, debe quitar primero el alias `curl` que crea de forma predeterminada. Este alias usa Invoke-WebRequest, un cmdlet de PowerShell, en lugar de cURL cuando se usa el comando `curl` desde un símbolo del sistema de PowerShell y devolverá errores para muchos de los comandos que se usan en este documento.
    > 
    > Para quitar este alias, use lo siguiente desde el símbolo del sistema de PowerShell:
    >
    > `Remove-item alias:curl`
    >
    > Una vez quitado el alias, debe poder usar la versión de cURL que instaló en el sistema.

##Creación de una plantilla

Las plantillas del Administrador de recursos de Azure son documentos JSON que describen un __grupo de recursos__ y todos los recursos incluidos (por ejemplo, HDInsight). Este enfoque basado en plantillas permite definir todos los recursos que se necesitan para HDInsight en una sola plantilla y administrar conjuntamente los cambios realizados en el grupo mediante __implementaciones__ que aplican los cambios al grupo.

Normalmente las plantillas se proporcionan en dos partes; la propia plantilla y un archivo de parámetros que se rellena con valores específicos para la configuración. Por ejemplo, el nombre del clúster, el nombre de administrador y la contraseña. Cuando se usa directamente la API de REST, debe combinar estos valores en un archivo. El formato de este documento JSON es:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Por ejemplo, lo siguiente es una combinación de los archivos de plantilla y de parámetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que crea un clúster basado en Linux, con una contraseña para proteger la cuenta de usuario de SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

Este ejemplo se usará en los pasos de este documento. Debe reemplazar los marcadores de posición _value_ de la sección __Parameters__ al final del documento con los valores que desee usar para el clúster.

##Iniciar sesión en su suscripción de Azure

Siga los pasos documentados en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-connect.md) y conéctese a su suscripción mediante el comando `azure login`.

##Creación de una entidad de servicio

> [AZURE.NOTE] Estos pasos son una versión resumida de la información proporcionada en la sección _Autenticación de una entidad de servicio con una contraseña - CLI de Azure_ del documento [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli). Estos pasos crean una nueva entidad de servicio que se puede utilizar para autenticar las solicitudes de API de REST utilizadas para crear recursos de Azure como un clúster de HDInsight.

1. Desde el símbolo del sistema, la sesión de terminal o la shell use el siguiente comando para enumerar las suscripciones de Azure.

        azure account list
        
    En la lista, seleccione la suscripción que desea usar y anote la columna __Id__. Se trata del __Id. de suscripción__ y se utilizará en la mayoría de los pasos de este documento.

2. Cree una aplicación en Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Reemplace los valores de `--name`, `--home-page`, `--identifier-uris` y por sus propios valores. Especifique una contraseña para la nueva entrada de Active Directory.
    
    > [AZURE.NOTE] Puesto que está creando esta aplicación para la autenticación a través de una entidad de servicio, los valores `--home-page` y `--identifier-uris` no necesitan hacer referencia a una página web real hospedada en Internet; solo deben ser identificadores URI únicos.
    
    Desde los datos devueltos, guarde el valor de __AppId__.
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Cree una entidad de servicio mediante el valor de __AppId__ devuelto previamente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Desde los datos devueltos, guarde el valor de __Identificador de objeto__.
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Asigne el rol __Propietario__ a la entidad de servicio mediante el valor de __Identificador de objeto__ devuelto previamente. También debe utilizar el __Id. de suscripción__ que obtuvo anteriormente.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Una vez que este comando se complete, la entidad de servicio ahora tiene acceso de propietario al identificador de suscripción especificado.

##Obtención de un token de autenticación

1. Utilice lo siguiente para encontrar el __identificador de inquilino__ de la suscripción.

        azure account show -s <subscription ID>
        
    Desde los datos devueltos, busque el __identificador de inquilino__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Genere un token nuevo mediante la API de REST de Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Reemplace __TenantID__, __AppID__ y __password__ por los valores obtenidos o usados anteriormente.

    Si esta solicitud se realiza correctamente, recibirá una respuesta 200 serie y el cuerpo de respuesta contendrá un documento JSON.

    El documento JSON que devuelve la solicitud incluirá un elemento denominado __access\_token__; el valor de este elemento es el token de acceso que hay que usar para la autenticación de las solicitudes que se utilizan en las secciones siguientes de este documento.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##Crear un grupo de recursos

Use lo siguiente para crear un grupo de recursos vacío. Debe crear el grupo primero para poder crear los recursos como el clúster de HDInsight.

* Reemplace __SubscriptionID__ por el identificador de la suscripción que recibió al crear la entidad de servicio.
* Reemplace __AccessToken__ por el token de acceso que recibió en el paso anterior.
* Reemplace __DataCenterLocation__ por el centro de datos en el que desea crear el grupo de recursos y los recursos. Por ejemplo, "Centro-Sur de EE.UU.". 
* Reemplace __ResourceGroupName__ por el nombre que desea usar para este grupo:

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Si esta solicitud se realiza correctamente, recibirá una respuesta 200 serie y el cuerpo de respuesta contendrá un documento JSON que incluye información sobre el grupo. El elemento `"provisioningState"` contendrá un valor de `"Succeeded"`.

##de una implementación

Use lo siguiente para implementar la configuración del clúster (plantilla y valores de parámetro) del grupo de recursos.

* Reemplace __SubscriptionID__ y __AccessToken__ por los valores usados anteriormente. 
* Reemplace __ResourceGroupName__ por el nombre del grupo de recursos que creó en la sección anterior.
* Reemplace __DeploymentName__ por el nombre que desea usar para esta implementación.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Si ha guardado el documento JSON que contiene la plantilla y los parámetros en un archivo, puede usar lo siguiente en lugar de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Si esta solicitud se realiza correctamente, recibirá una respuesta 200 serie y el cuerpo de respuesta contendrá un documento JSON que incluye información sobre la operación de implementación.

> [AZURE.IMPORTANT] Tenga en cuenta que en este momento la solicitud ya se envió pero sin completarse. La implementación puede tardar varios minutos, normalmente unos 15, en completarse.

##Comprobación del estado de la implementación

Use lo siguiente para comprobar el estado de la implementación.

* Reemplace __SubscriptionID__ y __AccessToken__ por los valores usados anteriormente. 
* Reemplace __ResourceGroupName__ por el nombre del grupo de recursos que creó en la sección anterior.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Así devolverá un documento JSON que incluye información sobre la operación de implementación. El elemento `"provisioningState"` incluirá el estado de la implementación; si contiene un valor de `"Succeeded"`, la implementación se completó correctamente. En este momento, el clúster debe estar disponible para su uso.

##Pasos siguientes

Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster.

###Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

<!---HONumber=AcomDC_0518_2016-->