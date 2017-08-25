## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Asociación de una cuenta de Almacenamiento de Azure al Centro de IoT

Como la aplicación de dispositivo simulado carga un archivo en un blob, debe tener una cuenta de [Azure Storage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) asociada a IoT Hub. Al asociar una cuenta de Azure Storage a un centro de IoT Hub, el centro genera un URI de SAS. Un dispositivo puede usar este URI de SAS para cargar de forma segura un archivo en un contenedor de blobs. El servicio Centro de IoT y los SDK de dispositivo coordinan el proceso que genera el URI de SAS y permite que esté disponible para un dispositivo para cargar un archivo.

Siga las instrucciones de [Configuración de cargas de archivos mediante Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md) para asociar una cuenta de Azure Storage al centro de IoT Hub. Asegúrese de que hay un contenedor de blobs asociado a su centro de IoT Hub y que las notificaciones de archivo están habilitadas.

![Habilitación de las notificaciones de archivo en el Portal](media/iot-hub-associate-storage/enable-file-notifications.png)