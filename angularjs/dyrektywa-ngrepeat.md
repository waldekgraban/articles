---
title: Dyrektywa ngRepeat
category: angularjs
thumbnail: ng-repeat.png
published: 2016-12-16T00:00:00Z
---
W poprzednich artykułach poznaliśmy kilka podstawowych dyrektyw wbudowanych we framework AngularJS. Pierwsza z nich *ng-app* służy do inicjalizacji Agulara. Kolejna *ng-controller* służy do **podłączenia kontrolera w danym miejscu statycznego drzewa DOM**. Kontroler można podłączyć też automatycznie za pomocą konfiguracji routingu jednak o tym innym razem.  Ostatnią poznaną dyrektywą jest *ng-model* niezbędna aby uruchomić mechanizm dwustronnego bindowania pomiędzy widokiem a kontrolerem.

<!--more-->

## Dyrektywa ngRepeat

Bardzo użyteczną dyrektywą wbudowaną w AngularJS jest *ng-repeat*. Dzięki niej możemy iterować wartości dowolnej kolekcji i wyświetlać je po stronie widoku. Oczywiście w Angularze czy też JavaScripcie możemy używać pętli, jednak **nie działają one po stronie drzewa DOM dokumentu**. Dyrektywa *ngRepeat* natomiast tak. Podstawowy schemat użycia *ng-repeat* jest następujący:

	ng-repeat="<element> in <kolekcja>"

Na potrzeby testów podepnijmy do *$scope* kontrolera listę liczb. Możemy ją bardzo prosto wyświetlić w widoku następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.listaLiczb = [1, 2, 3, 4, 5, 6];
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <span ng-repeat="liczba in listaLiczb">{{liczba}}, </span>
	</body>

Wynikiem działania powyższego kodu jest lista liczb oddzielonych przecinkami. *Ng-repeat* jest dyrektywą, która musi zostać osadzona w jakimś elemencie HTML. **Następnie klonuje ten element tyle razy jaki jest rozmiar przeglądanej kolekcji**. Aby wyświetlić aktualnie iterowany element posługujemy się klamrowym operatorem wyrażenia. Oto podgląd kodu strony w narzędziach deweloperskich Chrome:

{% include parts/postPicture.html page=page img="ng-repeat-1" %}

Oprócz powtórzenia *span* kilka razy, został on obłożony dodatkowymi komentarzami, które są niezbędne do debugowania platformy AngularJS i nie powinny nas aktualnie interesować. Ciekawym jest fakt, że powyższy kod zostaje dynamicznie wygenerowany **dopiero po fazie ładowania i kompilowania** Angulara (czyli po załadowaniu głównego modułu, kontrolera i przetworzeniu dyrektyw drzewa DOM). Sprawdzając źródło strony widzimy tyle:

{% include parts/postPicture.html page=page img="ng-repeat-2" %}

Naszym oczom ukazuje się fragment kodu nieskompilowany. Niestety **tak samo widzą go roboty sieciowe**, dlatego właśnie AngularJS nie naddaje się do pisania stron, które mają być pozycjonowane. Wszelkie parsery, pająki i roboty nie są wstanie przetworzyć Angulara  i nie widzą treści. (update 2017.03.01 wyjątkiem na dzień dzisiejszy jest Google, który parsuje AngularJS).

### Iterowanie kolekcji obiektów

Oczywiście dyrektywa *ng-repeat* jest znacznie potężniejsza niż wynika z poprzedniego akapitu. Można nią bez problemowo iterować i wyświetlać kolekcje obiektów. Stwórzmy przykładową kolekcję osób i osadźmy ją w tabelce:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.osoby = [
	            {imie: "Karol", nazwisko: "Trybulec", wiek: 24},
	            {imie: "Arek", nazwisko: "Kowalski", wiek: 12},
	            {imie: "Jarek", nazwisko: "Nowacki", wiek: 51},
	            {imie: "Marek", nazwisko: "Zawadzki", wiek: 21},
	        ];
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="osoba in osoby">
	            <td>{{osoba.imie}}</td>
	            <td>{{osoba.nazwisko}}</td>
	            <td>{{osoba.wiek}}</td>
	        </tr>
	    </table>
	</body>

Wyliczenie działa bezproblemowo. Wyświetlone zostały wszystkie elementy i wrzucone do tabelki:

{% include parts/postPicture.html page=page img="repeat-obiekty" %}

### Iterowanie atrybutów obiektu

