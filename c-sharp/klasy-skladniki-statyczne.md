---
title: Klasy i składniki statyczne
category: c-sharp
thumbnail: klasy-statyczne.png
published: 2015-01-24T00:00:00Z
---
Zrozumienie funkcji **danych statycznych** jest podstawą programowania obiektowego. W niniejszym artykule opiszę zasadę tworzenia **klas statycznych w C#**. Oprócz tego dowiesz się czym są **statyczne pola i metody** występujące w klasach. Ostatnim omówionym pojęciem będą **statyczne konstruktory**.

<!--more-->

## Wstęp do klas w C#

Język C# **jest językiem w pełni obiektowym**. Wszystko co tworzysz jest obiektem i dziedziczy po klasie *Object*. Programowanie obiektowe nie istniałoby bez pojęcia klasy.

**Nowo utworzona klasa jest nowym typem** stworzonym przez programistę. Programista może utworzyć dowolną ilość klas. Każda z nich reprezentuje osobny byt. Dla przykładu, możemy utworzyć klasę *człowiek*, reprezentującą byt ludzki. Nie reprezentuje ona pojedynczej osoby o określonych cechach tylko ludzką rasę w znaczeniu ogólnym. Klasa reprezentująca typ *człowiek* skupia w sobie składniki, umożliwiające opisanie cechy konkretnego człowieka.

Jeżeli chcemy aby nasza klasa (czyli nasz nowy typ) **reprezentował konkretną jednostkę**, należy utworzyć **instancję klasy**. Instancją klasy *człowiek* może być konkretna osoba o danym imieniu i nazwisku. Instancja klasy nazywana jest także obiektem danej klasy.

Obiekt klasy jest definiowany poprzez jej składniki. Składnikami są różne zmienne oraz funkcje. Składniki opisują rzeczywisty stan obiektu.

**Funkcje w klasach nazywamy metodami, a zmienne polami**. Tworzą one składniki (składowe) klasy. **Składniki klasy opisują stan instancji klasy**.
{: .alert-info}

Klasy definiujemy przy pomocy słowa kluczowego *class*. Składniki klasy mogą być *publiczne*, *prywatne* lub *chronione*. **Jeżeli składowa jest publiczna** wtedy mogą z niej korzystać **wszystkie inne obiekty** "z zewnątrz". **Jeżeli jest prywatna** to jest dostępna tylko wewnątrz naszej klasy. Oznacza to, że inne obiekty nie będą w stanie jej modyfikować ani odczytywać. Z kolei **dostęp do składowych chronionych** ma sama klasa oraz klasy dziedziczące po niej (podklasa).

Jeżeli definiujemy składową bez modyfikatora dostępu, wtedy **staje się ona automatycznie składową prywatną**.

Paradygmat programowania obiektowego mówi, aby *wszystkie pola klasy definiować jako prywatne*. Te pola, które powinny być dostępne na zewnątrz klasy nie powinny być publiczne, a jedynie *rozszerzone o dodanie publicznych właściwości*.
{: .alert-info}

Czym są właściwości możesz przeczytać przechodząc do artykułu [Właściwości i akcesory get set](/c-sharp/wlasciwosci-akcesory-get-set/)"

Oto ciało przykładowej klasy reprezentującej człowieka:

	class Czlowiek
	{
	    public string imie { get; set; }
	    public int wiek { get; set; }
	    
	    public void wypiszRokUrodzenia()
	    {
	        Console.WriteLine("{0} urodzil sie w roku {1}", imie, 2014 - wiek);
	    }
	}

Klasa posiada dwie publiczne właściwości, które **niejawnie generują dwa prywatne pola**. Jest do skrócona forma deklaracji właściwości. Oprócz tego posiadamy publiczną metodę, wypisującą na ekran rok urodzenia na podstawie wieku.

Oprócz tego, na etapie kompilacji do klasy zostanie **niejawnie dołączony publiczny domyślny konstruktor**. Gdyby nie to, to nie bylibyśmy w stanie utworzyć instancji klasy.

