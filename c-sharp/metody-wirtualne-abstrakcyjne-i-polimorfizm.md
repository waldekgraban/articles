---
title: Metody wirtualne, abstrakcyjne i polimorfizm
category: c-sharp
thumbnail: metody-wirtualne-abstrakcyjne.png
published: 2015-02-23T00:00:00Z
---
**Metody wirtualne** oraz **metody abstrakcyjne** są ściśle związane z **mechanizmem polimorfizmu**. Polimorfizm jest jednym z filarów paradygmatu programowania obiektowego. Jak wiadomo **język C# jest w całości językiem obiektowym**, dlatego tak ważne jest aby zapoznać się z jego podstawowymi konstrukcjami. Dowiesz się także, **kiedy lepiej wybrać metodę wirtualną a kiedy abstrakcyjną**.

<!--more-->

## Czym jest polimorfizm?

Na początku trochę suchej teorii. Polimorfizm jest jednym z czterech podstawowych założeń programowania obiektowego. Opisując najprościej, **polimorfizm polega na zdolności obiektu do różnych zachowań** zależnie od bieżącego wykonania programu.

**Polimorfizm** to filar programowania obiektowego. Polega na różnym zachowaniu **tych samych metod polimorficznych** (o tych samych deklaracjach) w klasach będących w relacji dziedziczenia.
{: .alert-info}

**Metodami polimorficznymi** (czyli obsługującymi mechanizm polimorfizmu) są w C# **metody wirtualne** oraz **metody abstrakcyjne**.

Do powyższej definicji polimorfizmu można się przyczepić, stricte definiuje ona **polimorfizm dynamiczny**. Oprócz tego w C# występuje także polimorfizm statyczny. Jednak to właśnie wiązania dynamiczne są sednem polimorfizmu, dlatego zdecydowałem się uogólnić definicję właśnie dla tego typu wielopostaciowości. Więcej szczegółów znajdziesz w kolejnych akapitach.

{% include parts/postPicture.html page=page img="polimorfizm-c" %}

Komu i po co to potrzebne? Jednym **przeznaczeniem polimorfizmu jest znaczne skrócenie i uproszczenie kodu programu.** Więcej na ten temat w kolejnych akapitach.

### Polimorfizm statyczny w C#

**Polimorfizmem statycznym w C#** jest **przeciążanie funkcji** i operatorów. W tej samej klasie może istnieć wiele metod o tej samej nazwie, **różniących się tylko parametrami**. Nie jest to zaskakujące, jednak warto wiedzieć, że przeciążanie także nazywane jest polimorfizmem. W tym artykule nie będę tego opisywał, jest to dobry temat na osobny wpis.

Polimorfizm statyczny zachodzi **podczas kompilacji programu**. Nie mamy wpływu na zachowanie metod podczas działania aplikacji. To, która metoda zostanie wybrana jest postanowione już na etapie komplikacji i zależy od ilości przekazanych do metody argumentów.

### Polimorfizm dynamiczny w C#

**Polimorfizm dynamiczny w C# to przesłanianie funkcji**. Jest to aspekt, który najbardziej nas interesuje. Rzadko używane jest pojęcie ‚polimorfizmu dynamicznego’, przeważnie pisze się tylko ‚polimorfizm’ i takiej terminologii będę się dalej trzymał.

Polimorfizm został opisany w akapicie wyższej. Co należy zapamiętać, aby włączyć mechanizm polimorfizmu, musimy w danej klasie utworzyć dowolną **metodę polimorficzną**. Na metody polimorficzne składają się **metody wirtualne oraz metody abstrakcyjne** (czysto wirtualne).

**Metody abstrakcyjne są przesłaniane**, dzięki temu osiąga się wielopostaciowość **interfejsu polimorficznego** w obrębie klas znajdujących się w relacji dziedziczenia. Metody polimorficzne przesłania się słowem kluczowym *override*.

Używanie terminu "przysłanianie" zamiast "przesłanianie" w terminologii paradygmatu programowania obiektowego jest błędne (a niestety często spotykane).

### Interfejs polimorficzny

Ściśle z polimorfizmem związane jest pojęcie **interfejsu polimorficznego**. Dla danego związku klas będących w relacji dziedziczenia, tworzymy **jeden interfejs polimorficzny** i osiągamy różne zachowanie tych samych metod, narzuconych przez interfejs.

Nie jest to artykuł o interfejsach występujących w C#, jednak czym one w ogóle są? Interfejsy to "umowa", mówiąca **jakie metody musi zaimplementować klasa** rozszerzająca dany interfejs.

**Interfejs polimorficzny** to "umowa", mówiąca jakie **metody polimorficzne** muszą implementować klasy pochodne. Dzięki temu grupa klas **posiada jeden interfejs polimorficzny** ale wiele tych samych metod o różnych zachowaniach (wielopostaciowość metod polimorficznych).
{: .alert-info}

