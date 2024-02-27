# Quand est-ce qu'il faut commenter ?

```{figure} ../images/comments.jpeg
:alt: Commentaires dans un code
:align: center

Les commentaires dans un code (Credits: Programmers Memes).
```
## Bons commentaires

### Droits d'auteur

Example de la [Licence MIT](https://fr.wikipedia.org/wiki/Licence_MIT) :
```{code} cpp
/*
Copyright <YEAR> <COPYRIGHT HOLDER>

Permission is hereby granted, free of charge, to any 
person obtaining a copy of this software and associated 
documentation files (the “Software”), to deal in the 
Software without restriction, including without 
limitation the rights to use, copy, modify, merge, 
publish, distribute, sublicense, and/or sell copies of 
the Software, and to permit persons to whom the Software 
is furnished to do so, subject to the following 
conditions:

The above copyright notice and this permission notice 
shall be included in all copies or substantial portions 
of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY 
KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO 
THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A 
PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL 
THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, 
DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF 
CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN 
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS 
IN THE SOFTWARE.
*/
```

### Choix d'implémentation

```{code} cpp
// Insertion sort is chosen for simplicity since the data set is small
```

:::{note} Note
:class: dropdown
Un commentaire pour expliquer un choix d'implémentation quand il y a plusieurs choix possibles peut-être utile. Un autre développeur peut ne pas être d'accord avec le choix effectué mais il peut comprendre le raisonnement derrière et l'améliorer s'il veut.
:::

### Clarifications

```{code} cpp
void testCompareTo() {
    WikiPagePath a = PathParser::parse("PageA");
    WikiPagePath ab = PathParser::parse("PageA.PageB");
    WikiPagePath b = PathParser::parse("PageB");
    WikiPagePath aa = PathParser::parse("PageA.PageA");
    WikiPagePath bb = PathParser::parse("PageB.PageB");
    WikiPagePath ba = PathParser::parse("PageB.PageA");

    assert(a.compareTo(a) == 0); // a == a
    assert(a.compareTo(b) != 0); // a != b
    assert(ab.compareTo(ab) == 0); // ab == ab
    assert(a.compareTo(b) == -1); // a < b
    assert(aa.compareTo(ab) == -1); // aa < ab
    assert(ba.compareTo(bb) == -1); // ba < bb
    assert(b.compareTo(a) == 1); // b > a
    assert(ab.compareTo(aa) == 1); // ab > aa
    assert(bb.compareTo(ba) == 1); // bb > ba
}
```
:::{note} Note
:class: dropdown
Parfois la syntaxe d'une librairie que l'on utilise est figée et on ne peut pas la changer même si elle n'est pas très claire à la lecture. Dans ce cas-là, il peut être utile de rajouter des commentaires qui clarifie la syntaxe.
:::

```{code} cpp
// format matched hh:mm:ss MM dd, yyyy
regex timeMatcher("\\d*:\\d*:\\d* \\w* \\d*, \\d*");
```

:::{important} Expressions régulières
:class: dropdown
Les expressions régulières sont souvent complexes mais nécessaires dans notre code. Dans ce cas-là, il est utile d'expliquer l'expression régulière utilisée.
:::

### Warning

```{code} cpp
// Dear maintainer:
// 
// Once you are done trying to 'optimize' this routine,
// and have realized what a terrible mistake that was,
// please increment the following counter as a warning
// to the next guy:
// 
// total_hours_wasted_here = 42
```

:::{note} Plus sérieusement,
:class: dropdown
Parfois, il est utile de prévenir un autre développeur d'une erreur souvent rencontrée (quand on essaye d'améliorer un code par exemple) ou souligner l'importance d'une procédure qui pourrait sembler inconséquente.
:::

```{code} cpp
// the trim is real important. It removes the starting
// spaces that could cause the item to be recognized
// as another list. 
string listItemContent = trim(match.str(3));
```

### TODO

```{code} cpp
// TODO : sorting procedure to be implemented
```

