## <a name="create-a-self-hosted-ir"></a>Creación de una instancia de IR autohospedada

En esta sección, creará una instancia de Integration Runtime autohospedado y la asociará a una máquina local con la base de datos de SQL Server. Integration Runtime autohospedado es el componente que copia los datos de SQL Server de la máquina a Azure Blob Storage. 

1. Cree una variable para el nombre del runtime de integración. Utilice un nombre único y anótelo. Lo usará más adelante en este tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Cree una instancia de Integration Runtime autohospedada. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Este es la salida de ejemplo:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Ejecute el comando siguiente para recuperar el estado de la instancia de Integration Runtime creada. Confirme que el valor de la propiedad **Estado** está establecida en **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Este es la salida de ejemplo:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Ejecute el siguiente comando para recuperar las **claves de autenticación** para registrar la instancia de Integration Runtime autohospedado con el servicio Data Factory en la nube. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Este es la salida de ejemplo:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Copie una de las claves (excluya las comillas dobles) para registrar la instancia de Integration Runtime autohospedado que instalará en el equipo en el paso siguiente.  

## <a name="install-integration-runtime"></a>Instalación de Integration Runtime
1. Si ya tiene **Microsoft Integration Runtime** en su máquina, desinstálela utilizando **Agregar o quitar programas**. 
2. [Descargue](https://www.microsoft.com/download/details.aspx?id=39717) la versión de Integration Runtime autohospedado en un equipo Windows local y ejecute la instalación. 
3. En la **página de bienvenida del Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Siguiente**.  
4. En la página del **Contrato de licencia para el usuario final**, acepte los términos del Contrato de licencia y haga clic en **Siguiente**. 
5. En la página **Carpeta de destino**, haga clic en **Siguiente**. 
6. En **Preparado para instalar Microsoft Integration Runtime**, haga clic en **Instalar**. 
7. Si aparece un mensaje de advertencia que indica que el equipo está configurado para entrar en modo de suspensión o hibernación si deja de usarse, haga clic en **Aceptar**. 
8. Si ve la ventana **Opciones de energía**, ciérrela y cambie a la ventana de instalación. 
9. En la página **Ha completado el Asistente para la instalación de Microsoft Integration Runtime**, haga clic en **Finalizar**.
10. En la página **Registro de Integration Runtime (autohospedado)**, pegue la clave guardada en la sección anterior y haga clic en **Registrar**. 

   ![Registro de Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. Verá el siguiente mensaje cuando la instancia de Integration Runtime autohospedada se haya registrado correctamente:

   ![Se registró correctamente](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. En la página **Nuevo nodo de Integration Runtime (autohospedado)**, haga clic en **Siguiente**. 

    ![Página Nuevo nodo de Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. En **Canal de comunicaciones de intranet**, haga clic en **Omitir**. Puede seleccionar una certificación TLS/SSL para proteger la comunicación entre nodos en un entorno de Integration Runtime con varios nodos. 

    ![Página Canal de comunicaciones de intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. En la página **Registro de Integration Runtime (autohospedado)**, haga clic en 
**Iniciar Configuration Manager**. 
6. Verá la siguiente página cuando el nodo esté conectado al servicio en la nube:

   ![El nodo está conectado](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. Ahora, pruebe la conectividad a la base de datos de SQL Server.

    ![Ficha Diagnóstico](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - En la ventana **Configuration Manager**, cambie a la pestaña **Diagnósticos**.
    - Seleccione **SqlServer** como **Tipo de origen de datos**.
    - Escriba el **nombre del servidor**.
    - Escriba el **nombre de la base de datos**. 
    - Seleccione el **modo de autenticación**. 
    - Escriba el **nombre de usuario**. 
    - Escriba la **contraseña** del nombre de usuario.
    - Haga clic en **Probar** para confirmar que el entorno de tiempo de ejecución de la integración puede conectarse a SQL Server. Verá una marca de verificación verde si la conexión es correcta. De lo contrario, verá un mensaje de error asociado al error. Solucione los problemas y asegúrese de que Integration Runtime puede conectarse a SQL Server.    

    > [!NOTE]
    > Anote estos valores (tipo de autenticación, servidor, base de datos, usuario, contraseña). Los usará más adelante en este tutorial. 
    