Przykładowo, możemy utworzyć interfejs polimorficzny dla klasy *Pracownik*. Jedną z metod klasy będzie metoda *Pracuj()*. Z klasy pracownik będzie dziedziczyła klasa *Szef* i *Sekretarka*. Metoda *Pracuj()* będzie się zachowywać inaczej dla każdej klasy pochodnej, a to jak będzie się zachowywać będzie zależało od **typu statycznego obiektu**, na rzecz którego jest wywoływana.

Uwaga! Nie można mylić **interfejsu polimorficznego** ze zwykłym **interfejsem w języku C#**. Interfejs to konstrukcja programistyczna deklarowana słowem *interface*, a interfejs polimorficzny to zbiór metod polimorficznych (wirtualnych i abstrakcyjnych) występujących w klasie bazowej, których implementacją obarczamy klasy pochodne.
{: .alert-danger} 

## Przesłanianie zwykłych metod

Cała idea wielopostaciowości kręci się w okół **dziedziczenia klas** oraz **przesłaniania metod**. Przesłanianie metod wywodzi się z polimorfizmu dynamicznego.

W przypadku zwykłego przesłonięcia metody, kompilator ostrzega nas i pyta, czy jest to aby na pewno efekt przez nas pożądany:

	public class Pracownik
	{
	    public void Pracuj() { Console.WriteLine("Pracownik.Pracuj()"); }
	}
	
	public class Sekretarka : Pracownik
	{
	    //przesłonięcie metody
	    public void Pracuj() { Console.WriteLine("Sekretarka.Pracuj()"); }
	}

Kod się kompiluje jednak występuje ostrzeżenie: *Warning ‚Sekretarka.Pracuj()’ hides inherited member ‚Pracownik.Pracuj()’. Use the new keyword if hiding was intended.*

Aby pozbyć się ostrzeżenia, wystarczy w linijce nr. 9 dodać słowo kluczowe *new*. Jedynym zadaniem *new* jest **powiadomienie kompilatora**, że przesłaniamy funkcję niepolimorficzną świadomie.

Takie przesłanianie niepolimorficznych funkcji nie jest poprawne, przeważnie oznacza błędnie zaprojektowany program. Jakie są skutki takiego przesłonięcia funkcji? W przypadku rzutowania klasy pochodnej na typ klasy bazowej, **zostanie wywołana metoda typu statycznego**:

	public class Pracownik
	{
	    public void Pracuj() { Console.WriteLine("Pracownik.Pracuj()"); }
	}
	
	public class Sekretarka : Pracownik
	{
	    // dodanie new usuwa ostrzeżenie kompilatora
	    // jednak new nie jest wymagane - nic nie zmienia
	    new public void Pracuj() { Console.WriteLine("Sekretarka.Pracuj()"); }
	}
	
	static void Main(string[] args)
	{
	    Pracownik p = new Sekretarka();
	    p.Pracuj();
	    // zwraca: Pracownik.Pracuj();
	    
	    Console.ReadKey();
	}

Od teraz, trzymaj się takich konstrukcji programistycznych z daleka. Na początku zapewne nie dostrzegasz **wad tego rozwiązania**, jednak zrozumiesz je w dalszych akapitach. Podstawową z takich wad jest znaczące powielanie kodu.

W tym momencie **wkracza polimorfizm**. Aby korzystać z jego zalet, będziemy **przesłaniać metody polimorficzne**, co jest opisane w następnym akapicie.

## Metody polimorficzne i przesłanianie metod

W C# istnieją dwa typy metod polimorficznych, które **uruchamiają mechanizm** **polimorfizmu**. Są to metody wirtualne oraz metody abstrakcyjne. Ich przedrostki dopisujemy do dowolnej metody danej klasy. Niżej są one omówione bardziej szczegółowo:

### Metody wirtualne

**Metoda wirtualna w C#** jest metodą polimorficzną. Jest to funkcja składowa dowolnej klasy oznaczona słowem kluczowym *virtual*.

**Metoda wirtualna** (funkcja wirtualna) jest metodą składową klasy, której wywołanie zależy od typu dynamicznego obiektu. Jej użycie **włącza mechanizm polimorfizmu dynamicznego**.
{: .alert-info} 

Metodę wirtualną tworzy się w klasie bazowej. We wszystkich klasach pochodnych można **polimorficznie przesłonić jej nazwę** i zapewnić jej inną implementację.

	public class Pracownik
	{
	    // metoda wirtualna
	    virtual public void Pracuj() { Console.WriteLine("Pracownik.Pracuj()"); }
	}

