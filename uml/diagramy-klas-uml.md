---
title: Diagramy klas UML
category: uml
thumbnail: diagram-klas-UML.png
published: 2015-02-01T00:00:00Z
---
Umiejętność **czytania oraz tworzenia diagramów klas UML** jest podstawą w przypadku zawodu programisty. Z takimi diagramami będziesz spotykał się w przeciągu całej swojej kariery. **Diagramy klas UML** są zawsze obecne we wszelkiego rodzaju dokumentacjach.

<!--more-->

## Diagramy klas UML

**Język UML** jest językiem służącym do graficznego modelowania różnego rodzaju aplikacji oraz systemów. Najczęściej wykorzystywany jest do **modelowania diagramów klas**.

**Diagram klas UML** jest statycznym diagramem, przedstawiającym strukturę aplikacji bądź systemu w paradygmacie programowania obiektowego.
{: .alert-info} 

Diagramy klas UML mogą pokazywać strukturę całego systemu bądź tylko jego części (i najczęściej tak jest).

Doskonale obrazują one poglądową strukturę programu. Definiują **jakie metody i pola powinna zawierać dana klasa**. Pokazują one także część ich implementacji. Oczywiście diagram przedstawia **tylko typy obiektów** bez ich instancji - za to odpowiedzialny jest **diagram obiektów UML**.

Należy pamiętać, że **informacje zawarte w diagramie klas UML są poglądowe**. Mają ułatwić implementację klas oraz **ukazać zasadę funkcjonowania systemu**.

Jasne jest, że nie da się zawrzeć na diagramie wszystkich informacji o danej klasie. Gdyby tak było, wtedy diagramy klas byłyby graficzną reprezentacją kodu - a tak nie jest. **Jeżeli w diagramie klas nie ma jakiegoś składnika**, to nie można zakładać że takowy nie istnieje w modelowanej aplikacji.

Jeżeli w **diagramie klas UML** brakuje jakiegoś składnika, nie oznacza to, że taki nie istnieje w systemie. **Poprawne jest także całkowite ukrycie składników klasy**, pozostawiając tylko jej nazwę, ale tylko w wypadku gdy nie jest to klasa silnie decydująca o modelu systemu (o wysokim priorytecie).
{: .alert-danger} 

## Reprezentacja klas w UML

W języku UML istnieją pewne standardy, mówiące w jaki sposób przedstawiać różne elementy. **Każdy obiekt reprezentowany jest przez jeden prostokąt**, w którym zawarte są jego składniki.

W prostokącie może znajdować się tylko nazwa elementu, tylko nazwa elementu wraz z polami bądź **wszystkie składniki czyli nazwa, pola i metody**. Dodatkowo do składników elementu można dołączać informacje o typie, typie zwracanym lub argumentach. Dobrym zwyczajem jest także **określanie poziom dostępu** do składników klasy.

Składniki klasy mogą posiadać różne **modyfikatory dostępu**:

- *+* to składnik publiczny (public)
- *#* to składnik chroniony (protected)
- *-* to składnik prywatny (private)
- *~* to składnik dostępny w obrębie projektu (package)
- metody abstrakcyjne w klasie abstrakcyjnej są *pochylone* lub <span style="text-decoration: underline;">podkreślone</span>.

Przykładowa diagram klasy na dwa sposoby:

{% include parts/postPicture.html page=page img="klasy-uml" %}

## Reprezentacja klas abstrakcyjnych i interfejsów w UML

Jeżeli chodzi o klasy abstrakcyjne sytuacja jest prosta, przedstawia się ją tak jak zwykłą klasę **ale jej nazwa napisana jest kursywą**. To jedyna różnica w przedstawianiu klas abstrakcyjnych za pomocą **UML**.

**Interfejsy przedstawia się w UML** tak jak klasy, jednak ich nazwa musi zostać poprzedzona słowem kluczowym *\<<interface\>>*. Słowo to **jest stereotypem** będącym standardem języka UML.

Dobrym zwyczajem, który osobiście stosuję, jest rozpoczynanie nazwy interfejsu od dużej litery *i*. Nie wynika to absolutnie ze standardu UMLa, jednak pozwala łatwiej analizować diagram.

Implementację interfejsu do danej klasy oznacza się **pustym białym grotem strzałki**, który znajduje się **na końcu przerywanej linii**. Oto przykład:

{% include parts/postPicture.html page=page img="interfejsy-uml" %}

