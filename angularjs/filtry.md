---
title: Filtry
category: angularjs
thumbnail: filtry.png
published: 2017-03-26T00:00:00Z
---
Filtry w Angularze są bardzo przydatnym narzędziem. Pomagają formatować dane i są wygodnym sposobem na reużywalność kodu. Tworząc aplikację w AngularJS mamy dostępnych wiele gotowych filtrów. Jeżeli nie spełnią one naszych oczekiwań, możliwe jest dopisanie swoich. Ich główną zaletą w stosunku do zwykłych funkcji jest to, że możemy ich używać zarówno po stronie kontrolerów jak i w widokach. Sprytne operowanie filtrami potrafi znacznie skrócić kod aplikacji.

<!--more-->

## Użycie filtrów w widoku

Używanie wbudowanych filtrów jest bardzo proste. Filtr możemy dokleić do dowolnego wyrażenia, oraz niektórych dyrektyw. Przykładowe użycie wygląda następująco:  

	{% raw %}{{ wyrażenie | <nazwa_filtru> }}{% endraw %}

Po wywołaniu powyższego kodu podczas wywołania wyrażenia zostanie dla niego zastosowany filtr. Filtry łączymy za pomocą operatora pionowej kreski (tzw. _pipe_).  Filtry możemy ze sobą łączyć, są one wywoływane w kolejności od lewej do prawej:

	{% raw %}{{ wyrażenie | <filtr1> | <filtr2> | <filtr3> }}{% endraw %}

Co ciekawe, niektóre filtry mogą przyjmować argumenty. Przekazujemy je do filtra po dwukropku.

	{% raw %}{{ wyrażenie | <filtr1> | <filtr2>:<arg1> | <filtr3>:<arg1>:<arg2> }}{% endraw %}

## Lista dostępnych filtrów

Dla wygody programista AngularJS udostępnia wiele wbudowanych filtrów. Oto ich lista:

<table class="table table-striped table-condensed">
  <tr>
    <th style="text-align: center;">
      Filter
    </th>
    
    <th style="text-align: center;">
      Opis
    </th>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">currency</code>
    </td>
    
    <td>
      Formatuje liczby do postaci waluty
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">date</code>
    </td>
    
    <td>
      Formatuje datę według wzorca
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">filter</code>
    </td>
    
    <td>
      Filtruje elementy kolekcję
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">json</code>
    </td>
    
    <td>
      Wyświetla obiekt jako tekst JSON
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">limitTo</code>
    </td>
    
    <td>
      Przycina tablice lub tekst do określonej długości
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">lowercase</code>
    </td>
    
    <td>
      Zamienia tekst na małe litery
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">number</code>
    </td>
    
    <td>
      Formatuje liczbę według wzorca
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">orderBy</code>
    </td>
    
    <td>
      Sortuje po określonym parametrze
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code class="w3-codespan">uppercase</code>
    </td>
    
    <td>
      Zamienia tekst na duże litery
    </td>
  </tr>
</table>

## Przykład użycia currency

_Currency_ jest bardzo wygodnym filtrem do formatowania walut. Jako jeden z wielu filtrów korzysta z tzw. _locale_ z biblioteki _i18n_. Jeżeli dołączymy odpowiedni plik _locale_ do projektu (np. z konfiguracją polską) wtedy domyślną walutą filtra będzie polski Złoty. Jeżeli nie dołączymy ustawień lokalizacji domyślną walutą będzie amerykański Dolar. Oto przykładowy kod:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.kwota = 2500.25;
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>{{kwota | currency}}</p>
	    <p>{{kwota | currency:'PLN'}}</p>
	    <p>{{kwota | currency:''}} zł</p>
	    <p>{{ 1234 | currency:''}} zł
	</body>

Efekt działania jest następujący:

{% include parts/postPicture.html page=page img="currency" %}

Zauważ jaka duża jest różnorodność osiągniętych wyników. W pierwszym przypadku **wykorzystane zostały ustawienia domyślne**. W drugim przypadku zmieniliśmy symbol waluty przekazując wprost parametr _PLN_.