Jaka jest **różnica między metodą wirtualną** a zwykłą? Różnica polega na zachowaniu wywoływania przesłoniętych metod podczas dziedziczenia klas.  
Rozważmy przykład znany z akapitu o przesłanianiu metod, wzbogacony o funkcję wirtualną:

	public class Pracownik
	{
	    // funkcja wirtualna
	    virtual public void Pracuj() { Console.WriteLine("Pracownik.Pracuj()"); }
	}
	
	public class Sekretarka : Pracownik
	{
	    // przesłaniamy
	    override public void Pracuj() { Console.WriteLine("Sekretarka.Pracuj()"); }
	}
	
	static void Main(string[] args)
	{
	    Pracownik p = new Sekretarka();
	    p.Pracuj();
	    // UWAGA! zwraca: Sekretarka.Pracuj();
	    
	    Console.ReadKey();
	}

W przypadku przesłonięcia metody niewirtualnej, zostanie wywołana metoda typu statycznego wskazywanego przez referencję, czyli metoda z klasy *Pracownik*. Ponieważ **użyliśmy metody wirtualnej** (polimorficznej), mimo rzutowania instancji klasy *Sekretarka* na typ bazowy *Pracownik*, wywołana zostaje metoda wirtualna klasy *Sekretarka*. **Występuje tutaj zależność od typu dynamicznego**, a nie statycznego jak w przypadku zwykłego przesłaniania.

Zauważ! Metoda klasy bazowej jest wirtualna. **Nie musimy jej przesłaniać**, ale jeżeli chcemy to musimy to zrobić za pomocą słowa kluczowego *override*.

#### Kilka wniosków na temat metod wirtualnych

  * metody, która nie jest wirtualna, nie można przesłonić poprzez użycie *override*
  * metody statyczne ani prywatne nie mogą być wirtualne
  * metoda wirtualna może być przesłonięta w klasie pochodnej &#8211; ale nie musi. W przypadku braku przesłonięcia zostanie wywołana metoda klasy bazowej

Najważniejszą cechą **metod wirtualnych**, jest ta opisana wyższej. **Nie jesteśmy zmuszeni do przesłonięcia metody wirtualnej w klasie bazowej.** Jest to główna różnica w stosunku do metod abstrakcyjnych.

Każda klasa w C# dziedziczy niejawnie z klasy *Object*, która z kolei udostępnia kilka metod wirtualnych.

	public class Object
	{
	    /* składowe wirtualne */
	    virtual public bool Equals(object o);
	    virtual protected void Finalize();
	    virtual public string ToString();
	    /* itp.. */
	}

Dzięki temu, możesz zawsze je nadpisać za pomocą *override*, nawet jeżeli jawnie nie rozszerzasz *Object*. Ponieważ to metody wirtualne, nie musisz ich przesłonić. W przypadku braku przesłonięcia zostanie wywołana implementacja domyślna z kasy bazowej *Object*.

### Metody abstrakcyjne

**Metoda abstrakcyjna w C#** jest metodą polimorficzną. Musi być zadeklarowana jako **funkcja składowa abstrakcyjnej klasy**, poprzedzona przedrostkiem *abstract*.

**Metoda abstrakcyjna zachowuje się dokładnie tak samo jak metoda wirtualna**, jedyna różnica leży w braku definicji ciała funkcji. **Tworząc metodę abstrakcyjną** deklarujesz funkcję (deklaracja to typ zwracany, nazwa i argumenty) ale nie definiujesz ciała funkcji.

Metodę abstrakcyjną można zadeklarować tylko w klasie abstrakcyjnej (także poprzedzonej słowem *abstract*). Dlaczego tak jest? Klasa abstrakcyjna jest klasą, **której instancji nie da się stworzyć**. Można po niej tylko dziedziczyć, rozszerzając ją o inne klasy**.** Klasy dziedziczące muszą implementować **metody abstrakcyjne klasy abstrakcyjnej**. Gdyby dało się utworzyć instancję klasy abstrakcyjnej, doszło by do sytuacji, że istnieje w niej abstrakcyjna funkcja bez definicji ciała.

Za zdefiniowanie ciała funkcji abstrakcyjnej odpowiedzialne są klasy pochodne. Odbywa się to poprzez przesłanianie słowem *override* tak samo jak w funkcjach wirtualnych. Krótki przykład:

	abstract public class Pracownik
	{
	    // funkcja abstrakcyjna, bez ciala funkcji(!) 
	    abstract public void Pracuj();
	}
	
	public class Sekretarka : Pracownik
	{
	    // przesłaniamy
	    override public void Pracuj() { Console.WriteLine("Sekretarka.Pracuj()"); }
	}
	
	static void Main(string[] args)
	{
	    Pracownik p = new Sekretarka();
	    p.Pracuj();
	    // UWAGA! zwraca: Sekretarka.Pracuj();
	    
	    Console.ReadKey();
	}

Warto dodać, że klasa abstrakcyjna może posiadać zwykłe metody, posiadające ciało funkcji **mimo posiadania metod abstrakcyjnych**. Cała reszta działa tak samo jak w funkcjach wirtualnych. **Wywoływana jest przesłonięta metoda, biorąc pod uwagę typ dynamiczny**.