Tworzenie instancji klasy może wyglądać następująco:

	class Program
	{
	    static void Main()
	    {
	        Czlowiek karol = new Czlowiek();
	        karol.imie = "Karol";
	        karol.wiek = 23;
	        karol.wypiszRokUrodzenia();
	        
	        Console.ReadKey();
	    }
	}
	
	class Czlowiek
	{
	    public string imie { get; set; }
	    public int wiek { get; set; }
	    
	    public void wypiszRokUrodzenia()
	    {
	        Console.WriteLine("{0} urodzil sie w roku {1}.", imie, 2015 - wiek);
	    }
	}

Kluczową linijką jest linijka nr 5. Składa się ona z dwóch kluczowych operacji. Pierwszą jest **utworzenie referencji dla instancji** klasy *Czlowiek*. Dzieje się to poprzez prostą deklarację *Czlowiek karol;*. **Referencja zostaje zapisana na stosie jednak jest pusta** (nie wskazuje na żadne miejsce w pamięci). Za pomocą operatora *new* **przypisujemy do pustej referencji instancję klasy** *Czlowiek*. Instancja klasy tworzona jest na stercie i zarządza nią mechanizm *Garbage Collectora**.*

Operator *new* wywołuje domyślny konstruktor, który został niejawnie dołączony do klasy w momencie kompilacji.

## Konstruktory w klasach

Konstruktor ma za zadanie **inicjalizowanie** pól klasy domyślnymi wartościami, **jest wywoływany w momencie tworzenia klasy**. Konstruktor domyślny zostaje dołączony do klasy niejawnie w momencie kompilacji, dzięki temu możliwe jest utworzenie obiektu klasy operatorem *new*. Konstruktor jest metodą o takiej samej nazwie jak nazwa klasy.

Warto wspomnieć o słowie kluczowym *this*. Zawiera ono w sobie referencję do danej instancji klasy w obszarze której jest używany. Przydaje się zawsze w momencie **przesłaniania nazw zmiennych** w konstruktorach i metodach.

Dodajmy do naszej klasy **konstruktor niestandardowy**. Będziemy za jego pomocą pobierać imię oraz wiek człowieka:

	class Czlowiek
	{
	    public string imie { get; set; }
	    public int wiek { get; set; }
	    
	    public void wypiszRokUrodzenia()
	    {
	        Console.WriteLine("{0} urodzil sie w roku {1}.", imie, 2015 - wiek);
	    }
	    
	    public Czlowiek(string imie, int wiek)
	    {
	        this.imie = imie;
	        this.wiek = wiek;
	    }
	}

Nazwy argumentów konstruktora są takie same jak nazwy pól klasy. Przez to pola klasy **zostały przesłonięte**. Dzięki słowie kluczowemu *this* odwołujemy się do pól klasy. Ponieważ stworzyliśmy konstruktor niestandardowy, możemy utworzyć naszą klasę oraz zainicjalizować ją początkowymi wartościami:

	class Program
	{
	    static void Main()
	    {
	        Czlowiek karol = new Czlowiek("Karol", 22); // dobrze
	        Czlowiek pusty = new Czlowiek() // błąd kompilacji
	        
	        karol.wypiszRokUrodzenia();
	        
	        Console.ReadKey();
	    }
	}
	
	class Czlowiek
	{
	    public string imie { get; set; }
	    public int wiek { get; set; }
	    
	    public void wypiszRokUrodzenia()
	    {
	        Console.WriteLine("{0} urodzil sie w roku {1}.", imie, 2015 - wiek);
	    }
	    
	    public Czlowiek(string imie, int wiek)
	    {
	        this.imie = imie;
	        this.wiek = wiek;
	    }
	}

Zwróć uwagę na linijkę 6. Jest w niej wywoływany konstruktor domyślny. Niestety, **ponieważ utworzyliśmy konstruktor niestandardowy, kompilator nie dołącza niejawnie domyślnego konstruktora**. Jeżeli takiego potrzebujemy &#8211; musimy dodać go sami.

