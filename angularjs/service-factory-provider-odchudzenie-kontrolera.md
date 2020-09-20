---
title: Service, factory i provider - odchudzenie kontrolera
category: angularjs
thumbnail: factory.png
published: 2016-08-02T00:00:00Z
---
Zaczynając pracę z **platformą programistyczną AngularJS** należy jak najszybciej zacząć korzystać z serwisów jakie oferuje. Dzięki nim możliwe jest **trzymanie porządku w kodzie i odchudzenie kontrolera aplikacji**. Jednymi z trzech podstawowych rodzajów serwisów są *service*, *factory* oraz *provider*. Zaczynając pracę z Angularem czasem trudno jest zrozumieć subtelne różnice pomiędzy nimi. Czasem wpływa to na umieszczanie zbyt dużej ilości logiki bezpośrednio w kontrolerze, co jest totalnie błędnym podejściem.

<!--more-->

## Architektura platformy AngularJS

AngularJS na początku swojego istnienia **architektonicznie bardzo przypominał użycie wzorca MVC**. Sytuacja zmieniła się z biegiem czasu wraz z powstawaniem kolejnych wersji tej platformy i obsługą np. mechanizmu wstrzykiwania zależności. Zagłębiając się w różne źródła można znaleźć setki informacji o tym jak sklasyfikować architektonicznie aplikacje stworzone w Angularze. Wielu inżynierów podaje swoje własne rozwiązania i interpretacje, co zaskutkowało **powstaniem pojęcia MVW** (z ang. model widok cokolwiek).

Analizując sytuację na dzień dzisiejszy aplikacjom Angularowym zaprojektowanym w specjalny sposób **najbliżej jest do symulowania wzorca o nazwie MVVM** (z ang. model widok widok-model). Wzorzec architektoniczny MVVM jest pochodnym dla wzorca MVC, jednak występują między nimi pewne różnice. Pierwszą z nich jest **źródło interakcji, które pochodzi bezpośrednio z widoku**. Użytkownik systemu poprzez wykonywanie dowolnych operacji na widoku powoduje poinformowanie warstwy widok-model o zachodzących zmianach. Warstwa widok-model **komunikuje się natomiast z modelami** manipulując danymi w odpowiedni sposób. Kontroler ze wzorca MVC został zastąpiony elementem widok-model, który to element jest pośrednikiem pomiędzy widokiem a modelem.

{% include parts/postPicture.html page=page img="mvvm" %}

**MVVM cechuje fakt, że widok nie wie nic o istnieniu modelu**, nie ma na jego temat żadnych informacji, taka sama relacja występuje w drugą stronę. W modelu MVC źródłem danych dla widoku jest model, jednak widok jest związany z modelem. Związanie występuje podczas wywołania akcji kontrolera. Dodatkowo **widok zawiera referencję do modelu, więc zna jego strukturę**. W architekturze MVVM wiązanie pomiędzy widokiem a widokiem-modelem występuje ciągle przez mechanizm two-way data binding, ale **nie występuje pomiędzy widokiem a modelem**.

{% include parts/postPicture.html page=page img="mvvm2" %}

Widok-model przechowuje stan widoku i wszelkie zachodzące na nim zmiany, relacja działa też w drugą stronę. To kiedy model zostanie wypełniony danymi (np. po kliknięciu w przycisk) zależy od logiki aplikacji. Głównym celem MVVM jest odcięcie się od widoku aplikacji, poprzez „wystawienie” jej danych **za pomocą mechanizmu dwustronnego bindowania**. Model-widok, którym w Angularze jest po prostu *$scope* aplikacji, jest interfejsem wystawiającym dane. Jednak widok, który z tych danych skorzysta, nie ma pojęcia o modelu danych, nie jest z nim w żaden sposób związany. Dzięki temu uzyskujemy pewną warstwę dodatkowej abstrakcji, dodatkową skalowalność i responsywność.

## Do czego służą serwisy?

Platforma AngularJS udostępnia trzy podstawowe serwisy: sa nimi *service*, *factory* oraz *provider*. Służą one do odseparowania logiki kontrolera do osobnych bytów. W architekturze MVVM serwisy zakwalifikować można do warstwy modelu danych, a kontroler do warstwy widok-model bezpośrednio nad *$scope* aplikacji. Kontroler w architekturze MVVM jako pośrednik pomiędzy widokiem a modelem **powinien przechowywać jak najmniejszą ilość logiki biznesowej**. Ona powinna znajdować się właśnie w serwisach. W kontrolerach można przechowywać logikę aplikacji, jeżeli ta nie jest zbyt rozbudowana. Podsumowując, główne zadania dla serwisów to:

