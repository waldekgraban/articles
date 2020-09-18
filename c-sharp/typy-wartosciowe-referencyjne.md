---
title: Typy wartościowe i referencyjne
category: c-sharp
thumbnail: typy-referencyjne-wartosciowe.png
published: 2015-01-16T00:00:00Z
---
W języku C# **istnieje kilka podstawowych typów danych**. Na pierwszy rzut oka nie widać między nimi żadnej różnicy, jednak pojawia się w charakterystycznych sytuacjach takich jak **przekazywanie parametrów do funkcji** czy **kopiowanie wartości zmiennych**. Dokładne zapoznanie się z typami danych pozwoli Ci unikać błędów charakterystycznych dla początkujących programistów.

<!--more-->

## Czym jest stos i sterta?

Poruszając temat typów danych warto wspomnieć **kilka słów o stosie i stercie**. Po pierwsze nie należy tych terminów mylić ze strukturami do przechowywania danych, możliwych do implementacji w wielu językach. Zarówno **stos jak i sterta są częścią pamięci wirtualnej jaka jest przydzielona aplikacji** podczas uruchamiania &#8211; są odrębne dla każdej aplikacji.

Każdy utworzony **wątek danej aplikacji korzysta z osobnego stosu**, więc jedna aplikacja może mieć ich kilka. Stos jest o wiele szybszy od sterty**, lądują na niego wszelkie zmienne oraz parametry przekazywane do funkcji**. Na stos zostaje wrzucany także **adres powrotu**.

Z programistycznego punktu widzenia "stos nas nie obchodzi". **Porządek na stosie w pewnym sensie utrzymuje system**. Deklarując zmienną trafia ona na stos i zostaje z niego zdjęta (usunięta), wtedy gdy wypadnie poza klamry zasięgu (blok kodu *{}*). Generalnie programista nie musi o nic dbać, ma dbać tylko o poprawny, czysty kod.

Sterta jest miejscem w pamięci wirtualnej procesu, gdzie trafiają **wszelkie klasy i ich instancje**. Szczególnie rozpatrując język C# **na stertę trafiają także interfejsy, tablice, delegaty**. Sterta jest zarządzana nie przez system operacyjny, a przez wirtualną maszynę .NET. Elementy trafiające na stertę są tworzone operatorem *new* (nie jest to żelazną zasadą) którego **zasada działania jest bardziej skomplikowana** niż zwykła deklaracja zmiennych lądujących na stos.

W innych językach programowania (C++) o porządek na stercie musimy zadbać sami zwalniając pamięć. W C# **pamięć sterty kontroluje Garbage Collector**, wyłapuje puste referencje i zwalnia miejsce.

Ciężko mi na temat sterty/stosu napisać coś więcej, ponieważ nie można ich porównywać bezpośrednio między dwoma różnymi językami i nie chcę popełnić gafy. W C++ programista wszystko robi sam, dlatego ma świadomość tego co robi. C# jest językiem bardzo zautomatyzowanym i wygodnym, lecz wiele rzeczy dzieje się za naszymi plecami i bez naszej wiedzy.

## Typy wartościowe

Podstawowym typem danych występującym w języku C# jest **typ wartościowy**. Jest to typ występujący powszechnie we wszystkich językach programowania.

**Typ wartościowy** jest podstawowym typem danych występującym w C#. Podczas **deklaracji typu wartościowego** kompilator alokuje odpowiednią ilość miejsca w pamięci, w której będzie przechowywana wartość zmiennej. **Typy wartościowe zawsze umieszczane są na stosie**.
{: .alert-info} 

Wszystkie typy wartościowe dziedziczą niejawnie z klasy *System.ValueType*. To właśnie ta klasa zapewnia nas, że obiekt zostanie umieszczony stosie. Dzięki temu **program ma do nich bardzo szybki dostęp**. Typy wartościowe są domyślnie **przekazywane przez wartość**, oznacza to, że do funkcji przekazywana jest ich kopia.

Typami wartościowymi w C# są: *int*, *double*, *float*, *long*, *byte*, *char*, *bool*, typy wyliczeniowe *enum*, *struktury*. Przykładowo, deklarując zmienną *int* deklarujemy typ wartościowy o wielkości 4b. Klasą pochodną jest klasa *System.ValueType* a więc zmienna będzie znajdować się na stosie. Mamy pewność, że znajduje się ona poza działaniem Garbage Collectora.

Ponieważ *System.ValueType* jest klasą  bazową wszystkich typów wartościowych poprawny będzie poniższy zapis:

	ValueType a = true;
	ValueType b = 5;
	
	Console.WriteLine(a.GetType()); //System.Boolean
	Console.WriteLine(b.GetType()); //System.Int32
	
	int c = (int)b; // wszystko ok

Kopiowanie wartości typów wartościowych jest takie samo jak we wszystkich innych językach. Kopię osiągamy poprzez operator przypisania *=*. Przekazując parametr do funkcji **operujemy na jego kopii**, więc po wyjściu z bloku funkcji oryginalna zmienna nie zostaje zmieniona.

#### Podsumowanie typu wartościowego:

