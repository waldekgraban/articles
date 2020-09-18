---
title: Scope i wyrażenia
category: angularjs
thumbnail: scope.png
published: 2016-12-16T00:00:00Z
---
Zrozumienie tego artykułu jest kluczowe aby móc tworzyć aplikacje w AngularJS. Do tej pory powinieneś wiedzieć czym jest Angular, umieć utworzyć moduł główny aplikacji i podpiąć do niego kontroler. Zdefiniowany przez nas moduł oraz kontroler zostają załadowane do pamięci w fazie ładowania. Następnie w fazie kompilacji AngularJS analizuje statyczne drzewo DOM dokumentu zbudowane ze znaczników HTML. W momencie natrafienia na dyrektywy *ng-application* oraz *ng-controller* zostaje utworzony nowy *$scope*.

<!--more-->

## Czym jest $scope

W większości języków programowania **możemy definiować własne zasięgi zmiennych**, w których są one widoczne. W C++ każda funkcja posiada swój własny zasięg ograniczony przez nawiasy klamrowe. Nic nie stoi na przeszkodzie aby w środku funkcji zagnieździć kolejne nawiasy klamrowe definiujące nowy zakres widoczności.

*$scope* w AngularJS jest niczym innym jak **zasięgiem widoczności danego kontrolera** i tworzy on warstwę widok-model wzorca MVVM. Każdy kontroler posiada swój unikalny *$scope* do którego przypięte są funkcje oraz zmienne widoczne w warstwie widoku. *$scope* jest jedynym elementem, który jest w stanie wystawić dane dla widoku i komunikować się z widokiem.

W czystym JavaScripcie **zasięgi zmiennych tworzone były poprzez funkcje anonimowe**, w AngularJS posiadamy kontrolery, które także są funkcjami.

*$scope* zostaje utworzony **automatycznie dla każdego kontrolera**, który zostanie zainicjalizowany dyrektywą *ng-controller* (lecz nie tylko o czym później). Oprócz tego, podczas inicjalizacji aplikacji za pomocą dyrektywy *ng-app* zostaje utworzony tzw. *$rootScope*, a więc zasięg bazowy dla wszystkich innych.

W przypadku zagnieżdżonych kontrolerów zasięgi także zostają zagnieżdżone. Każdy *$scope* posiada referencję do dziecka *child-scope* oraz do rodzica *parent-scope*. Rodzicem nadrzętnym jest *$rootScope*. Nie jest to informacja, która może Ci się przydać na początku kursu, jednak warto o takim fakcie wiedzieć. Rozwieje to Twoje wątpliwości szczególnie podczas przeglądania *$scope* za pomocą wtyczki ngInspector.

## Dwustronne bindowanie danych

Potęgą frameworków JavaScriptowych jest niezaprzeczalnie **mechanizm dwustronnego bindowania danych** *two-way data binding*. Jest to element obiektu *$scope* i zapewnia **dynamiczne odświeżanie danych pomiędzy widokiem a view-modelem** (czy też upraszczając kontrolerem).

Każdy kto pisał cokolwiek w JavaScript lub jego młodszym bracie jQuery wie jak uciążliwe jest wymienianie danych pomiędzy elementami HTML a skryptem. W AngularJS nie trzeba się o to martwić.

### Wyrażenia

Zanim przetestujemy mechanizm dwustronnego bindowania danych **trzeba dowiedzieć się czym są wyrażenia**. Wyrażenia w AngularJS służą do przekazywania danych pomiędzy kontrolerem a widokiem (w jedną stronę!):

{% include parts/postPicture.html page=page img="wyrazenia2" %}

Istnieją dwie metody na przekazanie wyrażenia do widoku:

- Przekazanie wyrażenia **za pomocą nawiasów klamrowych** np. `{% raw %}{{wyrażenie}}{% endraw %}`
- Przekazanie wyrażenia **za pomocą wbudowanej dyrektywy** np. `ng-bind="wyrażenie"`

Wyrażenia w AngularJS są takie same jak w JavaScript, mogą zawierać zmienne, literały oraz operatory. W celu przetestowania wyrażeń proponuję podpiąć do *$scope* zmienną o dowolnej treści. Przykładowo utworzę atrybut *imie* o wartości &#8222;Karol&#8221;. Definiowanie atrybutów i funkcji obywa się tak samo jak w przypadku JavaScript.:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.imie = "Karol"
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Wynik dodawania 5+2 to {% raw %}{{5+2}}{% endraw %}</p>
	    <p>Masz na imię {% raw %}{{imie}}{% endraw %}<p>
	</body>

Po otworzeniu powyższego kodu w przeglądarce Chrome przekonasz się, że zmienne zostały wyświetlone w poprawny sposób. Nawiasy klamrowe można zastąpić dyrektywą *ng-bind*:

	<p>Masz na imię {% raw %}{{imie}}{% endraw %}<p>
	<p>Masz na imię <span ng-bind="imie"></span><p>