- odchudzenie kontrolerów z nadmiaru logiki (biznesowej i aplikacji)
- wymiana danych pomiędzy różnymi kontrolerami
- dodatkowa warstwa abstrakcji umożliwiająca organizacje kodu w aplikacji

Warto pamiętać, że **każdy rodzaj serwisów w AngularJS jest singletonem**. Zostaje zainicjowany w chwili, kiedy jest potrzebny. Następnie ciągle **działamy w obrębie tej jednej instancji**. Stąd też w wygodny sposób można przesyłać informacje między kontrolerami.

### Spaghetti scope

Nieużywanie serwisów udostępnionych przez AngularJS skutkuje powstaniem tzw. *spaghetti scope*. Do tego zjawiska można zaliczyć wszelkie **przeładowane nadmiarem funkcji i zależności kontrolery**, w którym zaszyta jest cała logika biznesowa i logika aplikacji. Jedynym wyjściem z takiej sytuacji jest **przeprowadzenie gruntownej refaktoryzacji**. Im dalej w las &#8211; tym więcej drzew, w pewnym momencie refaktoryzacja może okazać się już niemożliwa.

Rozbudowany, przeładowany kontroler i brak serwisów automatycznie psują koncepcję *dependency injection*, która jest uznawana za główną zaletę platformy AngularJS. Testowanie staje się wówczas niemożliwe, ponieważ przepięcie zależności skutkuje wysypaniem warstwy widok-model  a więc kontrolerów.

## Factory (fabryka)

*Factory* jest najprostszym typem serwisów występujących w Angularze i jednocześnie najczęściej używanym. Tworzymy go używając funkcji *factory()* z odpowiednimi argumentami. Używając *factory* tworzymy wewnątrz niego nowy obiekt, który później zwracamy do kontrolera. Do obiektu można podpiąć różne metody i atrybuty.

	var app = angular.module("SimpleApp", []);
	
	app.factory("employeeFactory", function() {
	    
	    var employees = [
	        {name: "Karol", id: 1},
	        {name: "Arek", id: 2},
	        {name: "Tomek", id: 3},
	    ];
	    
	    var factory = {};
	    		
	    factory.getEmployees = function() {
	        return employees;
	    }
	    
	    return factory;
	});
	
	app.controller("SimpleController", function($scope, employeeFactory) {
	    var vm = this;
	    vm.employeeList = employeeFactory.getEmployees();
	});

Zwrócenie nowego obiektu można przedstawić też w prostszy sposób. Zamiast deklarować obiekt przypisując go do zmiennej *var*, można od razu zwrócić obiekt zawierający określone atrybuty.

	var app = angular.module("SimpleApp", []);
	
	app.factory("employeeFactory", function() {
	    
	    var employees = [
	        {name: "Karol", id: 1},
	        {name: "Arek", id: 2},
	        {name: "Tomek", id: 3},
	    ];
	    
	    return {
	        getEmployees: function() {
	            return employees;
	        }
	    }
	});
	
	app.controller("SimpleController", function($scope, employeeFactory) {
	    var vm = this;
	    vm.employeeList = employeeFactory.getEmployees();
	});

Dzięki użyciu *factory* cała logika odpowiedzialna za pobranie listy pracowników i przetworzenie jej, zostaje odseparowana do serwisu i nie znajduje się w kontrolerze.

## Service (serwis)

*Service* jest kolejnym bardzo często używanym rodzajem serwisu. W odróżnieniu do *factory*, odnosząc się do *service* nie zwracamy nowego obiektu. Wszystkie metody i atrybuty przypisujemy do niego za pomocą *this*. Podczas pierwszego użycia *service* zostaje on niejawnie zainicjowany słowem kluczowym *new*. Jest to typowe użycie *construction function* znanego z JavaScript.

	var app = angular.module("SimpleApp", []);
	
	app.service("employeeService", function() {
	    
	    var employees = [
	        {name: "Karol", id: 1},
	        {name: "Arek", id: 2},
	        {name: "Tomek", id: 3},
	    ];
	    
	    this.getEmployees = function() {
	        return employees;
	    }
	});
	
	app.controller("SimpleController", function($scope, employeeService) {
	    var vm = this;
	    vm.employeeList = employeeService.getEmployees();
	});