#### Podsumowanie metod abstrakcyjnych

- działają tak samo jak funkcje wirtualne, oprócz tego że nie podaje się definicji ciała funkcji w klasie bazowej
- muszą być zadeklarowane w klasie abstrakcyjnej
- nie mogą być prywatne ani statyczne
- klasa pochodna **musi przesłonić wszystkie metody abstrakcyjne**

## Kiedy używać metod wirtualnych a kiedy abstrakcyjnych?

Zarówno **metody abstrakcyjne** jak i **metody wirtualne** są polimorficzne i prawie niczym się nie różnią. Nasuwa się więc pytanie: kiedy używać jednych a kiedy drugich? Odpowiedź na to pytanie jest prosta.

**Metod wirtualnych** używamy wszędzie tam, gdzie może wystąpić powielanie kodu w przesłanianych funkcjach. Dlaczego? Używając polimorfizmu i metod wirtualnych, możemy wywołać po typie dynamicznym **metodę wirtualną klasy pochodnej** (domyślnie), a jeśli zechcemy **także metodę klasy pochodnej i bazowej**. Odbywa się to dzięki słowu kluczowemu *base* odnoszącemu się do klasy bazowej. Prosty przykład:

	public class Pracownik
	{
	    // funkcja wirtualna
	    virtual public void Pracuj()
	    {
	        Console.WriteLine("Autoryzacja");
	        Console.WriteLine("Sprawdź zlecenia");
	    }
	}
	
	public class Sekretarka : Pracownik
	{
	    // przesłaniamy
	    override public void Pracuj()
	    {
	        base.Pracuj();
	        Console.WriteLine("Obowiazki sekretarki"); 
	    }
	}
	
	static void Main(string[] args)
	{
	    Pracownik p = new Sekretarka();
	    p.Pracuj();
	    //Autoryzacja
	    //Sprawdz zlecenia
	    //Obowiazki sekretarki
	    
	    Console.ReadKey();
	}

W przykładzie wyżej, każdy pracownik przed rozpoczęciem pracy musi przejść proces autoryzacji a następnie sprawca zlecenia na dziś. Dopiero po tym etapie zaczyna właściwe dla swojego stanowiska obowiązki.

Jeżeli stworzylibyśmy reprezentację klas dla dużej ilości pracowników, **błędem byłoby użycie metody abstrakcyjnej**. Doskonale za to naddaje się tutaj **metoda wirtualna**, ponieważ zapobiegamy powielaniu kodu. Obowiązki wspólne dla każdego pracownika są wywoływane z **metody wirtualnej klasy bazowej**, a specjalistyczne obowiązki poszczególnych stanowisk dopiero później **przesłaniając funkcję wirtualną**.

Odwrotnie wygląda sprawa z **metodami abstrakcyjnymi**. Jeżeli masz pewność, że nie zajdzie zjawisko powielania kodu, możesz ich użyć. Przykładowy program dotyczący figur geometrycznych:

	abstract public class Figura 
	{
	    protected int a,b,c;
	    abstract public int Pole();
	}
	
	public class Kwadrat : Figura
	{
	    override public int Pole() { return a*a; }
	}
	
	public class Trojkat : Figura
	{
	    override public int Pole() { return 1/2*a*b; }
	}

W tym przykładzie użycie **metody wirtualnej nie miałoby sensu**. Pole powierzchni dla każdej figury liczy się zupełnie inaczej, więc wygodnie nam było użyć **metody abstrakcyjnej**. Dzięki temu mamy pewność, że klasa pochodna **musi dostarczyć własną definicję ciała metody** *Pole()*.

W przypadku **metod wirtualnych** klasa pochodna nie musi przesłonić metody klasy bazowej.

## Polimorfizm w C++, C# i Javie

Polimorfizm występuje we wszystkich obiektowych językach programowania. Generalnie zawsze polega on na tym samym &#8211; wielopostaciowości przesłanianych metod wirtualnych. Małe różnice występują natomiast w specyficznych elementach danego języka.

### C++ vs C#

W języku C++ uruchamiamy polimorfizm **dodając do klasy dowolną funkcję wirtualną**. W klasach pochodnych przesłaniamy funkcję, jednak nie poprzedzamy jej już żadnym słowem kluczowym. W C# konieczne jest dodanie *override*. W C++ dopuszczalne jest dopisanie *virtual* jednak nie jest to wymagane, a skoro nie jest to wiadomo &#8211; nie dopisujemy.

	class Pracownik {
	public:
	    virtual void Pracuj() {
	    		cout << "Pracownik -> Pracuj()" << endl;
	    }
	};
	
	class Sekretarka : public Pracownik {
	public:
	    void Pracuj() {
	    		cout << "Sekretarka -> Pracuj()" << endl;
	    }
	};
	
	int main() {
	    Pracownik* p = new Sekretarka();
	    p->Pracuj();
	}

