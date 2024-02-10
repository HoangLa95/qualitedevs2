<style type="text/css" rel="stylesheet">
.spoiler {
    visibility: hidden;
  }
  
  .spoiler::before {
    visibility: visible;
    content: "Spoiler alert! Hover me to see the answer."
  }
  
  .spoiler:hover {
    visibility: visible;
  }
  
  .spoiler:hover::before {
    display: none;
  }
</style>

# Problèmes techniques

## Problème d'Internet sur Debian

Si vous avez des problèmes d'Internet avec Firefox sur Debian à l'IUT d'Orsay, c'est parce qu'il ne fait pas la détection de proxy par défaut. Il faut juste le reconfigurer une fois.
1. Aller en haut à droite et cliquer sur les trois barres.
2. Cliquer sur **Paramètres**.
3. Cliquer sur **Général** sur la barre à gauche.
4. Aller tout en bas dans **Paramètres réseau**.
5. Cliquer sur **Paramètres...**.
6. Cocher **Détection automatique des paramètres de proxy pour ce réseau**.

## Configuration de proxy dans VSCodium

Si vous avez l'erreur **Error while fetching extensions. XHR failed** quand vous allez dans **Extensions** (dans la barre à gauche), alors il faut changer les paramètres de proxy dans VSCodium.
1. Appuyer sur **F1**.
2. Taper **User Settings** dans la barre de recherche.
3. Cliquer sur **Open User Settings**.
4. Dans la barre **Search Settings**, taper **Proxy**.
5. Dans **Http: Proxy** *(Applies to all profile)*, taper dans la barre `http://<serveur proxy>:<port>`. À l'IUT d'Orsay, le serveur est proxy.iut-orsay.fr et le port est 
:::
:class: spoiler
3128.
:::
