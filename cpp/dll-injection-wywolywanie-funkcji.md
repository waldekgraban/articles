---
title: Dll Injection - wywoływanie funkcji
category: cpp
thumbnail: dll-injection.png
published: 2013-01-04T00:00:00Z
---
**Dll Injection** jest to technika pozwalająca uruchomić dowolny kod w pamięci innego procesu, przez zmuszenie procesu do załadowania obcego pliku DLL. Istnieje wiele sposobów na wykonanie **Dll injection**, wszystkie mają pewne wady i zalety. W artykule opiszę w jaki sposób **wywołać funkcję innego procesu** posługując się **Dll Injection**.

<!--more-->

## Zbieranie informacji

Naszym celem jest stworzenie pliku DLL, który ułatwi granie w Sapera. Nie będziemy zajmować się prostym podmienianiem wartości, to zostało opisane we wcześniejszym artykule. Będziemy starać się podpiąć pod różne funkcje gry, jednak w tym celu będziemy musieli zdobyć **ich adresy**.

Istnieje wiele metod na znajdowanie **adresów funkcji**. Rzecz jasna musimy posługiwać się debbugerem. Musimy znaleźć jakiś punkt zaczepienia np. założyć breakpoint na wywołanie funkcji API lub szukać zmieniających się wartości w grze.

Saper jest grą dość mało skomplikowaną. Proponuję pobawić się funkcją odpowiedzialną za &#8222;wygrywanie&#8221; gry. Kiedy wygrywamy grę w saperze dzieje się kilka rzeczy: buźka robi się uśmiechnięta, słychać charakterystyczny dźwięk, pojawia się okienko na wpisanie nicku, pojawia się okienko z najlepszymi wynikami. Musimy znaleźć **adres funkcji**, która wywoływana jest w momencie wygranej.

Odpalamy **CheatEngine**. Będziemy szukać wartości mówiącej o przegranej/wygranej. Nie wiemy czy taka zmienna istnieje w grze, możemy to tylko przypuszczać. Aby szybko przechodzić grę, ustawiamy plansze niestandardową, szeroką _100&#215;100_ z ilością min _1_. Klikamy nową grę i skanujemy pamięć _(unknown initial value)_. Po wygranej grze szukamy zmienionych wartości _(changed value)_. Klikamy nową grę i znowu szukamy zmienionych wartości. Przegrywamy grę i szukamy nowych wartości. Powtarzamy te operację aż do momentu znalezienia _1-3_ adresów. Po zmianie wartości znalezionych adresów odkryłem, że adres _01005160_ wyświetla status gry (np. gdy podmienimy wartość na _3_, to wygramy).

{% include parts/postPicture.html page=page img="ss1" %}

Niestety jest to dopiero adres zwykłej zmiennej. Stanowi ona bardzo dobry punkt zaczepiania, względem którego na pewno uda nam się namierzyć funkcję odpowiedzialną za wygrywanie gry. Załóżmy **pułapkę na zapis** i sprawdźmy co modyfikuje wartość z pod naszego adresu. W tym celu klikamy prawym na adres i wybieramy pozycję _Find out what writes to this address_ i potwierdzamy klikając _OK_. Kilka razy klikamy na nową grę i kilka razy kujemy się.

{% include parts/postPicture.html page=page img="ss2" %} 

Na liście pojawiły się 3 pozycje. Dwie czerwone rejestrowane są w momencie włączenia nowej gry. Zielona w momencie skucia. Odrzucamy operację _AND_. Pozostałe dwie to rozkazy _MOV_ odpowiedzialne za kopiowanie pamięci. Tutaj metodą prób i błędów wybrałem jedną z funkcji. Czerwona nie prowadziła do niczego więc odrzuciłem ją, przetestowałem wcześniej w debbugerze i pominę ten krok aby było szybciej. Zapiszmy więc adres ostatniej funkcji oznaczonej na zielono, jest to _1003492_. Czas wykonać analizę zdobytego adresu.

Odpalamy **OllyDbg**. Przeciągamy do niego plik Saper.exe (wcześniej zrób jego kopię). Naciskamy _CTRL + G_ i wpisujemy adres naszej funkcji czyli _1003492_. Naciskamy _F2_ aby założyć pułapkę na adres. Naciskamy _F9_ aby uruchomić grę. Zgodnie z oczekiwaniami pułapka zatrzyma działanie programu w momencie skucia. Przyjrzyjmy się funkcji, jest ona bardzo długa i zaczyna się pod adresem _0100347C_. Funkcja jest skomplikowana, posiada wiele skoków i rozkazów, składa się z kilku części i widać że jest odpowiedzialna za wiele rzeczy. Potwierdzają to liczne rozkazy _TEST_. Powinniśmy zakładać breakpointy na wszystkie _CALLe_ w funkcji i eksperymentować z działaniem Sapera.

Nie musisz znać assemblera aby przeprowadzić prostą analizę. Wystarczy użyć metody prób i błędów, założyć kilka breakpointów i kilka razy skuć się/wygrać.