W języku C++ nie ma interfejsów ani metod abstrakcyjnych, są za to **metody czysto wirtualne**. Jeżeli klasa posiada jakąkolwiek metodę czysto wirtualną, przyjmujemy, że staje się umownie klasą abstrakcyjną.

	class Pracownik {
	public:
	    // metoda czysto wirtualna
	    virtual void Pracuj() = 0;
	};
	
	class Sekretarka : public Pracownik {
	public:
	    // musimy zaimplementować(!)
	    void Pracuj() {
	        cout << "Sekretarka -> Pracuj()" << endl;
	    }
	};
	
	int main() {
	    Pracownik* p = new Sekretarka();
	    p->Pracuj();
	}

W tym przypadku musimy zaimplementować metodę czysto wirtualną. Zwykłej metody wirtualnej oczywiście nie musimy. Zwykła metoda wirtualna w C++ **zachowuje się jak metoda wirtualna z C#**. Natomiast metoda czysto wirtualna z C++ **zachowuje się jak metoda abstrakcyjna z C#**.

Warto zapamiętać: w C++ nie występują klasy wirtualne, ponieważ nie występuje konstrukcja pozwalająca poprzeć słowa *class* słowem *abstract*. Nie występują także interfejsy.

Programiści przyjęli, że definiując w C++ klasę z funkcjami czysto wirtualnymi **oraz funkcjami normalnymi** osiągamy twór na wzór klasy abstrakcyjnej. Tworząc natomiast klasę zawierającą tylko funkcje wirtualne otrzymujemy twór na wzór interfejsu.

### Java vs C#

Java niczym szczególnym nie różni się od C#. W Javie **wszystkie funkcje domyślnie są wirtualne**. Są dostępne interfejsy i metody abstrakcyjne, które także są domyślnie wirtualne.

	class Pracownik {
	    public void Pracuj() {
	        System.out.print("Pracownik.Pracuj()");
	    }
	}
	
	class Sekretarka extends Pracownik {
	    public void Pracuj() {
	        System.out.print("Sekretarka.Pracuj()");
	    }
	}
	
	public class Main {
	    public static void main(String[] args) {
	        Pracownik p = new Sekretarka();
	        p.Pracuj();
	    }
	}

Chociaż wirtualna maszyna Javy i odśmiecacz pamięci działają bardzo dobrze, wciskanie polimorfizmu wszędzie tam gdzie nie jest potrzebny, **nawet do najprostszych programów** często odbija się na wydajności.

Mimo tego programiści C# nie odczują dużej różnicy przesiadając się na Javę, przynajmniej w przypadku tego aspektu.

Chcąc wyłączyć polimorfizm &#8211; przesłonić funkcję tak jak w C# za pomocą operatora *new*, jesteśmy zmuszeni w klasie bazowej zadeklarować funkcję jako prywatna.

## Przykłady użycia polimorfizmu

Istnieją **setki przykładów na użycie polimorfizmu**, można wymyślać je bez końca. Pokażę tutaj dwie duże zalety i ich reprezentację w kodzie. Obydwie skupiają się przede wszystkim na znacznym skróceniu kodu programu.

Sztandarowy przykład to **wywoływanie metod klas zebranych w kolekcji lub tablicy**. Dzięki zastosowaniu polimorfizmu **wywołujemy metodę polimorficzną związaną z typem statycznym**. Dzięki dziedziczeniu, które jest drugim filarem programowania obiektowego, możemy **klasy pochodne rzutować na referencję klasy bazowej**. Dzięki temu powstają potężne konstrukcje jak np. ta:

	public class Pracownik {
	    virtual public void Pracuj() { Console.WriteLine("Pracownik pracuje"); }
	}
	
	public class Sekretarka : Pracownik {
	    override public void Pracuj() { Console.WriteLine("Sekretarka pracuje"); }
	}
	
	public class Ochroniarz : Pracownik {
	    override public void Pracuj() { Console.WriteLine("Ochroniarz pracuje"); }
	}
	
	public class Ksiegowy : Pracownik {
	    override public void Pracuj() { Console.WriteLine("Księgowy pracuje"); }
	}
	
	static void Main(string[] args)
	{
	    List<Pracownik> lista = new List<Pracownik>();
	    lista.Add(new Sekretarka());
	    lista.Add(new Ochroniarz());
	    lista.Add(new Ksiegowy());
	    
	    foreach (var i in lista) {
	        // hurtowo wszystkich zaganiamy do pracy
	        i.Pracuj();
	    }
	    
	    Console.ReadKey();
	}

W konstrukcji *foreach* możemy jedną linijką kodu wywołać metody dla wielu różnych klas, wywodzących się z jednej klasy bazowej.