- wszystkie niejawnie rozszerzają klasę *System.ValueType*
- dzięki temu wszystkie **trafiają na stos**
- zmienne typów wartościowych są po prostu miejscami w pamięci
- są **domyślnie przekazywane przez wartość** (kopia)
- zmienna przestaje istnieć kiedy wyjdzie poza klamry zasięgu
- mogą mięć konstruktory niestandardowe
- **nie są w żadnym wypadku** zależne od Garbage Collectora

Istnieje pewien wyjątek, kiedy **typ wartościowy zostanie umieszczony na stercie** a nie na stosie. Dzieje się tak, jeżeli zostanie on opakowany w obiekt. Powód jest prosty: jeżeli typ prosty jest np. atrybutem klasy, nie możemy usunąć go ze stosu, gdy instancja klasy istnieje jeszcze na stercie i być może będzie używana. Pozostałe przypadki to np. tzw. boxing/unboxing, funkcje anonimowe, delegaty, iteratory oraz domknięcia. We wszystkich wymienionych przypadkach powód jest ten sam, czyli jawne (lub niejawne) opakowanie typu prostego w obiekt.

## Typy referencyjne

Drugim rodzajem typu danych są **typy referencyjne**. Występują one także w języku C++. Na omówieniu tego typu danych skupimy się bardziej szczegółowo, ponieważ istnieją w nim charakterystyczne mechanizmy, które należy zapamiętać.

**Typ referencyjny** jest typem umieszczanym na **stercie programu**. Konkretniej **referencja do pamięci umieszczana jest na stosie** a obszar pamięci do jakiego prowadzi referencja **znajduje się na stercie**.
{: .alert-info} 

Typami referencyjnymi w C# są elementy rozszerzające klasę *System.Object* oraz *System.String*, a więc są to np.: *klasy*, *delegacje*, *interfejsy*, *tablice*, zmienne *string* itd. Typów referencyjnych nie da się kopiować korzystając z operatora przypisania.

Główną **różnicą między typami wartościowymi a referencyjnymi** jest niezmienność wielkości **typów wartościowych** w przeciwieństwie do **typów referencyjnych**. Nigdy nie wiemy ile miejsca w pamięci będzie zajmować klasa, w przeciwieństwie do prostych zmiennych np. typu *int*, które zajmują *4b*.

Dlatego też, tworząc zmienną liczbową kompilator przydziela jej po prostu odpowiednią ilość miejsca. Tworząc klasę (typ referencyjny), kompilator wrzuca na stos referencję, która wskazuje na obszar pamięci znajdujący się na stercie. Obiekt klasy może się zmieniać w trakcie działania programu. Nad wszystkim czuwa Garbage Collector działający w osobnym wątku naszej aplikacji (sterta jest wspólna dla wszystkich wątków).

{% include parts/postPicture.html page=page img="typy-referencyjne-C" ext="gif" %}

#### Podsumowanie typu referencyjnego

- wszystkie niejawnie dziedziczą z klasy *System.Object* lub *System.String*.
- dzięki temu **wszystkie trafiają na stertę** (ale referencja do obiektu na stos)
- są domyślnie przekazywane do funkcji przez wartość
- są usuwane z pamięci za pomocą Garbage Collectora
- mogę mieć konstruktory

### Kopiowanie wartości typów referencyjnych

Jaki jest problem? **Typów referencyjnych** nie można kopiować zwykłym operatorem przypisania. Dlaczego? Rozważmy dwa poniższe przykłady. W pierwszej kolejności prosty przykład z typem wartościowym:

	int wiek1 = 20;
	int wiek2 = wiek1;
	
	Console.WriteLine(wiek1); // wyswietli 20
	Console.WriteLine(wiek2); // wyswietli 20
	
	wiek2 = 50;
	
	Console.WriteLine(wiek1); // wyswietli 20
	Console.WriteLine(wiek2); // wyswietli 50

W tym kodzie chyba nic nas nie dziwi. W 2 linijce następuje skopiowane wartości zmiennej *wiek1* do zmiennej *wiek2*. Obie zmienne ciągle znajdują się w osobnych miejscach w pamięci, więc gdy w linijce 7 zmieniamy wartość zmiennej *wiek2* to zmienna *wiek1* się nie zmienia.

Teraz ten sam przykład, z **wykorzystaniem typów referencyjnych**:

	class Osoba
	{
	    public String imie { get; set; }
	    public Osoba(string imie) { this.imie = imie; }
	    public void Info() { Console.WriteLine("imie: {0}", imie); }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        Osoba osoba1 = new Osoba("Karol");
	        Osoba osoba2 = osoba1;
	        
	        Console.WriteLine(osoba1.imie); // wyswietli Karol
	        Console.WriteLine(osoba1.imie); // wyswietli Karol
	        
	        osoba1.imie = "Karol";
	        osoba2.imie = "Arek";
	        
	        Console.WriteLine(osoba1.imie); // wyswietli Arek
	        Console.WriteLine(osoba1.imie); // wyswietli Arek
	        
	        Console.ReadKey();
	    }
	}

