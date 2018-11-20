# Instructions

Dans cet atelier, nous allons créer une extension permettant de répondre à une commande en affichant le nombre de mots conteus dans un fichier.

## Initialiser le workspace

Créer un nouveau dossier "techlabs", et ouvrir celui-ci dans VS Code ou une fenêtre de commande.

## Générer l'extension

```bash
yo code
```

Renseignez les paramètres suivants : 
* type : **New Extension (TypeScript)**
* name : **wordCounter**
* identifier : **wordCounter**
* description : **ma seconde extension**
* publisher name : **votre publisher name**
* Enable stricter TypeScript checking : **Y**
* Setup linting using 'tslint' : **Y**
* Initialize a git repository? : **n**

![](yoCodeHelloWorld.png)

Un nouveau dossier **wordCounter** a été créé.

## Initialiser le workspace

```bash
cd wordCounter
code .
```

Une nouvelle fenêtre VS Code doit alors s'ouvrir sur le dossier de votre extension nouvellement créée. 

## Debug de l'extension

Appuyez sur `F5`

Une nouvelle fenêtre VS Code doit s'ouvrir intitulée `Extension Development Host`.
Dans cette fenêtre, vous pouvez utiliser votre extension.

## Exécution de la commande

Appuyez sur `Ctrl + Shift + P` pour Windows (`Cmd +Shift + P` pour Mac) et lancez la commande `Hello World`.

![](commandHelloWorld.png)

Une fenêtre de message doit alors apparaître avec le message `Hello World`.

![](dialogHelloWorld.png)

## Modifier la commande

Ouvrez le fichier *package.json* et remplacez le code suivant :

```javascript
"activationEvents": [
    "onCommand:extension.countWords"
],// The module 'vscode' contains the VS Code extensibility API
// Import the necessary extensibility types to use in your code below
import {window, commands, Disposable, ExtensionContext, StatusBarAlignment, StatusBarItem, TextDocument} from 'vscode';

// This method is called when your extension is activated. Activation is
// controlled by the activation events defined in package.json.
export function activate(context: ExtensionContext) {

    // Use the console to output diagnostic information (console.log) and errors (console.error).
    // This line of code will only be executed once when your extension is activated.
    console.log('Congratulations, your extension "WordCounter" is now active!');

    // create a new word counter
    let wordCounter = new WordCounter();

    let disposable = commands.registerCommand('extension.countWords', () => {
        wordCounter.updateWordCount();
    });

    // Add to a list of disposables which are disposed when this extension is deactivated.
    context.subscriptions.push(wordCounter);
    context.subscriptions.push(disposable);
}

class WordCounter {

    private _statusBarItem: StatusBarItem =  window.createStatusBarItem(StatusBarAlignment.Left);

    public updateWordCount() {

        // Get the current text editor
        let editor = window.activeTextEditor;
        if (!editor) {
            this._statusBarItem.hide();
            return;
        }

        let doc = editor.document;

        // Only update status if a Markdown file
        if (doc.languageId === "markdown") {
            let wordCount = this._getWordCount(doc);

            // Update the status bar
            this._statusBarItem.text = wordCount !== 1 ? `${wordCount} Words` : '1 Word';
            this._statusBarItem.show();
        } else {
            this._statusBarItem.hide();
        }
    }

    public _getWordCount(doc: TextDocument): number {

        let docContent = doc.getText();

        // Parse out unwanted whitespace so the split is accurate
        docContent = docContent.replace(/(< ([^>]+)<)/g, '').replace(/\s+/g, ' ');
        docContent = docContent.replace(/^\s\s*/, '').replace(/\s\s*$/, '');
        let wordCount = 0;
        if (docContent !== "") {
            wordCount = docContent.split(" ").length;
        }

        return wordCount;
    }

    dispose() {
        this._statusBarItem.dispose();
    }
}
```

Appuyez sur `F5` pour lancer l'extension et lancez la commande `Count Words`.
