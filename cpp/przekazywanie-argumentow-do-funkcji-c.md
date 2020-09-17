---
title: Przekazywanie argumentów do funkcji
category: cpp
thumbnail: funkcje-parametry.png
published: 2013-01-26T00:00:00Z
---
Funkcje nie byłyby tak użyteczne, gdyby nie dało się do nich przekazywać **argumentów**. Osoby zaczynające przygodę z programowaniem często mylą sposoby ich przekazywania. Warto poznać różnice jakie istnieją w **przekazywaniu argumentów do funkcji** z użyciem różnych metod.

<!--more-->

## Przekazywanie argumentów przez wartość

Najpopularniejszą metodą budowania funkcji jest **przekazywanie argumentów przez wartość**. Funkcja musi zawierać **argumenty wraz z ich typami,** oddzielone przecinkami. Kiedy przekazujemy do funkcji jakąś zmienną, zostaje utworzona w pamięci jej kopia. Wszystko co dzieje się wewnątrz funkcji odbywa się tak naprawdę na kopii zmiennej przekazanej w argumencie poprzez wartość.

W takim wypadku konieczne staje się zwrócenie odpowiedniej wartości za pomocą *return* i przypisanie zwróconej wartości do odpowiednich zmiennych. Jeżeli funkcja nie zwróci żadnej wartości, wtedy jej wywołanie **może stać się bezcelowe**. Utworzona kopia zmiennej, nawet jeżeli zostanie zmodyfikowana, zostanie usunięta z pamięci po zakończeniu funkcji.

### Podsumowując przekazywanie przez wartość:

- zmienna przekazana do funkcji **jest jej kopią**
- wszelkie operacje odbywają się na kopii, więc **nie są widoczne poza blokiem funkcji**
- aby jakiekolwiek zmiany były widoczne poza ciałem funkcji, należy **przypisać do odpowiednich zmiennych poza funkcją wartość zwracaną przez funkcję** (*return*)
- ponieważ funkcja może zwrócić tylko jedną wartość (*return*) - możliwe jest zmodyfikowanie **tylko jednej zmiennej z poza funkcji**

Przykładowy kod:

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	int zwieksz(int liczba)
	{
	    // zmienna 'liczba' jest kopią zmiennej 'dlugosc'
	    
	    liczba = liczba * 2;
	    
	    return liczba;
	}
	
	int main()
	{
	    int dlugosc = 125;
	    
	    // przypisujemy nową wartość dla zmiennej
	    dlugosc = zwieksz(dlugosc);
	    
	    cout << dlugosc << endl;
	    
	    system("pause >nul");
	    return 0;
	}

## Przekazywanie argumentów przez wskaźnik

**Przekazywanie argumentów przez wskaźnik** jest możliwe zarówno w języku C++ jak i C. Użycie wskaźników razem z funkcjami jest bardzo wygodne i ekonomiczne. Zdarzają się sytuacje, kiedy skorzystanie ze wskaźników jest niezbędne do osiągnięcia odpowiedniego efektu.

Przekazując argumenty przez wartość, **funkcja może zwrócić tylko jeden parametr** za pomocą *return*. Oznacza to, że możliwe jest zmodyfikowanie wartości tylko jednej zmiennej występującej poza wywoływaną funkcją (tak jak w akapicie wyżej). Co w przypadku gdy podczas wywołania jednej funkcji **chcemy zmienić kilka zmiennych**? &#8211; tutaj niezbędne są wskaźniki. Jest to najlepszy przykład na zobrazowanie tego, dlaczego wskaźniki są przydatne.

### Podsumowując przekazywanie przez wskaźnik:

- argumenty wskaźnikowe wskazują na zmienne z poza funkcji, więc wewnątrz funkcji **nie są tworzone kopie**
- funkcja **może zmodyfikować wiele zmiennych z poza funkcji** (bez użycia *return*)
- wskaźniki także przekazujemy przez wartość, wynika to z faktu że wskaźnik też jest typem zmiennej (zmienna wskaźnikowa), jednak mimo że wskaźnik (adres miejsca w pamięci) zostanie skopiowany  to wartość wyłuskana ze wskaźnika nie jest już kopią

Przykładowy kod:

	#include <iostream>
	#include <cstdlib>

	using namespace std;

	void zwieksz_kilka(int *dl, int *wys, int *waga)
	{
	    // zmienna '*dl', '*wys' i '*waga' nie są kopiami
	    // operowanie na nich zmienia ich wartość w "całym" programie
	    // funkcja nie zwraca nic - bo nie ma sensu
	    
	    *dl = *dl * 2;
	    *wys = *wys * 2;
	    *waga = *waga * 2;
	}

	int main()
	{
	    // zmienne
	    int dlugosc = 125;
	    int wysokosc = 300;
	    int waga = 20;
	    
	    // wskaźniki do zmiennych
	    int *wsk_dlugosc = &dlugosc;
	    int *wsk_wysokosc = &wysokosc;
	    int *wsk_waga = &waga;
	    
	    // wywołanie funkcji
	    zwieksz_kilka(wsk_dlugosc, wsk_wysokosc, wsk_waga);
	    
	    // wyświetlenie nowych wartości
	    cout << dlugosc << endl;
	    cout << wysokosc << endl;
	    cout << waga << endl;
	    
	    system("pause >nul");
	    return 0;
	}

