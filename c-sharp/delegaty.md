---
title: Delegaty
category: c-sharp
thumbnail: delegaty.png
published: 2015-03-28T00:00:00Z
---
**Delegaty** są ściśle związane z językiem C#. **Delegaty** są bardzo często porównywane do wskaźników na funkcje znanych z języka C++. Oferują bardzo podobną funkcjonalność, jednak są o wiele bezpieczniejsze i udostępniają większe możliwości. Zapewniają kontrolę typów oraz wywołania asynchroniczne metod.

<!--more-->

## Czym są delegaty?

Krótka książkowa definicja, mówiąca **czym jest delegat**:

**Delegaty to obiekty, które wskazują na dowolne metody** znajdujące się w programie. Są definiowane za pomocą słowa kluczowego *delegate*. Przypominają działanie wskaźników z języka C++, jednak zapewniają kontrolę typów. Mogą wskazywać metody instancyjne oraz statyczne.
{: .alert-info} 

**Po co są potrzebne delegaty?** Problem używania wskaźników na funkcje istnieje od zawsze jeszcze od czasów świetności języków C/C++. Projektując różne rozbudowane systemy istniała potrzeba, aby funkcja wywoływana mogła komunikować się z funkcją wywołującą.

Wskaźniki na funkcje mogą być argumentami metod, dzięki temu do funkcji można przesłać adresy różnych funkcji zależnie od wymaganej sytuacji lub wyboru użytkownika. **Taką samą elastyczność zapewniają delegaty w języku C#**.

Idąc dalej tym tropem dochodzimy do tematu **funkcji wywołań zwrotnych** (callbacks). Znane przede wszystkim z C++ callbacki są po prostu wskaźnikami na funkcję, więc ich odpowiednikiem jest **delegata w C#**.

### Czym są callbaki?

Ideologia używania callbacków jest odwrotną do ideologii używania zwykłych funkcji. Posiadając dowolną metodę, programista zajmuje się jej wywołaniem względem instancji danej klasy.

Dzięki użyciu callbacków **sytuacja się odwraca**. Programista nie wywołuje funkcji manualnie, a jedynie definiuje funkcję o odpowiedniej nazwie i parametrach, do późniejszego wywołania **przez inne elementy systemu** (niezależne od naszego programu).

Opracowanie takiego mechanizmu było koniecznością aby zapewnić np. współpracę API Win32 z programami pisanymi pod Windowsa. Posługując się wskaźnikami na funkcje oraz callbackami, funkcje API Windowsa **nie miały narzuconego wywołania konkretnej metody** - mogły wywoływać wskaźnik na funkcję, a funkcję dopisywał na własną rękę programista.

Jest to **niesamowicie elastyczne** rozwiązanie. Dokładnie taki sam mechanizm zapewnia nam **język C# w postaci delegat** - mimo, że jest to język wysokiego poziomu i komunikacja z API Win32 nie jest już potrzebna.

### Nie tylko API Windowsa

Powyższe zastosowanie jest tylko przykładem, nie należy **wiązać delegatów** konkretnie z funkcjami API Windowsa. **Delegat jest wskaźnikiem** na dowolną funkcję lub metodę.

Niejednokrotnie ich użycie może przyczynić się do znacznej optymalizacji kodu naszego programu. Wszystko zależy od sytuacji. Przejdźmy do przykładów.

### Budowa delegatów

Delegaty są obiektami dziedziczącymi bezpośrednio niejawnie z *MulticastDelegate*. Ta z kolei dziedziczy z klasy *Delegate* oraz *Object*.

Deklarując **nowy delegat** kompilator automatycznie przekształca go na klasę (widać to w języku CIL). Klasa ta jest automatycznie zapieczętowana (*sealed*) a więc nie można z niej dziedziczyć. Automatycznie są także generowane niektóre metody. Oto lista **najważniejszych metod każdego delegata**:

- *Invoke()* - wywołuje metody podpięte do delegata (jedną lub listę metod). Przyjmuje takie same argumenty jak metoda związana z delegatem. **Działa synchronicznie** względem głównego wątku programu.
- *BeginInvoke()* - wywołuje metody związane z delegatem **w sposób asynchroniczny**. Wywołana metoda zostaje wykonana w osobnym wątku.

Jak widzisz w odróżnieniu od wskaźników funkcyjnych znanych z C++, delegaty w języku C# zapewniają bardzo wygodną możliwość **asynchronicznego wywoływania metod**. Jest to w wielu sytuacjach główna przyczyna używania w danym momencie delegata zamiast zwykłego wywołania funkcji.