## Provider (dostawca)

*Provider* jest wykorzystywany rzadziej, ale zalety jakie ze sobą niesie są niezastąpione przez żaden inny serwis AngularJS. *Provider* stanowi pewien rodzaj połączenia pomiędzy serwisem a fabryką. Jego główną zaletą jest to, że jako jedyny może zostać skonfigurowany w sekcji *config* naszej aplikacji, a więc możemy go przekonfigurować zanim zostanie w kontrolerze jego instancja.

Część konfiguracyjną dopisujemy do *providera* za pomocą słowa kluczowego *this* a więc tak jak w *service*. Będziemy mieć do niej dostęp w sekcji *config*. Część odpowiedzialną za zwrócenie obiektu z publicznymi metodami dostępnymi w obiekcie dopisujemy do funkcji *this.$get*, która w gruncie rzeczy następnie zwraca nowy obiekt, tak jak *factory*.

	var app = angular.module("SimpleApp", []);
	
	app.provider("employeeProvider", function() {
	    
	    var employees = [];
	    		
	    this.setEmployee = function(names) {
	        employees = names;
	    };
	    
	    this.$get = function() {
	        return {
	            getEmployees: function() {
	                return employees;
	            }
	        }
	    }
	});
	
	app.config(function(employeeProviderProvider) {
	    employeeProviderProvider.setEmployee(["Karol", "Marcin"]);
	});
	
	app.controller("SimpleController", function($scope, employeeProvider) {
	    var vm = this;
	    vm.employeeList = employeeProvider.getEmployees();
	});

Konfigurując *provider* w sekcji *config* musimy dodać do jego nazwy postfiks **Provider**. Cała reszta odbywa się tak jak w przypadku fabryki i serwisu.

## Kiedy używać providera?

Serwisu *provider* używamy zawsze wtedy, kiedy zależy nam na dodatkowej konfiguralności naszego serwisu. Możemy to zrobić w sekcji *config*. Składa się on w połowie z konstrukcji przypominającej *service* i w połowie *factory*.

## Kiedy używać factory i service?

Nie ma jednoznacznej odpowiedzi na pytanie kiedy lepiej użyć *factory* a kiedy *service*. Obydwoma serwisami możemy osiągnąć te same efekty. Różnią się one tylko i wyłącznie wewnętrzną konstrukcją i **sposobem zwrócenia danych**. *Service* jest tworzony na zasadzie *constructor function* ponieważ jego instancja jest przez środowisko AngularJS niejawnie inicjalizowana słowem *new*. W przypadku *factory* dostajemy referencję do obiektu zwróconego przez funkcję*.* 

Niech nie zmyli Cię fakt tworzenia nowych instancji jakiegokolwiek serwisu. Obydwa serwisy zarówno *service* oraz *factory* **są obiektami typu singleton**. Oznacza to, że wartości jakimi zostaną zainicjalizowane nie zmieniają się w całym cyklu życia aplikacji. Dotyczy to wszystkich innych serwisów w AngularJS &#8211; po inicjalizacji trafiają one do cache.

Zagłębiając się w różne źródła, można trafić na opinie aby *service* używać tam gdzie zależy nam na dostarczeniu dodatkowej funkcjonalności za pomocą bytów, dla których nie trzeba tworzyć instancji. Może to być np. **symulacja działania prostego API** lub wystawione funkcje pomocnicze w postaci helpera (na wzór działania klas statycznych z języków silnie typowanych). *Factory* natomiast tworzymy w wypadku, kiedy obiekt reprezentuje pewien osobny i autonomiczny byt, taki któremu można by (ze względów koncepcyjnych) stworzyć osobną instancję.

Nie zmienia to jednak faktu, że za pomocą *service* i *factory* można osiągnąć dokładnie te same efekty. Wynika to też ze skalowalności języka JavaScript. Pomimo, że obiekt zwracany przez *service* jest tworzony przez *constructor function*, można na upartego z metody zwrócić funkcję zwracającą obiekt przez referencję. Efekt będzie ten sam co z *factory*.