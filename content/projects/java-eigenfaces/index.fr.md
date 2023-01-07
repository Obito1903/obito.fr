---
title: "Java Eigenface"
date: 2022-04-11T14:05:11+01:00
draft: false
toc: false
description: "Reconnaissance faciale utilisant l'algorithme Eigenface, implémenté en Java."
repo: "https://github.com/Obito1903/java-eigenfaces"
---

A la fin de ma première année d'informatique à CY-Tech, mes camarades et moi avons
reçu un projet consistant à implémenter l'algorithme Eigenface en Java.
Un langage de programmation que nous avons appris tout au long de l'année.

Eigenface est l'un des algorithmes les plus basiques utilisés dans la
reconnaissance faciale. Il est assez simple à mettre en œuvre une fois compris.
Mais sa simplicité se fait au détriment de la précision.

{{< image src="eigenface.png" alt="Main page" position="center" style="border-radius: 8px;" >}}

## L'algorithme

L'algorithme Eigenface est basé sur une technique connue sous le nom
d'"Analyse en Composantes Principales", ou ACP. Il s'agit d'une méthode bien
connue, utilisée en Data Science pour simplifier l'analyse de données
à composantes multiples.

Elle permet de réduire au minimum le nombre de paramètres dans notre jeu de
données, en ne conservant que ceux qui sont pertinents pour notre analyse.

{{< image src="pca.png" alt="Main page" position="center" style="border-radius: 8px;" >}}

Cette technique nous donnera ce que l'on appelle des "valeurs propres" et
"vecteurs propres" (ou "visages propres"). En utilisant quelques astuces mathématiques,
nous pouvons entrer une image de la personne que nous voulons trouver dans notre
ensemble de données, et le résultat sera une liste des correspondances les plus
probables entre notre entrée et notre ensemble de visages.

Vous trouverez plus de détails sur l'algorithme sur les sites suivants : [Wikipedia](https://www.wikiwand.com/en/Eigenface), [GeeksForGeeks](https://www.geeksforgeeks.org/ml-face-recognition-using-eigenfaces-pca-algorithm/).