Możemy przyjąć, że znaleziona funkcja odpowiedzialna jest za skucie, wygranie oraz nową grę. Nakierować nas to może pierwsza linijka funkcji, która wywoływana jest w _3_ miejscach w kodzie (czyli skucie, nowa gra i wygrana). Kolejnym dowodem jest to, że modyfikuje zmienną z CheatEngine, która wskazywała status gry (wygrana, przegrana). Ostatnim ważnym faktem jest koniec funkcji. Są tam dwa wywołania co dziwne bez żadnych parametrów. Oczywiście chodzi o okienko najlepszych wyników oraz okienko do wpisania nicku.

Przetestujmy to. Zakładamy breakpoint na jedną z funkcji _CALL_ i przechodzimy grę na planszy niestandardowej. Pułapka zadziałała, nasze przypuszczenia potwierdzają się. Zapisujemy adres początku funkcji _0100347C_ oraz wywołania okienek _01003505_ i _0100350A_.

{% include parts/postPicture.html page=page img="ss3" %}

Ściągamy wszystkie pułapki. Klikamy prawym przyciskiem myszy na główne okno OllyDbg a następnie _Search For > All intermodular calls_. Wyświetli się lista wszystkich użytych API systemowych. Możemy założyć breakpoint na dowolną z nich, wybór mamy naprawdę duży. Ja wybrałem okienko about, funkcja odpowiedzialna za jego wyświetlenie to _ShellAboutW_. Zaznaczamy ją i zakładamy pułapkę _(F2)_. Naciskamy _F9_ i wybieramy z menu _Saper &#8211; informacje.._. Pułapka zadziałała. Zapisujemy adres początku funkcji _01003D1D_. Wywołuje ona okienko z informacjami.

Posiadamy już kilka przydatnych adresów, mogli byśmy kombinować z wyświetlaniem min, cyferek, planszy oraz z dźwiękami. API odpowiedzialne za dźwięki w grze to **PlaySoundW**. W ten sposób łatwo znajdziesz funkcję odpowiedzialną za odtwarzanie dźwięków.

Oto zdobyte przez nas adresy:

- *0x0100347C* - koniec gry (wygrana lub przegrana)
- *0x01003505* - okienko z wynikami
- *0x01003D1D* - okienko about

Uwaga! Jedna funkcja &#8222;koniec gry&#8221; odpowiedzialna jest za wiele operacji. Z tego powodu możemy przypuszczać, ze będzie ona **wywoływana z jakimiś parametrami**. **Śledzenie parametrów za pomocą debbugera** opisałem w artykule [Dll Injection Wywoływanie funkcji z parametrami](/cpp/dll-injection-wywolywanie-funkcji-z-parametrami/ "dll injection C++ ollydbg") dlatego nie będę robił tego drugi raz. Parametry w naszym wypadku bardzo łatwo zgadnąć: funkcja może być wywołana z parametrem _true_ lub _false_.

Można to też odczytać podczas analizy gry w debbugerze. Wszystkie wywołania naszej funkcji (są ich _3_) poprzedzone są instrukcjami _push 1_ lub _push 0_.

## Tworzenie DLL

Oto pusty szkielet pliku DLL, na jakim będziemy się wzorować:

	#include <windows.h>
	
	extern "C" BOOL __stdcall DllMain(HMODULE hDLL, DWORD Reason, LPVOID Reserved)
	{
	    switch(Reason)
	    {
	        case DLL_PROCESS_ATTACH:
	            MessageBox(0,"DLL załadowana","komunikat",0);
	            break;
	    
	        case DLL_PROCESS_DETACH:
	            MessageBox(0,"DLL wyrzucona z pamięci","komunikat",0);
	            break;
	    }
	    
	    return 0;
	}

Podczas załadowania biblioteki wykonywana jest instrukcja _DLL_PROCESS_ATTACH_, podczas zamknięcia _DLL\_PROCESS\_DETACH_. Istnieją także inne przełączniki ale nie będą wykorzystane w tym artykule. *extern &#8222;C&#8221; BOOL __stdcall DllMain* jest wymagany w kompilatorze **Code::Blocks**, jeżeli korzystasz z MVC wystarczy zwykłe *BOOL DllMain*.

Aby móc wywołać funkcję, której adres posiadamy, niezbędne jest stworzenie wskaźnika funkcyjnego. Jak stworzyć **wskaźnik na funkcję** opisałem dokładnie w innym artykule o [Ukrywaniu funkcji WinApi](/cpp/ukrywanie-funkcji-winapi/ "Ukrywanie funkcji C++"). Przeczytaj jeden akapit na ten temat aby zrozumieć zasadę wywoływania funkcji przez wskaźnik.

	void WygranaPrzegrana(int flaga)
	{
	    typedef void (__stdcall *wygrana)(DWORD); // deklaracja wskaznika
	    wygrana startWygrana = (wygrana)(0x0100347C); // przypisanie do wskaznika
	    startWygrana(flaga); // wywolanie
	}
	
	void Okno1()
	{
	    typedef void (__stdcall *okno1)(); // deklaracja wskaznika
	    okno1 startOkno1 = (okno1)(0x01003505); // przypisanie do wskaznika
	    startOkno1(); // wywolanie
	}
	
	void Informacja()
	{
	    typedef void (__stdcall *informacje)(); // deklaracja wskaznika
	    informacje startInformacja = (informacje)(0x01003D1D); // przypisanie do wskaznika
	    startInformacja(); // wywolanie
	}

