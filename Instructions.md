# Instuctions
### Create the project.

Visual Studio: Create New Project --> Blazor Web App
1. .NET 8.0
2. Interactive render mode: Server
3. Interactivity location: Global
4. Uncheck "Include sample pages"
5. Uncheck "Enlist in Aspire Orchestration".

--> Create.

OR

Using a console command:
```powershell
dotnet new blazor -o AppName -f net8.0 --interactivity server --empty
```

To run the project - go into your project Directory and write ```dotnet run```. If you are in the middle of developing - use ```dotnet watch``` - this way every change in your code will immediately register and display in the browser.

### Languges
To create a Blazor project we use languages like HTML and CSS for the page and C# for the inner logic and interactions between user and server. You can learn the basics of HTML on this web course: https://www.w3schools.com/html/html_basic.asp

### Initial structure
Now that you saw how a web-page should look we can start. The HTML code for our page is split between 3 directories:
- Components/App.razor - basic structure `<!DOCTYPE html>` and embeddings;
- Components/Layout/MainLayout.razor - blocks inside the `<body>` tag that are used for all the pages. Here we will create navigation;
- Components/Pages/Home.razor - our first page that has its own unique content.

### Navigation
We will begin with Navigation as it is essential for any multipage web-app. Go to the file Components/Layout/MainLayout.razor, it is used in all pages. Between @inherits and @Body add `<nav id="nav"></nav>` this will be our main tag for navigation, we also add ID for the tag to use it in near future. Inside the `<nav id="nav"></nav>` tag add `<NavLink>Create Character</NavLink>` a unique tag of Blazor technology. Add to the opening tag a class, link to the page (for the home page it's "" and for any other is "path to your page without '/'") and allocation `<NavLink class="navlink" href="" Match="NavLinkMatch.All">`. Repeat to create the second tag for the second page, but change the `href=""` and remove `Match=""`:
```HTML
<nav id="nav">
    <NavLink class="navlink" href="" Match="NavLinkMatch.All">Create Character</NavLink>
    <NavLink class="navlink" href="showcase">Show Character</NavLink>
</nav>
```
### Storage
After our navigation menu is created we need to create a place to store our data. Create a file Cards.cs in the Components folder. First, we create a simple class to define our characters we are going to create in web-page and a static class to store and deliver our data:
```C#
public class CharCard
{
    public string Name { get; set; } = "";
    public string Class { get; set; } = "";
    public int Str { get; set; }
    public int Dex { get; set; }
    public int Int { get; set; }
}

public static class Cards
{
    public static List<CharCard> Roster { get; set; } = new();
}
```
Quick note: a STATIC class will store data until the server reload, allowing data to persist even after the page is reloaded! (Does not work if the project type is WebAssembly or Auto)

### Main and Home pages
Finally we can move on to create our pages. Return to the MainLayout.razor and wrap the @Body with main:
```HTML
<main id="main">
@Body
</main>
```
Now, go to the Home.razor. There we see @page "/" meaning the path used in navigation and `<PageTitle>Home</PageTitle>` that will display page's title. Both of these tags are unique to Blazor and don't exist in html. We also need to add `@rendermode InteractiveServer`, another Blazor unique tag to make the further code interactable for users. Delete the code under these tags we can finally begin.
```HTML
@page "/"
<PageTitle>Home</PageTitle>
@rendermode InteractiveServer
```
Create a headline3 so that people (and you) understand the purpose of the page, then make an underline to separate it from our main block. Give the main block class "container" to make a mark for yourself. Then make two more divs, the `<div class="create">` and `<div class="show">`. Inside `<div class="create">` we will make character creation menu that will accept data from user and then give it to the menu inside the `<div class="show">`.
```HTML
<h3>Character creator</h3>
<div class="container">
	<div class="create"></div>
	<div class="show"></div>
</div>
```
### Tags
Now we are working with creation menu inside `<div class="create">`. Here we will use standart HTML tags paired with Blazor unique properties:
* @bind - immediately overrides data in variable with user input;
* @bind:event - is used for any instant interactions, in our case @bind:event="oninput" updates data in variable after every single keystroke;
* @bind:after - executes a C# method immediately after variable updates.
* @availablePoints - is used to display data from a variable without changing it.
* @(availablePoint == 0 ? "red" : "green") and similar constructions - are analogues of if/else used directly in HTML.
* @onclick="" - is an EventHandler similar to JavaScript's onclick="" that allows to execute chosen method on button click.

Use the code below and copy it STRICTLY in `<div class="create">` to create our menu:

```HTML
<div class="name_block">
            <label>Name: </label>
            <input @bind="charName" @bind:event="oninput" placeholder="Enter your name..." />
        </div>

        <div class="stats_block">
            <label>Class: </label>
            <select @bind="charClass" @bind:after="ApplyClassStats">
                <option value="Warrior">&#x2694; Warrior</option>
                <option value="Mage">&#129668; Mage</option>
                <option value="Archer">&#127993; Archer</option>
            </select>
        </div>

        <div class="point_block">
            <h4 style="color: @(availablePoints == 0 ? "red" : "green")">
                Your stat points: @availablePoints
            </h4>

            <p>
                &#128170; Strength: <strong>@strength</strong>
                <button @onclick="IncStr" disabled="@(availablePoints == 0)">+</button>
                <button @onclick="DecStr" disabled="@(strength == minStr)">-</button>
            </p>

            <p>
                &#128075; Dexterity: <strong>@dexterity</strong>
                <button @onclick="IncDex" disabled="@(availablePoints == 0)">+</button>
                <button @onclick="DecDex" disabled="@(dexterity == minDex)">-</button>
            </p>

            <p>
                &#x1f56e; Intelligence: <strong>@intelligence</strong>
                <button @onclick="IncInt" disabled="@(availablePoints == 0)">+</button>
                <button @onclick="DecInt" disabled="@(intelligence == minInt)">-</button>
            </p>
        </div>
```

### Character cards
Next is the `<div class="show">` where we will display all the data gained from `<div class="create">`. It is quite simple and the only Blazor-unique constructions are `@variablename` which we already discussed in a previous block and `@if (availablePoints==0) {}` which is a logical if/else construction that are used more commonly. Use the code below and copy it STRICTLY in `<div class="show">` to create a character card:

```HTML
<h3 class="show_name">@charName</h3>
        <p class="show_class">@charClass</p>
        <hr />
        <p>Strength: @strength</p>
        <p>Dexterity: @dexterity</p>
        <p>Intelligence: @intelligence</p>

        @if (availablePoints == 0)
        {
            @if (!string.IsNullOrEmpty(errorMessage))
            {
                <p class="error_mes">
                    @errorMessage
                </p>
            }

            <button class="button_save" @onclick="SaveChar">Save your character</button>
        }
    </div>
```

### Cards logic
Now that we have a code for a pretty web-page we need to create the variables themselves. After all the HTML code is written use a Blazor tag @code{} that allows to write object logic in C# in the same file as the HTML code. Here we only need to create variables and methods used previously along with error handlers. We do not need to create classes for characters and save list since we have already done it in `Cards.cs`. Use this code for the C# logic:
```C#
@code {
    private string errorMessage = "";

    private void SaveChar()
    {
       errorMessage = "";

        if (string.IsNullOrWhiteSpace(charName))
        {
            errorMessage = "Please enter a char name!";
            return;
        }
        foreach (var existingchar in Cards.Roster)
        {
            if (existingchar.Name.ToLower() == charName.ToLower())
            {
                errorMessage = "A char with this name already exists!";
                return;
            }
        }

        Cards.Roster.Add(new CharCard{Name = charName, Class = charClass, Str = strength, Dex = dexterity, Int = intelligence});

        charName = "";
        ApplyClassStats();
    }

    private string charName = "Unit";
    private string charClass = "Warrior";
    private int availablePoints = 5;

    private int strength = 5;
    private int dexterity = 2;
    private int intelligence = 1;

    private int minStr = 5;
    private int minDex = 2;
    private int minInt = 1;

    private void ApplyClassStats()
    {
        availablePoints = 5;

        if (charClass == "Warrior") { minStr = 5; minDex = 2; minInt = 1; }
        else if (charClass == "Mage") { minStr = 1; minDex = 2; minInt = 5; }
        else if (charClass == "Archer") { minStr = 2; minDex = 5; minInt = 1; }

        strength = minStr;
        dexterity = minDex;
        intelligence = minInt;
    }

    private void IncStr() { if (availablePoints > 0) { strength++; availablePoints--; } }
    private void DecStr() { if (strength > minStr) { strength--; availablePoints++; } }

    private void IncDex() { if (availablePoints > 0) { dexterity++; availablePoints--; } }
    private void DecDex() { if (dexterity > minDex) { dexterity--; availablePoints++; } }

    private void IncInt() { if (availablePoints > 0) { intelligence++; availablePoints--; } }
    private void DecInt() { if (intelligence > minInt) { intelligence--; availablePoints++; } }
}
```

### Cards showcase page
The first page is done, character cards are created and saved and now we need a way to show them. For that we will create a new file `Showcase.razor` and use it for cleaner and prettier way to show our characters. First we write the basic route, title and rendermode tags that you learned earlier, then make a nice page, using `@if` constructions, `@variables` and the `@foreach (){}` tag that is used the same way as C# `for (){}`. Also, here we use the lambda function, the usual feature for any button in any programming language. Here is the code for the page:
```HTML
<h3>Your Saved Characters: @Cards.Roster.Count</h3>
<hr class="underline">

<div class="show">
    <div class="list">
        @if (Cards.Roster.Count == 0)
        {
            <p class="note">No one is there yet.</p>
        }
        else
        {
            <div class="char_panel">
                @foreach (var character in Cards.Roster)
                {
                    <div class="char_block" style="border: @(selectedCharacter == character? "2px solid #007bff" : "1px solid #ccc");" @onclick="() => SelectChar(character)">
                        <h4 class="char_name">@character.Name</h4>
                        <p class="char_class">@character.Class</p>
                        <button class="button_del" @onclick="() => DeleteChar(character)" @onclick:stopPropagation="true">Delete</button>
                    </div>
                }
            </div>
        }
    </div>

    <div class="details">
        @if (selectedCharacter == null)
        {
            <div class="detail_null">
                <p>Select a character from the list to see details.</p>
            </div>
        }
        else
        {
            <div class="detail_card">
                <h2 class="detail_name">@selectedCharacter.Name</h2>
                <p class="detail_class">@selectedCharacter.Class</p>
                <hr>
                <h4 class="h4">Character Stats</h4>
                <div class="detail_stats">
                    <p class="stat">&#128170; Strength: <strong>@selectedCharacter.Str</strong></p>
                    <p class="stat">&#128075; Dexterity: <strong>@selectedCharacter.Dex</strong></p>
                    <p class="stat">&#x1f56e; Intelligence: <strong>@selectedCharacter.Int</strong></p>
                </div>
            </div>
        }
    </div>

</div>
```
### Cards showcase logic
Obviously we need to add new logic, however we only need to select character and delete selected character:
```C#
@code {
    private CharCard? selectedCharacter;

    private void SelectChar(CharCard character)
    {
        selectedCharacter = character;
    }

    private void DeleteChar(CharCard characterToRemove)
    {
        Cards.Roster.Remove(characterToRemove);

        if (selectedCharacter == characterToRemove)
        {
            selectedCharacter = null;
        }
    }
}
```

### Styling
As you might have noticed, in the offered code there are class="name" constructions in most tags. Although they are very good to mark div blocks so that your code is more readable, they also have another practical purpose. They are used in CSS files to style objects on your web-page. CSS can also be used in Blazor app and in fact it's much faster to connect to your HTML file. While usually you need to write `<link rel="stylesheet" href="name.css">` in your `<head>` to connect the file, Blazor allows you to create the CSS file in the same directory as your HTML file and it will connect if it has the same name. That means if you want to connect a CSS file to HTML file `Home.razor`, you need to create a file `Home.razor.css` in the same directory. You can change the names of your classes and decorate your web-pages using w3schools documentation: https://www.w3schools.com/css/css_syntax.asp

AN IMPORTANT NOTE FOR BLAZOR-UNIQUE TAGS! Since there's no `<NavLink>` tag in HTML, in a browser Blazor turns it into an `<a></a>` tag and attaches the new functional to it. That means that you can't use classes like usual and need to write the full route and add ::deep in between. For example, to add a class for our `<NavLink class="navlink">` we need to write:
```css
nav ::deep a.navlink{
	//your styles
}
```
### Java stuff
BONUS EXERCISE! Even though it is not included in our project, it is possible to use JavaScript language to modify HTML code like usual. It is done the same way as the CSS files except it is .js and not .css (`Home.razor.js`). So if any of you knows how to use JavaScript for Front-end development (or are not lazy enough to read how it's done: https://www.w3schools.com/js/js_htmldom.asp) you can add something unique, made completely by yourself to this project!

FUN FACT! There's already a piece of pure JavaScript in any Blazor project as it is used for one of its most important functions. In directory `Components/Layout` there are files `ReconnectModal.razor`, `ReconnectModal.razor.css` AND `ReconnectModal.razor.js` that contains JavaScript code for the WebSocket, allowing the web-app to update every change in code or variables in real time without the need to update the page yourself.
