

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)y, luego, haga clic en **+NUEVO** en la parte superior izquierda de la pantalla.
2. Haga clic en **Nuevo** y después en **Web y móvil**. Desplácese hacia abajo si es necesario y haga clic en **Centro de notificaciones**.
   
      ![Portal de Azure: Creación de centros de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Asegúrese de especificar un nombre único en el campo **Centro de notificaciones** . Seleccione el elemento **región**, **suscripción** y **grupo de recursos** (si dispone ya de uno). 
   
    Si ya tiene un espacio de nombres de Service Bus en el que desee crear el centro, selecciónelo mediante la opción **Seleccionar existente** del campo **Espacio de nombres**.  De lo contrario, puede utilizar el nombre predeterminado que se creará según el nombre del centro siempre que el nombre del espacio de nombres esté disponible. 
   
    Cuando esté listo, haga clic en **Crear**.
   
      ![Portal de Azure: Establecimiento de las propiedades del centro de notificación](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Una vez creados el centro de notificaciones y el espacio de nombres, se le llevará a la página del portal correspondiente. 
   
      ![Portal de Azure: Página del portal del centro de notificación](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)
5. Haga clic en **Configuración** y, después, en **Directivas de acceso**; anote las dos cadenas de conexión que están disponibles para usted, ya que las necesitará para controlar las notificaciones push más adelante.
   
      ![Portal de Azure: Cadenas de conexión del centro de notificación](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)



<!--HONumber=Dec16_HO1-->