Dodajmy do naszej biblioteki główną funkcje, którą potem wstrzykniemy do programu. Robimy to podczas ładowania biblioteki. Tworzymy nowy wątek _(CreateThread)_ wskazujący na funkcję główną o nazwie _Funkcja_saper_. W naszej głównej funkcji moglibyśmy się pokusić o skorzystanie z _SetTimer_ ale niepotrzebnie wydłużyło by to kod. Skorzystamy z mniej bezpiecznego rozwiązania, zapętlonej pętli opóźnianej za pomocą funkcji _Sleep_. Zadaniem pętli będzie przechwytywanie wcisniętych klawiszy i kojarzenie ich z odpowiednimi zadaniami.

	#define KLAWISZ_JEDEN 0x31
	#define KLAWISZ_DWA 0x32
	#define KLAWISZ_TRZY 0x33
	#define KLAWISZ_CZTERY 0x34
	#define KLAWISZ_PIEC 0x35
	#include <windows.h>	

	DWORD WINAPI Funkcja_saper(LPVOID);
	HMODULE hModule;

	void WygranaPrzegrana(int flaga);
	void Przegrana();
	void Okno1();
	void Informacja();

	// glowna funkcja DLLki
	extern "C" BOOL __stdcall DllMain(HMODULE hDLL, DWORD Reason, LPVOID Reserved)
	{
	    switch(Reason)
	    {
	        case DLL_PROCESS_ATTACH:
	            hModule = hDLL;
	            CreateThread(NULL, NULL, &Funkcja_saper, NULL, NULL, NULL); // odpalamy wątek
	            MessageBox(0,"DLLka zaladowana","komunikat",0);
	            break;	
	        case DLL_PROCESS_DETACH:
	            MessageBox(0,"DLLka usunieta","komunikat",0);
	            break;
	    }
	    return TRUE;
	}	
	// funkcja wątku
	DWORD WINAPI Funkcja_saper(LPVOID)
	{
	    while(1)
	    {
	        if(GetAsyncKeyState(KLAWISZ_JEDEN))
	            WygranaPrzegrana(1);    // 1 wygrana	
	        if(GetAsyncKeyState(KLAWISZ_DWA))
	            WygranaPrzegrana(0);    // 0 przegrana	
	        if(GetAsyncKeyState(KLAWISZ_TRZY))
	            Okno1();	
	        if(GetAsyncKeyState(KLAWISZ_CZTERY))
	            Informacja();	
	        if(GetAsyncKeyState(KLAWISZ_PIEC)) //wywalamy biblioteke
	            FreeLibraryAndExitThread(hModule, 0);	
	        Sleep(50);
	    }	
	    return 0;
	}	
	void WygranaPrzegrana(int flaga)
	{
	    typedef void (__stdcall *wygrana)(DWORD); // deklaracja wskaznika
	    wygrana startWygrana = (wygrana)(0x0100347C); // przypisanie do wskaznika
	    startWygrana(flaga); // wywolanie
	}	
	void Okno1()
	{
	    typedef void (__stdcall *okno1)(); // deklaracja wskaznika
	    okno1 startOkno1 = (okno1)(0x01003505); // przypisanie do wskaznika
	    startOkno1(); // wywolanie
	}	
	void Informacja()
	{
	    typedef void (__stdcall *informacje)(); // deklaracja wskaznika
	    informacje startInformacja = (informacje)(0x01003D1D); // przypisanie do wskaznika
	    startInformacja(); // wywolanie
	}

Po kompilacji kodu powinien pokazać się **plik DLL** ważący od 6-7kb. Nie zapomnij dodać przełącznika _BUILDING_DLL_ do kompilatora (powinien zrobić to automatycznie podczas wyboru projektu DLL).

Dobrym programem do wstrzykiwania DLLek jest **Winject**. Pobierz go i skopiuj do jego katalogu nasz plik DLL. Uruchom Sapera i za pomocą Winject podepnij naszą DLLke. Powinien ukazać się komunikat:

{% include parts/postPicture.html page=page img="ss4" %}

Teraz naciskaj klawisze od _1_ do _4_ aby zobaczyć efekty. Klawisz _5_ wywala DLLke z pamięci.

## Podsumowanie

Zapytasz pewnie po co bawić się w **Dll Injection** skoro można w łatwy sposób napisać trainer modyfikujący wartości w pamięci. Otóż Dll Injection daje nieograniczone możliwości, a używanie **WriteProcessMemory** pozwala jedynie nadpisywać wartości. Wstrzykując Dllke do procesu, posiadamy dostęp do wszystkich jego funkcji i zmiennych, możemy zakładać hooki na funkcje i kontrolować działanie programu. Nie jest to osiągalne zwykłym nadpisywaniem pamięci.