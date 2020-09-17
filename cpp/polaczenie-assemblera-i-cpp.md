---
title: Połączenie assemblera i C++
category: cpp
thumbnail: assembler-cpp.png
published: 2014-08-12T00:00:00Z
---
Ponownie dodaję artykuł zahaczający o temat **assemblera**. Na własnej skórze doświadczyłem dzisiaj problemów ze wstawką **assemblerową** w kodzie **C++**, dlatego postanowiłem stworzyć artykuł, w którym zbiorę w całość kilka często używanych trików. Artykuł jest skierowany dla użytkowników darmowego kompilatora **GCC**, ponoć w Visual Studio wiele problemów znika.

<!--more-->

## Assembler w C++

Jeżeli nie jesteś tylko zwykłym klepaczem kodu, który zna na pamięć wszystkie interfejsy oferowane przez Jave, prawdopodobnie nie raz próbowałeś oszukać jakieś gry lub programy &#8211; choćby dla rozrywki lub sprawdzania siebie. Po prostej edycji pamięci w końcu przychodzi czas, że nie da się ruszyć dalej **bez znajomości assemblera**. Assembler daje nieograniczone możliwości podczas **analizowania oprogramowania** a także w procesie jego wytwarzania.

Osobiście często używam środowiska Code::Blocks, ponieważ w wygodny sposób koloruję składnie i jest lekkie, a nic oprócz kolorowania składni nie potrzebuję. Jego drugą największą zaletą (w połączeniu z GCC) jest fakt, że jest to zestaw całkowicie darmowy. Dość dużym problemem z jakim się spotkałem, są wstawki assemblerowe (inline assembler). Wydaję mi się, że GCC podchodzi do swoich użytkowników trochę niedbale. W tym artykule opiszę kilka często stosowanych zabiegów.

## Syntaksa AT&T oraz Intela

**Kompilator GCC** domyślnie narzuca dla swoich użytkowników syntaksę AT&T, która w moim osobistym odczuciu jest koszmarna. Po dokładnej analizie widzę, że różnice nie wydają się szczególnie wielkie, jednak nauka jednej i drugiej jest dla mnie stratą czasu, tym bardziej kiedy z Assemblera korzystam odświętnie.

W kompilatorze GCC można dodać odpowiednią flagę *-masm=intel*. Dzięki niej możemy bez (prawie) żadnych ograniczeń korzystać z syntaksy Intela. W tym celu w środowisku Code::Blocks wchodzimy w menu *Project* > *Build Options* > *Other options*. Po dopisaniu flagi zapisujemy zmiany.

Aby dodać do kodu C++ fragment kodu assemblera, posługujemy się rozkazem *Asm* lub ***asm*. Kod assemblera musi znajdować się w cudzysłowach. Istnieje też kilka sposobów na osiągnięcie nowych linii:

	void funkcja1() {
	    __asm("mov eax, 5");
	    __asm("push eax");
	    __asm("add esp, 4");
	}
	
	void funkcja2() {
	    asm("\
	        mov eax, 5 \n\
	        push eax \n\
	        add esp, 4 \n\
	    ");
	}

Powyższe dwie funkcje są identyczne, różni je tylko sposób przejścia do nowych linii oraz formatowanie.

## Argumenty funkcji i zmienne lokalne

Tworząc wstawkę assemblera, możemy w prosty sposób uzyskać dostęp do argumentów funkcji C++, w której to wstawka się znajduje oraz zmiennych lokalnych tej funkcji. Dostęp ten zapewnia nam rejestr *EBP*, który jest wskaźnikiem do segmentu danych na stosie. Dane wyciągamy arytmetycznie zwiększając lub zmniejszając wskaźnik:

- *[EBP-8]* zmienna lokalna 2;
- *[EBP-4]* zmienna lokalna 1;
- *[EBP]* wskaźnik segmentu stosu
- *[EBP+4]* wskazuje na *EIP*
- *[EBP+8]* argument funkcji 1
- *[EBP+12]* argument funkcji 2

