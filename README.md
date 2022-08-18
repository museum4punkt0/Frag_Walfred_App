# Dialogbasierte Vermittlung der "Frag Walfred!" App


## Kurzbeschreibung
Für das Ozeaneum Stralsund wurde eine App entwickelt, die Navigation und Vermittlung ausgewogen miteinander verbindet. Besucher*innen werden durch Gamification-Elemente aktiviert und entlang digitaler Spots durch das Museum geführt. Begleitet und angeleitet werden die Besucher*innen vom digitalen Avatar Walfred, der auf dialogbasiertes Storytelling setzt.


## Entstehungskontext 
Die App "Frag Walfred!" ist entstanden im Verbundprojekt museum4punkt0 – Digitale Strategien für das Museum der Zukunft, Teilprojekt [(Digital) MEER erleben]. Das Projekt museum4punkt0 wird gefördert durch die Beauftragte der Bundesregierung für Kultur und Medien aufgrund eines Beschlusses des Deutschen Bundestages. Weitere Informationen: www.museum4punkt0.de


## Installation
Das Frontend der App wurde umgesetzt in einer Vite.js (`https://vitejs.dev/`) Umgebung.  Die Codebasis ist in Vue3 geschrieben.
Klonen Sie zuerst das Repository auf Ihr lokales System. Wechseln Sie in das Repository und installieren Sie mit `npm` benötigte Pakete.
Lokal ist das Projekt lauffähig mit `npm run dev` bzw. um zu builden mit `npm run build`. Im `dist`-Verzeichnis befindet sich der kompilierte Code, der auch auf einem Webserver platziert werden kann.
In unserem Fall zeigt eine Cordova-App auf das fertige `dist`-Verzeichnis auf einem Webserver.

## Benutzung  
Zu sehen ist der Aufbau der Chatlogik des Ozeaneum-Maskottchens "Walfred". 
Im enthaltenen Beispiel Code ist Walfreds Dialog hardcoded als JSON enthalten. 
In der Live App wird hier via einem CMS an eine Datenbank angebunden um das Museum dynamisch Content generieren zu lassen.


## Credits
UX, Design & Development by [Fluxguide Ausstellungssysteme](https://www.fluxguide.com/)

Concept by Deutsches Meeresmuseum and [Fluxguide Ausstellungssysteme](https://www.fluxguide.com/)

## Lizenz

MIT license

Copyright © 2022, [Fluxguide Ausstellungssysteme GmbH](https://www.fluxguide.com/) (Design, UX, Development) & Deutsches Meeresmuseum

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

The portions of the project that were developed by Fluxguide, 2022 as part of project are provided under the MIT license.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

Please refer to the license file which is included