## Tworzenie delegatów

W bardzo prosty sposób jesteśmy w stanie stworzyć dowolny **delegat** wskazujący na dowolne metody. Do tego celu należy posłużyć się słowem kluczowym *delegate*. Aby delegat mógł wskazywać na metodę, **musi posiadać taki sam typ zwracany oraz takie same argumenty**. Przykładowy delegat może wyglądać następująco:

	public delegate int Dzialanie(int x, int y); // delegat w C#
	typedef int ( * Dzialanie ) ( int, int ); // wskaźnik na funkcje w C++

Oprócz **przykładu delegata w C#**, umieściłem identyczny w działaniu wskaźnik na funkcję w języku C++. Zapewne od razu widzisz różnicę w czytelności i prostocie kodu. W C# nie występują żadne gwiazdki ani podejrzany format składni. **Delegat wygląda w C#** tak samo, jak zwykła deklaracja metody z dodanym słowem *delegate*.

Jak należy rozumieć utworzony przez nas delegat? Może on wskazywać na dowolną funkcję przyjmującą dwa argumenty typu *int* oraz zwracającą wartość typu *int*.

## Podpinanie metod do delegatów

Mając zadeklarowany delegat, możemy **podpiąć do niego dowolną metodę**. Ważne jest to, aby zgodne były argumenty oraz typ zwracany. Co ciekawe, **delegaty w C#** obsługują tzw. *multicasting*. Dzięki temu możliwe jest podpięcie wielu metod do jednego delegata.

**Podpinanie metod pod delegat** odbywa się za pomocą:

- pierwsza metoda podpinana jest poprzez konstruktor, **podczas tworzenia instancji delegata**
- każdą następną metodę możemy dodać/usunąć za pomocą operatorów *+=* oraz *-=*

Wywołując metody delegata możemy, ale nie musimy, użyć metody *Invoke()*. Spójrzmy na prosty przykład:

	public delegate void Dzialanie(int x, int y);
	
	public class Matma
	{
	    public void Dodaj(int l1, int l2) { Console.WriteLine(l1 + l2); }
	    public void Odejmij(int l1, int l2) { Console.WriteLine(l1 - l2); }
	}
	
	class Program
	{
	    static void Main()
	    {
	        Matma matma = new Matma();
	        
	        Dzialanie dzialanie = new Dzialanie(matma.Dodaj);
	        
	        // wywoła matma.Dodaj(5,5)
	        dzialanie(5,5);
	        
	        dzialanie += matma.Odejmij;
	        // wywoła matma.Dodaj(7,4)
	        // wywoła matma.Odejmij(7,4)
	        dzialanie(7, 4);
	        
	        dzialanie.Invoke(7,4) // to samo co wyzej
	        
	        Console.ReadKey();
	    }
	}

Do delegata *Dzialanie* mogliśmy podpiąć dwie metody *Matma.Dodaj* oraz *Matma.Odejmij*, dlatego że **mają taki sam typ zwracany** i przyjmują **takie same argumenty**.

## Delegaty jako funkcje wywołań zwrotnych

W rzeczywistych przypadkach, delegaty nie służą nam do robienia nikomu niepotrzebnych wskaźników na funkcje, tylko po to aby wywoływać funkcję poprzez delegat a nie jej nazwę. Takie zjawisko występuje tylko wtedy, **kiedy zależy nam na szybkim wywołaniu asynchronicznym** danej metody.

W większości przypadków delegaty używane są do **tworzenia funkcji zwrotnych** tzw. callbacków. W tym celu **deklarujemy delegat wewnątrz interesującej nas klasy** - nie ma w tym zachowaniu nic złego. Wręcz przeciwnie, deklarowanie delegatów wewnątrz innych klas jest całkiem normalne i często spotykane. Deklaracja delegata wewnątrz jakiejkolwiek klasy zostanie niejawnie rozwinięta przez kompilator do postaci klasy zagnieżdżonej.

Jeszcze raz, bardzo krótko - czym jest callback? Jest to funkcja zwrotna, której **idea używania jest przeciwna** do standardowego używania funkcji. Zamiast wywoływać interesujące nas funkcje, nasz obiekt wywołuje delegat. To **na co będzie wskazywał delegat w przyszłości**, zależy już od innych obiektów i programistów pracujących na naszej klasie.