Zwróć uwagę, argumenty funkcji to **zmienne wskaźnikowe czyli "adresy do zmiennych"**. Gwiazdka w argumentach **nie jest operatorem wyłuskania**, informuje ona kompilator że zmienna jest wskaźnikiem (czyli "adresem"). Jak można wykorzystać ten fakt? Nie trzeba deklarować zmiennych wskaźnikowych aby przekazywać argument funkcji przez wskaźnik. Można przekazać **bezpośrednio adres** za pomocą operatora pobrania adresu (&).

Przykładowy kod:

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	void zwieksz_kilka(int *dl, int *wys, int *waga)
	{
	    // zmienna '*dl', '*wys' i '*waga' nie są kopiami
	    // operowanie na nich zmienia ich wartość w "całym" programie
	    // funkcja nie zwraca nic - bo nie ma sensu
	    
	    *dl = *dl * 2;
	    *wys = *wys * 2;
	    *waga = *waga * 2;
	}
	
	int main()
	{
	    // zmienne
	    int dlugosc = 125;
	    int wysokosc = 300;
	    int waga = 20;
	    
	    // wywołanie funkcji z (&) (tak jak byśmy przekazywali wskaźniki)
	    zwieksz_kilka(&dlugosc, &wysokosc, &waga);
	    
	    // wyświetlenie nowych wartości
	    cout << dlugosc << endl;
	    cout << wysokosc << endl;
	    cout << waga << endl;
	    
	    system("pause >nul");
	    return 0;
	}

## Przekazywanie argumentów przez referencję

Referencja występuje jedynie w języku C++ (oraz innych językach wysokiego poziomy jak C# i Java). Wszystko odbywa się na takiej samej zasadzie jak w przypadku wskaźników. Sama **referencja także bardzo przypomina wskaźniki**. Ciężko jednoznacznie podać definicję referencji, która jednoznacznie odróżni ją od wskaźnika.

### Różnica pomiędzy referencją a wskaźnikiem

**Wskaźnik jest typem zmiennej**, czyli zmienną wskaźnikową. Do wskaźnika możemy przypisać dowolny adres pamięci, na którą ten wskaźnik będzie wskazywał. Dlatego przekazując wskaźnik do funkcji, zostaje on przekazany przez wartość (a więc skopiowany), jednak prawdziwą wartością wskaźnika jest właśnie adres pamięci. Próbując wyświetlić wskaźnik ukaże się naszym oczom adres pamięci. Aby natomiast dostać się do prawdziwej wartości wskaźnika, trzeba posłużyć się operatorem wyłuskania (gwiazdka), czyli operatorem pobrania wartości wskaźnika.

Skoro wskaźnik jest typem zmiennej, to w dowolnym momencie możemy zmienić adres na jaki wskazuje. **Referencja natomiast jest bezpośrednim adresem pamięci** danej zmiennej. Oznacza to, że nie można jej zmienić, skasować ani uszkodzić. Usunięcie wskaźnika, nie spowoduje utraty zmiennej na jaką wskazywał. Jakiekolwiek nadpisanie referencji, spowoduje natychmiastową utratę danych.

**Przekazywanie argumentów przez referencję** jest łatwiejsze, ponieważ nie trzeba operować na gwiazdkach i ampersandach. To jedyna różnica między referencją a wskaźnikami w argumentach funkcji. Przez to początkujący programiści często mylą lub wręcz nie wiedzą, z którego sposobu chcą skorzystać.

W funkcji tworzymy dowolną ilość argumentów wraz z typami. Nazwy argumentów poprzedzone są ampersandem (&). Zmienne wewnątrz funkcji nie są kopią, oznacza to że operując na zmiennych referencyjnych operujemy także na zmiennej oryginalnej z pod której wywołana została funkcja.

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	void zmien(int &liczba)
	{
	    // modyfikując referencję modyfikujemy też zmienną oryginalną
	    
	    liczba = 123456;
	}
	
	int main()
	{
	    int test = 0;
	    
	    // wywołanie funkcji (referencja zmiennej 'test')
	    zmien(test);
	    
	    // wyświetlenie nowej wartości
	    cout << test << endl;
	    
	    system("pause >nul");
	    return 0;
	}

## Przekazywanie tablic jako argument funkcji

Tablice wygodnie przekazywać do funkcji za pomocą wskaźników. Wynika to z faktu, że nazwa tablicy (bez podania indeksu) jest wskaźnikiem na jej pierwszy element.

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	// przekazujemy przez wskaźnik
	void wyswietl(int tab[])
	{
	    for (int i = 0; i<6; i++)
	        cout << tab[i] << endl;
	}
	
	int main()
	{
	    int tablica[6] = {1,2,3,4,5,6};
	    
	    // nazwa tablicy to wskaźnik na tablica[0]
	    wyswietl(tablica);
	    
	    system("pause >nul");
	    return 0;
	}

Nie tak pięknie wygląda sprawa z przekazywaniem do funkcji tablicy dwuwymiarowej (lub więcej). Ponieważ są to tablice wskaźników do wskaźników, w argumencie funkcji musimy podać wymiary tablicy. Inaczej program nie skompiluje się, ponieważ kompilator nie będzie umiał przewidzieć wielkości poszczególnych wymiarów. <code class="cpp"></code>

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	void wyswietl(int tab[3][3])
	{
	    for (int i = 0; i<3; i++)
	        for (int j = 0; j<3;j++)
	            cout << tab[i][j] << endl;
	}
	
	int main()
	{
	    int tablica[3][3];
	    
	    for (int i = 0; i<3; i++)
	        for (int j = 0; j<3;j++)
	    		    tablica[i][j] = i+j;
	    
	    wyswietl(tablica);
	    
	    system("pause >nul");
	    return 0;
	}