Domyślnie znak dolara wyświetlany jest przed kwotą. Chcąc wyświetlić polską walutę _zł_ za liczbą, możemy posłużyć się sztuczką. **Przekazujemy pusty parametr waluty** a interesującą nas walutę dopisujemy bezpośrednio poza wyrażeniem. Innym lepszym rozwiązaniem byłoby dołączenie odpowiedniej konfiguracji biblioteki _i18n_ przeznaczonej dla Polski.

## Przykład użycia filter

Filter o nazwie _filter_ służy do odfiltrowywania elementów kolekcji o określonych parametrach. Łączymy go z dyrektywą _ng-repeat_. Ma naprawdę sporo możliwości. Z jego pomocą łatwo można zrobić Angularową wyszukiwarkę. Oto przykładowy kod:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        
	        $scope.pracownicy = [
	            {id: 1, imie: "Karol", wiek: 24},
	            {id: 2, imie: "Kaja", wiek: 51},
	            {id: 3, imie: "Tomek", wiek: 22},
	            {id: 4, imie: "Justyna", wiek: 39},
	            {id: 5, imie: "Kazek", wiek: 8}
	        ]
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Szukaj: <input type="text" ng-model="szukaj"></p>
	    <table>
	        <tr ng-repeat="pracownik in pracownicy | filter : szukaj track by pracownik.id">
	            <td>{{pracownik.imie}}<td>
	            <td>{{pracownik.wiek}} lat</td>
	        </tr>
	    </table>
	</body>

Wynik działania jest następujący:

{% include parts/postPicture.html page=page img="angular-filter" ext="gif" %}

Pole wyszukiwania zbindowane jest bezpośrednio ze zmienną _szukaj_. Jej deklaracja nie była potrzebna w _$scope_ ponieważ odbędzie się automatycznie. Do dyrektywy _ng-repeat_ został natomiast dołączony filter. Filtruje on wartości właśnie po zmiennej _szukaj_. Zwróć uwagę, ponieważ filtry wykonują się od lewej do prawej dlatego _track by_ musiał zostać **dołączony za filtrem**, aby indeksować elementy przefiltrowane a nie te, które filtr wytnie.

Filtrowanie odbywa się po wszystkich atrybutach obiektów kolekcji _pracownicy_. Z tego powodu wpisanie wartości _5_ wyświetliło nam Kazimierza, który nie ma 5 lat &#8211; ma natomiast atrybut _id_ z taką wartością.

Możemy filtrować kolekcję tylko po jednej wartości przesyłając do _filter_ obiekt z nazwą atrybutu:

	<tr ng-repeat="pracownik in pracownicy | filter : {imie: szukaj} track by pracownik.id">
	    <td>{{pracownik.imie}}<td>
	    <td>{{pracownik.wiek}} lat</td>
	</tr>

Takie ustawienie spowoduje, że wyszukiwane będą tylko elementy, których atrybut _imie_ dopasowuje się do zmiennej _szukaj_.

## Przykład użycia json

Za pomocą filtra _json_ możemy w bardzo prosty sposób wyświetlić dowolny obiekt JavaScript podpięty do naszego _$scope_. Przykładowy kod:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        
	        $scope.osoba = {
	            imie: "Karol",
	            wiek: 24,
	            ulubionePiosenki: ['aaa', 'bbb', 'ccc']
	        }
	    }
	</script>
 
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <pre>{{osoba | json}}</pre>
	</body>

Efekt działania:

{% include parts/postPicture.html page=page img="angular-json" %}

## Przykład użycia orderBy

