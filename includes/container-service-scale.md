Después de [implementar un clúster de Azure Container Service](../articles/container-service/dcos-swarm/container-service-deployment.md), tendrá que cambiar el número de nodos de agente. Por ejemplo, puede que necesite más agentes para poder ejecutar más aplicaciones o instancias de contenedor. 

Puede cambiar el número de nodos de agente en un clúster DC/OS, Docker Swarm o Kubernete mediante Azure Portal o la CLI de Azure 2.0. 

## <a name="scale-with-the-azure-portal"></a>Escalado con el portal de Azure

1. En el [portal de Azure](https://portal.azure.com), busque **servicios de contenedores** y, luego, haga clic en el servicio de contenedores que quiere modificar.
2. En la hoja **Container service** (Servicio de contenedores), haga clic en **Agentes**.
3. En **VM Count** (Recuento de VM), escriba el número deseado de nodos de agente.

    ![Escalado de un grupo en el portal](./media/container-service-scale/container-service-scale-portal.png)

4. Para guardar la configuración, haga clic en **Save** (Guardar).

## <a name="scale-with-the-azure-cli-20"></a>Escalado con la CLI de Azure 2.0

Asegúrese de que [ha instalado](/cli/azure/install-az-cli2) la CLI de Azure 2.0 más reciente y que ha iniciado sesión en una cuenta de Azure (`az login`).

### <a name="see-the-current-agent-count"></a>Consulta del recuento actual de agentes
Para ver el número de agentes actualmente en el clúster, ejecute el comando `az acs show`. Esta acción muestra la configuración del clúster. Por ejemplo, el comando siguiente muestra la configuración del servicio de contenedores llamada `containerservice-myACSName` en el grupo de recursos `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

El comando devuelve el número de agentes en el valor `Count` bajo `AgentPoolProfiles`.

### <a name="use-the-az-acs-scale-command"></a>Uso del comando az acs scale
Para cambiar el números de nodos de agente, ejecute el comando `az acs scale` y proporcione el **grupo de recursos**, el **nombre del servicio de contenedores** y el **nuevo recuento de agentes** deseado. Con un número mayor o menor, puede reducir o aumentar verticalmente, respectivamente.

Por ejemplo, para cambiar el número de agentes en el clúster anterior a 10, escriba el siguiente comando:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

La CLI de Azure 2.0 devuelve una cadena JSON que representa la nueva configuración del servicio de contenedor, incluido el nuevo recuento de agentes.

Para obtener más opciones de comando, ejecute `az acs scale --help`.

## <a name="scaling-considerations"></a>Consideraciones sobre escalado

* El número de nodos de agente debe estar entre 1 y 100, ambos inclusive. 

* Su cuota de núcleos puede limitar el número de nodos de agente en un clúster.

* Las operaciones de escalado de nodos de agente se aplican a un conjunto de escalado de máquinas virtuales de Azure que contiene el grupo de agentes. En un clúster de DC/OS, solo los nodos de agente del grupo privado se escalan mediante las operaciones mostradas en este artículo.

* En función del orquestador que implemente en el clúster, puede escalar por separado el número de instancias de un contenedor que se ejecute en el clúster. Por ejemplo, en un clúster de DC/OS, use la [interfaz de usuario de Marathon](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) para cambiar el número de instancias de una aplicación de contenedor.

* Actualmente, no se admite el escalado automático de nodos de agente en un clúster de servicio de contenedores.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [más ejemplos](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) de uso de comandos de la CLI de Azure 2.0 con Azure Container Service.
* Aprenda más sobre los [grupos de agentes de DC/OS](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) en Azure Container Service.