Kolejnym przykładem jest przekazywanie klasy pochodnej do różnych specjalistycznych metod. Dzięki użyciu polimorfizmu możemy dopuścić się następującej konstrukcji:

	public void JakasMetoda(Pracownik p)
	{
	    p.jakasOperacja();
	}

Bez użycia funkcji wirtualnych metoda musiałaby wyglądać następująco:

	public void JakasMetoda(Pracownik p)
	{
	    if (p is Sekretarka)
	        jakasOperacjaA();
	    else if (p is Ksiegowy)
	        jakasOperacjaB();
	    else if (p is Ochroniarz)
	        jakasOperacjaC();
	}

Prawda, że kod jest o wiele dłuższy?

## Zastosowanie polimorfizmu w programowaniu

Początkującym programistom ciężko zrozumieć **gdzie można zastosować polimorfizm**. Oprócz podstawowych przykładów, które pokazałem w poprzednim akapicie, ciężko wymyślić coś bardziej spektakularnego. Biorąc pod uwagę konieczność dodatkowej logiki jaka wiąże się z użyciem polimorfizmu można odnieść wrażenie, że po prostu lepiej odpuścić. **Może lepiej dodać kilka instrukcji warunkowych więcej**, zamiast martwić się w przesłanianie metod?

Zaczynając od fundamentów musisz wiedzieć, że podstawowe filary programowania obiektowego to:

- **polimorfizm**
- dziedziczenie
- hermetyzacja
- abstrakcja

W paradygmacie programowania obiektowego chodzi o to, **aby modelować system i zachodzące w nim procesy w formie reużywalnych obiektów (bytów)**. Jest to proces bardzo trudny. Popełnienie błędów lub nieprzewidzenie pewnych rzeczy w początkowej fazie projektowania systemu będzie rzutować na cały okres jego życia (i nawiasem mówiąc będzie prowadzić do jego końca). Obiekty muszą być **jak najbardziej reużywalne i niezależne**, po to aby dało się je ponownie wykorzystać. Mówimy wtedy, że **kod programu jest wysoce skalowalny**. To z kolei skutkuje łatwością rozwijania i utrzymywania kodu. Duże systemy pisanie obiektowo przez kilka lat przez kilkudziesięciu programistów mają duży problem z osiągnięciem tej skalowalności i odpowiedniej jakości kodu. Miarą jakości kodu są odpowiednie metryki, jednak opisywanie ich to temat na osoby artykuł.

Aby wyjść naprzeciw oczekiwaniom programistów i usprawnić programowanie obiektowe **pewna grupa ludzi wymyśliła wzorce projektowe**. Są oni nazywani tzw. gangiem czworga. Ich książka jest podstawową lekturą, każdego początkującego programisty, ponieważ umiejętność korzystania ze wzorców projektowych do fundament w efektywnym programowaniu obiektowym.

Teraz dochodzimy do miejsca, **gdzie użycie i rozumienie polimorfizmu jest niezbędne**, czyli do wzorców projektowych. Wzorce projektowe są gotowymi rozwiązaniami na najbardziej powszechne problemy występujące w programowaniu obiektowym. **Bez polimorfizmu nie byłoby wzorców projektowych**. Jeżeli masz jakikolwiek problem ze zrozumieniem polimorfizmu lub dziedziczenia z całą pewnością nie będziesz też w stanie zrozumieć wzorców.

### Programowanie bez użycia polimirfozmu

Popatrzmy na problem od drugiej strony. Załóżmy, że **nie chcesz używać wzorców projektowych i nie chcesz używać polimorfizmu**. Czy da się w ten sposób napisać duży system? Oczywiście! O ile będzie to system pisany przez jedną osobę i w momencie jego pisania znamy całą jego specyfikację (wiemy, jak system ma działać). W takim wypadku kod programu zaczyna bardziej przypominać kod deklaratywny i to w formie strukturalnej (wykonanie instrukcja po instrukcji), przeplatany setkami instrukcji warunkowych. Nawet gdy użyjemy  w nim klas i posłużymy się językiem obiektowym, z obiektowością nie miałby to dużo wspólnego. **Obliczenie jakichkolwiek metryk dla takiego kodu zwróciłoby fatalne wartości**, ponieważ bez polimorfizmu nie jesteśmy w stanie stworzyć reużywalnych obiektów. Wszędzie występowałoby duże powiązanie klas.

Podsumowując, nie ma obowiązku używania polimorfizmu, jednak musimy go używać chcąc skorzystać ze wzorców. **Bez nich nigdy nie będziemy programować obiektowo**, a kod wcześniej czy później będzie przypominał bubla. Oczywiście **wszelkie te rozważania mają sens w paradygmacie programowania obiektowego**. W programowaniu logicznym lub funkcyjnym wszystko działa inaczej, inaczej modeluje się działanie programu.

## Zaawansowane dziedziczenie i polimorfizm

