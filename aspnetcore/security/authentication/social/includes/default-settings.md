---
ms.openlocfilehash: a7be92adffc06ac0f25b84ea15d1a8c4896f4f9d
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54397076"
---
<!--Don't update this for 2.2, use the 2.2 version --> L’appel à [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity) configure les paramètres de schéma par défaut. Le [AddAuthentication(String)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_String_) surcharger définit le [DefaultScheme](/dotnet/api/microsoft.aspnetcore.authentication.authenticationoptions.defaultscheme) propriété. Le [AddAuthentication (Action&lt;AuthenticationOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) surcharge permet de configurer les options d’authentification, qui peuvent être utilisées pour définir des schémas d’authentification par défaut à des fins différentes. Les appels suivants à `AddAuthentication` remplacement configuré précédemment [AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions) propriétés.

[AuthenticationBuilder](/dotnet/api/microsoft.aspnetcore.authentication.authenticationbuilder) les méthodes d’extension qui inscrivent un gestionnaire d’authentification peuvent être appelées uniquement une fois par le schéma d’authentification. Il existe des surcharges qui permettent de configurer les propriétés de schéma, le nom du schéma d’et le nom complet.
