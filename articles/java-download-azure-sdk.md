---
title: Descarga del SDK de Azure para Java
description: "Obtenga información acerca de cómo descargar el SDK de Azure para Java, con el código de ejemplo proporcionado para proyectos de Maven y pasos de instalación básica para el kit de herramientas de Azure para Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 25b7e591172ea810ce370f946eb46258c801ba36


---
# <a name="download-the-azure-sdk-for-java"></a>Descarga del SDK de Azure para Java
Este artículo contiene instrucciones para descargar e instalar las bibliotecas de Azure para Java.

**Nota:** Las bibliotecas de Azure para Java se distribuyen bajo la [licencia de Apache, versión 2.0][license].

## <a name="azure-libraries-for-java---manual-download"></a>Bibliotecas de Azure para Java: descarga manual
Para instalar manualmente las bibliotecas de Azure para Java, haga clic en <http://go.microsoft.com/fwlink/?LinkId=690320> para descargar un archivo ZIP que contenga todas las bibliotecas y todas sus dependencias.

Una vez haya descargado el archivo zip en su equipo, extraiga el contenido y use una de las opciones siguientes para agregar los archivos JAR a su proyecto:

* Importe los archivos JAR a su proyecto de Java en Eclipse.
* Configure la **ruta de acceso de compilación** para el proyecto de Java en Eclipse para incluir la ruta de acceso a los archivos JAR.

Para obtener información detallada acerca de cómo configurar las rutas de acceso de compilación en Eclipse, consulte el artículo [Ruta de acceso de compilación de Java] en el sitio web de Eclipse.

**Nota:** Consulte los archivos license.txt y ThirdPartyNotices.txt que se encuentran dentro del ZIP para obtener información sobre la licencia y sobre otras cuestiones.

## <a name="azure-libraries-for-java---building-with-maven"></a>Bibliotecas de Azure para Java: compilación con Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Paso 1: configurar el proyecto para usar Maven para la compilación
Para crear proyectos Maven en Eclipse que usan las bibliotecas de Azure para Java, siga las instrucciones del artículo [Getting Started with Azure Management Libraries for Java][maven-getting-started] (Introducción a las bibliotecas de administración de Azure para Java). 

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Paso 2: establecer la configuración de Maven con las dependencias necesarias
Una vez que el proyecto se haya configurado para usar Maven para la compilación, puede agregar el las dependencias necesarias al archivo pom.xml mediante una sintaxis similar a la del ejemplo siguiente. Tenga en cuenta que no es necesario agregar todas las dependencias que se muestran en el ejemplo siguiente; solo necesitará agregar las dependencias específicas que requiere el proyecto.

> [!NOTE]
> En cada elemento `<version>` del ejemplo anterior, reemplace los marcadores de posición "n.n.n" de este ejemplo por números de versión válidos, que pueden obtenerse del [repositorio de bibliotecas de Azure en Maven].
> 
> 

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="installing-the-azure-toolkit-for-eclipse"></a>Instalación del Kit de herramientas de Azure para Eclipse
Esta sección contiene instrucciones básicas para instalar el Kit de herramientas de Azure para Eclipse; para obtener instrucciones detalladas, consulte [Instalación del Kit de herramientas de Azure para Eclipse].

### <a name="prerequisites"></a>Requisitos previos
1. Sistemas operativos Windows que se muestran en el artículo [Novedades del kit de herramientas de Azure para Eclipse] .
2. Sistemas operativos Macintosh o Linux que se muestran en el artículo [Novedades del kit de herramientas de Azure para Eclipse] .
3. Eclipse IDE para Java EE Developers, Indigo o superior. Se puede descargar en <http://www.eclipse.org/downloads/>.

### <a name="basic-installation-steps"></a>Pasos de instalación básica
1. En Eclipse, en el menú **Help** (Ayuda), seleccione **Install New Software** (Instalar nuevo software).
2. Especifique la ubicación del sitio <http://dl.microsoft.com/eclipse> y presione **Entrar**.
3. Seleccione los elementos que se vayan a instalar y haga clic en **Finish**(Finalizar).

El kit de herramientas de Azure para Eclipse emplea la versión más reciente del SDK de Azure. Puede descargarlo mediante el Instalador de plataforma web (WebPI) en <http://go.microsoft.com/fwlink/?LinkID=252838>. Sin embargo, si no lo tiene instalado, cuando cree su primer proyecto de implementación de Azure, el kit de herramientas de Azure instalará automáticamente la versión adecuada del SDK de Azure.

## <a name="see-also"></a>Otras referencias
[Kit de herramientas de Azure para Eclipse]

[Instalación del Kit de herramientas de Azure para Eclipse] 

[Creación de una aplicación Hola a todos para Azure en Eclipse]

Para más información sobre el uso de Azure con Java, vea el [Centro de desarrolladores de Java de Azure].

<!-- URL List -->

[Centro de desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[repositorio de bibliotecas de Azure en Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creación de una aplicación Hola a todos para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalación del Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Ruta de acceso de compilación de Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Novedades del kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333



<!--HONumber=Nov16_HO3-->