Mamy niezgodność. W linijce 13 skopiowaliśmy imię *osoba1* do *osoba2*, więc dwa razy zobaczyliśmy napis "Karol". Jednak w linijce 18 i 19 ustawiliśmy dwa osobne imiona. Mimo tego **dwa razy zostało wyświetlone** imię "Arek". Co jest nie tak?

Operator przypisania kopiuje wartość zmiennej z prawej strony do zmiennej z lewej strony. Ponieważ **klasy są typami referencyjnymi** dlatego zmienne *osoba1* i *osoba2* także są referencjami.

**Referencja** jest to zmienna, której **wartością jest adres** do miejsca w pamięci, gdzie przechowywana jest jakaś **wartość lub instancja obiektu**.
{: .alert-info} 

Skoro wartością referencji jest adres, to operator przypisania skopiował właśnie adres na jaki referencja wskazuje. Nie należy mylić adresu referencji w pamięci, z adresem na jaki wskazuje referencja (czyli wartość referencji).

{% include parts/postPicture.html page=page img="referencja-C" %}

Powyższy obrazek ukazuje sedno problemu. Teraz dokładnie widać, że **dwie referencje na stosie** wskazują na to samo miejsce (na tę samą instancję klasy osoba) na stercie. **Jest to ewidentny błąd**.

Aby skopiować wartości pól poszczególnych obiektów, trzeba to robić pojedynczo pole po polu. Można też kopiować całe obiekty. Są z tym związane pojęcia **kopii płytkiej** oraz **kopii głębokiej**, jednak jest to materiał na osobny artykuł.

### Przekazywanie typów referencyjnych do funkcji przez wartość

Istnieje prosta zasada jaką trzeba zapamiętać, przesyłając **typy referencyjne do funkcji poprzez wartość**. Wewnątrz funkcji możemy zmienić stan obiektu na jaki wskazuje przesłana referencja, jednak nie można tej referencji zmienić. Przeanalizujmy poniższy program:

	class Osoba
	{
	    public String imie { get; set; }
	    public Osoba(string imie) { this.imie = imie; }
	    public void Info() { Console.WriteLine("imie: {0}", imie); }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        Osoba osoba1 = new Osoba("Karol");
	        osoba1.Info(); // imie: Karol
	        
	        Zmien(osoba1);
	        osoba1.Info(); //imie: Arek
	        
	        Console.ReadKey();
	    }
	    
	    public static void Zmien(Osoba o)
	    {
	        o.imie = "Arek";         // zadziała
	        o = new Osoba("Maciek"); // nie zadziała
	    }
	}

Tworzymy instancję klasy *Osoba* i wypełniamy ją danymi. **Przekazujemy referencję do obiektu do funkcji poprzez wartość**. Do funkcji powędrowała kopia referencji, ponieważ jest to charakterystyczne dla przekazywania przez wartość. Mimo, że przesłaliśmy kopię referencji, jej wartość wskazuje na stercie na **oryginalny obszar pamięci** zawierający instancję klasy *Osoba*.

Dlatego przypisanie nowego imienia w linijce 24 zadziała. Mimo że przesłaliśmy kopię referencji, operujemy na oryginalnym bloku pamięci na stercie. **W przypadku gdybyśmy przesyłali typ wartościowy, sytuacja wyglądała by całkiem inaczej!** Mielibyśmy wewnątrz funkcji kopię wartości, a więc jakiekolwiek operacje wewnątrz funkcji nie zmieniały by obiektu z poza niej.

W linijce 25 **nie jesteśmy w stanie zmienić wartości referencji**, ponieważ tak samo jak w przypadku przesyłania typów wartościowych, **zmiany nie będą widoczne poza ciałem funkcji**.

### Przekazywanie typów referencyjnych do funkcji przez referencję

Łatwo przewidzieć efekt jaki uzyskamy, przesyłając argumenty **typu referencyjnego** poprzez referencję. Osiągniemy **pełną możliwość modyfikacji instancji obiektu jak i wartości przekazanej referencji**. Zmodyfikujmy poprzedni kod dodając *ref* przed nazwą argumentu:

	class Osoba
	{
	    public String imie { get; set; }
	    public Osoba(string imie) { this.imie = imie; }
	    public void Info() { Console.WriteLine("imie: {0}", imie); }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        Osoba osoba1 = new Osoba("Karol");
	        osoba1.Info(); // imie: Karol
	        
	        Zmien(ref osoba1);
	        osoba1.Info(); //imie: Maciek
	        
	        Console.ReadKey();
	    }
	    
	    public static void Zmien(ref Osoba o)
	    {
	    		o.imie = "Arek";         // zadziała
	    		o = new Osoba("Maciek"); // także dzaiała!
	    }
	}

Tym razem w linijce 25 nadpisujemy referencję **nową instancją** klasy *Osoba*. Uzyskujemy pełną kontrolę zarówno nad wartością referencji jak i instancją klasy, na którą wskazuje.

Po drodze w linii 25 gubimy referencję do jednej z instancji. Zostanie to wykryte przez **Garbage Collector** a obiekt zostanie usunięty ze sterty.
