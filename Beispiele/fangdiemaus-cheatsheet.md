# Cheatsheet für Fangdiemaus

Wir werden uns zuerst mal anschauen, wie unser Spiel ausschauen wird.  Somit haben wir ein klares Ziel, um hinzuarbeiten.  
Danach fangen wir mit dem Spielprogrammieren vom Grund auf an.

---

## HTML-Skelett
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

## Spielfeld & Hintergrund
Wir brauchen nun das Spielfeld.

```html
<body>
  <div></div>
</body>
```

Man sieht nichts, wie wir gemerkt haben.  Dafür brauchen wir a bissl Styling mit CSS:

```html
<head>
  <style>
    #spielfeld {
      margin: 10px auto;
      height: 500px;
      width: 500px;
      background-color: #050f15;
    }
  </style>
</head>
<body>
  <div id="spielfeld"></div>
</body>
```

---

## Maus & Bewegung
Nun wollen wir die Maus hinzufügen!

```html
<body>
  <div id="spielfeld">
    <img src="maus.gif">
  </div>
</body>
```

Jetzt werden wir die erste coole Sache machen mit JS:  die Maus bewegen!  
Es gibt immer viele Lösungsmöglichkeiten für verschiedene Probleme, wenn ma programmiert.  
Wir zeigen euch eine Lösungsmöglichkeit:  die Maus _relativ_ zum Feld zu bewegen.  
Dafür muss ma dem Browser es Bescheid geben:

```html
<head>
  ...
  <style>
    ...
    #spielfeld {
      ...
      position: relative;
    }
    #maus {
      position: absolute;
    }
  </style>
</head>
<body>
  ...
    <img id="maus" src="maus.gif">
  ...
</body>
```

Somit definieren wir, dass die Position der Kindelemente von `#spielfeld` _relativ_ zum `#spielfeld` sind.  
Nun wollen wir die Maus bewegen.  
Um die Maus zu bewegen, müssen wir von JS auf die Maus _zugreifen_.  
Lass uns nun die Funktion zuerst schreiben, dann schauen wir was sie alles macht:

```html
<body>
  ...
  <script>
    let maus = document.getElementById("maus");
    let feld = document.getElementById("spielfeld");

    mausBewegen() {
      let maxX = feld.offsetWidth  - maus.offsetWidth;
      let maxY = feld.offsetHeight - maus.offsetHeight;
      let offsetLeft = Math.random() * maxX;
      let offsetTop  = Math.random() * maxY;
      maus.style.left = `${offsetLeft}px`;
      maus.style.top  = `${offsetTop}px`;
    }
  </script>
</body>
```

Die Koordinaten für solche absolute verschiebungen wie `style.left` und `style.top` sind von oben nach unten, links nach rechts.  
Deswegen bleibt die Maus in der oberen linken Ecke per default.  
Für `maxX` und `maxY` müssen wir deswegen die gesamte Länge und Breite von dem Feld nehmen, _minus_ die Länge und Breite von der Maus.  Somit vermeiden wir, dass die Maus _außerhalb_ vom Feld auftaucht.  
`offsetLeft` und `offsetTop` sind die wilkürliche, relative Verschiebungen von der Maus.  
Man stellt sich `maxX` und `maxY` als eine Gerade vor, und `Math.random` zeigt auf einem wilkürlichen Punkt drauf.  
Dann überschreiben wir `style.left` und `style.top` mithilfe von Template Strings.

Es passiert noch nichts, weil das nur eine _Definition_ der Funktion ist.  Wir müssen sie noch _aufrufen_.

```html
<script>
  ...
  mausBewegen();
</script>
```

Warum funktioniert es nicht wie erwartet?  Wir haben ja die gesamte Länge und Breite von der Maus von den gesamten Länge und Breite des Felds subtrahiert, und trotzdem taucht die Maus falsch auf...  
[Erklären wie Browsers ein HTML Dokument laden]  
Bilder dauern a bissl länger als andere Elemente.  Unser Problem liegt darin, dass wenn wir `mausBewegen()` aufrufen, hat das Bild noch nicht geladen!  Dann ist `maxX` bspw. als `feld.offsetWidth - 0` definiert, weil `maus` noch nicht geladen wurde!  
[Erklären wie Lifetime Hooks funktionieren]  
Wir müssen daher `mausBewegen()` erst nachdem die Maus vom Browser geladen wurde.

```html
<script>
  ...
  maus.onload = () => mausBewegen();
</script>
```

Das ist eine komische schreibweise für Funktionen...  Aber sie ist notwendig, wenn wir Funktionen überschreiben wollen.  
Hooks wie `onload` sind einfach Funktionen die zum richtigen Zeitpunkt vom Browser aufgerufen werden.

Jetzt wollen wir, dass die Maus nicht nur einmal, sondern _ständig_ sich bewegt.  
Dafür brauchen wir _zeitliche_ Veränderungen, und zwar so:

```html
<script>
  mausBewegen() {
    ...
    setTimeout(mausBewegen, 1000);
  }
</script>
```

Somit bewegt sich die Maus jede Sekunde neu, und wir sind erstmal mit der Bewegung der Maus fertig!

---

## Punkte
Jetzt wollen wir Punkte sammeln.  Bei Fangdiemaus kriegt man Punkte, wenn man auf die Maus klickt.  
Dafür nutzen wir wieder ein Lifetime Hook von der Maus:

```html
<script>
  ...
  maus.onclick = () => console.log("click!");
</script>
```

Mit `console.log()` Statements kann man etwas schnell überprüfen ob etwas funktionert oder nicht, bevor man Zeit an etwas investiert!

