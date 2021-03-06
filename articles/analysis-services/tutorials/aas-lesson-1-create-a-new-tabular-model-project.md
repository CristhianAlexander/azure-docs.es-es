---
title: "Lección 1 del tutorial de Azure Analysis Services: Creación de un nuevo proyecto de modelo tabular| Microsoft Docs"
description: "Describe cómo crear un proyecto del tutorial de Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 40aac182af22d03c4cff535fd8c87b29ecae376a
ms.contentlocale: es-es
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-1-create-a-new-tabular-model-project"></a>Lección 1: Creación de un nuevo proyecto de modelo tabular

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

En esta lección usará la herramienta SQL Server Data Tools (SSDT) para crear un proyecto de modelo tabular en el nivel de compatibilidad 1400. Una vez creado el proyecto, puede empezar a agregar datos y a crear el modelo. En esta lección también se proporciona una breve introducción al entorno de creación de modelos tabulares de SSDT.  
  
Tiempo estimado para completar esta lección: **10 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema es la primera lección de un tutorial de creación de modelos tabulares. Para llevar a cabo esta lección, hay una serie de requisitos previos que debe cumplir. Para obtener más información, consulte [Azure Analysis Services: Tutorial de Adventure Works](../tutorials/aas-adventure-works-tutorial.md).  
  
## <a name="create-a-new-tabular-model-project"></a>Crear un proyecto de modelo tabular  
  
#### <a name="to-create-a-new-tabular-model-project"></a>Para crear un proyecto de modelo tabular  
  
1.  En SSDT, en el menú **Archivo**, haga clic en **Nuevo** > **Proyecto**.  
  
2.  En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalados** > **Business Intelligence** > **Analysis Services** y haga clic en **Proyecto tabular de Analysis Services**.  
  
3.  En **Nombre**, escriba **Ventas por Internet AW** y especifique una ubicación para los archivos del proyecto.  
  
    De forma predeterminada, el **Nombre de la solución** será el mismo que el del proyecto, aunque puede escribir uno distinto.  
  
4.  Haga clic en **Aceptar**.  
  
5.  En el cuadro de diálogo **Diseñador de modelos tabulares**, seleccione **Área de trabajo integrada**.  
  
    El área de trabajo hospeda una base de datos de modelos tabulares con el mismo nombre que el proyecto durante la creación del modelo. Por área de trabajo integrada se entiende que SSDT usa una instancia integrada, sin tener que instalar una instancia de servidor de Analysis Services independiente solo para la creación de modelos.
      
6.  En **nivel de compatibilidad**, seleccione **SQL Server 2017 / Azure Analysis Services (1400)**.   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    Si no ve SQL Server 2017 / Azure Analysis Services (1400) en el cuadro de lista Nivel de compatibilidad, significa que no está usando la versión más reciente de SQL Server Data Tools. Para obtener la versión más reciente, consulte [Install SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) (Instalar SQL Server Data Tools).  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>Descripción del entorno de creación de modelos tabulares de SSDT  
Ahora que ha creado un proyecto de modelo tabular, dedique un momento a examinar el entorno de creación de modelos tabulares de SSDT.  
  
Una vez creado el proyecto, se abrirá en SSDT. En el lado derecho, en **Explorador de modelos tabulares**, aparece una vista de árbol de los objetos presentes en el modelo. Como todavía no se han importado datos, las carpetas están vacías. Puede hacer clic con el botón derecho en una carpeta de objetos para llevar a cabo acciones, de manera similar a una barra de menús. Durante este tutorial se usa el Explorador de modelos tabulares para navegar por los distintos objetos del proyecto de modelo.

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

Haga clic en la pestaña **Explorador de soluciones**. Aquí se encuentra el archivo **Model.bim**. Si no ve la ventana del diseñador a la izquierda (la ventana vacía con la pestaña Model.bim), en el **Explorador de soluciones**, en el proyecto **Ventas por Internet AW**, haga doble clic en el archivo **Model.bim**. El archivo Model.bim contiene los metadatos del proyecto de modelo. 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
Haga clic en **Model.bim**. En la ventana **Propiedades** aparecen las propiedades del modelo; la más importante es la propiedad **Modo DirectQuery**. Esta propiedad especifica si el modelo se implementa en modo en memoria (desactivado) o en modo DirectQuery (activado). En este tutorial se crea y se implementa el modelo en modo en memoria.

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
Al crear un proyecto de modelo, algunas de las propiedades del modelo se establecen automáticamente según las opciones del modelado de datos que se pueden especificar en el menú **Herramientas** > cuadro de diálogo **Opciones**. Las propiedades Copia de seguridad de datos, Retención de área de trabajo y Servidor de área de trabajo especifican cómo y dónde se crea y se conserva en memoria la base de datos del área de trabajo (la base de datos de creación de modelos), y cómo y dónde se efectúa su copia de seguridad. Si es necesario, puede cambiar estas opciones más adelante, pero de momento las dejaremos tal como están.  

En el **Explorador de soluciones**, haga clic con el botón derecho en **Ventas por Internet AW** (proyecto) y haga clic en **Propiedades**. Aparecerá el cuadro de diálogo **Páginas de propiedades de Ventas por Internet AW**. Al implementar el modelo más adelante, se establecen algunas de estas propiedades.  
  
Al instalar SSDT se han agregado varios elementos de menú nuevos al entorno de Visual Studio. Haga clic en el menú **Model** (Modelo). Desde aquí puede importar datos, actualizar los datos del área de trabajo, examinar el modelo en Excel, crear perspectivas y roles, seleccionar la vista de modelo y establecer opciones de cálculo. Haga clic en el menú **Tabla**. Desde aquí puede crear y administrar las relaciones, especificar la configuración de las tablas de fecha, crear particiones y editar las propiedades de las tablas. Al hacer clic en el menú **Columna**, puede agregar y eliminar columnas de una tabla, inmovilizar columnas y especificar el criterio de ordenación. SSDT también agrega algunos botones a la barra. Más útil es la característica Autosuma, que sirve para crear una medida de agregación estándar para una columna seleccionada. Hay otros botones de la barra de herramientas que ofrecen un acceso rápido a las características y los comandos más usados.  
  
Explore algunos de los cuadros de diálogo y ubicaciones de distintas características específicas de la creación de modelos tabulares. Aunque algunos elementos aún no están activos, puede hacerse una idea del entorno de creación de modelos tabulares.  
  

## <a name="whats-next"></a>Pasos siguientes
[Lección 2: Obtención de datos](../tutorials/aas-lesson-2-get-data.md)

  
  
  