Umieszczając delegat wewnątrz jakiejś klasy trzymajmy się zasad:

  * **definicja delegata musi być publiczna** - zostanie niejawnie rozwinięta do definicji zagnieżdżonej klasy
  * zgodnie z **zasadą hermetyzacji**, zmienna delegata **musi być prywatna**. Obudowujemy ją &#8222;setterem&#8221; w celu zapewnienia dostępu z zewnątrz (kiedyś tę rolę przejmą za Ciebie *eventy*)

Dla przykładu, zaprojektujmy klasę udającą element jakiegoś systemu. Klasa będzie miała podstawowe funkcjonalności takie jak autoryzacja czy logowanie. **Dzięki użyciu delegatów** będziemy mogli zaimplementować w naszej klasie **system logowania operacji** w postaci callbacka.

	public class System
	{
	    //publiczna definicja delegaty (będzie klasą zagnieżdżoną)
	    public delegate void Logi(string wiadomosc);
	    
	    //prywatna zmienna delegata
	    private Logi _wyslijLogi;
	    
	    //setter dla zmeinnej delegata
	    public void DodajCallback(Logi funkcja)
	    {
	        _wyslijLogi += funkcja;
	    }
	    
	    public void UsunCallback(Logi funkcja)
	    {
	        _wyslijLogi += funkcja;
	    }
	    
	    //przykladowa funkcjonalność naszej klasy
	    public bool Logowanie(string user, string password)
	    {
	        /* jakeiś operacje odpowiedzialne za Autoryzację */
	        
	        _wyslijLogi("Nastąpiła próba logowania użytkownika: " + user);
	        
	        return true;
	    }
	}
	
	class Program
	{
	    static void Main()
	    {
	        //tworzymy instancję klasy i podpinamy funckję zwrotną
	        System system = new System();
	        system.DodajCallback(CallbackLogi);
	        
	        system.Logowanie("user", "pass");
	        
	        Console.ReadKey();
	    }
	    
	    static void CallbackLogi(string wiadomosc)
	    {
	        Console.WriteLine(wiadomosc);
	        Console.WriteLine("Została wywołana funkcja zwrotna");
	    }
	}

Powyższy przykład ukazuje ideologię **używania delegatów jako funkcji zwrotnych**. Programista otrzymujący gotową klasę, może zaimplementować funkcję wywołań zwrotnych i podpiąć ją w odpowiedni sposób.

Sytuacja wyglądała by jeszcze lepiej, gdybyśmy podpinali funkcję wywołań zwrotnych w konstruktorze klasy podczas **tworzenia jej instancji**. W ten sposób działa wiele funkcji z API Win32.

Co ciekawe, dzięki obsłudze multicastingu, w powyższym przykładzie możemy podpiąć dowolną ilość funkcji wywołań zwrotnych. Zostaną one wywołane wszystkie podczas wywołania metody *System.Logowanie()*.

### Delegaty jako parametry funkcji

Nic nie stoi na przeszkodzie aby użyć delegata jako parametr funkcji. Takie działanie nadal jest nazywane mianem funkcji zwrotnej. Spójrz na przykład:

	public class Liczby
	{
	    // deklaracja publicznej delegaty
	    public delegate void DelOperacja(ref ArrayList list);
	    
	    //prywatna kolekcja
	    ArrayList _listaLiczb = new ArrayList();
	    
	    //konstruktor
	    public Liczby(params int[] liczby)
	    {
	        _listaLiczb.AddRange(liczby);
	    }
	    
	    //callback wywołujący delegatę. Co wywoła? Nie wiadomo!
	    //zalezy to od kogoś, kto będzie pracował na naszej klasie
	    public void Operacja(DelOperacja operacja)
	    {
	        operacja(ref _listaLiczb);
	    }
	}
	
	//statyczna klasa z metodami, które pasują do callbacka
	public static class Operacje
	{
	    public static void Drukuj(ref ArrayList lista)
	    {
	        foreach (var i in lista) { Console.Write(i); }
	        Console.WriteLine();
	    }
	    
	    public static void Odwroc(ref ArrayList lista)
	    {
	        lista.Reverse();
	    }
	}
	
	class Program
	{
	    static void Main()
	    {
	        Liczby l = new Liczby(1, 2, 3, 4, 5, 6);
	        
	        //wypisze liczby
	        l.Operacja(Operacje.Drukuj);
	        
	        //wypisze liczby w odwrotnej kolejności
	        l.Operacja(Operacje.Odwroc);
	        l.Operacja(Operacje.Drukuj);
	        
	        Console.ReadKey();
	    }
	}

