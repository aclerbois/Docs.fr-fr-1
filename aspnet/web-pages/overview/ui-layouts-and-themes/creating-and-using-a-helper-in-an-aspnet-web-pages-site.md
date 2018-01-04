---
uid: web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
title: "Création et à l’aide d’un programme d’assistance dans ASP.NET Web Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Cet article décrit comment créer un programme d’assistance dans un site Web ASP.NET Web Pages (Razor). Un programme d’assistance est un composant réutilisable qui inclut le code et le balisage perf..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: 46bff772-01e0-40f0-9ae6-9e18c5442ee6
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 5d0c1ae09d8fbc91ff76cd4045d439abafee7736
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="creating-and-using-a-helper-in-an-aspnet-web-pages-razor-site"></a><span data-ttu-id="a3ded-104">Création et utilisation d’un programme d’assistance dans un Site de Web Pages (Razor) ASP.NET</span><span class="sxs-lookup"><span data-stu-id="a3ded-104">Creating and Using a Helper in an ASP.NET Web Pages (Razor) Site</span></span>
====================
<span data-ttu-id="a3ded-105">par [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="a3ded-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="a3ded-106">Cet article décrit comment créer un programme d’assistance dans un site Web ASP.NET Web Pages (Razor).</span><span class="sxs-lookup"><span data-stu-id="a3ded-106">This article describes how to create a helper in an ASP.NET Web Pages (Razor) website.</span></span> <span data-ttu-id="a3ded-107">A *assistance* est un composant réutilisable qui inclut le code et le balisage pour effectuer une tâche qui peut être fastidieux ou complexe.</span><span class="sxs-lookup"><span data-stu-id="a3ded-107">A *helper* is a reusable component that includes code and markup to perform a task that might be tedious or complex.</span></span>
> 
> <span data-ttu-id="a3ded-108">**Ce que vous allez apprendre :**</span><span class="sxs-lookup"><span data-stu-id="a3ded-108">**What you'll learn:**</span></span> 
> 
> - <span data-ttu-id="a3ded-109">Comment créer et utiliser un programme d’assistance simple.</span><span class="sxs-lookup"><span data-stu-id="a3ded-109">How to create and use a simple helper.</span></span>
> 
> <span data-ttu-id="a3ded-110">Voici les fonctionnalités d’ASP.NET introduites dans l’article :</span><span class="sxs-lookup"><span data-stu-id="a3ded-110">These are the ASP.NET features introduced in the article:</span></span>
> 
> - <span data-ttu-id="a3ded-111">Le `@helper` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="a3ded-111">The `@helper` syntax.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="a3ded-112">Versions du logiciel utilisées dans le didacticiel</span><span class="sxs-lookup"><span data-stu-id="a3ded-112">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="a3ded-113">Pages Web ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="a3ded-113">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="a3ded-114">Ce didacticiel fonctionne également avec ASP.NET Web Pages 2.</span><span class="sxs-lookup"><span data-stu-id="a3ded-114">This tutorial also works with ASP.NET Web Pages 2.</span></span>


## <a name="overview-of-helpers"></a><span data-ttu-id="a3ded-115">Vue d’ensemble des applications d’assistance</span><span class="sxs-lookup"><span data-stu-id="a3ded-115">Overview of Helpers</span></span>

<span data-ttu-id="a3ded-116">Si vous avez besoin effectuer les mêmes tâches sur les différentes pages de votre site, vous pouvez utiliser un programme d’assistance.</span><span class="sxs-lookup"><span data-stu-id="a3ded-116">If you need to perform the same tasks on different pages in your site, you can use a helper.</span></span> <span data-ttu-id="a3ded-117">Les Pages Web ASP.NET inclut un certain nombre de programmes d’assistance et beaucoup d’autres que vous pouvez télécharger et installer.</span><span class="sxs-lookup"><span data-stu-id="a3ded-117">ASP.NET Web Pages includes a number of helpers, and there are many more that you can download and install.</span></span> <span data-ttu-id="a3ded-118">(Les programmes d’assistance intégrées dans ASP.NET Web Pages est répertoriés dans le [référence rapide de l’API ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202907).) Si aucun des programmes d’assistance existants répondent à vos besoins, vous pouvez créer votre propre programme d’assistance.</span><span class="sxs-lookup"><span data-stu-id="a3ded-118">(A list of the built-in helpers in ASP.NET Web Pages is listed in the [ASP.NET API Quick Reference](https://go.microsoft.com/fwlink/?LinkId=202907).) If none of the existing helpers meet your needs, you can create your own helper.</span></span>

<span data-ttu-id="a3ded-119">Une application d’assistance permet d’utiliser un bloc courants de code sur plusieurs pages.</span><span class="sxs-lookup"><span data-stu-id="a3ded-119">A helper lets you use a common block of code across multiple pages.</span></span> <span data-ttu-id="a3ded-120">Vous voulez que votre page souvent créer un élément de la Remarque qui est défini indépendamment des paragraphes normaux.</span><span class="sxs-lookup"><span data-stu-id="a3ded-120">Suppose that in your page you often want to create a note item that's set apart from normal paragraphs.</span></span> <span data-ttu-id="a3ded-121">Par exemple la note est créée en tant qu’un `<div>` élément qui a le format d’une zone avec une bordure.</span><span class="sxs-lookup"><span data-stu-id="a3ded-121">Perhaps the note is created as a `<div>` element that's styled as a box with a border.</span></span> <span data-ttu-id="a3ded-122">Au lieu d’ajouter ce même balisage à une page chaque fois que vous souhaitez afficher une note, vous pouvez empaqueter le balisage comme une application d’assistance.</span><span class="sxs-lookup"><span data-stu-id="a3ded-122">Rather than add this same markup to a page every time you want to display a note, you can package the markup as a helper.</span></span> <span data-ttu-id="a3ded-123">Vous pouvez ensuite insérer la Remarque avec une seule ligne de code n’importe où vous en avez besoin.</span><span class="sxs-lookup"><span data-stu-id="a3ded-123">You can then insert the note with a single line of code anywhere you need it.</span></span>

<span data-ttu-id="a3ded-124">À l’aide d’un programme d’assistance ainsi de rend le code dans chacune de vos pages plus simple et plus facile à lire.</span><span class="sxs-lookup"><span data-stu-id="a3ded-124">Using a helper like this makes the code in each of your pages simpler and easier to read.</span></span> <span data-ttu-id="a3ded-125">Il rend également plus facile à maintenir votre site, car si vous devez modifier l’apparence des notes, vous pouvez modifier le balisage dans un seul emplacement.</span><span class="sxs-lookup"><span data-stu-id="a3ded-125">It also makes it easier to maintain your site, because if you need to change how the notes look, you can change the markup in one place.</span></span>

## <a name="creating-a-helper"></a><span data-ttu-id="a3ded-126">Création d’une application d’assistance</span><span class="sxs-lookup"><span data-stu-id="a3ded-126">Creating a Helper</span></span>

<span data-ttu-id="a3ded-127">Cette procédure vous montre comment créer l’application d’assistance qui crée une note, comme décrit ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="a3ded-127">This procedure shows you how to create the helper that creates the note, as just described.</span></span> <span data-ttu-id="a3ded-128">Il s’agit d’un exemple simple, mais l’application d’assistance personnalisée peut inclure un balisage et le code ASP.NET dont vous avez besoin.</span><span class="sxs-lookup"><span data-stu-id="a3ded-128">This is a simple example, but the custom helper can include any markup and ASP.NET code that you need.</span></span>

1. <span data-ttu-id="a3ded-129">Dans le dossier racine du site Web, créez un dossier nommé *application\_Code*.</span><span class="sxs-lookup"><span data-stu-id="a3ded-129">In the root folder of the website, create a folder named *App\_Code*.</span></span> <span data-ttu-id="a3ded-130">Il s’agit d’un nom de dossier réservé dans ASP.NET où vous pouvez placer le code des composants tels que des programmes d’assistance.</span><span class="sxs-lookup"><span data-stu-id="a3ded-130">This is a reserved folder name in ASP.NET where you can put code for components like helpers.</span></span>
2. <span data-ttu-id="a3ded-131">Dans le *application\_Code* dossier créer un nouveau *.cshtml* de fichier et nommez-la *MyHelpers.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a3ded-131">In the *App\_Code* folder create a new *.cshtml* file and name it *MyHelpers.cshtml*.</span></span>
3. <span data-ttu-id="a3ded-132">Remplacez le contenu existant avec les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="a3ded-132">Replace the existing content with the following:</span></span>

    [!code-cshtml[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    <span data-ttu-id="a3ded-133">Le code utilise le `@helper` syntaxe pour déclarer une nouvelle assistance de nommé `MakeNote`.</span><span class="sxs-lookup"><span data-stu-id="a3ded-133">The code uses the `@helper` syntax to declare a new helper named `MakeNote`.</span></span> <span data-ttu-id="a3ded-134">Ce programme d’assistance particulier vous permet de passer un paramètre nommé `content` qui peut contenir une combinaison de texte et de balises.</span><span class="sxs-lookup"><span data-stu-id="a3ded-134">This particular helper lets you pass a parameter named `content` that can contain a combination of text and markup.</span></span> <span data-ttu-id="a3ded-135">L’application d’assistance insère la chaîne dans le corps de la Remarque à l’aide du `@content` variable.</span><span class="sxs-lookup"><span data-stu-id="a3ded-135">The helper inserts the string into the note body using the `@content` variable.</span></span>

    <span data-ttu-id="a3ded-136">Notez que le fichier est nommé *MyHelpers.cshtml*, mais l’application d’assistance est nommée `MakeNote`.</span><span class="sxs-lookup"><span data-stu-id="a3ded-136">Notice that the file is named *MyHelpers.cshtml*, but the helper is named `MakeNote`.</span></span> <span data-ttu-id="a3ded-137">Vous pouvez placer plusieurs applications d’assistance personnalisées dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="a3ded-137">You can put multiple custom helpers into a single file.</span></span>
4. <span data-ttu-id="a3ded-138">Enregistrez et fermez le fichier.</span><span class="sxs-lookup"><span data-stu-id="a3ded-138">Save and close the file.</span></span>

## <a name="using-the-helper-in-a-page"></a><span data-ttu-id="a3ded-139">À l’aide de l’application d’assistance dans une Page</span><span class="sxs-lookup"><span data-stu-id="a3ded-139">Using the Helper in a Page</span></span>

1. <span data-ttu-id="a3ded-140">Dans le dossier racine, créez un fichier vide appelé *TestHelper.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a3ded-140">In the root folder, create a new blank file called *TestHelper.cshtml*.</span></span>
2. <span data-ttu-id="a3ded-141">Ajoutez le code suivant au fichier :</span><span class="sxs-lookup"><span data-stu-id="a3ded-141">Add the following code to the file:</span></span>

    [!code-html[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample2.html)]

    <span data-ttu-id="a3ded-142">Pour appeler l’application d’assistance que vous avez créé, utilisez `@` suivi par le nom du fichier où l’application d’assistance est, un point, puis le nom de l’application d’assistance.</span><span class="sxs-lookup"><span data-stu-id="a3ded-142">To call the helper you created, use `@` followed by the file name where the helper is, a dot, and then the helper name.</span></span> <span data-ttu-id="a3ded-143">(Si vous avez plusieurs dossiers dans le *application\_Code* dossier, vous pouvez utiliser la syntaxe `@FolderName.FileName.HelperName` pour appeler votre application d’assistance dans n’importe quelle imbriquées au niveau du dossier).</span><span class="sxs-lookup"><span data-stu-id="a3ded-143">(If you had multiple folders in the *App\_Code* folder, you could use the syntax `@FolderName.FileName.HelperName` to call your helper within any nested folder level).</span></span> <span data-ttu-id="a3ded-144">Le texte que vous ajoutez des guillemets entre parenthèses est le texte qui affiche l’application d’assistance dans le cadre de la note dans la page web.</span><span class="sxs-lookup"><span data-stu-id="a3ded-144">The text that you add in quotation marks within the parentheses is the text that the helper will display as part of the note in the web page.</span></span>
3. <span data-ttu-id="a3ded-145">Enregistrez la page et l’exécuter dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a3ded-145">Save the page and run it in a browser.</span></span> <span data-ttu-id="a3ded-146">L’application d’assistance génère l’élément note à droite dans lequel vous avez appelé l’application d’assistance : entre les deux paragraphes.</span><span class="sxs-lookup"><span data-stu-id="a3ded-146">The helper generates the note item right where you called the helper: between the two paragraphs.</span></span>

    ![Capture d’écran affichant la page dans le navigateur et comment l’application d’assistance généré le balisage qui place une zone autour du texte spécifié.](creating-and-using-a-helper-in-an-aspnet-web-pages-site/_static/image1.jpg)

## <a name="additional-resources"></a><span data-ttu-id="a3ded-148">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a3ded-148">Additional Resources</span></span>


<span data-ttu-id="a3ded-149">[Menu horizontal sous la forme d’un programme d’assistance Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341).</span><span class="sxs-lookup"><span data-stu-id="a3ded-149">[Horizontal menu as a Razor helper](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341).</span></span> <span data-ttu-id="a3ded-150">Cette entrée de blog par Mike Pope montre comment créer un menu horizontal comme un programme d’assistance à l’aide du balisage, CSS et le code.</span><span class="sxs-lookup"><span data-stu-id="a3ded-150">This blog entry by Mike Pope shows how to create a horizontal menu as a helper using markup, CSS, and code.</span></span>

<span data-ttu-id="a3ded-151">[Tirant parti de HTML5 dans ASP.NET Web Pages des programmes d’assistance pour WebMatrix et ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx).</span><span class="sxs-lookup"><span data-stu-id="a3ded-151">[Leveraging HTML5 in ASP.NET Web Pages Helpers for WebMatrix and ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx).</span></span> <span data-ttu-id="a3ded-152">Cette entrée de blog par Sam Abraham montre un programme d’assistance qui restitue une HTML5 `Canvas` élément.</span><span class="sxs-lookup"><span data-stu-id="a3ded-152">This blog entry by Sam Abraham shows a helper that renders an HTML5 `Canvas` element.</span></span>

<span data-ttu-id="a3ded-153">[La différence entre @Helpers et @Functions dans WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix).</span><span class="sxs-lookup"><span data-stu-id="a3ded-153">[The Difference Between @Helpers and @Functions in WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix).</span></span> <span data-ttu-id="a3ded-154">Décrit de cette entrée de blog par Mike Brind `@helper` syntaxe et `@function` syntaxe et quand les utiliser.</span><span class="sxs-lookup"><span data-stu-id="a3ded-154">This blog entry by Mike Brind describes `@helper` syntax and `@function` syntax and when to use each.</span></span>