Dyrektywa *ng-repeat* została wyposażona w możliwość iterowania atrybutów danego obiektu. W tym celu należy posłużyć się składnią:

	ng-repeat="(<klucz>, <wartosc>) in <obiekt>"

Przykładowy kod wygląda następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.osoba = {
	            imie: "Karol",
	            nazwisko: "Trybulec",
	            kraj: "Polska",
	            plec: "mężczyzna"
	        };
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="(klucz, wartosc) in osoba">
	            <td>{{klucz}}</td>
	            <td>{{wartosc}}</td>
	        </tr>
	    </table>
	</body>

### Iterowanie zagnieżdżonych kolekcji

Dyrektywę *ng-repeat* można zagnieżdżać wewnątrz siebie, jeżeli istnieje taka potrzeba. Taka sytuacja może wystąpić np. w przypadku posiadania kolekcji kolekcji, lub listy obiektów które posiadają kolekcję jako atrybut. Przykładowy kod wygląda następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.uczniowie = [
	            {
	                imie: "Karol",
	                przedmioty: [
	                    {
	                        nazwa: "matematyka", 
	                        oceny: [5,4,3]
	                    },
	                    {
	                        nazwa: "fizyka", 
	                        oceny: [3,2,4,1]
	                    }
	                ]
	            },
	            {
	                imie: "Arek",
	                przedmioty: [
	                    {
	                        nazwa: "j. polski", 
	                        oceny: [5,1,2,4]
	                    }
	                ]
	            }
	        ];
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="osoba in uczniowie">
	            <td>{{osoba.imie}}</td>
	            <td>
	                <div ng-repeat="przedmiot in osoba.przedmioty">
	                    przedmiot: {{przedmiot.nazwa}} <br>
	                    oceny: <span ng-repeat="ocena in przedmiot.oceny">{{ocena}}</span>
	                    
	                    <br><br>
	                </div>
	            </td>
	        </tr>
	    </table>
	</body>

Efekt działania programu jest następujący:

{% include parts/postPicture.html page=page img="Untitled-1" %}

Przy tworzeniu takich konstrukcji trzeba kierować się zdrowym rozsądkiem. W niektórych przypadkach optymalniejsze może okazać się przetworzenie kolekcji w jezyku JavaScript i uproszenie jej, a następnie przesłanie jej do dyrektywy *ng-repeat*. Operacje na drzewie DOM dokumentu są kosztowne i w przypadku skomplikowanych szablonów mogą spowalniać działanie przeglądarki.

## $scope elementów kolekcji

Każda iteracja *ng-repeat* i wyświetlenie elementu tworzy dodatkowy osobny *$scope*, który jest dzieckiem *$scope* aktualnego kontrolera. Dlaczego tak jest wynika wprost z budowy Angularowych dyrektyw i zostanie wytłumaczone w dalszych częściach kursu. Podgląd w ngInspector wygląda następująco:

{% include parts/postPicture.html page=page img="nginspector-ngrepeat" %}

Każdy *$scope* pojedynczego elementu kolekcji dostaje swoje unikalne atrybuty, które mogą się nam bardzo przydać. Oto ich lista:

- *$index* (number) - indeks aktualnego elementu
- *$first* (boolean) - flaga informująca czy jest to pierwszy element (pierwsza iteracja na kolekcji)
- *$middle* (boolean) - flaga informująca czy jesteśmy równo w środku kolekcji
- *$last* (boolean) - flaga informująca czy jest to ostatni element (ostatnia iteracja na kolekcji)
- *$even* (boolean) - flaga informaująca czy jest to element parzysty
- *$odd* (boolean) - flaga informująca czy jest to element nieparzysty

Z wymienionych atrybutów możemy korzystać w każdym miejscu struktury HTML znajdującej się wewnątrz *ng-repeat*. Bardzo przydatny jest atrybtut *$index* oraz *$first* i *$last*. Często wykorzystywane są **do tworzenia domknięć pojemników, listingów i tabel** (np. gdy ostatni element tabeli musi mieć mocniejsze podkreślenie). Aby to osiągnąć trzeba skorzystać z instrukcji warunkowej o czym w będzie w następnej lekcji.

## Optymalizacja kolekcji

Kolekcja wyświetlona za pomocą dyrektywy *ng-repeat* zostaje osadzona w statycznym drzewie DOM dokumentu. Podmiana referencji lub nawet przeładowanie kolekcji spowoduje **usunięcie wszystkich jej elementów i wyświetlenie ich ponownie**. W przypadku kolekcji większej niż 500 elementów użytkownik odczuje półsekundowe spowolnienie przeglądarki.

