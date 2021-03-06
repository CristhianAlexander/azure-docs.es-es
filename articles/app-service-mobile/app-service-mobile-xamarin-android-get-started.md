---
title: "Introducción a Aplicaciones móviles de Azure para aplicaciones Xamarin.Android"
description: "Siga este tutorial para empezar a usar Aplicaciones móviles de Azure para el desarrollo de Xamarin Android."
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 4d34bb29df95ae83952d8f421f3f2a9118ad5e1d
ms.contentlocale: es-es
ms.lasthandoff: 01/20/2017

---
# <a name="create-a-xamarinandroid-app"></a>Creación de una aplicación Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Información general
En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación Xamarin.Android. Para obtener más información, consulte [¿Qué son las aplicaciones móviles?](app-service-mobile-value-prop.md)

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Completar este tutorial es un requisito previo para todos los tutoriales de aplicaciones móviles para aplicaciones Xamarin.Android.

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe cumplir los siguientes requisitos previos:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio con Xamarin. Consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx) para obtener instrucciones.

> [!NOTE]
> Si desea empezar a usar Azure App Service antes de suscribirse para obtener una cuenta de Azure, vaya a [Cree su aplicación de Azure App Service](https://azure.microsoft.com/try/app-service/mobile/).  Podrá crear inmediatamente una Mobile App de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
>
>

## <a name="create-an-azure-mobile-app-backend"></a>Creación de un nuevo back-end de Azure Mobile App
Siga estos pasos para crear un back-end de Mobile App.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Ahora ha aprovisionado un back-end de aplicación móvil de Azure que puede usarse por las aplicaciones del cliente móvil. Después, descargue un proyecto de servidor para un back-end de "lista de tareas" sencillo y publíquelo en Azure.

## <a name="configure-the-server-project"></a>Configuración del proyecto de servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Descarga y ejecución de la aplicación Xamarin.Android
1. En **Descargar y ejecutar el proyecto de Xamarin.Android**, haga clic en el botón **Descargar**.

      Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.
2. Presione la tecla **F5** para compilar el proyecto e iniciar la aplicación.
3. En la aplicación, escriba un texto significativo, como *Realizar el tutorial* y luego haga clic en el botón **Agregar**.

    ![][10]

    Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de la aplicación móvil devuelve los elementos almacenados en la tabla y los datos aparecen en la lista.

   > [!NOTE]
   > Puede revisar el código de acceso al back-end de aplicación móvil para consultar e insertar datos; este se encuentra en el archivo de C# ToDoActivity.cs.
   >
   >

## <a name="next-steps"></a>Pasos siguientes
* [Add Offline Sync to your app](app-service-mobile-xamarin-android-get-started-offline-data.md) (Incorporación de sincronización sin conexión a la aplicación)
* [Adición de la autenticación a la aplicación Xamarin.Android ](app-service-mobile-xamarin-android-get-started-users.md)
* [Agregar notificaciones push a la aplicación de Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)
* [Uso del cliente administrado para Aplicaciones móviles de Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203