Poniżej znajdują się dwie te same funkcje, napisane w czystym C++ oraz korzystając ze wstawki assemblera:

	#include <iostream>
	
	using namespace std;
	
	int fun1(int a, int b)
	{
	    int suma;
	    
	    suma = a + b;
	    suma = suma + 1;
	    
	    return suma;
	}
	
	int fun2(int a, int b)
	{
	    int suma;
	    
	    asm("\
	        mov eax, [ebp + 8] \n\
	        add eax, [ebp + 12] \n\
	        add eax, 1 \n\
	        mov [ebp-4], eax \n\
	    ");
	    
	    return suma;
	}
	
	int main()
	{
	    cout << "C++: " << fun1(3,5) << endl;
	    cout << "assembler: " << fun2(3,5) << endl;
	    
	    return 0;
	}

Nie musisz zwracać wartości funkcji poprzez *return*. Wartość zwracana funkcji zawsze znajduje się w rejestrze *EAX* w każdej konwencji wywołania (stdcall, cdecl, fastcall):

	#include <iostream>
	
	using namespace std;
	
	int suma(int a, int b)
	{
	    asm("\
	        mov eax, [ebp + 8] \n\
	        add eax, [ebp + 12] ");
	}
	
	int main()
	{
			cout << "Suma liczb: " << suma(3,5) << endl;
	
			return 0;
	}

Argumenty funkcji można także umieszczać na stosie, jednak nie zwolni nas to z obowiązku operowania rejestrem *EBP* a więc nie ma to sensu.

## Funkcje w assemblerze

Czasem może się zdarzyć, że będziesz potrzebował napisać funkcję w assemblerze. Jest to zabieg dość prosty, jednak w kompilatorze GCC należy skorzystać z dyrektywy *extern "C"*. W przeciwnym wypadku funkcja we wstawce assemblera, **nie będzie widoczna** dla reszty programu w C++.

Tworząc funkcję w assemblerze piszemy w C++ jej sygnaturę. Nie zapominamy aby umieścić ją w klauzuli *extern &#8222;C&#8221;*. Następnie we wstawce assemblera definiujemy ją globalnie używając rozkazu *.global* poprzedzając etykietę funkcji znakiem podkreślenia *_*. Ostatnim krokiem jest napisanie definicji funkcji w assemblerze:

	#include <iostream>
	
	using namespace std;
	
	extern "C" { int suma(int a, int b); }
	asm("\
	    .global _suma \n\
	    _suma: \n\
	    push ebp \n\
	    mov ebp, esp \n\
	    mov eax, [ebp+8] \n\
	    mov ebx, [ebp+12] \n\
	    add eax, ebx \n\
	    mov esp,ebp \n\
	    pop ebp \n\
	    ret \n");
	
	int main()
	{
	    cout << "Suma liczb: " << suma(3,5) << endl;
	    return 0;
	}

Ponieważ całą funkcję napisaliśmy sami, niezbędne jest umieszczenie **prologu i epilogu funkcji**. Kod można nieco skrócić, zastępując prolog i epilog gotowymi funkcjami *enter* i *leave*. We wstawce także działają one bez zarzutu:

	extern "C" { int suma(int a, int b); }
	asm("\
	    .global _suma \n\
	    _suma:  \n\
	    enter 0, 8 \n\
	    mov eax, [ebp+8] \n\
	    mov ebx, [ebp+12] \n\
	    add eax, ebx \n\
	    leave \n\
	    ret \n");

Nic nie stoi na przeszkodzie aby utworzyć do wstawek assemblerowych normalne wskaźniki funkcyjne:

	#include <iostream>
	
	using namespace std;
	
	extern "C" { int suma(int a, int b); }
	asm("\
	    .global _suma \n\
	    _suma: \n\
	    enter 0, 8 \n\
	    mov eax, [ebp+8] \n\
	    mov ebx, [ebp+12] \n\
	    add eax, ebx \n\
	    leave \n\
	    ret \n");
	
	typedef int (*pt)(int, int);
	
	int main()
	{
	    pt wskaznik = (pt)suma;
	    
	    cout << "Suma liczb: " << wskaznik(3,5) << endl;
	    return 0;
	}

## Podsumowanie

Nie należy się bać korzystania z assemblera. Niesie on ze sobą wiele korzyści, ponieważ daje programiście możliwość tworzenie funkcji *naked*, które nie są wcale zmieniane przez kompilator. Jest także niezbędny podczas pisania wszelkiego typu trainerów czy hooków.