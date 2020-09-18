---
title: Zdarzenia
category: angularjs
thumbnail: zdarzenia.png
published: 2016-12-18T00:00:00Z
---
Zdarzenia są bardzo ważnym mechanizmem umożliwiającym interakcję użytkownika z naszą aplikację. Oczywiście, w AngularJS zdarzenia zostały obsłużone wyjątkowo dobrze. Czym są zdarzenia? Są to przechwycenia wszelkich operacji jakie może wykonać użytkownik w naszej aplikacji np. kliknięcie myszką, naciśnięcie klawisza.

<!--more-->

## Podpinanie zdarzeń

Zdarzenia w AngularJS podpinamy poprzez **dodanie do dowolnego elementu HTML odpowiedniej dyrektywy** reprezentującej zdarzenie. Jako parametr dyrektywy należy podać funkcję zwrotną, która zostanie wykonana w danej sytuacji. Składania zdarzenia:

	<element nazwa_zdarzenia="funkcja" />

A więc przykładowo zdarzenie kliknięcia w przycisk wywołujące funkcje _$scope.logowanie()_ wygląda następująco:

	<button ng-click="logowanie()">Zaloguj</button>

Pełny kod działającej aplikacji:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        $scope.uzytkownik = {
	            login: '',
	            haslo: ''
	        };
	        $scope.logowanie = function() {
	            console.log('wpisany login: ' + $scope.uzytkownik.login);
	            console.log('wpisane haslo: ' + $scope.uzytkownik.haslo);
	            console.log('ktos kliknął logowanie!');
	        };
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Login: <input ng-model="uzytkownik.login" /></p>
	    <p>Hasło: <input ng-model="uzytkownik.haslo" /></p>
	    <button ng-click="logowanie()">Zaloguj</button>
	</body>

Do przycisku logowania zostało podpięte zdarzenie _ng-click_ wywołujące funkcje _logowanie()_. Bindując zmienne lub funkcje podpięte do _$scope_ z warstwą widoku zawsze pomijamy nazwę _$scope_. Efekt działania jest następujący:

{% include parts/postPicture.html page=page img="ngclick" ext="gif" %}

W przypadku podania nieistniejącej nazwy funkcji do parametru dyrektywy zdarzenia, niestety nic się nie stanie. W konsoli nie pojawi się żaden błąd. Jeżeli coś nie działa a w konsoli nie ma błędów, **warto w pierwszej kolejności sprawdzić czy zdarzenia podpięte są pod istniejące funkcje** lub czy nie zapomnieliśmy o nawiasach.

## Lista dostępnych zdarzeń

Oto lista dostępnych zdarzeń:

<table class="table table-striped table-condensed">
  <tr>
    <th style="text-align: center;">
      Zdarzenie
    </th>
    
    <th style="text-align: center;">
      Wywoływany gdy
    </th>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-blur</code>
    </td>
    
    <td>
      Dany element traci aktywność (traci focus)
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-change</code>
    </td>
    
    <td>
      Zmieni się model <em>ng-model</em> danego elementu
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-click</code>
    </td>
    
    <td>
      Kliknięcie myszką
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-copy</code>
    </td>
    
    <td>
      Nastąpi skopiowanie wartości elementu
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-cut</code>
    </td>
    
    <td>
      Nastąpi wycięcie wartości elementu
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-dblclick</code>
    </td>
    
    <td>
      Podwójne kliknięcie myszką
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-focus</code>
    </td>
    
    <td>
      Dany element zostaje aktywny (focus)
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-keydown</code>
    </td>
    
    <td>
      Naciśnięcie klawisza
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-keypress</code>
    </td>
    
    <td>
      Naciśnięcie klawisza (rozróżnia wielkość liter)
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-keyup</code>
    </td>
    
    <td>
      Puszczenie klawisza
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-mousedown</code>
    </td>
    
    <td>
      Naciśnięcie klawisza myszy
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-mouseenter</code>
    </td>
    
    <td>
      Najechanie myszą na element gdziekolwiek
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-mouseleave</code>
    </td>
    
    <td>
      Zjechanie myszą z elementu
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-mousemove</code>
    </td>
    
    <td>
      Poruszanie myszą po elemencie
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-mouseover</code>
    </td>
    
    <td>
      Najechanie myszą na element lub jego dzieci
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-mouseup</code>
    </td>
    
    <td>
      Puszczenie klawisza myszy
    </td>
  </tr>
  
  <tr>
    <td style="text-align: center;">
      <code>ng-paste</code>
    </td>
    
    <td>
      Wklejenie wartości do elementu
    </td>
  </tr>
</table>

Większości wymienionych zdarzeń w codziennej pracy nie używa się. Najważniejsze zdarzenia, które na pewno należy zapamiętać to _ng-click_ oraz _ng-change_. Za pomocą kliknięcia możemy obsłużyć **wszystkie przyciski i formularze** w aplikacji.

Z kolei _ng-change_ to wywołuje funkcje zwrotną podczas jakiejkolwiek zmiany modelu przez użytkownika, więc może np. przeliczyć w tle dane. Jest to dobra alternatywa dla zakładania obserwatorów na _$scope_ o czym będzie w innym artykule.

## Zdarzenie ng-change

Wcześniej czy później zdasz sobie sprawę, że _ng-change_ jest jednym z najbardziej przydatnych zdarzeń. Aby zdarzenie działało, **musi być nałożone na obiekt posiadający model** podpięty dyrektywą _ng-model_. To właśnie zmiana wartości modelu wpływa na wywołanie _ng-change_. Przykładowe użycie:

	<script>
	    var app = angular.module("mainModule", []);
	    
	    app.controller('simpleCtrl', simpleController);
	    
	    function simpleController($scope) {
	        
	        $scope.check = false;
	        $scope.ilosc = 0;
	        
	        $scope.klikniecie = function() {
	            $scope.ilosc++;
	        }
	    }
	</script>
	
	<body ng-app="mainModule" ng-controller="simpleCtrl">
	    <p>Zaznacz checkbox: <input type="checkbox" ng-model="check" ng-change="klikniecie()" /></p>
	    <p>
	        Checkbox jest 
	        <span ng-if="check" style="color:#00CE00; font-weight:bold;">zaznaczony</span>
	        <span ng-if="!check" style="color:#FF0000; font-weight:bold;">odznaczony</span>
	    <p>
	    <p>Został kliknięty <b>{{ilosc}}</b> razy</p>
	</body>

Efekt działania jest przewidywalny:

{% include parts/postPicture.html page=page img="bbb" ext="gif" %}

Przy jakiejkolwiek zmianie modelu podpiętego do pola typu _checkbox_ zostaje wywołana funkcja _klikniecie()_. Zwiększa ona wartość zmiennej _ilosc_, który jest następnie wyświetlany za pomocą wyrażenia _{{ilosc}}_. Dodatkowo zastosowana dyrektywa _ng-if_ sprawdza aktalny stan pola i wyświetla odpowiedni _span_ z tekstem.