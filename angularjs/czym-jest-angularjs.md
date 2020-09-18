---
title: Czym jest AngularJS?
category: angularjs
thumbnail: angular.png
published: 2016-12-03T00:00:00Z
---
Ze względu na brak informacji w polskim internecie na temat **świetnej biblioteki jaką jest AngularJS**, postanowiłem spróbować własnych sił i napisać takowy. Jest to pierwsza seria wpisów w stylu kursu jaką umieszczę na tym blogu. Zaletą kursu jest to, że skupia odpowiednią ilość danych w jednym miejscu, a przede wszystkim odpowiednie wpisy są ułożone w logiczną całość.

<!--more-->

## Szybki rozwój frontendu

Kilka lat temu dobry frontendowiec musiał umieć stworzyć ładnie wyglądający szablon. Była to osoba w dużej mierze zajmująca się graficznym projektem strony. W momencie gdy szablon był gotowy, do akcji wkraczał np. programista PHP. Dodawał on odpowiedni kod, dodając do witryny całą funkcjonalność.

W ostatnim czasie frontend rozwinął się bardzo silnie. Internet przejął bardzo duży fragment rynku handlowego i usługowego. Dzisiaj biznes, który nie istnieje w internecie, nie istnieje wcale. Dzięki temu narodziło się wiele gałęzi biznesu zajmującymi się **aplikacjami internetowymi**, pozycjonowaniem i wirtualną reklamą.

Rozwój aplikacji internetowych wymusił rozwój technologii, w których są one tworzone. Pojawił się nowe języki programowania lepsze od języka PHP, w których tworzone są serwisy obsługujące miliony klientów dziennie. Ciekawe jest to, że niesamowicie rozwinął także się sam JvaScript.

## Ewolucja JavaScriptu

Kiedy uczyłem się programować w szkole podstawowej, język skryptowy **JavaScript kojarzony był tylko z niepotrzebnymi komponentami** dołączanymi do stron WWW. Jeżeli drogi czytelniku sięgnąłbyś do książek powiedzmy z 2003 roku, w połowie z nich JavaScript opisywany byłby jak wirus i najgorsze zło, którego trzeba unikać.

Wielu ekspertów twierdziło, że JavaScript nie ma racji bytu ponieważ wykonywany jest po stronie przeglądarki, co za tym idzie użytkownik może wyłączyć jego obsługę. Co za tym idzie **niemożliwym było wykorzystanie JavaScriptu to żadnych poważnych zadań**. Ostatecznie język ten wykorzystywany był np. w połączeniu z AJAXem w celu asynchronicznego wyczytywania danych.

Aktualnie JavaScript jest numerem jeden w budowaniu warstwy klienckiej wszelakich aplikacji internetowych. Ponieważ pisanie w czystym JavaScripcie było złożone i generowało dużą ilość kodu, zaczęły się rozwijać platformy programistyczne mające na celu przyśpieszyć ten proces. Powstało ich całkiem sporo i są ciągle rozwijane a **jednym z nich jest AngularJS**.

## Platforma programistyczna AngularJS

**AngularJS to platforma programistyczna** języka JavaScript rozwijana przez firmę Google. Angular jest całkiem darmowy także do zastosowań komercyjnych. Cechuje go wszystko to co wszelkie inne platformy programistyczne. Bardzo szybko da się w nim napisać skomplikowaną na pozór aplikację &#8211; występuje w nim **dwustronne bindowanie wartości zmiennych**. AngularJS dobrze sprawdza się także w rozdzieleniu logiki aplikacji od warstwy prezentacji. Stosując go backend nie zajmuje się generowaniem kodu HTML tylko dostarczeniem surowych danych np. za pomocą REST.

AngularJS służy do tworzenia stron SPA (single page applications). Oznacza to, że pierwsze żądanie i pierwsze wejście do aplikacji internetowej powinno dostarczyć wszelkie wymagane pliki arkusza stylów oraz szablonu. Reszta danych wczytywana jest asynchronicznie z backendu w zależności od kontekstu i dynamicznie prezentowana.

## Gdzie nie używać AngularJS

AngularJS powinien być przede wszystkim wykorzystywany podczas tworzenia **zaawansowanych aplikacji, w których ważniejsze jest zapewnienie dużej ilości funkcji i logiki** niż statycznych stron z dużą ilością tekstu. Musisz wiedzieć o tym, że wyrażenia Angularowe służące do wyświetlania danych nie są przetwarzane przez przeglądarki internetowe, dlatego pozycjonowanie aplikacji Angularowych jest wręcz niemożliwe bez dodatkowych sztuczek i bibliotek.

Stąd AngularJS doskonale sprawdza się w sytuacjach takich jak aplikacje bankowe, panele administracyjne, zaawansowane kalkulatory i aplikacje przetwarzające rozbudowane formularze HTML. Totalnie nie sprawdza się natomiast na blogach, serwisach informacyjnych i innych stron z dużą ilością tekstu i artykułów.