Wynik dwóch powyższych linijek będzie ten sam. Jedyna różnica w przypadku posługiwania się dyrektywą jest to, że musi być ona osadzona w jakimś znaczniku HTML. Z tego powodu konieczne było utworzenie dodatkowego *spana* wewnątrz akapitu aby można było zbindować do niego wartość zmiennej. Z tego powodu bardziej popularne i wygodniejsze są nawiasy klamrowe.

Spójrzmy jak wygląda struktura zakresów podglądając je za pomocą wtyczki ngInspector:

{% include parts/postPicture.html page=page img="ng-inspector" %}

Wyraźnie widać moduł, podpięty do niego *$rootScope* oraz jego dziecko czyli *$scope* naszego kontrolera. Uwaga, aby dodatek ngInspector działał plik jaki badamy **musi być serwowany przez serwer np. przez serwer lokalny**. Odwołując się do pliku za pomocą ścieżki *file://* wtyczka nie uruchomi się. Szybki darmowy serwer HTTP jest wbudowany np. w npm (http-server). Można użyć też XAMPA lub innych podobnych.

### Modele

Użycie modelu dostarcza nam 100% prawdziwego mechanizmu dwustronnego bindowania, z którego słyną frameworki JavaScriptowe. W przypadku modelu bindowanie odbywa się w dwie strony, a więc zmiany które zajdą w formularzu są widocznie w kontrolerze oraz na odwrót:

{% include parts/postPicture.html page=page img="modele" %}

Modele można podpinać wyłącznie do kontrolek HTMLowych czyli np. do elementów *input*. Jedyną możliwością podpięcia modelu jest użycie wbudowanej dyrektywy *ng-model*. Przykładowo:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.imie = "Karol"
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Masz na imie <input ng-model="imie" /><p>
	</body>

Powyższy kod podpina do pola tekstowego model *$scope.imie*. Po uruchomieniu kodu wartość zmiennej zostanie automatycznie zbindowana do widoku.

## Pełny przykład bindowania danych

Sprawdźmy teraz, dlaczego AngularJS jest tak fajny jak o nim mówią. Poznaliśmy **podstawowe mechanizmu umożliwiające bindowania danych**, spróbujmy je ze sobą połączyć. W tym celu zdefiniujmy model dla zmiennej *imie* oraz *nazwisko* a następnie za pomocą wyrażenia wyświetlmy jak nazywa się użytkownik:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.imie = "";
	        $scope.nazwisko = "";
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Imię: <input ng-model="imie" /></p>
	    <p>Nazwisko: <input ng-model="nazwisko" /></p>
	    <br>
	    <p>Nazywasz się {% raw %}{{imie}}{% endraw %} {% raw %}{{nazwisko}}{% endraw %}.<p>
	</body>

W powyższym przykładzie użytkownik może wpisać do dwóch pól tekstowych wartości tekstowe. Jest dla nich zdefiniowany model. Za pomocą wyrażeń wartość zostaje automatycznie wyświetlona niżej jako zwykły element strony HTML:

{% include parts/postPicture.html page=page img="bbbb" ext="gif" %}

Jak widać na załączonym przykładzie AngularJS odświeża dane ze *$scope* w czasie rzeczywistym. Jest to niewątpliwie jego największa zaleta. Używając czystego JavaScriptu ilość utworzonego kodu byłaby ogromna. Nawet używając jQuery pomógłby on jedynie szybciej załapać uchwyt dla poszczególnego pola tekstowego &#8211; nic więcej.

## Grupowanie w obiekty i czysty scope

Pisząc aplikacje w AngularJS musimy dbać o porządek w kontrolerze a przede wszystkim o porządek w *$scope*. **Nigdy nie należy podpinać zmiennych bezpośrednio do scope **szczególnie jeżeli tworzą jakiś model biznesowy. Powyższy przykład pokazany dla celów naukowych jest błędny, o wiele lepszym rozwiązaniem będzie zgrupowanie danych osobowych do obiektu *osoba*. Lepsza organizacja powyższego przykładu:

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
	    <br>
	    <p>Nazywasz się {% raw %}{{osoba.imie}}{% endraw %} {% raw %}{{osoba.nazwisko}}{% endraw %}.<p>
	</body>

*$scope* jest obiektem ważnym, tworzy warstwę widoku-modelu i jest odpowiedzialny za bindowanie danych pomiędzy kontrolerem a widokiem. Jego zaśmiecenie powoduje znaczne straty wydajności w działaniu aplikacji. Odpowiednie grupowanie atrybutów i podpinanie ich jako atrybutów obiektów pomaga w utrzymaniu porządku.

W żadnym wypadku **nie możemy uszkodzić albo nadpisać zakresu**, możemy jedynie dodawać do niego atrybuty i funkcje, które są nam potrzebne. Istnieją też inne sposoby na dbanie o &#8222;lekkość&#8221; zakresu i zostaną opisane w kolejnych artykułach.