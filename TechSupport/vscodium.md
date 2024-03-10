# VSCodium

(vscodium-proxy)=
## Configuration de proxy dans VSCodium

Si vous avez l'erreur **Error while fetching extensions. XHR failed** quand vous allez dans **Extensions** (dans la barre à gauche), alors il faut changer les paramètres de proxy dans VSCodium.
1. Appuyer sur **F1**.
2. Taper **User Settings** dans la barre de recherche.
3. Cliquer sur **Open User Settings**.
4. Dans la barre **Search Settings**, taper **Proxy**.
5. Dans **Http: Proxy** *(Applies to all profile)*, taper dans la barre `http://<serveur proxy>:<port>`. À l'IUT d'Orsay, le serveur est proxy.iut-orsay.fr et le port est 3128.

(vscodium-debugger)=
## C/C++ Runner Debugger setup
Il faut changer le fichier de configuration `launch_template.json`. 

**Pour le retrouver à travers un terminal :**
```{code} sh
cd ~/.vscode-oss/extensions/franneck94.c-cpp-runner-9.4.7-universal/templates
```

Si `.vscode-oss` ne se trouve pas dans `~/`, vous pouvez essayer de suivre les étapes suivantes.

**À travers un navigateur :**
- Rendez-vous sur `home` ou votre dossier personnel.
- Appuyer sur `Ctrl+H` pour voir les dossiers cachés (les dossiers de configurations qui commencent par `.` comme `.vscode-oss` sont cachés).
- Aller dans `.vscode-oss` puis, `extensions`, `franneck94.c-cpp-runner-9.4.7-universal` et finalement `templates`.

**Remplacer le contenu de `launch_template.json` par :**
```{code} json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C/C++ Runner: Debug Session",
      "type": "lldb",
      "request": "launch",
      "args": [],
      "console": "externalTerminal",
      "cwd": "${fileDirname}",
      "program": "${fileDirname}/build/Debug/outDebug",
    }
  ]
}
```