Zwróć uwagę na dziwny przypadek ukazany w przykładzie. Mimo, że klasa *Liczby* pozornie **nie udostępnia żadnej funkcjonalności** - wygląda bardziej jak niestandardowa kolekcja obiektów - to **dzięki funkcjom wywołań zwrotnych i delegatom** możemy bardzo rozszerzać jej funkcjonalność.

Dzieje się tak dlatego, że programista tworzący klasę *Liczby* pomyślał, że warto będzie **zaimplementować w niej callback** o nazwie *Operacja*. Wywołuje ona funkcję zwrotną z referencją do prywatnej kolekcji, do której nie można byłoby dostać się winny sposób.

## Delegaty generyczne

Z poprzednich artykułów od razu rzuca się w oczy, **jak nieelastyczne są delegaty**. Zapewniają one **bardzo ścisłą kontrolę typów**, dlatego nie reagują dobrze na jakiekolwiek zmiany w kodzie.

Rozwiązaniem tego problemu są delegaty generyczne. Zanim typy generyczne został wprowadzone do języka C#, można było uzyskać podobny efekt korzystając z **mechanizmu pakowania** (boxing). Polegało to po prostu na utworzeniu delegaty, której argumentem był *object*.

Takie rozwiązanie przynosiło kilka wad:

  * wszystkie argumenty musiały podlegać **pakowaniu i rozpakowywaniu**, przez co spada wydajność
  * nie istniała zupełnie żadna kontrola typów, ponieważ wszystko da się rzutować na *object*

## Gotowe delegaty Func i Action

W którejś wersji frameworka .NET pojawił się zestaw kilku gotowych delegatów, z czego najbardziej interesującymi są **Func oraz Action**.

Dzięki nim proces tworzenia delegatów został skrócony do absolutnego minimum. Nie trzeba tworzyć własnych deklaracji, wystarczy użyć gotowego rozwiązania.

### Delegat Func

Delegat *Func* jest **delegatem generycznym**. Podczas deklaracji trzeba określić typ zwracany oraz typ parametrów. Konstruktor został przeciążony aż 17 razy, dlatego możemy być pewni, że dopasujemy go do każdej naszej funkcji.

Należy pamiętać, że w kwadratowych dzióbkach końcowy parametr jest zawsze tym zwracanym. Wszystkie jakie dopiszemy wcześniej będą wejściowymi:

	Func<out>
	Func<in, out>
	Func<in, in, out>
	itd..

Formalna definicja delegata *Func* wygląda następująco:

	public delegate TResult Func<in T, out TResult>(T arg);

Oprócz tego został on oczywiście 17 razy przeciążony. Oto Przykład użycia delegaty *Func*:

	public class Matematyka
	{
	    public int Dodawanie(int x, int y)
	    {
	        return x + y;
	    }
	}
	
	class Program
	{
	    static void Main()
	    {
	        Matematyka m = new Matematyka();
	        
	        //podpięcie delegata Func do metody klasy Matematyka
	        Func<int,int,int> dodawanie = m.Dodawanie;
	        
	        Console.WriteLine(dodawanie(1, 2));
	        
	        Console.ReadKey();
	    }
	}

### Delegat Action

Działanie delegata *Action* jest identyczne, jednak używamy go w przypadku procedur. Definicja formalna może wyglądać następująco:

	public delegate void Action<in T>(T arg);

Jedyna różnica występuje podczas określania parametru generycznego. **Wszystkie typy wymienione w kwadratowych dzióbkach będą parametrami**. Spójrzmy na przykład:

	static void Main()
	{
	    Matematyka m = new Matematyka();
	    
	    //podpięcie delegata Action do Console.WriteLine
	    Action<string> wypisz = System.Console.WriteLine;
	    
	    wypisz("jakis tekst");
	    
	    Console.ReadKey();
	}

W przykładzie utworzyliśmy **delegat** o nazwie *wypisz*, który wskazuje na funkcję *System.Console.WriteLine*. Określiliśmy mu jeden argument typu *string*.

## Podsumowanie

Delegaty są bardzo prostym i lekkim działem. Ich zrozumienie jest bardzo ważne, ponieważ **delegaty są fundamentem** do dobrego zrozumienia wątków oraz zdarzeń.

O zdarzeniach, metodach anonimowych i wyrażeniach lambda napiszę w innym wpisie.