Kiedy dodamy do klasy konstruktor niestandardowy, kompilator **nie utworzy niejawnie konstruktora domyślnego!** Jeżeli takiego potrzebujemy, musimy zadeklarować go osobiście. Klasa może posiadać wiele różnych konstruktorów.
{: .alert-danger}

Możliwe jest także zdefiniowanie konstruktora prywatnego. Wtedy **nie będzie możliwości utworzenia instancji klasy**. Jest to zabieg celowy i pożądany w kilku specyficznych sytuacjach, np. podczas implementacji wzorca projektowego Singleton.

## Metody i pola statyczne w klasie

Krótkie wprowadzenie do klas pokazało Ci jak one funkcjonują. **Wszystkie metody i pola w klasach są związane z określoną instancją**. Przykładowo, **możemy utworzyć wiele instancji klasy człowiek**, każdy z nich będzie miał inny wiek oraz inne imię.

Dodając do klasy **metodę statyczną** **będzie ona związana z typem** a nie instancją.

**Metody statycznej można używać** bez konieczności tworzenia instancji klasy. Jest ona związana z **typem**. Metoda staje się metodą statyczną jeżeli dodamy do niej modyfikator *static*.
{: .alert-info}

Najlepszym przykładem metody statycznej jest główna funkcja programu o nazwie *Main()*. Czy nie zastanawiałeś się dlaczego zawsze poprzedzona jest słowem *static*?

	class Program
	{
	    static void Main()
	    {
	        // pusty program
	        Console.ReadKey();
	    }
	}

Ponieważ w C# wszystko jest klasą i obiektem, to samo tyczy się głównego programu. *Main()* czyli "element początkowy programu" jest także metodą statyczną, **dzięki temu może być wywołana bez konieczności tworzenia instancji** klasy *Program*.

Innym przykładem metody statycznej są wszystkie metody klasy *System.Console*:

	static void Main()
	{
	    Console c = new Console();
	    c.WriteLine("metoda niestatyczna"); //błąd
	    
	    Console.WriteLine("metoda statyczna"); //ok
	    
	    Console.ReadKey();
	}

Metody klasy *Console* wywołujemy bez tworzenia instancji klasy. **Próba utworzenia instancji wygeneruje błąd**, ponieważ klasa *System.Console* także posiada modyfikator *static*. Oznacza to, **że jest klasą statyczną** i nie możliwe jest utworzenie jej instancji.

**Pola statyczne** także są związane z typem klasy. *Ich wartość jest wspólna dla każdej instancji*.
{: .alert-info}

Nasuwa się pytanie, po co używać pól statycznych? Przydają się one w specyficznych sytuacjach. Działają podobnie na zasadzie zmiennych globalnych. Najczęściej **polami statycznymi** definiuje się pola, które dla każdej instancji powtarzają się (mają tą samą wartość).

## Przykład klasy statycznej

Aby zobrazować funkcjonalność **elementów statycznych w C#**, utwórzmy przykładową klasę zawierającą stałe matematyczne. Mogłaby się przydać podczas pisania jakiegoś programu, który przeprowadza obliczenia.

	class Program
	{
	    static void Main()
	    {
	        Console.WriteLine("Wartość PI to: {0}", StaleMatematyczne.Pi);
	        
	        Console.ReadKey();
	    }
	}
	
	class StaleMatematyczne
	{
	    public static double Pi = 3.14159;
	    public static double stalaEulera = 0.57721;
	    public static double zlotyPodzial = 1.61803;
	}

Jak widzisz, pola klasy *StaleMatematyczne* są statyczne, więc **można ich użyć bez konieczności tworzenia instancji**. Jednak klasa nie jest klasą statyczną i ciągle można utworzyć jej instancję. Ponieważ klasa udostępnia tylko kilka stałych, warto dodać do niej modyfikator *static*. Wtedy nikt bez potrzeby nie utworzy instancji obiektu.

