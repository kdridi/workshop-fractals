# workshop-fractals

## Installation des outils

### Installer p5.js

Pour installer p5.js, il faut télécharger le fichier zip sur le site officiel : https://p5js.org/download/

Puis cliquer sur le lien **p5.js complete** pour télécharger le fichier zip.

Ensuite, il faut décompresser le fichier zip dans le dossier de votre choix.

Nous allons ensuite ouvrir ce répertoire avec vscode.

---

### Installer Live Server

Pour installer Live Server, il faut afficher l'installeur de package vscode en appuyant sur **Ctrl+Shift+X**.

Ensuite, il faut taper **Live Server** dans la barre de recherche.

Enfin, il faut cliquer sur le bouton **Install**.

Ce plugin va permettre de lancer un serveur web local pour pouvoir tester notre code en temps réel.

---

## Mandelbrot : Les différentes étapes

### Initialisation de l'écran

Nous allons commencer par éditer le fichier **sketch.js** afin de créer une scène en assignant une couleur aléatoire à chaque pixel de l'écran.

```js
// La fonction setup est appelée une seule fois au démarrage du programme
function setup() {
	createCanvas(windowWidth, windowHeight)
	pixelDensity(1)
}

// La fonction draw est appelée à chaque frame
function draw() {
	background(0)

	// on charget la variable pixels qui contient les données de l'écran
	loadPixels()
	for (let py = 0; py < height; py++) {
		for (let px = 0; px < width; px++) {
			const i = 4 * (px + width * py)

			const r = Math.random()
			const g = Math.random()
			const b = Math.random()

			pixels[i + 0] = r * 255
			pixels[i + 1] = g * 255
			pixels[i + 2] = b * 255
			pixels[i + 3] = 255
		}
	}
	// on met à jour les données de l'écran
	updatePixels()
}

// La fonction windowResized est appelée à chaque fois que la taille de la fenêtre change
function windowResized() {
	// On redimensionne le canvas
	resizeCanvas(windowWidth, windowHeight)
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/qByeWoR)

---

### Gestion des couleurs dans un repère orthonormé

Nous allons maintenant modifier notre code afin de pouvoir gérer les couleurs dans un repère orthonormé. Nous considérons que le repère est centré sur l'origine et que les coordonnées sont comprises entre -1 et 1.

Pour cela, nous allons:

- Créer une fonction **cqlculatePosition()** qui va calculer la position d'un pixel dans le repère orthonormé

```js
function calculatePosition(x, y) {
	// TODO : transforme la position du pixel de [0, width][0, height] à [-1, +1][+1, -1]
	// INDICE : utilisez la fonction map de p5js
	return { x, y }
}
```

- Créer la fonction **calculateColor()** afin de calculer la couleur d'un pixel dans le repère orthonormé. Dans un premier temps, nous allons colorer l'écran en noir sauf la zone 0 < x < 1, 0 < y < 1 qui sera colorée en rouge.

```js
function calculateColor(x, y) {
	const color = { r: 0, g: 0, b: 0 }
	// TODO : colorer la zone 0 < x < 1, 0 < y < 1 en rouge
	return color
}
```

- Modifier la fonction **draw()** afin d'utiliser les fonctions **calculatePosition()** et **calculateColor()**.

```js
const { x, y } = calculatePosition(px, py)
const { r, g, b } = calculateColor(x, y)

const i = 4 * (px + width * py)
pixels[i + 0] = r * 255
pixels[i + 1] = g * 255
pixels[i + 2] = b * 255
pixels[i + 3] = 255
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/GRBVKdd)

---

### Affichage de l'ensemble de Mandelbrot

Comme le décrit Wikipédia, l'ensemble de Mandelbrot est l'ensemble des nombres complexes c pour lesquels la suite définie par:

- z(0) = 0
- z(n+1) = z(n)² + c

converge.

Nous considérerons que chaque pixel de l'écran correspond à un nombre complexe c. Nous allons donc calculer la suite pour chaque pixel et colorer le pixel en fonction de la valeur de la suite.

