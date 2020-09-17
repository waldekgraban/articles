---
title: Konstruktory
category: cpp
thumbnail: konstruktory.png
published: 2014-03-06T00:00:00Z
---
W języku **C++** istnieje wiele rodzajów **konstruktorów**. Ich implementacja w klasach jest bardzo łatwa i nie sprawia problemów nawet początkującym programistom. Drobny problem z **konstruktorami** może pojawić się w bardziej zaawansowanych przypadkach np. podczas **dziedziczenia**.

<!--more-->

## Czym jest konstruktor?

**Konstruktor** to metoda, którą zawiera każda klasa. Jest ona wywoływana automatycznie podczas tworzenia instancji danej klasy. **Konstruktor** jest odpowiedzialny za inicjację klasy, czyli np. wypełnianie pól początkowymi wartościami.
{: .alert-info}

Istnieje wiele rodzajów **konstruktorów**. Możemy definiować je jawnie, mamy wtedy pełną kontrolę nad jego wyglądem i zachowaniem. W przypadku braku konstruktora, zostanie on niejawny dodany do klasy automatycznie podczas kompilacji programu.

Dodanie konstruktora do klasy jest bardzo proste i polega na dodaniu do klasy **metody o tej samej nazwie co klasa**. Należy zapamiętać, aby nie podawać typu zwracanego.

## Konstruktor domyślny (bezparametrowy)

Podstawowym rodzajem konstruktorów są **konstruktory domyślne**. Cechują się one brakiem jakichkolwiek parametrów &#8211; co za tym idzie, podczas tworzenia instancji danej klasy nie przekazujemy **żadnych argumentów** do konstruktora.

	class Osoba {
	public:
	    string imie;
	    int wiek;
	    
	    Osoba() {
	        imie = "brak";
	        wiek = 0;
	        cout << "konstruktor domyslny\n";
	    }
	};
	
	int main()
	{
	    Osoba *Karol = new Osoba();
	    Osoba Arek;
	    
	    system("pause");
	    
	    return 0;
	}

Po uruchomieniu powyższego programu, dwa razy wyświetli się napis *konstruktor domyślny*. Nie ma znaczenia czy obiekt tworzony jest statycznie czy dynamicznie, podczas tworzenia instancji klasy **konstruktor** i tak został wywołany.

Co ciekawe **konstruktor domyślny** może posiadać dowolną ilość parametrów, jednak wszystkie muszą mieć **zdefiniowaną wartość domyślną** (aby nie trzeba było podawać ich wartości podczas inicjacji klasy).

	class Osoba {
	public:
	    string imie;
	    int wiek;
	    
	    Osoba(string imie = "brak", int wiek = 0) {
	        this->imie = imie;
	        this->wiek = wiek;
	        cout << "konstruktor domyslny\n";
	    }
	};
	
	int main()
	{
	    Osoba *Karol = new Osoba();
	    Osoba Arek("Arek", 12);
	    
	    cout << Karol->imie << endl; //wyswietli brak
	    cout << Arek.imie << endl;   //wyswietli Arek
	    
	    system("pause");
	    
	    return 0;
	}

Mimo posiadania argumentów, konstruktor dalej jest nazywany **domyślnym**, ponieważ podanie argumentów nie jest wymagane.

## Konstruktor wieloargumentowy

Jest to **zwykły konstruktor** najczęściej spotykany i najczęściej używany. Zawiera on listę argumentów, dzięki którym możliwa jest inicjacja pól podczas tworzenia obiektu.

	class Osoba {
	public:
	    string imie;
	    int wiek;
	    
	    Osoba(string imie, int wiek) {
	        this->imie = imie;
	        this->wiek = wiek;
	        cout << "konstruktor wieloargumentowy\n";
	    }
	    
	    void toString() {
	        cout << imie << "\n" << wiek << "\n";
	    }
	};
	
	int main()
	{
	    Osoba *Arek = new Osoba();  // bląd kompilacji - brak parametrów!
	    
	    Osoba *Karol = new Osoba("Karol", 22);  // dobrze
	    Karol->toString();
	    
	    system("pause");
	    
	    return 0;
	}

Uruchomienie powyższego kodu nie uda się, ponieważ podczas tworzenia obiektu *Arek*, nie zostały podane **wymagane argumenty**. W przypadku obiektu *Karol* pola zostaną poprawnie zainicjowane a następnie wyświetlone w konsoli.

