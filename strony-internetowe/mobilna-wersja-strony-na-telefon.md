---
title: Mobilna wersja strony na telefon
category: strony-internetowe
thumbnail: strona-na-telefon.png
published: 2013-08-08T00:00:00Z
---
Prowadzisz stronę lub bloga? Powinieneś rozważyć wprowadzenie **mobilnej wersji strony**. W dzisiejszych czasach **strona przystosowana do telefonów** **komórkowych** to absolutna podstawa każdego serwisu. Stworzenie **strony mobilnej** jest prostym zabiegiem, z którym każdy może sobie poradzić.

<!--more-->

## Czy warto prowadzić mobilną stronę?

Uwaga! Obecnie najlepszym rozwiązaniem dotyczącym stron mobilnych jest zbudowanie **responsywnego szablonu**. Po przeczytaniu tego artykułu, zachęcam Cię do przeczytania [Jak zrobić stronę responsywną?](/html-css/strona-responsywna/).
{: .alert-danger} 

XXI wiek to bardzo szybki **rozwój telefonów** i komputerów. Obecnie, każdy produkowany telefon jest wyposażony w odbiornik WiFi. Jeżeli prowadzisz własną stronę, sprawdź jak dużo ruchu pochodzi właśnie od urządzeń mobilnych. Być może wcześniej nie zwracałeś na to uwagi?

Zależnie od tematyki Twojej strony, nawet 30% odwiedzin dziennie może pochodzić z urządzeń takich jak telefony komórkowe. Telefony są wygodne, wszyscy ich używają. Po co siedzieć kilka godzin na twardym krześle, skoro telefon daje dostęp do wszystkich ulubionych miejsc w sieci, a używając go można np. leżeć na łóżku lub czekać w długiej kolejce.

Urządzenia mobilne zalewają rynek, ludzie nie potrafią się bez nich obejść. Przystosowanie strony i bloga do **poprawnego wyświetlania na telefonie** to obowiązek a nie tylko bajerancka funkcja serwisu.

## Komputer a telefon

Największy problem w telefonach, ipodach, palmtopach itp. to przede wszystkim **małe rozmiary wyświetlacza**. Strona o szerokości 1200px nie może się wyświetlać poprawnie na wyświetlaczu 320px. Jeżeli nawet załaduje się cała i w żadnym miejscu nie zostaje ucięta, to najprawdopodobniej użytkownik będzie miał wielkie problemy z przeczytaniem jej. Niezbędne okaże się powiększanie tekstu, pół biedy jeżeli będzie to telefon z ekranem dotykowym.

Kolejnym dużym problemem jest **różnorodność przeglądarek internetowych** i producentów telefonów. Rynek komputerowy to obecnie 2-3 liczące się przeglądarki, które twardo trzymają się standardów narzucanych przez konsorcjum W3C. Przeglądarki na telefonach działają natomiast bardzo różnie i często bywają zawodne. Istnieje duża ilość firm i duża ilość przeglądarek, z tego powodu im bardziej skomplikowana strona, tym większa szansa na **złe działanie na określonym modelu** telefonu.

## Szablon przystosowany do telefonów

**Szablon przyjazny dla telefonu** powinien być lekki i przejrzysty. Koniecznie musi być zbudowany na "jednej kolumnie". W telefonach z małymi wyświetlaczami boczne menu na pewno się nie zmieści, musi być ono wyświetlone na górze strony. Oto cechy szablonu dobrze przystosowanego do telefonów komórkowych:

