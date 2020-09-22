---
title: Odczytywanie pamięci wskaźnikami generycznymi
category: cpp
thumbnail: wskazniki-generyczne.png
published: 2014-01-03T00:00:00Z
---
Czy zastanawiałeś się kiedyś **do czego przydają się wskaźniki generyczne**? Jednym z ciekawych zastosowań **wskaźników generycznych** jest **odczytywanie pamięci**. W połączeniu z **dll injection** uzyskujemy na prawdę wygodne narzędzie. Możliwe, że ten artykuł nie zmieni Twojego życia, jednak postanowiłem opisać w nim tę prostą metodę. W tym artykule przeczytasz o tym, jak odczytywać pamięć procesu po wstrzyknięciu do niego biblioteki DLL (**dll injection**).

<!--more-->

## Co to jest wskaźnik generyczny?

Zmienna wskaźnika (czyli wskaźnik) to zmienna wskazująca na jakiś obszar pamięci. Wskaźnik musi być tego samego typu jakiego jest zmienna na którą wskazuje. Oprócz zmiennych, wskaźniki mogą wskazywać na różne obiekty.

**Wskaźnikiem generycznym** nazywamy wskaźnik typu _void_. Oznacza to, że wskazuje on na jakiś **obszar pamięci** z góry nie określony. Wskaźniki generyczne można bardzo łatwo rzutować na wszelkie inne typy. Rzutowanie wskaźnika generycznego jest jedynym sposobem w jaki można wyświetlić wskazywaną przez niego **wartość**.  <code class="cpp"></code>

	int pesel = 1234;
	void *wsk = &pesel;
	
	*(int*)wsk = 4321;              //rzutowanie na *int
	cout << *(int*)wsk << endl;     //rzutowanie na *int

Więcej o wskaźnikach dowiesz się z artykułu [wskaźniki C++](https://www.p-programowanie.pl/cpp/wskazniki/ "wskaźniki C++").

##  Odczytywanie pamięci i DLL Injection

Jeżeli interesujesz się tematem **inżynierii wstecznej** zapewne nie raz korzystałeś z **dll injection**. Jest to technika przydatna szczególnie podczas pisania różnych trainerów do gier lub zwiększania funkcjonalności programów.

Po wstrzyknięciu biblioteki DLL, nie musisz używać _ReadProcessMemory_ aby **odczytać pamięć z procesu**, ponieważ już się w nim znajdujesz (w odpalonym wewnątrz procesu wątku). Posiadając adres konkretnej zmiennej w pamięci, możesz ją odczytać lub nadpisać, tworząc właśnie **wskaźnik generyczny**.

Posłużmy się gotowymi adresami pamięci z artykułu [odczytywanie pamięci procesów C++](/cpp/edycja-pamieci-procesow/). Odczytywaliśmy w nim wartości zmiennych liczbowych całkowitych z gry Saper (Saper z Windows XP!).

- Adres ilości min: *0x1005194*

Posiadamy adres oraz szkielet pustej DLLki. Bibliotekę DLL będę wstrzykiwał za pomocą programu Winject. Obecnie wygląda ona następująco:

	extern "C" BOOL __stdcall DllMain(HMODULE hDLL, DWORD Reason, LPVOID Reserved)
	{
	    switch(Reason)
	    {
	        case DLL_PROCESS_ATTACH:
	        
	        break;
	    }
	    return TRUE;
	}

Utwórzmy teraz **wskaźnik generyczny** na nasz adres.

Możemy odczytywać zarówno zmienne liczbowe jak i tekstowe. W przypadku **odczytywania zmiennych** liczbowych warto rzutować na _BYTE*_ lub _unsigned char*_. Na końcu trzeba także rzutować niejawnie na _int_ aby nie dostać znaczka tylko liczbę. W przypadku zmiennych tekstowych oczywiście rzutujemy na _char*_.

Kompletny kod wygląda następująco:

	#include <fstream>
	#include <string>
	
	using namespace std;
	
	extern "C" BOOL __stdcall DllMain(HMODULE hDLL, DWORD Reason, LPVOID Reserved)
	{
	    switch(Reason)
	    {
	        case DLL_PROCESS_ATTACH:
	            void* wsk = (void*)0x1005194;
	            int miny = (int)*(unsigned char*)wsk;   // odczytujemy wartość min
	            miny = miny * 2;                        // mnożymy razy 2
	            *(char*)wsk = miny;                     // zapisujemy nową wartość zmiennej
	        
	            break;
	    }
	    return TRUE;
	}

Aby efekt był widoczny w oknie sapera wystarczy oznaczyć flagę prawym przyciskiem myszy w dowolnym miejscu.

Dzięki takiemu rozwiązaniu oszczędzamy czas oraz **kod staje się krótszy**. Nie musimy zdobywać uchwytu ani identyfikatora procesu.