Klasa implementująca interfejs <strong>musi implementować jego metody</strong>. W przypadku klasy abstrakcyjnej, klasa która dziedziczy, <strong>musi implementować metody abstrakcyjne</strong>. Metody w klasie abstrakcyjnej pochyliłem i podkreśliłem. Chyba najczęściej spotykane są te pochylone.

## Związki pomiędzy klasami w UML

**Diagramy klas UML** byłyby bezużyteczne, gdyby nie można było określać powiązań jakie między nimi występują. Poniżej opiszę **wszystkie rodzaje związków między klasami w UML** oraz dołączę do nich przykłady. Krótka infografika podsumowująca:

{% include parts/postPicture.html page=page img="diagram-klas-uml" %}

W **relacjach pomiędzy klasami** mogą występować **cechy krotności**:

- *1* dokładnie jeden obiekt
- *0..3* od zera do trzech obiektów
- _*_ dowolna ilość obiektów
- *3..* od trzech do dowolnej ilości obiektów

**Krotności** umieszczamy po obu stronach zależności. Jeżeli krotność nie jest podana należy przyjąć, że ma wartość *1*.

### Zależność

**Zależność jest najsłabszą relacją** jaka może występować pomiędzy dwoma klasami. Oznacza, że jedna klasa chwilowo wykorzystuje drugą, lub wie o jej istnieniu. Jak sama nazwa wskazuje - występuje zależność. Zmiana w jednej z klas **może spowodować ale (nie musi)** konieczność zmian w drugiej klasie.

{% include parts/postPicture.html page=page img="zaleznosc-uml" %}

Powyższą zależność należy czytać w sposób: **klasa portfel jest zależna od klasy Pieniadze**.

Przykładem na zobrazowanie zależności między klasami jest przekazywanie argumentów do funkcji:

	class Portfel
	{
	    // zależność Portfel od Pieniądze
	    void Dodaj(Pieniadze p)
	    {
	        /*...*/
	    }
	}

Poprawnie zaprojektowany system **powinien zawierać jak najmniej zależności**. Wszelkie zależności utrudniają rozbudowę istniejącego projektu.  **Aby pozbyć się zależności**, można w argumencie funkcji przyjmować poszczególne pola klasy *Pieniadze* zamiast całego obiektu. Jednak wtedy zachodzi tzw. obsesja typów prostych (ang. *primitive obession*), której powinniśmy unikać. Wszystko zależy od ilości pól, które musimy wykorzystać.

### Asocjacja

Asocjacja jest związkiem pomiędzy dwoma klasami. **Jest silniejszym wiązaniem niż zależność**. Oznacza, że dwa obiekty są od siebie zależne. Asocjacja może być przykładowo atrybutem klasy. W asocjacji dwie klasy nie wpływają na siebie. Oznacza to, że usunięcie jednego obiektu nie wpływa w żadnym stopniu na drugi.

Asocjacje mogą być jednokierunkowe, dwukierunkowe a nawet nieokreślone (bez grotów na końcu linii). **W diagramach klas UML asocjacji używa się rzadko**. Wynika to z faktu jej wysokiego poziomu abstrakcyjności.

Istnieje **pewna dowolność** w czytaniu asocjacji. Aby pomóc w ich odczytywaniu często są one dodatkowo opisane. Dowolność w czytaniu asocjacji jest spowodowana tym, że nie wiemy, jak dokładnie (jak mocno) związane są ze sobą obiekty.

{% include parts/postPicture.html page=page img="asocjacja-uml" %}

Znacznie częściej stosowane są **szczególne formy asocjacji czyli agregacje częściowe i całkowite**. Sama asocjacja jest relacją nieco luźniejszą, przez co w diagramach UML klas spotykana jest rzadko.

### Agregacja częściowa

**Agregacja częściowa jest** mocniejszą odmianą zwykłej **asocjacji**. Jeżeli spotkasz się z nazwą samej agregacji, oznacza to że chodzi o agregację częściową. Jest to związek dwóch klas w formie relacji całość-część. **Ważnym jest fakt, że usunięcie klasy całość nie wpływa na istnienie klasy część**.

W **Agregacji częściowej** element częściowy może należeć do elementu głównego, jednak nie jest od niego zależny. Usunięcie elementu głównego nie wpływa na usunięcie elementu częściowego. Element częściowy może także należeć do wielu elementów głównych. Sprawa wydaje się skomplikowana, ale rozważmy przykład:

{% include parts/postPicture.html page=page img="agregacja-czesciowa" %}

Ten prosty **diagram agregacji częściowej** należy czytać w następujący sposób: **klasa katalog ma klasę dokument**.

