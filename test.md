---
layout: default
---

# Projets

[Link to another page](./test.md).
## Simulation d'un Système Solaire en C

Ce projet a été réalisé dans le cadre du cours de physique de l'HEPIA.
Notre objectif était de mettre en pratique les concepts de physique, notamment les lois de Newton, à travers la simulation d'un système planétaire.

La simulation prend en compte les interactions gravitationnelles entre les planètes une étoile centrale en se basant sur les lois de la physique.

### Screenshots

![Image du système solaire](/assets/systeme-solaire/image.png)

### Exécuter le programme

1. Avoir un environnement de développement C configuré.
2. Cloner le répertoire.

```bash
git clone https://github.com/ArianDervishaj/simulation-systeme-solaire.git
```
3. Compiler et lancer le projet.

```bash
cd simulation-systeme-solaire
make main
./main
```

### En voir plus

[Cliquer ici pour voir la répertoire sur github](https://github.com/ArianDervishaj/simulation-systeme-solaire)

## Simulation de lignes de champs électriques en C

Ce programme simule les lignes de champs électriques en utilisant le langage C. Il prend en compte les charges électriques et calcule les champs électriques résultants, puis trace les lignes de champs correspondantes. La simulation permet de visualiser le comportement du champ électrique en présence de multiples particules chargées.

### Screenshots

#### Simulation avec 10 charges positives, 10 charges négatives et 50 lignes de champs

![Simulation avec 10 charges positives, 10 charges négatives et 50 lignes de champs](/assets/lignes-de-champs/image.png)

#### Simulation avec 100 charges positives, 100 charges négatives et 5000 lignes de champs

![Simulation avec 100 charges positives, 100 charges négatives et 5000 lignes de champst](/assets/lignes-de-champs/image-1.png)

#### Simulation avec 5 charges positives, 10 charges négatives et 500 lignes de champs

![Simulation avec 5 charges positives, 10 charges négatives et 500 lignes de champs](/assets/lignes-de-champs/image-2.png)
### Exécuter le programme

1. Avoir un environnement de développement C configuré.
2. Cloner le répertoire.

```bash
git clone https://github.com/ArianDervishaj/simulation-systeme-solaire.git
```
3. Compiler et lancer le projet.

```bash
cd simulation-lignes-de-champs
make main
./main $Nombre_de_charge_positives $Nombre_de_charge_négatives $Nombre_de_ligne_de_champs
```
### En voir plus

[Cliquer ici pour voir la répertoire sur github](https://github.com/ArianDervishaj/simulation-lignes-de-champs)

Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```