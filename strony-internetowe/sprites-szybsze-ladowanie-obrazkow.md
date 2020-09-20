---
title: Sprites - szybsze ładowanie obrazków
category: strony-internetowe
thumbnail: sprites.png
published: 2012-07-27T00:00:00Z
---
**Sprites** to małe obrazki używane na stronach. Mogą to być ikony menu, detale ozdobne i wszelkie inne małe elementy graficzne, często wykorzystywane z atrybutem *hover*. **Używanie CSS Sprites polega na zastąpieniu kilku obrazków jednym**. Sprites są używane we wszystkich dużych serwisach.

<!--more-->

## Na czym polega używanie sprites

Załóżmy, że mamy ikonę menu zmieniającą kolor po najechaniu. Normalnie taki efekt uzyskujemy dodając do dowolnego elementu kod CSS z atrybutem *hover*. Po najechaniu wczytany zostaje inny obrazek. Korzystając ze sprites wszystko będzie działać tak samo, jednak kilka małych obrazków zastępujemy jednym. Efekt ten często nazywany jest **CSS Rollover.**

## Po co używać sprites?

Używanie sprites jest obecnie standardem, stosowanym przez wszystkie duże serwisy. Najważniejsze zalety stosowania tej techniki to:

- mniejsze zużycie transferu
- **brak migotania po najechaniu na zdjęcie**
- lepsza organizacja grafiki (wszystko w jednym pliku zamiast 20 osobnych)

Przeważnie sprites używamy właśnie po to, aby pozbyć się migotania. Przeglądarka WWW wczytuje pliki graficzne z Twojego hostingu **wtedy gdy są potrzebne**. Jeżeli masz na stronie przycisk, który po najechaniu zmienia tło, to nie zostanie ono wczytane aż do momentu pierwszego użycia. Skutkiem tego jest **charakterystyczne mignięcie** po pierwszym najechaniu na dany element. Sprites eliminuje ten problem.

Dodatkowo zamiast wysyłać dziesiątki żądań HTTP w celu wczytania wielu obrazków, wystarczy wysłać ich kilka aby wczytać całą grafikę strony.

## Przykład działania

W jednym pliku graficznym trzymamy kilka obrazków używanych na stronie. Jeżeli chcemy aby jeden z obrazków został tłem jakiegoś elementu, musimy poprzez arkusz stylów **ustawić tło obrazka dla elementu, a następnie ustalić granice** w jakich znajduje się interesująca nas grafika. W przypadku braku ustalenia granic zostałby wyświetlony cały plik sprites ze wszystkimi obrazkami.

W zdarzeniu *hover* nie zmieniamy tła obrazka, a jedynie jego przesunięcie.

W przykładzie posłużę się stworzeniem przycisku zmieniającego swój kolor (obrazek tła) podczas najechania myszką. Tłem przycisku będzie obrazek z zastosowaną techniką CSS Sprites.

## Tworzenie obrazka

To są dwa osobne przykładowe obrazki.

{% include parts/postPicture.html page=page img="1" %}

{% include parts/postPicture.html page=page img="2" %}

Miniaturki mają wymiary *100 x 25*.  Znajdują się w dwóch osobnych plikach. Aby użyć **techniki CSS Sprites** muszę zamieścić je w jednym pliku o wymiarach *100 x 50*. Efekt może wyglądać tak:

{% include parts/postPicture.html page=page img="3" %}

Oczywiście obrazki można ustawiać także obok siebie, pod sobą lub w każdej innej kombinacji. To nie ma żadnego znaczenia.

## Kod HTML i CSS

Tworzymy *button* posiadający klasę *.przycisk* i importujemy arkusz stylów:

	<html>
	    <head>
	        <link rel="stylesheet" type="text/css" href="style.css">
	    </head>
	    <body>
	        <button class="przycisk" type="submit"></button>
	    </body>
	</html>

Kolejnym zadaniem jest stworzenie arkusza stylów zawierający definicję klasy *przycisk*.

Najpierw ustalamy wymiary zgodne z wymiarami szarego tła (100&#215;25). Następnie dla atrybutu *hover* zmieniamy położenie tła za pomocą *background-position***.** Przesuniemy tło o 25 pikseli w dół skutkiem czego będzie wyświetlenie tła niebieskiego.

	.przycisk {
	    height: 25px;
	    width: 100px;
	    background: url('obrazek.png');
	    cursor:pointer;
	}
	
	.przycisk:hover {
	    background-position:0px -25px;
	}
