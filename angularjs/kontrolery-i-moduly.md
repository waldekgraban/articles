---
title: Kontrolery i moduły
category: angularjs
thumbnail: kontrolery-moduly.png
published: 2016-12-15T00:00:00Z
---
Kontrolery i moduły są podstawowymi bytami w każdej aplikacji AngularJS. Są one odpowiedzialne za **podzielenie aplikacji na logiczne warstwy abstrakcji a także za sterowanie jej przepływem**. Ucząc się Angulara prawdopodobnie będziesz używał jednego modułu i jednego kontrolera. Ich większa ilość jest potrzebna w momencie tworzenia konkretnej aplikacji, a poprawne zaplanowanie takiej struktury zostanie opisane w kolejnych artykułach.

<!--more-->

## Główny moduł aplikacji

Moduły aplikacji tworzą abstrakcyjną warstwę aplikacji służącą **jedynie do utrzymania porządku w kodzie**. Tworząc aplikację AngularJS nie musisz dzielić jej na moduły, jednak wymogiem jest zdefiniowanie **przynajmniej jednego tzw. głównego modułu aplikacji**, do którego będzie można podpiąć kontrolery.

Do uruchomienia Angulara wystarczy użyć wbudowanej dyrektywy *ng-app=&#8221;nazwa&#8221;*. W przypadku podania wartości szuka ona właśnie modułu o takiej nazwie &#8211; jest to moduł główny. Na potrzeby kursu będziemy używać tylko jednego modułu, większa ilość przydaje się przy tworzeniu konkretnych aplikacji co zostanie opisane w innym artykule.

Definicja modułu aplikacji sprowadza się do jednej linii kodu:

	var app = angular.module("mainModule", []);

Nazwa modułu to *mainModule* ze względu, że jest to główny moduł aplikacji. Obiekt zostaje przypisany do zmiennej o nazwie *app*. Oczywiście nazwy możesz dobierać według własnych upodobań. Główny moduł aplikacji opina zasięgiem nasz widok dokładnie w tych atrybutach w jakich został zainicjowany.

{% include parts/postPicture.html page=page img="angular-aplikacja2" %}

Zwróć uwagę, że moduł nie posiada własnego &#8222;ciała&#8221;. **Nie da się zdefiniować w nim zmiennych, funkcji ani umieścić w nim żadnej logiki**. Jest tylko abstrakcyjnym elementem, do którego będziemy podpinać nasze kontrolery.

## Kontroler aplikacji

Kontroler aplikacji AngularJS jest zwykłą funkcją. **Jego instancja** zostaje utworzona w momencie napotkania dyrektywy *ng-controller="nazwa"* w kodzie HTML. Dyrektywa *ng-controller* szuka kontrolera o podanej przez nas nazwie, jeżeli go nie znajdzie AngularJS rzuci w konsoli wyjątek.

Definicja przykładowego kontrolera wygląda następująco:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleController', function() {
	        // ciało kontrolera
	    });
	</script>

	<body ng-app="mainModule" ng-controller="simpleController">
	    Angular start {% raw %}{{3+2}}{% endraw %}
	</body>

W powyższym przykładzie **do utworzenia kontrolera użyta została funkcja anonimowa**. Jest to rozwiązanie najczęściej spotykane w różnych artykułach i kursach. Wydaje się w miarę proste i logiczne. Mimo to zachęcam Cię, aby trzymać się konwencji, którą zaprezentuję poniżej:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController() {
	        //ciało kontrolera
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    Angular start {% raw %}{{3+2}}{% endraw %}
	</body>

Funkcja kontrolera została zadeklarowana jako normalna nazwana funkcja a nie funkcja anonimowa. Dzięki temu definicje kontrolerów nie przeplatają się z ich deklaracjami co ma ogromne znaczenie w przyszłości, gdy będziesz tworzył średniej wielkości aplikacje. **Taka konwencja jest też bardziej czytelna** w momencie kiedy zaczniemy korzystać z mechanizmu wstrzykiwania zależności (opisany w innym artykule).

Kontroler aplikacji jest warstwą, która zawiera w sobie *$scope* (z ang. przestrzeń/zasięg). Każdy kontroler ma swój własny *$scope*, do którego przypinamy różne atrybuty oraz metody widoczne później w widoku. *$scope* tworzy warstwę widok-model wzorca MVVM i moim zdaniem jest nieprzetłumaczalna na język polski. Z tego względu do końca serii będę posługiwał się tym pojęciem.

Oto przykładowa struktura aplikacji:

{% include parts/postPicture.html page=page img="angular-aplikacja" %}

Gdy w kolejnych lekcjach do kontrolerów a konkretniej do *$scope* danego kontrolera będziemy podpinać zmienne i metody, **ich zasięg będzie taki jak na rysunku wyżej**. Kontroler zainicjalizowany dyrektywą *ng-controller* ma dostęp tylko do danych widoku elementów drzewa DOM które są mu podrzędne. Kontrolery nie mogą wymieniać między sobą danych ani wpływać na swój stan (przynajmniej bez zastosowania dodatkowych mechanizmów).

Oczywiście możliwe jest podpięcie jednego kontrolera na całą gałąź atrybutu *body* a nawet *html*. Wtedy moduł i kontroler opinałby swoim zasięgiem całą stronę.