Jetzt brauchen wir Punkte hinzufügen:

```html
<script>
  let punkte = 0;
  ...
  maus.onclick = () => {
    punkte++;
    console.log(`Punkte: ${punkte}`);
  };
</script>
```

Aber viel besser wäre es, die Punkte auf der Seite zu sehen statt Konsole.

```html
<body>
  <div id="spielfeld">
    ...
  </div>
  <p>Punkte: <span id="punkteAusgabe"></span></p>

  <script>
    let punkteAusgabe = document.getElementById("punkteAusgabe");
    ...
    maus.onclick = () => {
      punkte++;
      punkteAusgabe.textContent = punkte;
    };
    punkteAusgabe.textContent = punkte;
  </script>
</body>
```

`<span>` ist wie ein `<div>`, aber es ist kein Block, sondern Inline.  Es ist nützlich für solche dynamischen Werten wie Punkte, die ständig geupdated werden.

Somit sind wir mit diesem Feature fertig! Nun können wir uns auf das Styling fokussieren.  
Lass uns dem Spieler einen Hinweis geben, dass er die Maus klicken darf, indem der Kursor anders ausschaut, wenn er über die Maus liegt.

```html
<style>
  ...
  #maus:hover {
    cursor: pointer;
  }
</style>
```

[Pseudoklassen erklären]  
Nun wollen wir den Text a bissl größer machen und zentralisieren.

```html
<head>
  ...
  <style>
    p {
      width: 500px;
      font-size: 25px;
      text-align: left;
    }
    #spielfeld {
      ~margin: 10px auto;~
      ...
    }
    ...
    #inhalt {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  </style>
</head>
<body>
  <div id="inhalt">
    <div id="spielfeld">
      ...
    <p>Punkte: <span id="punkteAusgabe"></span></p>
  </div>
  ...
</body>
```

[Flexbox erklären]

---

## Geschwindigkeit & Level
Das Spiel ist prinzipiell schon fertig.  So wie es ist, wird es sehr schnell fad, weil es gibt nichts Neues dazu.  
Wenn wir das Spiel a bissl lustiger machen wollen, brauchen wir bspw. eine Geschwindigkeit der Maus geben.  
Wir können sagen, dass die Maus 10% schneller wird pro Level.  
Lass uns so machen, dass es ein neues Level gibt pro 3 Treffer:

```html
<script>
  let level = 1;
  let treffer = 0;
  ...
  maus.onclick = () => {
    punkte++;
    punkteAusgabe.textContent = punkte;
    treffer++;
    if (treffer === 3) {
      treffer = 0;
      level++;
    }
  };
  ...
</script>
```

Wir wollen jetzt dem Spieler zeigen, in welchem Level er ist, genau wie wir mit den Punkten gemacht haben:

```html
<body>
  ...
    <p>Punkte: <span id="punkteAusgabe"></span></p>
    <p>Level: <span id="levelAusgabe"></span></p>
  </div>

  <script>
    let levelAusgabe = document.getElementById("levelAusgabe");
    ...
    maus.onclick = () => {
      ...
      if (treffer === 3) {
        treffer = 0;
        level++;
        levelAusgabe.textContent = level;
      }
    };
  </script>
</body>
```

Die Funktion für das `onclick` Hook macht momentan zwei verschiedene Dinge:
1. Die Punkte aktualisieren
2. Die Levels aktualisieren

Lass uns diese zwei verschiedene Konzepte in zwei verschiedene Funktionen trennen, damit der Code lesbarer wird:

```html
<script>
  ...

  function aktualisierePunkte() {
    punkte++;
    punkteAusgabe.textContent = punkte;
  }

  function aktualisiereLevel() {
    treffer++;
    if (treffer === 3) {
      treffer = 0;
      level++;
      levelAusgabe.textContent = level;
    }
  }

  maus.onload  = () => mausBewegen();
  maus.onclick = () => {
    aktualisierePunkte();
    aktualisiereLevel();
  };
  ...
</script>
```

Jetzt fügen wir die Geschwindigkeit hinzu.  Sie wird um 10% erhöht bei jedem neuen Level.

```html
<script>
  let geschwindigkeit = 1000;
  ...
  function mausBewegen() {
    ...
    setTimeout(mausBewegen, geschwindigkeit);
  }
  ...
  function aktualisiereLevel() {
    ...
      geschwindigkeit *= 0.9;
      levelAusgabe.textContent = level;
    }
  }
  ...
</script>
```

Jetzt, dass es bei jedem neuem Level schwieriger wird, um auf die Maus zu klicken, ist es nur fair, wenn man mehr Punkte kriegt je höher das Level.

```html
<script>
  let zusatz = 1;
  ...
  function aktualisierePunkte() {
    punkte += zusatz;
    ...
  }
  ...
  function aktualisiereLevel() {
    ...
      zusatz *= level;
      levelAusgabe.textContent = level;
    }
  }
  ...
</script>
```

---

## Extras
Hier sind wir fertig mit dem Spiel!  Nun bauen wir eine/mehrere der folgenden Features, um a bissl KI beim Programmieren ihnen zu zeigen:
1. Lebensystem: der Spieler hat 3 Leben;  wenn sie falsch klicken wird ein Leben weggenommen
2. Timersystem: jedes Level hat ein Timer;  wenn Timer aus, ist das Spiel aus
3. Styling: besseres Styling mit KI übernehmen
4. Animation: Animation der Mausbewegung geben, damit es schöner ausschaut
5. Highscore-System: Highscore werden mit localStorage gespeichert und ausgegeben
6. Soundsystem: ein richtiger Klick, ein falscher Klick, neues Level, und Gameover haben Geräusche

