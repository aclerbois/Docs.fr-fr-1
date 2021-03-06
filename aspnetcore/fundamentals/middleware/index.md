---
title: Intergiciel (middleware) ASP.NET Core
author: rick-anderson
description: Découvrez le middleware ASP.NET Core et le pipeline de requête.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: fundamentals/middleware/index
ms.openlocfilehash: c55dbd5a9ac31f55daf1cb3146fb18b91b016919
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341587"
---
# <a name="aspnet-core-middleware"></a>Intergiciel (middleware) ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

Un middleware est un logiciel qui est assemblé dans un pipeline d’application pour gérer les requêtes et les réponses. Chaque composant :

* Choisit de passer la requête au composant suivant dans le pipeline.
* Peut travailler avant et après le composant suivant dans le pipeline.

Les délégués de requête sont utilisés pour créer le pipeline de requête. Les délégués de requête gèrent chaque requête HTTP.

Les délégués de requête sont configurés à l’aide des méthodes d’extension <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> et <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Chaque délégué de requête peut être spécifié inline comme méthode anonyme (appelée intergiciel inline) ou peut être défini dans une classe réutilisable. Ces classes réutilisables et les méthodes anonymes inline sont des *middlewares*, également appelés *composants de middleware*. Chaque composant de middleware du pipeline de requête est chargé d’appeler le composant suivant du pipeline ou de court-circuiter le pipeline.

<xref:migration/http-modules> explique la différence entre les pipelines de requêtes dans ASP.NET Core et ASP.NET 4.x, puis fournit d’autres exemples de middlewares.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Créer un pipeline de middleware avec IApplicationBuilder

Le pipeline de requête ASP.NET Core est composé d’une séquence de délégués de requête, appelés l’un après l’autre. Le diagramme suivant illustre le concept. Le thread d’exécution suit les flèches noires.

![Modèle de traitement des requêtes montrant une requête qui arrive et qui est traitée par trois middlewares, puis la réponse qui quitte l’application. Chaque intergiciel exécute sa logique et transmet la requête à l’intergiciel suivant à l’instruction next(). Une fois que le troisième middleware a traité la requête, celle-ci repasse par les deux middlewares précédents dans l’ordre inverse pour être à nouveau traitée après ses instructions next(), avant de quitter l’application sous forme de réponse au client.](index/_static/request-delegate-pipeline.png)

Chaque délégué peut effectuer des opérations avant et après le délégué suivant. Un délégué peut également décider de ne pas passer une requête au délégué suivant. On parle alors de *court-circuit du pipeline de requête*. Un court-circuit est souvent souhaitable car il évite le travail inutile. Par exemple, le middleware Fichier statique peut retourner une requête pour un fichier statique et court-circuiter le reste du pipeline. Les délégués de gestion des exceptions sont appelés tôt dans le pipeline pour qu’ils puissent intercepter les exceptions qui se produisent dans les phases ultérieures du pipeline.

L’application ASP.NET Core la plus simple possible définit un seul délégué de requête qui gère toutes les requêtes. Ce cas ne fait pas appel à un pipeline de requête réel. À la place, une seule fonction anonyme est appelée en réponse à chaque requête HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

Le premier délégué <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> termine le pipeline.

Chaînez plusieurs délégués de requête avec <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Le paramètre `next` représente le délégué suivant dans le pipeline. Vous pouvez court-circuiter le pipeline en n’appelant *pas* le paramètre *next*. Vous pouvez généralement effectuer des actions à la fois avant et après le délégué suivant, comme illustré dans cet exemple :

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

> [!WARNING]
> N’appelez pas `next.Invoke` une fois que la réponse a été envoyée au client. Les changements apportés à <xref:Microsoft.AspNetCore.Http.HttpResponse> après le démarrage de la réponse lèvent une exception. Par exemple, les changements comme la définition des en-têtes et du code d’état lèvent une exception. Écrire dans le corps de la réponse après avoir appelé `next` :
>
> * Peut entraîner une violation de protocole. Par exemple, écrire plus que le `Content-Length` indiqué.
> * Peut altérer le format du corps. Par exemple, l’écriture d’un pied de page HTML dans un fichier CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> est un indice utile pour indiquer si les en-têtes ont été envoyés ou si le corps a fait l’objet d’écritures.

## <a name="order"></a>Trier

L’ordre dans lequel les composants de middleware sont ajoutés dans la méthode `Startup.Configure` définit l’ordre dans lequel ils sont appelés sur les requêtes et l’ordre inverse pour la réponse. L’ordre est critique pour la sécurité, les performances et le fonctionnement.

La méthode `Startup.Configure` suivante ajoute des composants middleware utiles pour les scénarios d’application courants :

::: moniker range=">= aspnetcore-2.0"