Aby móc poruszać się po kolekcji obiektów Angular dodaje każdemu elementowi kolekcji atrybut *$$hashKey*. Stanowi on unikalny identyfikator dla każdego elementu w aplikacji. Jeżeli zmieniony zostanie element bez zmiany jego *$$hashKeya*, wtedy w dokumencie odświeżają się jedynie jego atrybuty (bez usunięcia i utworzenia elementu na nowo). Dzięki temu, **zmiana atrybutów pojedynczego elementu kolekcji jest szybka** i odbywa się w czasie rzeczywistym (zostaje on zlokalizowany po *$$hashKey*).

Niestety podmiana referencji kolekcji spowoduje zmianę hashy, dlatego mimo że obiekty są takie same (mają takie same atrybuty) struktura DOM dokumentu i tak zostanie przeładowana. Aby nie identyfikować elementów kolekcji po *$$hashKey* została do *ng-repeat* wprowadzona opcja *track by*. Ma ona następującą strukturę:

	ng-repeat="<element> in <kolekcja> track by <unikalny_atrybut>"

Przykładowy kod wygląda następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.przedmioty = [
	            {
	                id:1, nazwa: "matematyka", 
	            },
	            {
	                id: 2, nazwa: "fizyka", 
	            }
	        ];
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="przedmiot in przedmioty">
	            <td>{{przedmiot.nazwa}}</td>
	        </tr>
	    </table>
	</body>

Gdy podejrzymy *$scope* aplikacji, zobaczymy że obiekty na liście posiadają unikalny hash doklejony przez Angulara:

{% include parts/postPicture.html page=page img="ng-repeat-4" %}

Ponieważ elementy kolekcji posiadają unikalny atrybut *id* możemy wykorzystać go zamiast unikalnego hasha. Kod po modyfikacji będzie wyglądał następująco:

	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="przedmiot in przedmioty track by przedmiot.id">
	            <td>{{przedmiot.nazwa}}</td>
	        </tr>
	    </table>
	</body>

Podglądając *$scope* aplikacji przekonamy się, że atrybuty *$$hashKey* zniknęły. Dzięki temu zabiegowi, przeładowanie kolekcji czy nawet podmiana jej referencji **nie spowoduje skasowania wszystkich elementów w strukturze DOM dokumentu**. Angular znajdzie elementy, których atrybuty się zmieniły, a następnie zidentyfikuje je po unikalnym identyfikatorze i uaktualni. Korzystanie z opcji *track by* potrafi znacząco przyśpieszyć działanie aplikacji wykorzystujących kolekcje kilku a nawet kilkunastokrotnie.

### Kolekcje typów prostych

Bardzo niebezpieczne jest wyświetlanie kolekcji złożonych z typów prostych. Atrybut *$$hashKey* może być dodany tylko do obiektu. Iterując kolekcję liczb (np. listę ocen) Angular nie może identyfikować poszczególnych elementów po unikalnym hashu. Z tego powodu, jeżeli kolekcja typów prostych posiada dwie takie same wartości naszym oczom ukarze się błąd:

*Error: [ngRepeat:dupes] Duplicates in a repeater are not allowed. Use ‚track by’ expression to specify unique keys. Repeater: ocena in oceny, Duplicate key: number:1, Duplicate value: 1*

Przykładowym błędnym kodem jest następująca lista ocen złożona z typów prostych:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.oceny = [1,2,3,4,5,1];
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="ocena in oceny">
	            <td>ocena</td>
	        </tr>
	    </table>
	</body>

Rozwiązaniem tego problemu jest **nie wyświetlanie kolekcji typów prostych** za pomocą *ng-repeat*. Wychodząc na przeciw wymaganiom programistów w jednej z wersji Angulara została wprowadzona możliwość indeksowania kolekcji posługując się ich numerem porządkowym w kolekcji (czyli indeksem kolekcji). Kod wykorzystujący indeksowanie po numerze kolekcji wygląda następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.oceny = [1,2,3,4,5,1];
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <table border="1" cellpadding="10">
	        <tr ng-repeat="ocena in oceny track by $index">
	            <td>ocena</td>
	        </tr>
	    </table>
	</body>

Powyższy kod nie spowoduje wyświetlenia błędu. Mimo tego, lepszym rozwiązaniem jest obudowanie kolekcji typów prostych w obiekt i atrybut (do obiektu możliwe jest doklejenie hasha).