Si la suite diverge alors nous colorons le pixel en noir. Si la suite converge alors nous colorons le pixel en fonction du nombre d'itérations nécessaires pour que la suite converge.

Nous allons:

- Créer la fonction **calculateIterations(x, y, max)** qui va calculer le nombre d'itérations nécessaires pour que la suite diverge.

```js
function calculateIterations(x, y, max) {
	// TODO : calculer le nombre d'itérations nécessaires pour que la suite diverge
	// INDICE : La suite diverge si |z(n)| > 2
}
```

- Modifier la fonction **calculateColor()** afin d'utiliser la fonction **calculateIterations()**.

```js
function calculateColor(x, y) {
	const color = { r: 0, g: 0, b: 0 }
	const max = 30
	const count = calculateIterations(x, y, max)
	color.g = count / max
	return color
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/VwBoZGr)

---

### Recentrer l'ensemble de Mandelbrot

Nous allons mettre en place un système de caméra zoomable afin de pouvoir zoomer sur l'ensemble de Mandelbrot. Dans un premier temps, nous allons décaler de 0.5 l'ensemble de Mandelbrot sur l'axe des abscisses. Et nous allons également dézoomer avec un facteur 2.

Nous allons:

- Créer l'objet **camera** qui va contenir les informations de la caméra.

```js
const camera = {
	x: -0.5,
	y: 0,
	z: 2.0,
}
```

- Modifier la fonction **calculatePosition()** afin de prendre en compte la caméra.

```js
function calculatePosition(px, py) {
	const ww = width / Math.min(width, height)
	const wh = height / Math.min(width, height)

	const x = 0 // TODO : calculer la position x du pixel dans le repère orthonormé
	const y = 0 // TODO : calculer la position y du pixel dans le repère orthonormé

	return { x, y }
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/ExpqxKX)

---

### Navigation dans l'ensemble de Mandelbrot

Nous allons mettre en place un système de navigation dans l'ensemble de Mandelbrot. Nous allons pouvoir modifier le zoom et la qualité de rendu à l'aide du clavier et recentrer la caméra à l'aide de click de souris.

Nous allons:

- Créer la fonction **keyPressed()** afin de pouvoir modifier le zoom et la qualité de rendu à l'aide du clavier.

```js
function keyPressed() {
	if (false) {
	} else if (key === 'r') {
		camera.x = -0.5
		camera.y = 0
		camera.z = 1.5
		camera.iterations = 30
	} else if (key === 'z') {
		// TODO : update Zoom
	} else if (key === 'Z') {
		// TODO : update Zoom
	} else if (key === 'a') {
		// TODO : update Iterations
	} else if (key === 's') {
		// TODO : update Iterations
	}
}
```

- Créer la fonction **mouseClicked()** pour pouvoir recentrer la caméra

```js
function mouseClicked() {
	// TODO : update camera.x/y from mouseX/Y
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/XWBvWMg)

---

### Affichage de l'ensemble de Julia

Nous allons maintenant afficher l'ensemble de Julia. Pour cela, nous allons utiliser la même fonction **calculateIterations()** que pour l'ensemble de Mandelbrot.

Cette fois-ci les coordonnées du pixel correspondrons à la valeur de z(0) de la suite. La valeur de c sera fixée à 0.285 + 0.01i.

La valeur zn servira à calculer la couleur du pixel.

```js
function calculateColor(x, y) {
	const color = { r: 0, g: 0, b: 0 }
	const max = camera.iterations
	const [count, za, zb] = calculateIterations(x, y, max)
	color.g = count / max
	color.r = za
	color.b = zb
	return color
}

function calculateIterations(x, y, max) {
	let iterations = 0

	const ca = 0.285
	const cb = 0.01

	let za = x
	let zb = y

	for (let n = 0; n < max; n++) {
		const a = za * za - zb * zb + ca
		const b = za * zb + za * zb + cb

		za = a
		zb = b

		if (za * za + zb * zb > 2 * 2) break

		iterations += 1
	}

	return [iterations, za, zb]
}
```

[Voir sur CodePen](https://codepen.io/kdridi/embed/poZMoQe)

---
