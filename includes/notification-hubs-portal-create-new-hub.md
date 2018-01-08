

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Seleccione **Nuevo** > **Web y móvil** > **Centro de notificaciones**.
   
      ![Azure Portal: creación de centros de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. En el cuadro **Centro de notificaciones**, escriba un nombre único. Seleccione el elemento **Región**, **Suscripción** y **Grupo de recursos** (si dispone ya de uno). 
   
      Si aún no tiene un espacio de nombres de Service Bus, puede usar el predeterminado, que se crea en función del nombre del centro (si está disponible el espacio de nombres).
    
      Si ya tiene un espacio de nombres de Service Bus donde crear el centro, siga estos pasos:

    a. En el área **Espacio de nombres**, seleccione el vínculo **Seleccionar uno existente**. 
   
    b. Seleccione **Crear**.
   
      ![Azure Portal: establecimiento de las propiedades del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Después de crear el espacio de nombres y el centro de notificaciones, seleccione **Todos los recursos** y, a continuación, seleccione el centro de notificaciones creado en la lista para abrirlo. 
   
      ![Azure Portal: página del portal del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-resources.png)

5. Seleccione **Directivas de acceso** en la lista. Anote las dos cadenas de conexión disponibles. Las necesitará para gestionar las notificaciones push más tarde.

      >[!IMPORTANT]
      >**NO** use la clave DefaultFullSharedAccessSignature en su aplicación. Está pensada para usarse solamente en el back-end.
      >
   
      ![Azure Portal: cadenas de conexión del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

