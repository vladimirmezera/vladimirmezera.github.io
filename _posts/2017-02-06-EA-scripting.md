Enteprise Architect skriptování
=====

Dnes bych chtěl napsat pár řádků o Enterprise Architectu (Sparx Systems) a jeho možnostech skriptování. Tento nástroj je všude velmi rozšířený, protože umožnuje udržovat kompletní dokumentaci o vývoji.  Pro vytváření lze využít standardní UML diagramy, ale lze využít i jiné modely například pro business procesy  BPMN diagramy. V nových verzí EA je již integrována možnost vytvářet i grafické návrhy obrazovek, takže lze kompletní dokumentaci k projektu udržet na jednom místě.
![BPMN diagram](/public/bpmn.png "BPMN diagram")

To co se mí dále líbí je možnost ukládat projekty do databáze (Oracle, MySQL) a sdílet tak projekty napříč firmou bez nutnosti ukládání na sdílený disk. EA lze dnes provozovat jak na OS Windows, tak není problém podle návodu rozchodit EA i v Linuxu. V dnešní době mi nejvíce chybí absence generování automatické dokumentace z projektů, tak abych tuto činnost mohl dát na starost nějakému periodickému scheduleru. Z EA lze generovat HTML dokumentaci, ale absence automatizace je na škodu.

![EA13](/public/ea.png "Enterprise Architect 13")

Mým cílem zde není popisovat EA, ale spíše ukázat možnosti skriptování, které EA nabízí.

Modely v EA můžete vytvářet ručně, můžete je generovat například ze zdrojových kódů nebo pomocí pluginu. Někdy ovšem potřebujete provést úpravy v projektech, které se jednoduše nedají dělat pomocí ručního zásahu, případně potřebujete naimportovat struktury, na které nelze využít standardní nástroje. V tomto případě vám může pomoci skriptování.

EA nabízí několik možností vytváření typů skriptů – podporuje VBScript, JScript nebo Javascript. Protože standardně moje pracovní stanice běží na linuxu (v korporátním prostředí je to občas problematické), začal jsem používat Javascript. Je to i z toho důvodu, že jsem si již několik aplikací napsal pomocí Javascript frameworků.
Javascript, JScript a VBScript mají stejné možnosti využití API Enterprise Architectu. Nejvíce jsou zdokumentované skripty u JScript a VBScript. U Javascriptu je dokumentace méně, nicméně je možnost inspirovat se z těchto skriptů.

![Scripting window](/public/scripting.png "Scripting window")


V případě, že otevřete portlet Scripting, zobrazí se vám základní nabídka skriptů, které je ideální na začátku si vyzkoušet. Skripty mohou běžet ve standartním režimu, případně je lze spouštět v Debug režimu.

Několik doporučení
-----

### Udržování skriptů
Doporučuji skripty udržovat v nějakém verzovacím nástroji. Ideální je znovupoužitelné funkce rozdělit do vlastních knihoven, které si poté můžete načítat do jednotlivých skriptů (DRY - Don't Repeat Yourself). Příklad knihovny, která udržuje konstanty.

````javascript
!INC Local Scripts.EAConstants-JScript

````

### Nápověda při vytváření skriptů
Lze využí oficiální dokumentaci Object modelu Enterprise Architectu. Nicméně tato dokumentace není úplně přehledná a spoustu informací zde není uvedeno, nebo není úplně jasné.
Při vytváření skriptů je Enterprise architect schopný napovídat, v případě, že ví o jaký objekt se jedná. V případě že chcete vědět jaké funkce můžete zavolat na daném objektu, můžete provést následující operaci.

````javascript
var currentModel as EA.Package
var currentDiagram as EA.Diagram
var currentElement as EA.Element
var currentConnector as EA.Connector
````

Poté se vám bude automaticky zobrazovat nápověda při zadání `currentModel.`.


### Logování
Při logování můžete využít Session.Ouput, který vypíše vstup na standartní vstup. Doporučují využívat přímo logování, které je v základní knihovně k dispozici v Enterprise Architectu. Ukázka použití logování je například v mé ukázce skriptu zde..

````javascript
!INC Local Scripts.EAConstants-JScript
!INC EAScriptLib.JScript-Logging

//DEBUG LEVEL
var LOGLEVEL = LOGLEVEL_DEBUG
//INFO LEVEL
// var LOGLEVEL = LOGVEL_INFO
function main()
{
  LOGDebug("Debug")
  LOGInfo("Info")
}
main()
````
Volbou úrovně logování (LOGLEVEL_DEBUG, LOGLEVEL_INFO) poté můžete měnit množství událostí, které se budou zobrazovat ve výstupu. 

### Práce se soubory

Velmi často je potřeba pracovat se soubory (načítat vstupy, případně ukládat výstupy) a poté je možnost využít objekty, kterými Enteprise Architect umožnuje pracovat se soubory.

````javascript
!INC Local Scripts.EAConstants-JScript
!INC EAScriptLib.JScript-Logging

//DEBUG LEVEL
var LOGLEVEL = LOGLEVEL_DEBUG
//INFO LEVEL
// var LOGLEVEL = LOGVEL_INFO
function main()
{

    var fs = new COMObject("Scripting.FileSystemObject")
    var f = fs.GetFile(FILE_NAME)
    var ts = f.OpenAsTextStream(FILE_READ)
    var strline = null
    var lineNumber = 0

    while (!ts.AtEndOfStream) {
        strLine = ts.ReadLine()
        LOGDebug(strLine)
        lineNumber++
    }

    LOGInfo("The file " +FILE_NAME +" has " +lineNumber+" lines")
}
main()
````

### Automatický layout diagramu
V případě, že generujete velké množství elementů do diagramu, případně více diagramů, můžete i tyto nad těmito diagramy layoutovat diagram.

````javascript 
    !INC Local Scripts.EAConstants-JScript
    .
    .
    .
    .	
    //Auto layout
    Repository.GetProjectInterface().LayoutDiagramEx (resultDiagram.DiagramGUID, lsLayoutDirectionRight, 67, 40, 20, true)
````

### Elektronická kniha
Pro začátek doporučuji použít knihu od Thomas Kiliana „Scripting Enterprise Architect“. Jsou zde vysvětleny základní práce a různými objekty jako Element, Diagram, Connector, konfigurace atributů, vytváření package nebo modelů.

### Links

* [Ukázkové příklady skriptů](https://github.com/vladimirmezera/ea-scripts/)
* [Odkaz na skripting v EA](http://www.sparxsystems.com/enterprise_architect_user_guide/10/automation_and_scripting/the_scripter_window.html)
* [Enterprise Architect Object model](http://www.sparxsystems.com/enterprise_architect_user_guide/9.3/automation/theautomationinterface.html)


