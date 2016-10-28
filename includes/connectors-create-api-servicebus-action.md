Ahora que ha agregado un desencadenador, es hora de hacer algo interesante con los datos que genera. Siga estos pasos para agregar una acción **Service Bus - Send message** (Bus de servicio: enviar mensaje). Esta acción enviará un mensaje al Bus de servicio.

Siga estos pasos para crear la acción envío del mensaje:  

1. Seleccione **+ New step** (+ Nuevo paso) para agregar la acción.  
- Seleccione el vínculo **Add an action** (Agregar una acción). Se abrirá el cuadro de búsqueda en el que podrá buscar cualquier acción que quiera realizar. En este ejemplo, nos interesan las acciones del Bus de servicio.    
![Imagen 1 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-1.png)   
- Escriba *bus de servicio* para buscar las acciones relacionadas con el Bus de servicio.
- Seleccione **Service Bus - Send message** (Bus de servicio: enviar mensaje). Esta es la acción que se realizará.  
![Imagen 2 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-2.png)   
- Escriba el contenido del mensaje. Este es un paso necesario.  
- Escriba el nombre de la cola o del tema a los que se enviará el mensaje. Este paso también es necesario.  
- Opcionalmente, puede proporcionar otros detalles sobre el mensaje.       
![Imagen 3 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-3.png)   
- Guarde los cambios en el flujo de trabajo.    
![Imagen 4 de acción del Bus de servicio](./media/connectors-create-api-servicebus/action-4.png)      
  
  

<!---HONumber=AcomDC_0810_2016-->
