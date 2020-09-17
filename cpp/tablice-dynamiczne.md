---
title: Tablice dynamiczne
category: cpp
thumbnail: tablice-dynamiczne.png
published: 2013-01-12T00:00:00Z
---
Tablice dynamiczne **jednowymiarowe** oraz **dwuwymiarowe** są ściśle związane ze wskaźnikami. Użycie wskaźników to jedyna metoda uzyskania tablicy dynamicznej. Istnieje duża potrzeba na używanie tablic dynamicznych, programista ma nad nimi pełną kontrolę. Możemy decydować o ich wymiarach (kształcie) oraz o wielkości.

<!--more-->

## Informacje o tablicach statycznych

Aby dobrze poznać **tablice dynamiczne** należy zrozumieć działanie zwykłych statycznych tablic. **Inicjowanie tablicy** jest bardzo proste, podczas inicjacji możemy z góry określić elementy jakie zawiera tablica.

	int tablica[5] = { 10, 11, 12, 13, 14};
	
Odwoływanie się i wyświetlanie poszczególnych elementów oczywiście nie stanowi dla Ciebie problemu. Ciekawostką może okazać się fakt, że **nazwa tablicy** jest jednocześnie wskaźnikiem na jej pierwszy element.

	int tablica[5] = { 10, 11, 12, 13, 14};

	cout << *tablica;   // wyświetli tablica[0];
	cout << tablica;    // wartość wskaźnika, adres pierwszego elementu

**Tablice statyczne** nie dają nam możliwości decydowania o ich wymiarach podczas działania programu. Oznacza to że musimy znać wielkość tablicy na poziomie tworzenia aplikacji. Kolejną wadą takowych tablic jest fakt, iż generując **tablicę dwuwymiarową** musi być ona prostokątna.

	int tablica[2][3];

Powyższy kod to deklaracja tablicy prostokątnej o wymiarach *2x3*.

## Tablica dynamiczna jednowymiarowa

**Deklarując tablicę dynamiczną** należy zadeklarować wskaźnik tego samego typu co elementy tablicy. Następnie rezerwujemy miejsce w pamięci o określonym typie (takim samym jak wskaźnik). Służy do tego rozkaz *new*. **Tablicy dynamicznej** używamy tak samo jak zwykłą tablice statyczną, nie trzeba operować wskaźnikami, wskaźniki potrzebne są tylko przy deklaracji. Wynika to z faktu opisanego wyżej - tablica statyczna to też wskaźniki chociaż nie są do końca widoczne.

	int * tablica = new int[3];

	tablica[0] = 11;
	tablica[1] = 12;
	tablica[2] = 13;

	delete [] tablica;

Każdy dynamiczny obiekt utworzony podczas działania programu należy na końcu  usunąć poleceniem *delete*. Przy usuwaniu tablicy dodatkowo dodajemy kwadratowy nawias czyli  *delete []*. Dzięki użyciu tablicy dynamicznej użytkownik ma możliwość decydowania o rozmiarze tablicy podczas działania programu:

	int rozmiar;

	cout << "Podaj rozmiar tablicy:" << endl;
	cin >> rozmiar;

	int * tablica = new int[rozmiar];

	delete [] tablica;

## Tablica dynamiczna dwuwymiarowa

**Tablica dynamiczna dwuwymiarowa** to tak naprawdę **tablica wskaźników** do poszczególnych wymiarów. Jaka płynie z tego korzyść? Podczas deklaracji tablicy mamy pełną kontrolę nad wielkością poszczególnych wymiarów, statycznie nie da się osiągnąć takich efektów:

{% include parts/postPicture.html page=page img="tabs" %}

Generowanie tablicy **dwuwymiarowej dynamicznej** odbywa podobnie jak jednowymiarowej. Zamiast tworzyć wskaźnik do jednego wymiaru, tworzymy tablicę wskaźników wskazujących na wymiary.

	int ** tablica = new int * [3];

	tablica[0] = new int [3];   // wskaźnik tablica[0] wskazuje na nową tablicę
	tablica[1] = new int [3];   // wskaźnik tablica[1] wskazuje na nową tablicę
	tablica[2] = new int [3];   // wskaźnik tablica[2] wskazuje na nową tablicę

	tablica[2][2] = 123;
	cout << tablica[2][2];

	// zwalniamy pamiec
	delete [] tablica[0];
	delete [] tablica[1];
	delete [] tablica[2];
	delete [] tablica;

Graficzne przedstawienie powyższego kodu:

{% include parts/postPicture.html page=page img="tabs2" %}

Generowanie tablicy dwuwymiarowej dynamicznej bardzo wygodnie robić za pomocą pętli. Każdy obrót pętli przypisuje wskaźnik do nowego wymiaru: <code class="cpp"></code>

	int wymiar = 3; //rozmiar

	// tablica na wskazniki
	int ** tablica = new int * [wymiar];

	// generowanie poszczegolnych wymiarów
	for (int i = 0; i<wymiar; i++)
			tablica[i] = new int [wymiar];

	// zwalnianie pamieci
	for (int i = 0; i<wymiar; i++)
			delete [] tablica[i];

	delete [] tablica;