Filter _orderBy_ jest niezwykle pomocnym filtrem, używanym do budowania zaawansowanych list. Możemy zastosować go do dyrektywy _ng-repeat_ i bardzo dobrze łączy się z on z filter _filter_. Używając go możemy ustalić po jakim atrybucie przefiltrować kolekcję:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        
	        $scope.atrybutSortowania = 'id';
	        
	        $scope.osoby = [
	            {id: 1, imie: "Karol", wiek: 24},
	            {id: 2, imie: "Zenon", wiek: 12},
	            {id: 3, imie: "Maciej", wiek: 33},
	            {id: 4, imie: "Beata", wiek: 12}
	        ];
	        
	        $scope.sortuj = function(atrybut) {
	            $scope.atrybutSortowania = atrybut;
	        }
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table>
	        <tr>
	            <th ng-click="sortuj('id')">id</th>
	            <th ng-click="sortuj('imie')">imie</th>
	            <th ng-click="sortuj('wiek')">wiek</th>
	        </tr>
	        <tr ng-repeat="osoba in osoby | orderBy: atrybutSortowania track by osoba.id">
	            <td>{{osoba.id}}</td>
	            <td>{{osoba.imie}}</td>
	            <td>{{osoba.wiek}}</td>
	        </tr>
	    </table>
	</body>

Efekt działania:

{% include parts/postPicture.html page=page img="angular-orderBY" ext="gif" %}

Kod jest rozbudowany, ale bardzo prosty. Filter _orderBy_ przyjmuje jako parametr nazwę atrybutu kolekcji, po której ma sortować. Jeżeli parametr nie zostanie przekazany jako _string_ wtedy szuka on funkcji lub zmiennej podpiętej do _$scope_ o takiej nazwie. W naszym przypadku dodana została funkcja, ustawiająca parametr sortujący na właściwą wartość. Sortowanie odbywa się po parametrze sortującym.

Łącząc filtr _orderBy_ z filter _filter_ można uzyskać zaawansowaną tabelkę z możliwością sortowania kolumn i dynamicznego wyszukiwania. Dokładając do nich filter _limitTo_ można stworzyć paginację wartości np. po 50 elementów w tabelce na stronę.

## Użycie filtrów w kontrolerze

Filtrów Angularowych można użyć także w kodzie kontrolera aplikacji. W tym celu należy skorzystać z serwisu o nazwie _$filter_. Zwraca on instancję filtra o nazwie przekazanej jako argument. Aby móc użyć serwisu należy wstrzyknąć go jako zależność do kontrolera. Przykładowy kod wygląda następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope, $filter) {
	        
	        var kwota = 123456.60;
	        
	        $scope.kwotaFormatowana = $filter('currency')(kwota);
	        
	        $scope.kwotaFormatowana2 = $filter('currency')(kwota, '');
	        
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>{{kwotaFormatowana}}</p>
	    <p>{{kwotaFormatowana2}} zł</p>
	</body>

Efekt działania:

{% include parts/postPicture.html page=page img="angular-filter" %}

Oprócz wykorzystania filtru, mamy możliwość przesyłana do niego parametrów co widać w przykładzie wyżej. Wyczyszczony został domyślny symbol waluty a następnie ręcznie w widoku dopisana waluta _zł_.

## Własne filtry

Platforma AngularJS pozwala na pisanie własnych filtrów. Nie jest problemem napisanie własnej funkcji, która filtruje tablicę po określonej wartości atrybutu, jednak dzięki wprowadzeniu filtrów kod aplikacji Angular jest uporządkowany. Filtry znajdują się w filtrach, kontrolery w kontrolerach a komponenty w komponentach.

Z tego powodu zachęcam Cię, do trzymania funkcji odpowiedzialnych za filtrowanie tablic właśnie w postaci filtrów.

Aby utworzyć własny filter należy skorzystać z funkcji konstruującej wywołanej na instancji modułu. Przykładowo:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.filter('mojFilter', function() {
	        //cialo filtra
	    });
	</script>

Tak samo jak w przypadku kontrolerów, możemy stworzyć filter jako funkcję anonimową lub normalną:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.filter('mojFilter', function() {
	        //anonimowy
	    });
	    
	    app.filter('mojFilter2');
	    function mojFilter2() {
	        //normalny
	    }
	</script>

## Przykładowy własny filter

Stwórzmy własny filter Angularowy wyświetlający tylko ludzi wysokich. Ważną cechą filtra musi być jego skalowalność, a więc możliwość dynamicznej edycji wzrostu progowego. Kodem początkowym niech będzie następujący prosty kod:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope, $filter) {
	        $scope.osoby = [
	            {id: 1, imie: "Karol", wzrost: 180},
	            {id: 2, imie: "Arek", wzrost: 178},
	            {id: 3, imie: "Natalia", wzrost: 172},
	            {id: 4, imie: "Przemek", wzrost: 192},
	        ];
	    }
			
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table>
	        <tr ng-repeat="osoba in osoby track by osoba.id">
	            <td>{{osoba.imie}}</td>
	            <td>{{osoba.wzrost}}cm</td>
	        </tr>
	    </table>
	</body>

