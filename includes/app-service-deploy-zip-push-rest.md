## <a name="rest"></a>Implementación mediante las API de REST 
 
Puede usar el [servicio de implementación de las API de REST](https://github.com/projectkudu/kudu/wiki/REST-API) para implementar el archivo .zip en su aplicación en Azure. Solo tiene que enviar una solicitud POST a https://<app_name>.scm.azurewebsites.net/api/zipdeploy. La solicitud POST debe contener el archivo .zip en el cuerpo del mensaje. Las credenciales de implementación de la aplicación se proporcionan en la solicitud mediante la autenticación de HTTP BASIC. Para obtener más información, consulte la [referencia de implementación para la inserción del archivo .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

En el ejemplo siguiente se usa la herramienta cURL para enviar una solicitud que contiene el archivo .zip. Puede ejecutar cURL desde el terminal en un equipo Mac o Linux, o mediante Bash en Windows. Reemplace el marcador de posición `<zip_file_path>` por la ruta de acceso a la ubicación del archivo .zip del proyecto. Reemplace también `<app_name>` por el nombre único de la aplicación.

Reemplace el marcador de posición `<deployment_user>` por el nombre de usuario de las credenciales de implementación. Cuando cURL se lo solicite, escriba la contraseña. Para obtener información sobre cómo establecer las credenciales de implementación de la aplicación, consulte [Establecimiento y restablecimiento de credenciales de nivel de usuario](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Esta solicitud desencadena la implementación de inserción desde el archivo .zip cargado. Puede revisar las implementaciones actuales y pasadas mediante el punto de conexión https://<app_name>.scm.azurewebsites.net/api/deployments, tal como se muestra en el siguiente ejemplo de cURL. Una vez más, reemplace `<app_name>` por el nombre de la aplicación y `<deployment_user>` por el nombre de usuario de las credenciales de implementación.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```