Najlepiej agregację częściową zobrazować sobie na przykładzie typów referencyjnych w języku C#. Jeżeli utworzysz instancję klasy częściowej  to pamięć zostanie zarezerwowana na stercie. Naszą klasą częściową jest *Dokument*. Klasą główną jest klasa *Katalog*. Zgodnie z diagramem UML jest ona w relacji z klasą *Dokument*, jednak ponieważ **jest to agregacja częściowa** to klasa *Dokument* nie jest od niej zależna.

Oznacza to, że klasa *Katalog* przechowuje jedynie referencję do klasy *Dokument*, która znajduje się na stercie. Usunięcie klasy *Katalog* a wraz z nią referencji, spowoduje usunięcie jej ze stosu. Na stercie dalej będzie znajdować się klasa częściowa. Prosty kod w C# obrazujący **agregację częściową**:

	class Katalog
	{
	    private Dokument _swiadectwo;
	    
	    public void SetSwiatectwo(Dokument swiadectwo)
	    {
	        // relacja agregacji częściowej
	        _swiadectwo = swiadectwo;
	    }
	}

### Agregacja całkowita (kompozycja)

Ostatnią odmianą asocjacji jest **agregacja całkowita** nazywana często **kompozycją**. Działa podobnie na zasadzie **agregacji częściowej** z tą różnicą, że kontroluje cykl życia części. Oznacza to, że po usunięciu klasy głównej, zostanie usunięta klasa częściowa.

{% include parts/postPicture.html page=page img="agregacja-calkowita" %}

Powyższy obrazek można czytać w następujący sposób: **klasa System ma na własność klasę Plik**. Pamiętaj, że nie chodzi tutaj o zawieranie albo zagnieżdżenie tylko o związek relacji całkowitej, czyli **odpowiedzialność klasy głównej za istnienie klasy częściowej**.

Przykład programu będzie podobny jak w agregacji częściowej. Jedyną różnicą jest fakt, że klasa *System* **będzie odpowiedzialna** za utworzenie instancji klasy *Plik* na stercie oraz za przechowywanie referencji do niej. Jaki z tego wniosek? Wzorując się przykładem C#, po usunięciu klasy *System* klasa *Plik* także zostałaby usunięta ze sterty przez garbage collector, po wykryciu braku referencji.

	class System
	{
	    // relacja agregacji całkowitej
	    private Plik _plik1 = new Plik();
	}

Rzecz jasna, nie ma znaczenia w którym miejscu odbywa się tworzenie instancji klasy. Jeżeli klasa główna tworzy jakiekolwiek referencje wewnątrz, to zawsze będzie to relacja **agregacji całkowitej**.

	class System
	{
	    private Plik _plik1;
	    
	    public System()
	    {
	        // relacja agregacji całkowitej
	        _plik1 = new Plik();
	    }
	}

### Dziedziczenie

Nie ma sensu rozpisywać się na ten temat. **Dziedziczenie** jest głównym filarem paradygmatu programowania obiektowego. **Dziedziczenie umożliwia wyodrębnienie** cech wspólnych dla kilku klas i zamknięciu ich w klasie bardziej ogólnej - o wyższym poziomie abstrakcji.

Klasy dziedziczące po klasie bazowej przejmują jej cechy. Pozwala to znacznie skrócić kod i zorganizować kod od strony logicznej.

{% include parts/postPicture.html page=page img="dziedziczenie-uml" %}

Klasa *Osoba* posiada pewne cechy wspólne dla wszystkich klas, które ją rozszerzają. Przykładowy kod C#:

	class Osoba
	{
	    public string Imie { get; set; }
	}
	
	class Student : Osoba
	{
	    public string NazwaUczelni { get; set; }
	}
	
	/*...*/

## Klasy zagnieżdżone w UML

Na diagramie klas UML możemy umieścić także klasę zagnieżdżoną. Jej symbolem jest puste kółko z czarnym krzyżykiem.

{% include parts/postPicture.html page=page img="zagniezdzenie-uml" %}

Diagram należy czytać w następujący sposób: **klasa Silnik jest zagnieżdżona w klasie Samochód**. Przykładowy kod:

	class Samochod
	{
	    private int _waga;
	    
	    private class Silnik
	    {
	        private int _moc;
	    }
	}

## Przykłady diagramów

Po krótkim omówieniu podstawowych elementów **diagramów klas UML** umieszczę kilka praktycznych schematów do przeanalizowania. Do schematów dołączę kody źródłowe napisane w języku C#.