Aby naprawić powyższy program możemy zamienić **konstruktor wieloargumentowy** na **konstruktor domyślny**, dopisując domyślne wartości argumentów. Innym często stosowanym zabiegiem jest **przeciążenie konstruktorów**.

## Przeciążanie konstruktorów

Konstruktory możemy **przeciążać** tak jak wszystkie inne metody. Nie jest to żaden błąd, raczej operacja często stosowana i spotykana w programach.

	class figura {
			int a;
			int b;
	
	public:
	    // konstruktor domyslny
	    figura() {
	        a = 0;
	        b = 0;
	        cout << "konstruktor domyslny" << endl;
	    }
	    
	    figura(int a) {
	        this->a = a;
	        cout << "konstruktor (kwadrat)" << endl;
	    }
	    
	    figura(int a, int b) {
	        this->a = a;
	        this->b = b;
	        cout << "konstruktor(prostokat)" << endl;
	    }
	};
	
	int main()
	{
	    figura *nowa = new figura; // wyswietli: konstruktor domyslny
	    figura *kwadrat = new figura(5); // wyswietli: konstruktor (kwadrat)
	    figura *prostokat = new figura(5,3); // wyswietli: konstruktor (prostokat)
	    
	    system("pause");
	    
	    return 0;
	}

## Konstruktor kopiujący

**Konstruktor kopiujący** jest przypadkiem szczególnym, ponieważ wywoływany jest tylko w momencie kopiowania danego obiektu. Przyjmuje on tylko jeden argument &#8211; referencję do swojej klasy. Jeżeli nie zdefiniujemy **konstruktora kopiującego**, zostanie od zdefiniowany niejawnie przez kompilator (nawet w przypadku gdy w klasie są też inne konstruktory).

	class Osoba {
	public:
	    string imie;
	    int wiek;
	    
	    Osoba (const Osoba &osoba) {
	        imie = osoba.imie;
	        wiek = osoba.wiek;
	        cout << "konstruktor kopiujacy\n";
	    }
	    
	    Osoba(string imie = "", int wiek = 0) {
	        this->imie = imie;
	        this->wiek = wiek;
	    }
	    
	    void toString() {
	        cout << imie << "\n" << wiek << "\n";
	    }
	};
	
	int main()
	{
	    Osoba *Karol = new Osoba("Karol", 22);  // dobrze
	    
	    Osoba Arek (*Karol);  // kopiowanie obiektu Karol
	    
	    Arek.toString();
	    
	    system("pause");
	    
	    return 0;
	}

W przykładzie, obiekt *Karol* zostanie skopiowany i wywoła się **konstruktor kopiujący**. Po wykonaniu programu wyświetlą się dane, którymi został zainicjowany obiekt *Karol*.

Po co jawnie implementować **konstruktor kopiujący**? Jest to niezbędne w przypadku klas zawierających wskaźniki. Podczas kopiowania obiektu, zostają skopiowane wszystkie wartości jego pól. Wartością zmiennej wskaźnikowej jest po prostu **adres na jaki wskazuje**, a więc zostaje skopiowany adres a nie **wskazywana wartość**.

Efektem tej niedogodności jest sytuacja, w której dwie różne kopie obiektów zawierają pola wskaźnikowe, wskazujące na ten sam adres (a więc obiekty nie są od siebie niezależne, bo modyfikacja jednego wpływa na stan drugiego).

#### Kiedy zostaje wywołany konstruktor kopiujący

- podczas **kopiowania obiektów**
- podczas **przekazywania obiektu do funkcji przez wartość**, ponieważ wtedy tworzona jest na stosie jego kopia

Przykładowy kod:

	class Osoba {
	public:
	    string imie;
	    
	    Osoba (const Osoba &osoba) {
	        imie = osoba.imie;
	        cout << "konstruktor kopiujacy\n";
	    }
	    
	    Osoba(string imie = "") {
	        this->imie = imie;
	    }
	    
	};
	
	void fun(Osoba a) {
	    cout << "wywolanie funkcji\n";
	}
	
	int main()
	{
	    
	    Osoba *Karol = new Osoba("Karol");
	    
	    Osoba Arek (*Karol);  // pierwsze wywolanie konst kopiujacego
	    fun(Arek);            // drugie wywolanie konst kopiujacego
	    
	    system("pause");
	    
	    return 0;
	}

W innym artykule opisałem [listę inicjalizacyjną C++](/cpp/lista-inicjalizacyjna/), która jest poszerzeniem możliwości **zwykłych konstruktorów**. Artykuł może Cię zainteresować.