Aby dodać filter należy:

- zdefiniować nazwę filtra poprzez funkcję konstruującą _<moduł>.filter(<nazwa_filtra>)_.
- podpiąć do filtra **funkcję filtra**, która zwróci anonimową funckję filtrującą
- anonimowa metoda filtra powinna przyjmować parametry **kolekcji wejściowej** oraz **kryterium filtrowania**
- anonimowa metoda filtra powinna zwrócić przefiltrowaną tablicę wyników

Metoda filtra może w naszym wypadku wyglądać następująco:

	function wysocyLudzieFilter() {
	    return function(input, wzrostProgowy) {
	        var przefiltrowane = [];
	        
	        //jezeli ktos nie podał parametru to przyjmij 180cm
	        if (typeof wzrostProgowy === "undefined") {
	            wzrostProgowy = 180;
	        }
	        
	        for (var i = 0; i<input.length; i++) {
	            if (input[i].wzrost >= wzrostProgowy) { //jeżeli spełnia warunek wzrostu
	                przefiltrowane.push(input[i]) //to dodaj do nowej tablicy
	            }
	        }
	        
	        return przefiltrowane; //zwróć nową tablicę
	    }
	};

Pierwszym parametrem _input_ jest kolekcja wejściowa, zostanie ona przesłana do filtra automatycznie. Kolejne argumenty to dodatkowe parametry filtra, które możemy zdefiniować. W powyższym kodzie jest to _wzrostProgowy_ powyżej którego człowiek jest uznawany za wysokiego. Jeżeli nie zostanie on przekazany do filtra, wtedy przyjmijmy że jego wartość wynosi 180.

Po podpięciu funkcji do instancji filtra należy zastosować go w dyrektywie _ng-repeat_:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    app.filter('wysocyLudzieFilter', wysocyLudzieFilter);
	    
	    
	    function simpleController($scope, $filter) {
	        $scope.wzrostMin = 180;
	        
	        $scope.osoby = [
	            {id: 1, imie: "Karol", wzrost: 180},
	            {id: 2, imie: "Arek", wzrost: 178},
	            {id: 3, imie: "Natalia", wzrost: 172},
	            {id: 4, imie: "Przemek", wzrost: 192},
	        ];
	    }
	    
	function wysocyLudzieFilter() {
	    return function(input, wzrostProgowy) {
	        var przefiltrowane = [];
	        
	        //jezeli ktos nie podał parametru to przyjmij 180cm
	        if (typeof wzrostProgowy === "undefined") {
	            wzrostProgowy = 180;
	        }
	        
	        for (var i = 0; i<input.length; i++) {
	            if (input[i].wzrost >= wzrostProgowy) { //jeżeli spełnia warunek wzrostu
	                przefiltrowane.push(input[i]) //to dodaj do nowej tablicy
	            }
	        }
	        
	        return przefiltrowane; //zwróć nową tablicę
	    }
	};
			
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <input type="number" ng-model="wzrostMin" /> <br><br>
	    <table>
	        <tr ng-repeat="osoba in osoby | wysocyLudzieFilter:wzrostMin track by osoba.id">
	            <td>{{osoba.imie}}</td>
	            <td>{{osoba.wzrost}}cm</td>
	        </tr>
	    </table>
	</body>


Efekt działania:

{% include parts/postPicture.html page=page img="angular-wysocy" ext="gif" %}
