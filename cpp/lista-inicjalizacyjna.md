---
title: Lista inicjalizacyjna
category: cpp
thumbnail: lista-inicjalizacyjna.png
published: 2014-03-14T00:00:00Z
---
W niniejszym artykule omówię **listę inicjalizacyjną** w języku **C++**. Jest ona bardzo często używana podczas bardziej rozbudowanych projektów programistycznych. Zastosowanie **listy inicjalizacyjnej** jest niezbędne podczas używania zaawansowanych mechanizmów programowania obiektowego.

<!--more-->

## Czym jest lista inicjalizacyjna

**Lista inicjalizacyjna** jest rozszerzeniem możliwości zwykłego konstruktora. Jej zadaniem jest **inicjalizacja** składowych nowego obiektu. Ważnym jest fakt, że wykonuje się ona jeszcze **zanim obiekt zacznie istnieć**.

Na pierwszy rzut oka ciężko znaleźć różnicę pomiędzy **konstruktorem** a **listą inicjalizacyjną**. Obydwa mechanizmy są ze sobą bardzo ściśle związane. Sama lista stanowi poszerzenie możliwości konstruktora, a więc nie można zdefiniować **listy inicjalizacyjnej** nie definiując **konstruktora** w danej klasie.

Cechą konstruktorów jest to, że wykonują się one w momencie kiedy obiekt klasy już istnieje. Co za tym idzie, **konstruktory** mogą modyfikować wartości składowych klas jednak w niektórych przypadkach staje się to niemożliwe. Jeżeli składową klasy jest zmienna *const* wtedy nie będziemy mieli możliwości nadania jej wartości poprzez konstruktor.

**Lista inicjalizacyjna**  znajduje się w definicji konstruktora i poprzedzona jest dwukropkiem. Argumenty w **liście inicjalizacyjnej** przypisywane są składowym klasy w specyficzny sposób, przeanalizuj poniższy obrazek:

{% include parts/postPicture.html page=page img="lista-inicjalizacyjna-c" %}

Wartość zmiennej *WIEK*, która jest **argumentem konstruktora** przypisywana jest zmiennej *wiek*, która jest składnikiem klasy. Po przecinku można wypisać kolejne **inicjalizowane** składowe.

Zwróć uwagę, że w przypadku użycia **listy inicjalizacyjnej** ciało konstruktora jest puste. Można oczywiście umieścić w nim jakiejś instrukcje. Niektóre składowe mogą być **inicjalizowane** poprzez konstruktor a inne poprzez **listę inicjalizacyjną**.

## Składowe stałe const

Tak jak było napisane w poprzednim akapicie, w konstruktorze nie można przypisać zmiennej *cons* żadnej wartości, ponieważ podczas wywołania konstruktora **obiekt już istnieje**. Poniższy przykład **nie skompiluje się**:

	class Osoba {
	public:
	    const int wiek;
	    const int wzrost;
	    string imie;
	    
	    Osoba(int WIEK, int WZROST, string IMIE) {
	        // błąd kompilacji !!!
	        this->wiek = WIEK;
	        this->wzrost = WZROST;
	        this->imie = IMIE;
	    }
	};

Zamierzony efekt można osiągnąć używając **listy inicjalizacyjnej**. Składowe *wiek* oraz *wzrost* musimy zainicjalizować na liście, jednak wartość składowej *imie* można przypisać w konstruktorze:

	class Osoba {
	public:
	    const int wiek;
	    const int wzrost;
	    string imie;
	    
	    Osoba(int WIEK, int WZROST, string IMIE) : wiek(WIEK), wzrost(WZROST) {
	        this->imie = IMIE;
	    }
	};

Jeżeli jest taka możliwość, powinno się jednak wszystkie możliwe składowe inicjalizować za pomocą listy inicjalizacyjnej, ponieważ jest to rozwiązanie **szybsze** i **wydajniejsze** od użycia konstruktorów.

## Wywołanie konstruktorów obiektów składowych

**Lista inicjalizacyjna** jest jedyną metodą na wywołanie **konstruktorów obiektów składowych**. Jest to przypadek kiedy nie używamy żadnego dziedziczenia, jedynie jedna klasa jest elementem innej klasy.

	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	class Silnik {
	public:
	    int moc;
	    int max_moment_obr;
	    
	    Silnik(int moc, int moment) {
	        this->moc = moc;
	        this->max_moment_obr = moment;
	    }
	};
	
	class Samochod {
	public:
	    Silnik silnik;
	    string model;
	    
	    Samochod(string MODEL, int MOC, int MOMENT)
	        : silnik(MOC, MOMENT), model(MODEL) {
	            // puste cialo konstruktora
	    }
	    
	    void informacje() {
	        cout << "model: " << model
	            << "\nmoc: " << silnik.moc
	            << "\nmax moment obrotowy: " << silnik.max_moment_obr << "\n\n";
	    }
	};
	
	int main()
	{
	    Samochod *fiat = new Samochod("Fiat 126p", 23, 3200);
	    
	    fiat->informacje();
	    
	    system("pause");
	    return 0;
	}

W momencie kompilacji programu, kompilator szuka **konstruktora** domyślnego dla klasy *Silnik* ponieważ jest ona składnikiem klasy *Samochod*. Ponieważ klasa *Silnik* nie posiada konstruktora domyślnego kompilacja programu się nie uda.

Nie można **zainicjalizować** wartości klasy *Silnik* w konstruktorze klasy *Samochod*, ponieważ w momencie wywołania konstruktora klasy *Samochod* składowa *Silnik silnik;* już istnieje. Trzeba wykorzystać **listę inicjalizacyjną** i w niej wywołać konstruktor wieloargumentowy.

## Lista inicjalizacyjna a tablice

Nie ma możliwości inicjalizacji **tablicy** za pomocą **listy inicjalizacyjnej**.