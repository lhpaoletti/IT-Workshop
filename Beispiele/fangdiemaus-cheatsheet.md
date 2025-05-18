# Cheatsheet für Fangdiemaus

Wir werden uns zuerst mal anschauen, wie unser Spiel ausschauen wird.  Somit haben wir ein klares Ziel, um hinzuarbeiten.  
Danache fangen wir mit dem Spielprogrammieren vom Grund auf an.

---

## 1. HTML-Skelett
Wir bauen zuerst mal das Skelett von der Seite.  HTML Dateien haben ein bestimmtes Format, wie schon vorhin erwähnt:

```html
<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="utf-8">
  <title>Fang die Maus!</title>
</head>
<body>
</body>
</html>
```

---

## 2. Spielfeld & Hintergrund
Wir brauchen nun das Spielfeld.

```html
<body>
  <div></div>
</body>
```

Man sieht nichts, wie wir gemerkt haben.  Dafür brauchen wir a bissl Styling mit CSS:

```html
<head>
  #spielfeld {
    margin: 10px auto;
    height: 500px;
    width: 500px;
    background-color: #050f15;
  }
</head>
<body>
  <div id="spielfeld"></div>
</body>
```

Wir brauchen nun auch a bissl Styling für den Hintergrund, damit es a bissl besser ausschaut als nur Weiß:

```html
body {
  background-color: #222;
}
```