Zadania dotyczące polimorfizmu i nadpisywania funkcji **zawsze pojawiają się na rozmowach kwalifikacyjnych**. Dlatego warto dobrze przyswoić sobie ten temat.

Jeżeli doczytałeś artykuł do tego momentu, polimorfizm może wydawać Ci się prosty. Nic bardziej mylnego! W tym akapicie udowodnię Ci, że istnieje wiele konstrukcji programistycznych w których bardzo łatwo się pogubić!

Język C# nie pozwala na dziedziczenie z wielu klas bazowych &#8211; i bardzo dobrze, eliminuje to wiele dodatkowych problemów (ang. the diamond inheritance problem). Mimo tego **działanie mechanizmu polimorfizmu** jest oczywiste tylko w przypadku powiązania relacją dziedziczenia dwóch klas.

W przypadku dziedziczenia z wielu klas bazowych, zawierających **metody wirtualne i abstrakcyjne** sytuacja zaczyna się komplikować (ang. inheritance chain problem). Gdy dodatkowo przepleciemy je zwykłymi metodami, oraz metody wirtualne zaczną wywoływać inne metody wirtualne &#8211; łatwo się zgubić. Rozważmy przykład:

	public class A
	{
	    public void NormalFun() { Console.WriteLine("A NormalFun()"); }
	    public virtual void VirtualFun() { Console.WriteLine("A VirtualFun()"); VirtualFun2();}
	    public virtual void VirtualFun2()  { Console.WriteLine("A VirtualFun2()"); }
	}
	
	public class B : A
	{
	    public void NormalFun() { Console.WriteLine("B NormalFun()"); }
	    public virtual void VirtualFun() { Console.WriteLine("B VirtualFun()"); }
	    public override void VirtualFun2() { Console.WriteLine("B VirtualFun2()"); }
	}
	
	public class C : B
	{
	    public override void VirtualFun() { Console.WriteLine("C VirtualFun()"); }
	}
	
	public class D : C
	{
	    public void NormalFun() { Console.WriteLine("D NormalFun()"); }
	    public override void VirtualFun() { Console.WriteLine("D VirtualFun()"); }
	    public override void VirtualFun2() { Console.WriteLine("D VirtualFun2()"); }
	}
	
	public abstract class E : D
	{
	    public virtual void VirtualFun() {  Console.WriteLine("E VirtualFun()"); }
	    public abstract void VirtualFun2();
	}
	
	public class F : E
	{
	    public override void VirtualFun() { Console.WriteLine("F VirtualFun()"); }
	    public override void VirtualFun2() { Console.WriteLine("F VirtualFun2()"); }
	}
	
	class Program
	{
	    static void Main(string[] args)
	    {
	        // tutaj beda wywolania funkcji
	        
	        Console.ReadKey();
	    }
	}

Powyższy przykład ukazuje sedno problemu. Każdy znajdzie w nim coś dla siebie. Jest to połączenie **metod wirtualnych, metod abstrakcyjnych** i zwykłych.

Przeanalizujmy krok po kroku kilka wywołań, aby nie powielać kodu przyjmijmy, że znajdują się one w linijce 43. Spróbuj przewidzieć, co zostanie wypisane na ekran po wykonaniu tego kodu:

#### Przykład:

	A x = new B();
	x.VirtualFun();

#### Analiza:

- wczesne wiązanie: klasa A (od niej zaczniemy), późne wiązanie: klasa B (na niej skończymy)
- (w klasie A) typowana do wykonania jest funkcja A::VirtualFun. Jest wirtualna więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie B) typowana do wykonania jest funkcja przesłonięta B::VirtualFun. Jest wirtualna więc przerywamy łańcuch dziedziczenia (nie nadpisuje).
- wywołujemy A::VirtualFun
- (w klasie A) funkcja A::VirtualFun wywołuje funkcje VirtualFun2
- (w klasie A) typowana do wykonania jest funkcja A::VirtualFun2. Jest wirtualna więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie B) typowana do wykonania jest funkcja przesłonięta B::VirtualFun2. To ostatnia klasa więc nie sprawdzamy wyżej.
- wywołujemy B::VirtualFun2

Wynik działania programu:

	A VirtualFun()
	B VirtualFun2()

#### Przykład:

	A x = new D();
	x.VirtualFun();

#### Analiza:

- wczesne wiązanie: klasa A (od niej zaczniemy), późne wiązanie: klasa D (na niej skończymy)
- (w klasie A) typowana do wykonania jest funkcja wirtualna A::VirtualFun. Jest wirtualna więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie B) typowana do wykonania jest funkcja przesłonięta B::VirtualFun. Jest wirtualna więc przerywamy łańcuch dziedziczenia (nie nadpisuje).
- wywołujemy A::VirtualFun
- (w klasie A) funkcja A::VirtualFun wywołuje funkcje VirtualFun2
- (w klasie A) typowana do wykonania jest funkcja A::VirtualFun2. Jest wirtualna więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie B) typowana do wykonania jest funkcja przesłonięta B::VirtualFun2. Przesłania poprzednią funkcję, sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie C) brak funkcji VirtualFun2 więc dziedziczymy ją z klasy bazowej B::VirtualFun2.
- (w klasie C) typowana do wykonania jest funkcja przesłonięta B::VirtualFun2. Przesłania poprzednią funkcję, sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie D) typowana do wykonania jest funkcja przesłonięta D::VirtualFun2. To ostatnia klasa więc nie sprawdzamy wyżej.
- wywołujemy D::VirtualFun2

