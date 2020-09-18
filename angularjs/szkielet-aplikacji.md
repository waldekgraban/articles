---
title: Szkielet aplikacji
category: angularjs
thumbnail: szkielet-aplikacji.png
published: 2016-12-15T00:00:00Z
---
AngularJS to platforma programistyczna w całości napisana w języku JavaScript. Aby móc tworzyć aplikację opartą o AngularJS niezbędne jest zaimportowanie pliku źródłowego Angulara. Tak jak większość plików JavaScriptowych **można go doczytywać lokalnie lub dynamicznie z zewnętrznego serwera**. Informacje o wszystkich wersjach znajdziesz wchodząc na stronę [www.angularjs.org](https://angularjs.org/).

<!--more-->

## Wersje AngularJS

Na dzień dzisiejszy wersje Angular prezentują się następująco:

- wersja 1.6 &#8211; testowa
- **wersja 1.5 &#8211; stabilna produkcyjna**
- wersja 1.2 &#8211; stara wersja wspierająca m.in. IE8, nie polecana do zastosowań produkcyjnych

Wersja 1.2 jest kopią starej wersji AngularJS, która jeszcze daje radę integrować się ze starymi przeglądarkami takimi jak IE8. Są na nią nanoszone wszelkie poprawki bezpieczeństwa bez żadnych dodatkowych ulepszeń.

Tworząc aplikację komercyjną/produkcyjną używanie wersji testowej jest ryzykowane, ponieważ nie ma pewności czy zmiany jakie w niej zachodzą zostaną ostatecznie włączone do głównej stabilnej wersji frameworka. W kursie nie musimy się tym martwić.

## Pobieranie AngularJS

Pisząc projekt AngularJS z prawdziwego zdarzenia powinniśmy posługiwać się **narzędziami służącymi do automatyzacji pracy takimi jak NodeJS czy Grunt**. Są to narzędzia znane każdemu programiście front-endu. Pomagają one przebudowywać projekt, instalować i usuwać dodatkowe biblioteki takie jak AngularJS, dbają o posiadanie najnowszych wersji bibliotek a także zaciemniają kod (obfuskacja) i minifikują go. Na ten temat pojawi się na pewno osobny wpis.

W niniejszym kursie będę używał AngularJS wczytywanego dynamicznie z zewnętrznego serwera z pod adresu, jednak nic nie stoi na przeszkodzie aby pobrać AngularJS poprzez narzędzie *npm* dołączone do NodeJS.

<https://ajax.googleapis.com/ajax/libs/angularjs/1.6.0/angular.js>

## Niezbędne narzędzia dla programisty AngularJS

Aby zacząć tworzyć aplikacje oparte o platformę AngularJS potrzebujemy trzech podstawowych rzeczy:

- przeglądarki Google Chrome
- wtyczki ngInspector
- pliku **.html* z podstawowym szkieletem strony z dołączonym skryptem AngularJS (link wyżej)

Przez długi czas programowałem w AngularJS używając przeglądarki Firefox, jednak jest to niewygodne. Google niesamowicie rozbudował możliwości Chrome szczególnie jeśli chodzi o narzędzia dla programistów. Chrome posiada **lepsze możliwości debugowania kodu** oraz **wbudowany klient RESTa**.

Momentem przełomowym kiedy przestałem używać przeglądarki Firefox do programowania było wprowadzenie przez Mozillę zabezpieczenia blokującego używanie nieautoryzowanych wtyczek. Przez to, nie byłem wstanie używać dodatku ngInspector dla Firefoxa, a jest to narzędzie nieocenione.

NgInspector jest prostym narzędziem pozwalającym przeglądać modele kontrolerów aplikacji AngularJS. Co prawda, wartości zmiennych można wyświetlić w konsoli deweloperskiej jednak jest to operacja nieznacznie dłuższa, a programista musi znać nazwę i lokalizację zmiennej, którą chce wyświetlić. Dzięki używaniu ngInspectora **mamy podgląd na całą strukturę modelu ze wszystkimi zagnieżdżeniami** i wartościami.

## Podstawowy szkielet aplikacji

Oto treść pliku, która może być używana przez Ciebie jako szkielet do pisania pierwszych aplikacji w AngularJS:

	<!DOCTYPE html>
	<html lang="pl-PL">

	<head>
	    <meta http-equiv="content-type" content="text/html; charset=utf-8" />
	    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.0/angular.js"></script>
	</head>

	<body ng-app>
	    Angular start {% raw %}{{3+2}}{% endraw %}
	</body>
			
	</html>

Po uruchomieniu pliku w przeglądarce Chrome na ekranie powinien pojawić się napis &#8222;Angular start 5&#8221;. Jeżeli tak się nie stało, informacji o błędach szukaj w konsoli deweloperskiej. Wyświetlają się tam wszystkie błędy aplikacji.

Do tagu *<body>* została dołączona dyrektywa *ng-app*. AngularJS parsując drzewo DOM dokumentu szuka jej ponieważ jest to dyrektywa startowa dla aplikacji angularowych. W momencie jej znalezienia zaczyna działać Angular. Podwójne nawiasy klamrowe są tzw. wyrażeniem i zostają automatycznie obliczone, dlatego zwracają wynik dodawania.

W przypadku braku dyrektywy *ng-app* nawiasy klamrowe nie zostaną potraktowane jako wyrażenie AngularJS i wyświetlą się jako zwykł tekst.