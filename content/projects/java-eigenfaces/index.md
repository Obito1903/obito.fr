---
title: "Java Eigenface"
date: 2022-04-11T14:05:11+01:00
draft: false
toc: false
description: "Facial recognotion using the Eigenface algorithm, implemented in Java."
repo: "https://github.com/Obito1903/java-eigenfaces"
---

At the end of my first year in computer science at CY-Tech, my classmates and
I were given a project consisting of implementing the Eigenface algorithm in Java.
 A programming language that we've learned through the year.

Eigenface is one of the most basic algorithm used in facial recognition.
It is pretty straightforward to implement once understood. But its simplicity
come at the cost of precision.

{{< image src="eigenface.png" alt="Main page" position="center" style="border-radius: 8px;" >}}

## The Algorithm

The Eigenface algorithm is based on a technique known as "Principal component analysis", or PCA for short. It's a well-known method used in data science to simplify multi-component data analysis.

It allows for reducing the number of parameters, in our dataset to a minimum, keeping only the relevant ones for us to analyze.

{{< image src="pca.png" alt="Main page" position="center" style="border-radius: 8px;" >}}

The technique will give us what's known as "eigenvalues" and "eigenvectors" (or "eigenfaces"). And using and some math trickery, we can input an image of the person we want to find in our dataset, and the result will be a list of the most probable matches between, our input and our dataset.

More details on the algorithm can be found in sites like [Wikipedia](https://www.wikiwand.com/en/Eigenface) or [GeeksForGeeks](https://www.geeksforgeeks.org/ml-face-recognition-using-eigenfaces-pca-algorithm/).
