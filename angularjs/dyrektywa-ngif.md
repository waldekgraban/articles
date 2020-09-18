---
title: Dyrektywa ngIf
category: angularjs
thumbnail: ngshow-ngif.png
published: 2016-12-17T00:00:00Z
---
Programowanie we wszystkich językach opiera się na budowaniu przepływu programu za pomocą instrukcji warunkowych. W AngularJS po stronie kontrolera używamy **dokładnie takich samych konstrukcji jak w języku JavaScript**. Oznacza to, że bez żadnych problemów możemy korzystać z *if*, *switch*, operatorów trójargumentowych i wszystkiego co wymyślimy. Operatory warunkowe dostępne w kontrolerach nie są dostępne w warstwie widoku (a więc pliku HTML). Zamiast tego Twórcy AngularJS przygotowali dla programistów specjalną dyrektywę *ng-if*.

<!--more-->

## Dyrektywa ngIf

Aby użyć dyrektywy *ng-if* należy osadzić ją w dowolnym atrybucie HTML. **Jej wartością może być dowolne wyrażenie**. Przykładowo następujące porównanie nigdy nie zostanie spełnione:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.imie = "";
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <div ng-if="1 == 5">
	        Niewidoczny element
	    </div>
	</body>

Podglądając wygenerowany kod można zauważyć, że **jeżeli warunek dyrektywy nie jest spełniony to nie zostanie wygenerowane statyczne drzewo DOM** dla elementu:

{% include parts/postPicture.html page=page img="ng-if" %}

Jest to bardzo ważny wniosek. Używając dyrektywy *ng-if* należy pamiętać, że za każdym razem przetworzone zostanie statyczne drzewo DOM dokumentu. W przypadku kiedy warunek nie jest spełniony, **zostaje wycięta cała treść strony zagnieżdżona** w dyrektywie.

Tak samo jak w przypadku dyrektywy *ng-repeat* elementy zagnieżdżone mają podpięty swój osobny *$scope*.

Przykład użycia dyrektywy *ng-if* może wyglądać następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.osoba = {
	            imie: "",
	            nazwisko: ""
	        };
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Imię: <input ng-model="osoba.imie" /></p>
	    <p>Nazwisko: <input ng-model="osoba.nazwisko" /></p>
	    
	    <p><label>Czy pracujesz? <input type="checkbox" ng-model="osoba.czyPracuje" /></label></p>
	    <p ng-if="osoba.czyPracuje">
	        Podaj nazwę firmy: <input ng-model="osoba.firma" />
	    </p>
	    
	    <br>
	    <p>Nazywasz się <b>{{osoba.imie}} {{osoba.nazwisko}}</b>.<p>
	    <p ng-if="osoba.czyPracuje && osoba.firma.length">Pracujesz w <b>{{osoba.firma}}</b>.<p>
	</body>

Wnioski na temat *ng-if* są następujące:

- jeżeli wartość wyrażenia dyrektywy nie jest spełniona, **zmodyfikowane zostaje statyczne drzewo DOM dokumentu**.
- dla elementów znajdujących się w dyrektywie zostaje utworzony osobny *$scope* (zagnieżdżony względem *$scope* kontrolera)

Nadużywanie *ng-if* może znacznie spowolnić ładowanie strony. Uzależniając wyświetlanie danych elementów od warunków okazuje się, że nasze *$scopy* stają się bardzo zagnieżdżone i nieuporządkowane. Z tego powodu powstała dyrektywa *ng-show* oraz *ng-hide*.

## Dyrektywy ngShow i ngHide

Twórcy AngularJS zauważyli, że nie za każdym razem kiedy zależy nam na ukryciu danego elementu chcemy od razu przebudowywać drzewo DOM co wiąże się ze spadkami wydajności.

Dyrektywy *ng-show* oraz *ng-hide* działają w ten sam sposób, tzn. przyjmują jako parametr wyrażenie, jednak **ukrywanie elementów odbywa się poprzez dodanie do nich atrybutu CSS** o nazwie *display: none !important*. **W większości przypadków lepszym wyborem będzie użycie właśnie wyżej wymienionych dyrektyw**. Bardzo dobrze sprawdzają się do budowania elementów typu pokaż/ukryj (z ang. toggle). Tylko nieznacznie obciążają pamięć.

Przykładowy kod:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.toggle = false;
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <h4>Lista zakupów <a href="#" ng-click="toggle = !toggle">[pokaż/ukryj]</a></h4>
	    <ul ng-show="toggle">
	        <li>3 bułki</li>
	        <li>majonez</li>
	        <li>czekolada</li>
	    </ul>
	</body>

W przykładzie użyte zostało zdarzenie *ng-click*. Nie musisz się nim przejmować, jego jedynym zadaniem jest nadawanie wartości zmiennej *toggle* na przeciwną względem aktualnej. Zamiast podpięcia *ng-click* możliwe było skorzystanie np. z pola typu *checkbox* z podpiętym modelem. Zdarzenia zostaną opisane w kolejnym artykule. Działanie prezentuje się następująco:
 
{% include parts/postPicture.html page=page img="ng-show" ext="gif" %}

Na załączonym gifie widać wyraźnie, że do ukrycia elementu zostaje użyta klasa *ng-hide*. Drzewo DOM dokumentu pozostaje bez zmian, nie zostają też utworzone nowe *$scope*. Dyrektywa *ng-hide* jest przeciwieństwem *ng-show* i można używać ich zamiennie. Obydwoma uzyskujemy ten sam efekt, ewentualną różnicą jest dodanie negacji przed wyrażeniem.

## Kiedy używać ngShow a kiedy ngIf

Dyrektywy *ng-show* używamy w większości wypadków, szczególnie tam gdzie zależy nam aby na chwilę ukryć pewne fragmentu strony w zależności od stanu aplikacji.

*Ng-if* przydaje się gdy szacujemy, że fragment drzewa HTML który ukrywa nie zostanie pokazany w dużej ilości przypadków. Nie ma sensu generować 100 linijek kodu trzymanych w pamięci przeglądarki i inicjalizujących swoje modele jeżeli dana funkcjonalność używana jest przez 20% odwiedzających użytkowników. Dodatkowo, *ng-if* przydaje się podczas walidacji formularzy, co zostanie opisane w innym artykule. Pokrótce chodzi o to, że ukrywając *input* który jest wymagany, nie zostaje on zaliczony do elementów walidowanego formularza. *Ng-show* nie daje takich efektów.