Rozszerzmy klasę, dodajmy do niej możliwość obliczania pola koła. Zmieńmy nazwę klasy na jakąś bardziej ogólną.

**Elementy statyczne** mogą używać tylko danych statycznych i wywoływać metody statyczne. W przeciwnym wypadku wystąpi błąd kompilacji.
{: .alert-danger}

Z uwagi na fakt opisany wyżej, metoda obliczająca pole koła musi być funkcją statyczną. Przykładowa implementacja wygląda następująco:

	class Program
	{
	    static void Main()
	    {
	        int promien_kola = 6;
	        
	        Matematyka.poleKola(promien_kola);
	        Matematyka.obwodKola(promien_kola);
	        
	        Console.ReadKey();
	    }
	}
	
	class Matematyka
	{
	    public static double Pi = 3.14159;
	    
	    public static void poleKola(int r)
	    {
	        Console.WriteLine("Pole kola to: {0}", Matematyka.Pi * r * r);
	    }
	    
	    public static void obwodKola(int r)
	    {
	        Console.WriteLine("Obwód kola to: {0}", Matematyka.Pi * r * 2);
	    }
	}

Warto wspomnieć, że inicjalizacją domyślnych wartości dla pól klasy zajmuje się konstruktor, ale co w przypadku składników statycznych?

## Konstruktory statyczne

Omawiając **składniki statyczne** koniecznie trzeba napisać kilka słów o **statycznych konstruktorach w C#**.

Składową statyczną można zainicjalizować zwykłym konstruktorem, jednak ten zostanie wywołany tylko w przypadku utworzenia instancji klasy. **Statycznego konstruktora** używamy wtedy, kiedy chcemy zainicjalizować **składową statyczną** bez tworzenia instancji klasy.

Jeżeli cała klasa jest statyczna, wtedy ratuje nas jedynie **konstruktor statyczny**. Jeżeli klasa jest zwykłą klasą, możemy rozważyć użycie tego lub tego, jednak inicjalizacja pola statycznego zwykłym konstruktorem jest dość nieprzewidywalna &#8211; nigdy nie wiemy kiedy i czy programista zdecyduje się utworzyć instancję.

Każda klasa może posiadać **tylko jeden statyczny konstruktor** i nie może on być ani publiczny ani prywatny (nie piszemy żadnych modyfikatorów). Jak myślisz, kiedy zostanie on wywołany? Nie jest to do końca jasne.

Klasa może posiadać **jeden konstruktor statyczny**. Zostanie on wywołany **w momencie tworzenia instancji klasy** lub podczas odwoływania się do statycznego elementu klasy.
{: .alert-info}

Przykładowa definicja statycznego konstruktora:

	class Matematyka
	{
	    public static double Pi;
	    
	    static Matematyka()
	    {
	        Matematyka.Pi = 3.14;
	    }
	}

W przypadku połączenia konstruktora statycznego ze zwykłymi, zawsze **pierwszy zostanie wywołany ten statyczny**.

Krótkie podsumowanie **konstruktorów statycznych:**

- w danej klasie może znajdować się **tylko jeden statyczny konstruktor**
- zostaje wykonany **tylko raz** niezależnie od ilości instancji klasy (w przypadku gdy także nie jest statyczna)
- nie ma żadnego modyfikatora dostępu
- nie może przyjmować żadnych argumentów
- jest wywoływany podczas tworzenia instancji (dla klas niestatycznych) lub podczas pierwszego dostępu do statycznej składowej
- jest thread-safe (synchronizacja wątków)
- zawsze wykonuje się przed zwykłymi konstruktorami

Weź pod uwagę, że jeżeli jakaś klasa jest klasą niestatyczną, oraz posiada statyczne pole, to użycie zwykłego konstruktora do inicjalizacji statycznej zmiennej nie jest zbyt mądre. Utworzenie setek instancji klasy spowoduje **zresetowanie i przypisanie na nowo statycznej zmiennej tyle samo razy**. W przypadku użycia konstruktora statycznego **operacja przypisania odbędzie się tylko raz**.