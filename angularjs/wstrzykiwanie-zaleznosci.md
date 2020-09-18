---
title: Wstrzykiwanie zależności
category: angularjs
thumbnail: wstrzykiwanie-zaleznosci.png
published: 2016-12-18T00:00:00Z
---
Rozumienie mechanizmu wstrzykiwania zależności nie jest Ci niezbędne do stworzenia prostej aplikacji AngularJS, jednak na pewno da Ci dobre **spojrzenie na działanie całego frameworka**. Wstrzykiwanie zależności jest bardzo popularnym wzorcem projektowym, który **sprawdza się w aplikacjach modularnych bardzo dobrze**. Pisząc aplikację w AngularJS przede wszystkim dzielimy ją na dziesiątki modułów, kontrolerów oraz serwisów dlatego twórcy Angulara nie mogli nie zaimplementować tego mechanizmu.

<!--more-->

## Idea wstrzykiwania zależności

Rozważmy hipotetyczną sytuację, w której nasz obiekt wywołuję operację wypisywania logów do konsoli. W tym celu w dowolnej funkcji w naszym *$scope* wywołujemy metodę *Console.writeline()*. Wypuszczając aplikację produkcyjne logów musimy się pozbyć. Jeżeli nasza aplikacja ma kilka tysięcy linii kodu **problematyczne będzie usuwanie wszystkich wywołań tej funkcji** w kodzie.

Tutaj z pomocą przychodzi wstrzykiwanie zależności a więc tzw. odwrócenie kontroli. Główna idea wstrzykiwania zależności polega na tym, aby nie wiązać obiektu referencją do innego obiektu z którego korzysta na stałe. Zamiast tego lepiej przekazać **tę referencję np. przez konstruktor albo parametr**. Dzięki temu zmieniając obiekt wypisywania logów na ekran w jednym miejscu, zmienia się zachowanie całej aplikacji, która korzysta z tego obiektu. Mało tego, możemy wstrzykiwany obiekt podmienić i wstrzyknąć tam coś zupełnie innego.

## Wstrzykiwanie zależności do kontrolera

Aby wstrzyknąć zależność do kontrolera w AngularJS wystarczy dopisać nazwę obiektu do parametrów funkcji kontrolera. Właśnie w ten sposób we wszystkich poprzednich wpisach wstrzykiwany był obiekt *$scope*.:

{% include parts/postPicture.html page=page img="wstrzyknieciezaleznosci" ext="jpg" %}

W kolejnych artykułach do kontrolera trzeba będzie wstrzykiwać zależności do serwisów, aby zwiększyć jego funkcjonalność. W bardziej rozbudowanych aplikacjach często na liście zależności jest ich więcej niż 10.

## Adnotowanie wstrzykiwanych obiektów

Wypuszczając aplikację AngularJS produkcyjnie przeważnie minifikuje się jej kod. Po pierwsze aby zmniejszyć rozmiar pliku a po drugie aby kod zaciemnić. **Podczas minifikacji nazwy parametrów wszelkich funkcji są zamieniane na jednoliterowe odpowiedniki** (można ponieważ JavaScript nie jest silnie typowany). Z tego powodu twórcy AngularJS umożliwili opisywanie wstrzykiwanych obiektów.

Aby adnotować wstrzykiwanie zależności trzeba wypełnić tablicę *$inject* dostępną na obiekcie funkcji kontrolera. Wygląda to następująco:

	var app = angular.module("mainModule", []);
	
	app.controller('simpleCtrl', simpleController);
	
	simpleController.$inject = ['$scope'];
	function simpleController($scope) {
	    $scope.uzytkownik = {
	        login: '',
	        haslo: ''
	    };
	    $scope.logowanie = function() {
	        console.log($scope.uzytkownik.login 
	            + ' ' 
	            + $scope.uzytkownik.haslo);
	    }
	}

Ponieważ wartości tekstowe nie są minifikowane ani zaciemniane (rozwaliłoby to aplikację gdyby było inaczej) **adnotacje podawane są jako ciągi znaków.** Dzięki temu zamiast korzystać z nazwy *$scope* w parametrze kontrolera możemy zmienić jej nazwę na dowolnie inną:

	var app = angular.module("mainModule", []);
	
	app.controller('simpleCtrl', simpleController);
	
	simpleController.$inject = ['$scope'];
	function simpleController(lala) {
	    lala.uzytkownik = {
	        login: '',
	        haslo: ''
	    };
	    lala.logowanie = function() {
	        console.log(lala.uzytkownik.login 
	            + ' ' 
	            + lala.uzytkownik.haslo);
	    }
	}

Działanie aplikacji zupełnie się nie zmieniło.

Dodawanie adnotacji do wstrzykiwanych zależności jest standardem. Od teraz będzie się pojawiać w każdym kolejnym wpisie o Angularze. Aby kompilator był wstanie dopasować adnotację do zależności pozycje w tablicy adnotacji *$inject* **muszą się pokrywać z kolejnością parametrów funkcji** kontrolera. Jest to bardzo ważna zasada.

## Notacja inline

W wielu miejscach w internecie można też spotkać się z innym formatem dodawania adnotacji do wstrzykiwanych zależności umieszczonych &#8222;w jednej linii&#8221;. Aby osiągnąć taki cel, kontrolery muszą być definiowane za pomocą funkcji anonimowej. Wygląda to tak:

	// kontroler jako funkcja anonimowa
	app.controller('simpleCtrl', function($scope) {
	    $scope.uzytkownik = {
	        login: '',
	        haslo: ''
	    };
	});
	
	// kontroler jako funkcja anonimowa z adnotacjami
	app.controller('simpleCtrl', ['$scope', function($scope) {
	    $scope.uzytkownik = {
	        login: '',
	        haslo: ''
	    };
	}]);

Być może na pierwszy rzut oka nie jest to dla Ciebie zbyt przejrzysty zapis. Osobiście tez go nie używam. Funkcja anonimowa została poprzedzona nawiasami kwadratowymi i jest ostatnim elementem tablicy adnotacji.

## Korzyści płynące ze wstrzykiwania zależności

Mechanizm wstrzykiwania zależności jest typowym wzorcem odwrócenia kontroli (z ang. inversion of control). Przydaje się **przede wszystkim podczas pisania testów do kodu**. Używając serwisów, których głównym zadaniem jest przejmowanie zaawansowanej logiki aby nie zapychać kontrolerów, możemy wstrzykiwać ich różne instancje bez jakichkolwiek zmian kodu kontrolera.

Przykładowo, dla testów możemy zmienić zależność bazy danych i wstrzyknąć instancję obiektu, który zwraca statyczne dane JSON. Podobną strategię wstrzykiwania zależności realizuje np. framework Ninject na platformie ASP.NET MVC. Korzyści i zasady postępowania są takie same.

Bez stosowania wstrzykiwania zależności **kod programu jest kodem nietestowalnym**. Nie da się napisać testów dla kontrolera, który pobiera dane z zewnętrznego źródła. Jedyną możliwością jest odpięcie źródła danych (tzn. bazy danych) i zastąpienie ich danymi testowymi (fikcyjnymi).