1. Gestion des erreurs/exceptions
1. Protocole HSTS (HTTP Strict Transport Security)
1. Redirection HTTPS
1. Serveur de fichiers statiques
1. Application d’une stratégie de cookies
1. Authentification
1. Session
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. Gestion des erreurs/exceptions
1. Fichiers statiques
1. Authentification
1. Session
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

Dans l’exemple de code précédent, chaque méthode d’extension d’intergiciel (middleware) est exposée sur <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> à travers l’espace de noms <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> est le premier composant d’intergiciel ajouté au pipeline. Par conséquent, le middleware Gestion des exceptions intercepte toutes les exceptions qui se produisent dans les appels ultérieurs.

Le middleware Fichier statique est appelé tôt dans le pipeline pour qu’il puisse gérer les requêtes et procéder au court-circuit sans passer par les composants restants. Le middleware Fichier statique ne fournit **aucune** vérification d’autorisation. Tous les fichiers qu’il sert, notamment ceux sous *wwwroot* sont accessibles à tous. Pour obtenir une approche permettant de sécuriser les fichiers statiques, consultez <xref:fundamentals/static-files>.

::: moniker range=">= aspnetcore-2.0"

Si la requête n’est pas gérée par le middleware Fichier statique, elle est transmise au middleware Authentification (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), qui effectue l’authentification. Le middleware Authentification ne court-circuite pas les requêtes non authentifiées. Même s’il authentifie les requêtes, l’autorisation (et le refus) interviennent uniquement après que MVC a sélectionné une page Razor/un contrôleur MVC et une action spécifiques.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Si la requête n’est pas gérée par le middleware Fichier statique, elle est transmise au middleware Identité (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), qui effectue l’authentification. L’intergiciel Identité ne court-circuite pas les requêtes non authentifiées. Même s’il authentifie les requêtes, l’autorisation (et le refus) interviennent uniquement après que MVC a sélectionné un contrôleur et une action spécifiques.

::: moniker-end

L’exemple suivant montre un ordre de middlewares où les requêtes pour les fichiers statiques sont gérées par le middleware Fichier statique avant le middleware Compression de la réponse. Les fichiers statiques ne sont pas compressés avec cet ordre de middlewares. Les réponses MVC de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> peuvent être compressées.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a>Use, Run et Map

Configurez le pipeline HTTP avec `Use`, `Run` et `Map`. La méthode `Use` peut court-circuiter le pipeline (autrement dit, si elle n’appelle pas un délégué de requête `next`). `Run` est une convention, et certains composants d’intergiciel peuvent exposer des méthodes `Run[Middleware]` qui s’exécutent à la fin du pipeline.

Les extensions <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> sont utilisées comme convention pour créer une branche dans le pipeline. `Map*` crée une branche dans le pipeline de requête en fonction des correspondances du chemin de requête donné. Si le chemin de requête commence par le chemin donné, la branche est exécutée.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent.

| Demande             | Réponse                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Quand `Map` est utilisé, les segments de chemin mis en correspondance sont supprimés de `HttpRequest.Path` et ajoutés à `HttpRequest.PathBase` pour chaque requête.

[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) crée une branche dans le pipeline de requête en fonction du résultat du prédicat donné. Un prédicat de type `Func<HttpContext, bool>` peut être utilisé pour mapper les requêtes à une nouvelle branche du pipeline. Dans l’exemple suivant, un prédicat est utilisé pour détecter la présence d’une variable de chaîne de requête `branch` :

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

Le tableau suivant présente les requêtes et les réponses de `http://localhost:1234` avec le code précédent.

| Demande                       | Réponse                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

`Map` prend en charge l’imbrication, par exemple :

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

`Map` peut également faire correspondre plusieurs segments à la fois :

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>Intergiciels (middleware) intégrés

ASP.NET Core est fourni avec les composants de middleware suivant. La colonne *Ordre* fournit des notes sur l’emplacement du middleware dans le pipeline de requête et sur les conditions dans lesquelles le middleware peut mettre fin à la requête et empêcher les autres middlewares de traiter une requête.

