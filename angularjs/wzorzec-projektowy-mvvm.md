---
title: Wzorzec projektowy MVVM
category: angularjs
thumbnail: angular.png
published: 2016-12-04T00:00:00Z
---
Niezmierną zaletą platformy programistycznej AngularJS jest możliwość łatwego dzielenia aplikacji na warstwy. Podstawową jednostką grupującą aplikację jest moduł, skupia on w sobie różne kontrolery. **Kontrolery posiadają osobne przestrzenie i zakresy** (o czym będzie w późniejszym rozdziale). Kontroler jest w stanie bindować dane z widokiem i tutaj pojawia się główny problem: w wielu źródłach jest napisane, że **programując w Angularze tworzymy aplikację w architekturze MVC** &#8211; nic bardziej mylnego. Mimo, że występują tutaj widoki, kontrolery i modele porównanie architektury aplikacji AngularJS z architekturą MVC jest nieprawidłowe, wręcz błędne.

<!--more-->

### Działanie architektury MVC

W architekturze MVC **punktem wejścia do systemu jest kontroler aplikacji**. Kontroler jest bezstanowym obiektem odpowiedzialnym za logikę. Przetwarza on żądanie i przekazuje do widoku odpowiedni model wypełniony danymi. Bardzo ważnym faktem jest to, że kontroler musi posiadać referencję zarówno do widoku jak i do modelu danych.

{% include parts/postPicture.html page=page img="mvc" %}

Kontroler jako punkt wejścia wie o widoku oraz o modelu z jakiego będzie korzystał. **Związanie modelu i widoku następuje już podczas wywołania danej akcji kontrolera**. Doskonałym przykładem takiej infrastruktury są np. aplikacje napisane w ASP.NET MVC.

### AngularJS i architektura MVVM

Aplikacje napisane w AngularJS nie spełniają warunków architektury MVC. Są tworzone w architekturze MVVM czyli &#8222;model widok widok-model&#8221; (z ang. model view view-model). Odejście od MVC jest spowodowane rozwojem frontendu. MVC jest typowo bezstanowy, każda akcja posiada swoją własną ścieżkę w postaci adresu URL. Kiedy frontend zrobił się ładniejszy, a co za tym idzie bardziej zaawansowany, a co za tym idzie cięższy, **pojawiła się konieczność ulepszenia wzorca MVC.** Pewną dogodnością było używanie zapytań AJAX jednak rodziły one tyle samo problemów co korzyści. W MVVM warstwę bezstanowego kontrolera zamieniono na warstwę widok-modelu, która była już w stanie zapamiętać i przetwarzać dane (nie była bezstanowa).

W AngularJS **punktem wejścia do systemu nie są kontrolery tylko widoki**. Najdrobniejsze zmiany widoku od razu wpływają na stan systemu, bez wysyłania dodatkowych żądań. Dzieje się to dzięki mechanizmowi dwustronnego bindowania danych. Zmiana wartości w modelu (lub po prostu zmiennej) automatycznie zostanie wyświetlona użytkownikowi. To samo  w drugą stronę, zmiana wartości w formularzu przez użytkownika od razu zostanie zauważona w modelu danych. Istotne jest to, że **widok jako warstwa, nie jest w żaden sposób związana z modelem danych**. Łączy je ze sobą widok-model. Nie jest to już warstwa bezstanowa w odróżnieniu od kontrolera w MVC.

{% include parts/postPicture.html page=page img="mvvm" %}

Jak widać w Angularowej architekturze MVVM nie ma kontrolera mimo, że istnieje on jako jedna z warstw tej platformy. Angularowy kontroler sam w sobie jest niczym, jest jedynie dodatkową warstwą abstrakcji z których zbudowane są moduły. **Moduły i kontrolery pomagają utrzymywać porządek w kodzie** ale nic poza tym. Kontroler z Angulara można porównać z klasą w języku C# &#8211; żadnego związku z MVC ona nie posiada.

Warstwę kontrolera znaną z MVC w AngularJS pełni widok-model a w jego skład wchodzi zakres (czyli _$scope)_ oraz mechanizm dwustronnego bindowania danych. Jeżeli nie wiesz czym one są, dowiesz się tego w kolejnych lekcjach.

### Kompromis architektoniczny czyli MVW

Rozważania na temat stylów projektowania aplikacji często rodzą więcej pytań niż odpowiedzi. Ciężko zestawić ze sobą dwie architektury i doszukiwać się miedzy nimi różnic i podobieństw. Wielu mądrych ludzi ma różne opinie na temat klasyfikacji poszczególnych platform. **W celu znalezienia kompromisu** istnieje także architektura &#8222;model widok cokolwiek&#8221; (z ang. model view whatever). Doskonale pasuje ona do aplikacji stworzonych w AngularJS.

Na koniec tego artykułu warto podsumować: we wzorcu MVC źródłem wejścia do systemu jest kontroler i to on binduje widok z modelem a więc posiada referencje do nich. We wzorcu MVVM źródłem wejścia jest widok, i nie ma on żadnego pojęcia o modelach istniejących w systemie. Jakkolwiek nie sklasyfikujesz swojej aplikacji lub jakiejkolwiek roli nie przypiszesz kontrolerowi z platformy AngularJS warto zapamiętać &#8211; nie jest to architektura MVC.