Wynik działania programu:

	A VirtualFun()
	D VirtualFun2()

#### Przykład:

	C x = new F();
	x.VirtualFun();
	x.NormalFun();

#### Analiza:

- wczesne wiązanie: klasa C (od niej zaczniemy), późne wiązanie: klasa F (na niej skończymy)
- (w klasie C) typowana do wykonania jest funkcja C::VirtualFun. Jest przesłonięta więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie D) typowana do wykonania jest funkcja przesłonięta D::VirtualFun. Jest przesłonięta więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie E) typowana do wykonania jest funkcja przesłonięta E::VirtualFun. Jest wirtualna więc przerywamy łańcuch dziedziczenia (nie nadpisuje).
- wywołujmy D::VirtualFun
- (w klasie C) typowana do wykonania jest funkcja NormalFun. Bbrak funkcji NormalFun więc dziedziczymy ją z klasy bazowej B::NormalFun.
- wywołujemy B::NormalFun.

Wynik działania:

	D VirtualFun()
	B NormalFun()

#### Przykład:

	D x = new F();
	x.VirtualFun();
	x.NormalFun();

#### Analiza:

- wczesne wiązanie: klasa D (od niej zaczniemy), późne wiązanie: klasa F (na niej skończymy)
- (w klasie D) typowana do wykonania jest funkcja D::VirtualFun. Jest przesłonięta więc sprawdzamy czy nie jest wyżej przesłonięta?
- (w klasie E) typowana do wykonania jest funkcja E::VirtualFun. Jest wirtualna więc przerywamy łańcuch dziedziczenia (nie nadpisuje).
- wywołujmy D::VirtualFun
- (w klasie D) typowana do wykonania jest funkcja D::NormalFun. Jest odnaleziona więc wywołujemy:
- wywołujmy D::NormalFun

Wynik działania:

	D VirtualFun()
	D NormalFun()

###  Podsumowanie i kilka ważnych zasad

Jak widzisz, **dziedziczenie wielu klas z metodami wirtualnymi** może doprowadzić do lekkich problemów. Zalecam Ci poćwiczyć takie przykłady, ponieważ zawsze pojawiają się na testach kwalifikacyjnych. Prawdopodobnie nie aż w takim natężeniu, ale zawsze są zadania gdzie dziedziczenie odbywa się dla ilości klas n>2.

Oto podstawowe zasady na jakie należy zwracać uwagę w tak rozbudowanych przykładach:

- Analizujemy łańcuch klas zaczynając od typu statycznego kończąc na typie dynamicznym.
- Wywołanie **metod normalnych** (niepolimorficznych) zależy od typu statycznego, jeżeli takiej metody nie ma, są dziedziczone z klas bazowych (**wędrujemy w dół po hierarchii dziedziczenia**).
- Wywołanie metod polimorficznych (abstract i virtual) zależy od typu dynamicznego
- Jeżeli zaczynamy analizę od **metody abstrakcyjnej lub wirtualnej**, **wędrujemy w górę** po metodach przesłoniętych aż do typu statycznego obiektu (ostatnia klasa) lub aż do napotkania kolejnej metody wirtualnej lub abstrakcyjnej.
- Jeżeli zaczniemy analizę od metody przesłaniającej i napotkamy metodę abstrakcyjną lub wirtualną, kończymy analizę bez dochodzenia do typu statycznego. Zostaje przerwany łańcuch dziedziczenia z powodu wygenerowania nowej tabeli *vtable*.

Niestety, te **przykłady da się jeszcze bardziej utrudnić**, np. wprowadzając metody wirtualne i abstrakcyjne do dwóch dziedziczących po sobie klas abstrakcyjnych. Polecam Ci zainteresować się takim przypadkiem. Nie umieszczam go w tym artykule, ponieważ analiza byłaby zbyt długa i nieczytelna.

## Podsumowanie

Warto przyswoić sobie działanie polimorfizmu, napisać kilka prostych programów korzystających z tego mechanizmu. Obiektowość bez używania polimorfizmu nie istnieje, a przynajmniej nie wykorzystuje swoich wszystkich atutów.

**Polimorfizm** oprócz tego, że niezbędny w rozbudowanych projektach, często znajduje swoje zastosowanie w implementacji wielu wzorców projektowych.