### Documenter

:::{important} Disclaimer
:class: dropdown
En tant que développeur, vous serez amené à coder vos propres librairies et donc de les documenter. Il y a plusieurs types de documentations dont la documentation pour utilisateur ou pour développeur. 

Il y a des outils pour générer de la documentation (sous forme de site web par exemple) à partir des commentaires dans le code. Cela nécessite donc des syntaxes à respecter. 

Dans ce cours, nous n'allons pas rentrer dans les détails de comment écrire de la documentation (ce qui nécessite tout un cours en lui-même). Pourtant, nous allons voir des exemples de documentations et apprendre à écrire une documentation générique (sans syntaxe particulier) dans l'esprit de la documentation bien écrite. 
:::

```{code} cpp
/*
Summary line.

Extended description of function.

Parameters:
    - firstArgument (FirstType): Description of firstArgument.
    - secondArgument (SecondType): Description of secondArgument.

Return:
    ReturnType: Description of return value.
*/
ReturnType function(FirstType firstArgument, SecondType secondArgument)
```

## Mauvais commentaires

Beaucoup de commentaires sont une excuse pour du code de mauvais qualité. Voici les erreurs à éviter.

### Commentaires cryptiques

[La racine carrée inverse rapide](https://fr.wikipedia.org/wiki/Racine_carr%C3%A9e_inverse_rapide).
```{code} cpp
float Q_rsqrt( float number )
{
	long i;
	float x2, y;
	const float threehalfs = 1.5F;

	x2 = number * 0.5F;
	y = number;
	i = * ( long * ) &y; // evil floating point bit level hacking
	i = 0x5f3759df - ( i >> 1 ); // what the fuck?
	y = * ( float * ) &i;
	y = y * ( threehalfs - ( x2 * y * y ) ); // 1st iteration
//	y = y * ( threehalfs - ( x2 * y * y ) ); // 2nd iteration, this can be removed

	return y;
}
```

:::{note} Note
:class: dropdown
Des commentaires qui nécessitent eux-mêmes d'autres commentaires pour comprendre ce qui se passe ne sont pas des bons commentaires.
:::

### Commentaires avec trop d'informations

```{code} cpp
/*
RFC 2045 - Multipurpose Internet Mail Extensions (MIME)
Part One: Format of Internet Message Bodies
section 6.8. Base64 Content-Transfer-Encoding
The encoding process represents 24-bit groups of input bits as output
strings of 4 encoded characters. Proceeding from left to right, a
24-bit input group is formed by concatenating 3 8-bit input groups.
These 24 bits are then treated as 4 concatenated 6-bit groups, each
of which is translated into a single digit in the base64 alphabet.
When encoding a bit stream via the base64 encoding, the bit stream
must be presumed to be ordered with the most-significant-bit first.
That is, the first bit in the stream will be the high-order bit in
the first 8-bit byte, and the eighth bit will be the low-order bit in
the first 8-bit byte, and so on.
*/
```

### Commentaires redondants

```{code} cpp
/*
Add a CD.

This function adds a CD.

Parameters :
    - title (string): The title of the CD.
    - author (string): The author of the CD.
    - numberOfTracks (int): The number of tracks on the CD.
    - durationInMinutes (int): The duration of the CD in minutes.

Return:
    void : This function does not return anything.
*/
void addCD(string title, string author, int numberOfTracks, int durationInMinutes)
```

:::{warning} Trop de documentations !
:class: dropdown
La documentation peut-être redondante quand le code est déjà très explicite. Par contre, la documentation peut quand même être nécessaire, surtout quand il s'agit de la documentation pour utilisateur. Une solution possible dans ce cas-ci est de simplifier les commentaires.

```{code} cpp
/*
Add a CD.

Parameters :
    - title (string)
    - author (string)
    - numberOfTracks (int)
    - durationInMinutes (int)
*/
void addCD(string title, string author, int numberOfTracks, int durationInMinutes)
```

La simplification ici évite aussi des commentaires faux dans le futur. Imaginons qu'un jour `durationInMinutes` devienne `durationInSeconds`, les IDE sont capables de changer le nom de la variable partout dans le code mais pas les commentaires qui vont avec !
:::

### Commenter au lieu de coder proprement

```{code} cpp
bool isPasswordValid(const string& password) {
    // Pattern matches that:
    // - Must be between 8 and 20 digits
    // - Must contain at least one uppercase letter and one lowercase letter
    // - Must contain one number
    // - Must not include whitespace
    // - Must contain one of the following special characters: ! # $ % ^ & * 
    regex pattern("^(?=.*[A-Z])(?=.*[a-z])(?=.*[0-9])(?=.*[!#$%^&*])[\\S]{8,20}$");
    return regex_match(password, pattern);
}
```

:::{warning} Un bon commentaire ?
:class: dropdown
Le commentaire ci-dessus peut sembler utile parce qu'il explique l'expression régulière illisible qui permet de vérifier qu'un mot de passe est valide. 

Pourtant, ces commentaires ne sont qu'une excuse pour du mauvais code. Avec nos principes de code propre, `isPasswordValid` devrait plutôt être refactorisé de la façon suivante.

```{code} cpp
bool lengthIsInRangeInclusive(const string& str) {
    return str.length() >= 8 && str.length() <= 20;
}

bool containsUppercaseLetter(const string& str) {
    return regex_match(str, regex("(?=.*[A-Z]).*"));
}

bool containsLowercaseLetter(const string& str) {
    return regex_match(str, regex("(?=.*[a-z]).*"));
}

bool containsNumericDigit(const string& str) {
    return regex_match(str, regex("(?=.*[0-9]).*"));
}

bool doesNotContainWhitespace(const string& str) {
    return !regex_match(str, regex("(?=.*[\\s]).*"));
}

bool containsSpecialCharacter(const string& str) {
    return regex_match(str, regex("(?=.*[!#$%^&*\\.]).*"));
}

bool isPasswordValid(const string& password) {
    return lengthIsInRangeInclusive(password) &&
           containsUppercaseLetter(password) &&
           containsLowercaseLetter(password) &&
           containsNumericDigit(password) &&
           doesNotContainWhitespace(password) &&
           containsSpecialCharacter(password);
}
```
:::

### Laisser du code commenté

```{code} cpp
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(), formatter.getByteCount());
//InputStream resultsStream = formatter.getResultStream();
//StreamReader reader = new StreamReader(resultsStream);
//response.setContent(reader.read(formatter.getByteCount()));
```

:::{note} Note
:class: dropdown
On est souvent amené à commenter des lignes des codes lors du développement. Pourtant, il ne faut pas les laisser trainer sans explication. Un autre programmeur qui lirait ce code et qui ne comprendrait pas ce que font les lignes commentés n'aurait pas le courage de les supprimer et ces lignes vont rester dans le code à jamais sans aucune explication et induiront peut-être des futurs développeurs en erreur.

Maintenant, avec les outils de versionnage comme Git. Il est presque impossible de perdre du code (si vous faites bien vos commits de façon régulière !). N'ayez pas peur de supprimer du code puis d'aller le chercher plus tard dans l'historique des commits.
:::

### Commenter au lieu de versionner

```{code} cpp
/*
11-Oct-2001 : Re-organised the class and moved it to new package com.jrefinery.date (DG);

05-Nov-2001 : Added a getDescription() method, and eliminated NotableDate class (DG);

05-Dec-2001 : Fixed bug in SpreadsheetDate class (DG);
*/
```

:::{note} Note
:class: dropdown
Certains pourraient penser que les laisser les notes sur l'évolution du code pour les autres développeurs dans les commentaires est une bonne pratique. 

Effectivement, cela serait une bonne pratique **si Git n'existait pas**. Avec le versionnage, un `git log` suffit (bien sûr à condition que les messages de commits sont bien écrits) !.
:::