| Intergiciel (middleware) | Description | Trier |
| ---------- | ----------- | ----- |
| [Authentification](xref:security/authentication/identity) | Prend en charge l’authentification. | Avant que `HttpContext.User` ne soit nécessaire. Terminal pour les rappels OAuth. |
| [Stratégie de cookies](xref:security/gdpr) | Effectue le suivi de consentement des utilisateurs pour le stockage des informations personnelles et applique des normes minimales pour les champs de cookie, comme `secure` et `SameSite`. | Avant le middleware qui émet les cookies. Exemples : authentification, session, MVC (TempData). |
| [CORS](xref:security/cors) | Configure le partage des ressources cross-origin (CORS). | Avant les composants qui utilisent CORS. |
| [Diagnostics](xref:fundamentals/error-handling) | Configure les diagnostics. | Avant les composants qui génèrent des erreurs. |
| [En-têtes transférés](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | Transfère les en-têtes en proxy vers la requête actuelle. | Avant les composants qui consomment les champs mis à jour. Exemples : schéma, hôte, IP du client, méthode. |
| [Contrôle d’intégrité](xref:host-and-deploy/health-checks) | Contrôle l’intégrité d’une application ASP.NET Core et de ses dépendances, notamment la disponibilité de la base de données. | Terminal si une requête correspond à un point de terminaison de contrôle d’intégrité. |
| [Remplacement de la méthode HTTP](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | Autorise une requête POST entrante à remplacer la méthode. | Avant les composants qui consomment la méthode mise à jour. |
| [Redirection HTTPS](xref:security/enforcing-ssl#require-https) | Redirige toutes les requêtes HTTP vers HTTPS (ASP.NET Core 2.1 ou ultérieur). | Avant les composants qui consomment l’URL. |
| [HSTS (HTTP Strict Transport Security)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware d’amélioration de la sécurité qui ajoute un en-tête de réponse spécial (ASP.NET Core 2.1 ou ultérieur). | Avant l’envoi des réponses et après les composants qui modifient les requêtes. Exemples : en-têtes transférés, réécriture d’URL. |
| [MVC](xref:mvc/overview) | Traite les requêtes avec MVC/Razor Pages (ASP.NET Core 2.0 ou version ultérieure). | Terminal si une requête correspond à un itinéraire. |
| [OWIN](xref:fundamentals/owin) | Interopérabilité avec le middleware, les serveurs et les applications OWIN. | Terminal si le middleware OWIN traite entièrement la requête. |
| [Mise en cache des réponses](xref:performance/caching/middleware) | Prend en charge la mise en cache des réponses. | Avant les composants qui nécessitent la mise en cache. |
| [Compression des réponses](xref:performance/response-compression) | Prend en charge la compression des réponses. | Avant les composants qui nécessitent la compression. |
| [Localisation des requêtes](xref:fundamentals/localization) | Prend en charge la localisation. | Avant la localisation des composants sensibles. |
| [Le routage](xref:fundamentals/routing) | Définit et contraint des routes de requête. | Terminal pour les routes correspondantes. |
| [Session](xref:fundamentals/app-state) | Prend en charge la gestion des sessions utilisateur. | Avant les composants qui nécessitent la session. |
| [Fichiers statiques](xref:fundamentals/static-files) | Prend en charge le traitement des fichiers statiques et l’exploration des répertoires. | Terminal si une requête correspond à un fichier. |
| [Réécriture d’URL](xref:fundamentals/url-rewriting) | Prend en charge la réécriture d’URL et la redirection des requêtes. | Avant les composants qui consomment l’URL. |
| [WebSockets](xref:fundamentals/websockets) | Autorise le protocole WebSockets. | Avant les composants qui sont nécessaires pour accepter les requêtes WebSocket. |

## <a name="write-middleware"></a>Middleware Écriture

Les intergiciels sont généralement encapsulés dans une classe et exposés avec une méthode d’extension. Prenez en compte le middleware suivant, qui définit la culture de la requête actuelle à partir d’une chaîne de requête :

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

L’exemple de code précédent est utilisé pour illustrer la création d’un composant de middleware. Pour plus d’informations sur la prise en charge intégrée de la localisation par ASP.NET Core, consultez <xref:fundamentals/localization>.

Vous pouvez tester l’intergiciel en passant la culture, par exemple `http://localhost:7997/?culture=no`.

Le code suivant déplace le délégué de l’intergiciel dans une classe :

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

Le nom de la méthode `Task` du middleware doit être `Invoke`. Dans ASP.NET Core 2.0 ou ultérieur, le nom peut être `Invoke` ou `InvokeAsync`.

::: moniker-end

La méthode d’extension suivante expose le middleware par le biais de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

Le code suivant appelle l’intergiciel à partir de `Startup.Configure` :

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

L’intergiciel doit suivre le [principe de dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) en exposant ses dépendances dans son constructeur. L’intergiciel est construit une fois par *durée de vie d’application*. Consultez la section [Dépendances par requête](#per-request-dependencies) si vous avez besoin de partager des services avec un middleware au sein d’une requête.

Les composants de middleware peuvent résoudre leurs dépendances à partir de l’[injection de dépendances](xref:fundamentals/dependency-injection) à l’aide des paramètres du constructeur. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) peut également accepter des paramètres supplémentaires directement.

### <a name="per-request-dependencies"></a>Dépendances par requête

Étant donné que le middleware est construit au démarrage de l’application, et non par requête, les services de durée de vie *délimités* utilisés par les constructeurs du middleware ne sont pas partagés avec d’autres types injectés par des dépendances lors de chaque requête. Si vous devez partager un service *délimité* entre votre intergiciel et d’autres types, ajoutez-le à la signature de la méthode `Invoke`. La méthode `Invoke` peut accepter des paramètres supplémentaires qui sont renseignés par injection de dépendances :

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
