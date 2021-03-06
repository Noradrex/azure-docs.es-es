

1. En Visual Studio, abra el proyecto que ha modificado al completar el tutorial **Introducción a los datos**.
2. Presione la tecla **F5** para ejecutar la aplicación y, a continuación, escriba texto en **Insertar TodoItem** y haga clic en **Guardar**.
3. Repita el paso anterior al menos tres veces de manera que disponga de más de tres elementos almacenados en la tabla TodoItem.
4. En el archivo MainPage.xaml.cs, reemplace el método **RefreshTodoItems** por el siguiente código:
   
        private async void RefreshTodoItems()
        {
            // Define a filtered query that returns the top 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                            .Where(todoItem => todoItem.Complete == false)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }
   
      Cuando se ejecuta durante el enlace de datos, esta consulta devuelve los tres elementos principales que no están marcados como completados.
5. Presione la tecla **F5** para ejecutar la aplicación.
   
      Observe que solo se muestran los tres primeros resultados de la tabla TodoItem.
6. (Opcional) Vea el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler].
   
       Observe que el método `Take(3)` se ha traducido en la opción de consulta `$top=3` en el URI de la consulta.
7. Actualice el método **RefreshTodoItems** una vez más con el código siguiente:
   
        private async void RefreshTodoItems()
        {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            IMobileServiceTableQuery<TodoItem> query = todoTable
                           .Where(todoItem => todoItem.Complete == false)
                           .Skip(3)
                           .Take(3);
            items = await query.ToCollectionAsync();
            ListItems.ItemsSource = items;
        }
   
       Esta consulta omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.
   
   > [!NOTE]
   > En este tutorial se usa un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos <strong>Take</strong> y <strong>Skip</strong>. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores. También puede llamar al método <strong>IncludeTotalCount</strong> para obtener el recuento total de elementos disponibles en el servidor, junto con los datos paginados.
   > 
   > 
8. (Opcional) Vea de nuevo el URI de la solicitud enviada al servicio móvil.
   
       Observe que el método `Skip(3)` se ha traducido en la opción de consulta `$skip=3` en el URI de la consulta.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

<!---HONumber=Oct15_HO3-->