- bardzo mało grafiki
- budowa jednokolumnowa (logo, menu, treść , stopka &#8211; wszystko jedno pod drugim)
- opisywanie grafiki atrybutem *ALT* w przypadku gdy nie zostanie wczytana
- całkowite oddzielenie kodu HTML od CSS
- absolutny brak tabelek i ramek podczas projektowania szablonu
- obowiązkowe kodowanie UTF-8
- nie stosujemy JavaScript
- odpowiednia deklaracja typu dokumentu
- odpowiednie Meta-tagi
- warto odchudzić szablon (usunąć ankiety, pogodę, imieniny i podobne dodatki)
- brak narzuconej szerokości (powinna dopasować się automatycznie np. *width: 100%*)

Szablon najlepiej umieścić na osobnej sub-domenie naszego serwisu, bardzo popularne jest poprzedzanie nazwy serwisu literką m (od "mobile") np. zamiast **www.serwis.pl** będzie **www.m.serwis.pl**.

Można także używać dwóch osobnych arkuszy stylów i podmieniać je w zależności od urządzenia na jakim strona jest wyświetlana. Jedyną trudnością w obu przypadkach jest zadbanie o poprawne rozpoznawanie urządzeń mobilnych.

## Odpowiednie meta-tagi i typ dokumentu

Strony przystosowane do telefonów powinny być pisane w **XHTML Mobile**, którego najnowsza wersja to 1.2.

	<!DOCTYPE html PUBLIC "-//WAPFORUM//DTD XHTML Mobile 1.2//EN"
	    "http://www.openmobilealliance.org/tech/DTD/xhtml-mobile12.dtd">

Choć meta-tagi w pozycjonowaniu nie grają już praktycznie żadnej roli, bardzo ważne są w szablonach **mobile-friendly**. Za pomocą meta-tagu **handheldFriendly** informujemy urządzenie, czy strona została zoptymalizowana pod kątem urządzeń mobilnych.

	<meta name="handheldFriendly" content="true" />

Tag **MobileOptimized**, informuje urządzenie jaka jest optymalna rozdzielczość ekranu, do wyświetlenia strony. Ponieważ dostępnych na rynku urządzeń jest tak wiele, przywykłem wpisywać tutaj **minimalną** rozdzielczość przyjazną dla urządzenia. Tworząc szablon ustawiam jego minimalną szerokość na 240px (*min-width:240px;)* a następnie ustawiam tę liczbę jako wartość meta-tagu. Uważam że 240px-320px to minimum choć wiadomo że występują wyświetlacze mniejsze.

	<meta name="MobileOptimized" content="240">

Ostatnim najważniejszym tagiem, który opiszę jest **viewport**. Przyjmuje on wiele parametrów opisujących optymalną szerokość urządzenia, powiększenie początkowe i możliwość powiększania tekstu przez użytkownika urządzenia mobilnego. Pamiętaj, że są to tylko meta-tagi i nie ma żadnej gwarancji, że będą respektowane przez przeglądarkę.

Oto lista parametrów tego meta-tagu:

- **width **&#8211; optymalna szerokość wyświetlacza, najlepiej ustawić na: "*width=device-width"*.
- **initial-scale** &#8211; powiększenie początkowe, zawsze ustawiamy na wartość 1.
- **maximum-scale** &#8211; maksymalna możliwa wartość powiększenia, jeżeli nie ma potrzeby to **nie używaj**.
- **user-scalable** &#8211; przyjmuje wartość *yes* lub *no*. Włącza lub wyłącza możliwość powiększania strony. Na prawdę, nie warto odbierać użytkownikom tej możliwości.

Optymalna postać meta tagu wygląda następująco: <code class="xml"></code>

	<meta name="viewport" content="width=device-width; initial-scale=1" />

## Detekcja telefonu

Dość kłopotliwą sprawą jest odróżnienie urządzenia mobilnego od PC-ta. W moim przekonaniu najlepiej używać skryptu, który rozpoznaje **user-agent** poprzez PHP. JavaScript na telefonach jest często wyłączone więc używanie tej technologii to słaby pomysł. Wielu ludzi, próbuje zgadywać urządzenie także po jego rozdzielczości.

Wyciągnięcie nazwy systemu operacyjnego z **user-agent** jest łatwe i szybkie. W tym serwisie już powstał artykuł na ten temat: [informacje o użytkowniku PHP.](/php/informacje-o-uzytkowniku-php/).

## Strona mobilna a WordPress

Użytkownicy WordPressa zawsze mają z górki. Istnieje fajna wtyczka o nazwie **Any Mobile Theme Switcher**, która zmienia szablon w WordPressie jeżeli rozpozna urządzenie mobilne. Jest to bardzo szybki sposób, dzięki któremu półautomatycznie zyskasz **mobilną wersję swojego bloga**. Szablon można wykonać bardzo łatwo, stosując się do wskazówek z tego artykułu, resztę zrobi za Ciebie skrypt.

## Zakończenie

**Mobilna strona** to gra warta świeczki. Jej wykonanie jest proste i przynosi wiele korzyści. Dość modne staje się tworzenie szablonów w technologii **Jquery Mobile**. Czytałem, widziałem &#8211; nie spodobało mi się. Efekt dość fajny jednak praca włożona w jego osiągnięcie także nie jest mała. Być może kiedyś ten framework stanie się popularny i wyznaczy jakieś standardy w tej dziedzinie, na dzień dzisiejszy nie zauważyłem tego.

Czytając na temat stron mobilnych, możesz spotkać się z określeniem **strona responsywna**. **Szablonem responsywnym** nazywa się szablon, który dostosowuje się do zmiany rozdzielczości tak bardzo, że poprawnie wyświetla się także na telefonach. Więcej na temat **stron responsywnych** dowiesz się czytając mój artykuł [Jak zrobić stronę responsywną?](/html-css/strona-responsywna/).
