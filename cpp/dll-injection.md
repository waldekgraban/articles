---
title: Dll Injection
category: cpp
thumbnail: dll-injection.png
published: 2014-07-06T00:00:00Z
---
Szykuje się artykuł, opisujący wykonanie **techniki** **DLL Injection**. Do napisania artykułu przymierzałem się już wiele miesięcy temu, miał być pierwszym dotyczącym **reverse engineeringu**. Ciągle odwlekałem go na później, ponieważ kiepsko u mnie z czasem, a chciałem napisać go solidnie. Umiejętność wykonania **DLL Injection** wiele razy przyda Ci się, szczególnie w programach edytujących pamięć innych aplikacji.

<!--more-->

## Czym jest DLL Injection

Pojęcia **DLL Injection** nie należy postrzegać dosłownie. W głównej mierze chodzi o uruchomienie dowolnego kodu (napisanego przez nas) w **obcym procesie**. Wspominam o tym z powodu dostania kilku listów na temat artykułu [Wywoływanie funkcji poprzez DLL Injection](/cpp/dll-injection-wywolywanie-funkcji/). Kilka osób było oburzonych faktem, że w artykule dotyczącym **DLL Injection** posłużyłem się gotowym injectorem, zamiast napisać własny.

**DLL Injection** (ang. wstrzyknięcie DLL) jest techniką, która pozwala na uruchomienie dowolnego kodu w **przestrzeni adresowej** innego procesu. Przygotowany przez nas **plik DLL** wstrzykujemy programem nazywanym **injectorem** (strzykawka).
{: .alert-info} 

{% include parts/postPicture.html page=page img="injection1" %}

Według mnie, jest to technika bardzo silnie związana z **reverse engineeringiem**. W większości przypadków wykorzystywana jest do **zmiany zachowań** innych aplikacji oraz **modyfikacji ich funkcjonalności**. Idąc dalej tym tropem, należy śmiało stwierdzić, że tę technikę wykorzystuje zdecydowana większość wirusów i innych programów uprzykrzających życie.

Dzięki przeprowadzeniu "ataku" **DLL Injection**, w obrębie przestrzeni adresowej innego procesu uruchamiamy osobny **wątek** (ang. thread), zawierający kod naszego autorstwa. Wątek znajdujący się w obcym procesie ma **swobodny dostęp do pamięci** tego procesu, co za tym idzie może modyfikować wszelkie zmienne oraz wywoływać jego funkcje. Chcąc modyfikować zmienne wystarczy użyć w tym celu [wskaźników generycznych](/cpp/odczytywanie-pamieci-wskaznikami-generycznymi/), a do **wywoływania funkcji** wystarczy utworzyć wskaźnik. Schemat **DLL Injection** metodą **CreateRemoteThread** wygląda następująco:

- otwieramy proces **OpenProcess** w celu uzyskania uchwytu
- alokujemy w nim kilka bajtów za pomocą **VirtualAllocEx** aby mieć miejsce na nazwę DLLki
- w zalokowane miejsce (pkt 2) wpisujemy nazwę pliku DLL funkcją **WriteProcessMemory**
- znajdujemy adres funkcji **LoadRibrary** w przestrzeni adresowej procesu za pomocą **GetProcAddress**
- startujemy nowy wątek w procesie jako początek podając mu adres **LoadRibrary** (pkt 4) a jako argument adres nazwy DLLki  (pkt 2)

**Injector** może posługiwać się różnymi metodami w celu **wstrzyknięcia pliku DLL**. W tym artykule opiszę tylko metodę opartą na **CreateRemoteThread**.

## DLL Injection &#8211; metoda CreateRemoteThread

Jak wspomniałem, istnieje wiele metod na wstrzyknięcie pliku DLL do innego procesu. Jedną z najbardziej popularnych metod (niestety **bardzo wykrywalnych** przez antywirusy) jest użycie *CreateRemoteThread* wraz z funkcją *LoadRibrary*. Zaletą tej metody jest duża skuteczność i kompatybilność z różnymi wersjami Windowsów (od XP wzwyż). W dużym skrócie polega ona na "zmuszeniu" obcej aplikacji, do wywołania funkcji *LoadRibrary*, która z kolei wczyta nasz plik DLL.

Pierwszym krokiem jest otwarcie procesu, do którego chcemy wstrzyknąć **plik DLL**. Zrobimy to za pomocą funkcji *OpenProcess* w zamian otrzymując **uchwyt procesu**. Argumentem jaki przyjmuje funkcja jest m.in. *PID*. Proces należy otworzyć z flagą zapewniającą pełną kontrolę *PROCESS_ALL_ACCESS*.

{% include parts/postPicture.html page=page img="injection2" %}

Posiadając uchwyt procesu, możemy przystąpić do **alokowania pamięci**, potrzebnej do zapisania nazwy pliku DLL. Zrobimy to za pomocą funkcji *VirtualAllocEx*. Parametrami funkcji są: **uchwyt procesu**, **długość nazwy DLL**, flagi *MEM_RESERVE \| MEM_COMMIT* zapewniające rezerwację **wirtualnej pamięci** wypełnionej zerami (pustej) oraz flaga *PAGE_READWRITE* zapewniająca możliwość odczytu i zapisu nowego fragmentu pamięci.

Należy pamiętać, że do nazwy pliku DLL należy dopisać *NULL* kończący C-stringa oraz uwzględnić na niego dodatkowe miejsce. W przypadku podania tylko nazwy pliku DLL, plik musi znajdować się w tym samym katalogu co program, w który wstrzykujemy DLLkę. Możliwe jest oczywiście podanie pełniej ścieżki, wtedy plik DLL może znajdować się w byle jakiej lokalizacji. W takim przypadku wskazane jest użycie *GetFullPathName*.

{% include parts/postPicture.html page=page img="injection4" ext="gif" %}

Do zarezerwowanego miejsca w pamięci wpisujemy nazwę DLLki korzystając z *WriteProcessMemory*.

{% include parts/postPicture.html page=page img="dll-injection3" ext="gif" %}

Aby wczytać DLLkę używamy funkcji *LoadRibraryA*. Jest ona importowana z biblioteki *kernel32.dll*. Oznacza to, że DLL Injection tą metodą, możemy wykonać tylko do aplikacji importujących *kernel32.dll*. Na szczęście używa jej prawie każda aplikacja Win32. Aby móc wykorzystać funkcję *LoadRibraryA* musimy znaleźć jej adres w pamięci procesu, do którego wstrzykujemy DLLkę. *LoadRibraryA* przyjmuje jako argument **wskaźnik na nazwę pliku DLL** (dokładniej *LPCTSTR* czyli *const char*).

Adres funkcji znajdziemy używając *GetProcAddress* z dodatkowym *GetModuleHandle*:

{% include parts/postPicture.html page=page img="dll-injection5" ext="gif" %}

Wszystko zostało przygotowane do **wstrzyknięcia DLL**. Ostatnim krokiem jest wystartowanie nowego wątku w procesie posługując się *CreateRemoteThread*. Jako główną funkcję startową wątku *lpStartAddress* podajemy adres funkcji *LoadRibraryA*. Parametrem przekazanym do głównej funkcji nowego wątku *lpParameter* będzie wskaźnik na nazwę wpisaną funkcją *WriteProcessMemory*.

{% include parts/postPicture.html page=page img="dll-injection-CreateRemoteThread" ext="gif" %}

Wątek startuje i wykonuje się główna funkcja biblioteki DLL. Kompletny kod **injectora napisanego w C++** może wyglądać następująco:

	#include <iostream>
	#include <windows.h>
	
	using namespace std;
	
	int main()
	{
	    
	    HANDLE HProc;
	    LPVOID LibAddr, DllAdr;
	    
	    char Dll[9] = "dll.dll\0";
	    
	    //pid aplikacji do ktorej wstrzykujemy DLLke
	    DWORD pid = 5816;
	    
	    // otwieramy proces
	    HProc = OpenProcess(PROCESS_ALL_ACCESS, FALSE, pid);
	    
	    // alokujemy pamiec i zapisujemy adres
	    DllAdr = (LPVOID)VirtualAllocEx(HProc, NULL, strlen(Dll), MEM_RESERVE|MEM_COMMIT, PAGE_READWRITE);
	    
	    // wpisujemy nazwe dllki do pamieci
	    WriteProcessMemory(HProc, (LPVOID)DllAdr, Dll,strlen(Dll), NULL);
	    
	    // szukamy adresu LoadRibraryA i zapisujemy go
	    LibAddr = (LPVOID)GetProcAddress(GetModuleHandle("kernel32.dll"), "LoadLibraryA");
	    
	    // startujemy watek podajac adres do LoadRibraryA
	    // oraz adres do sciezki do DLLki
	    CreateRemoteThread(HProc, NULL, NULL, (LPTHREAD_START_ROUTINE)LibAddr, (LPVOID)DllAdr, NULL, NULL);
	    
	    CloseHandle(HProc);
	    
	    return 0;
	}

Jak to zwykle bywa, nie ma w moim kodzie obsługi błędów. Nie umieściłem ich po to aby kod był treściwy i krótki. Należy takową oczywiście dodać.

## Komunikacja z DLLką

**Plik DLL** wstrzyknięty do pamięci innego procesu, nie musi być główną instancją naszego programu. Może pełnić jedynie rolę "modułu" komunikującego się z naszą bazową aplikacją. Do komunikacji pomiędzy **naszym programem** a **naszą DLLką** wstrzykniętą do obcego procesu, należy użyć **komunikacji międzyprocesowej** (ang. IPC). Zazwyczaj w takim przypadku  **injector** oprócz funkcji wstrzykiwania DLL posiada także interfejs i funkcje dla użytkownika (wysyłające sygnały do wstrzykniętej DLLki poprzez IPC).

{% include parts/postPicture.html page=page img="ipc" %}

Techniki **IPC** zapewniają możliwość **wymiany informacji** pomiędzy dwoma aplikacjami poprzez:

- pliki mapowane w pamięci (ang. *file mapping*)
- pamięć współdzieloną (ang. *shared memory*)
- semafory (ang. *semaphores*)
- łącza (ang. *pipes*)
- gniazda (ang. *sockets*)
- kolejki komunikatów (ang. *message queues*)

Więcej na ten temat, ukaże się w osobnym artykule.

## Podsumowanie

Istnieją inne metody umożliwiające przeprowadzenie **DLL Injection**. Najpopularniejsze z nich to:

- CreateRemoteThread & LoadRibrary (opisana w artykule)
- CreateRemoteThread & WriteProcessMemory (wstrzyknięcie do CodeCave kodu DLLki, bez wczytywania jej funkcją LoadRibrary)
- SetWindowsHookEx

Łatwo znaleźć o nich informacje w internecie, przeważnie nie po polsku.

Metoda opisana w artykule **działa zarówno na systemach x86 oraz x64**. W przypadku wystąpienia błędu *ERROR_ACCESS_DENIED* o kodzie *0x5* prawie na pewno chodzi o wstrzykiwanie DLLki skompilowanej dla architektury x86 do aplikacji działającej w architekturze x64. Jeżeli skompilujesz DLLke w Code::Blocks x86 nie będziesz wstanie wstrzyknąć jej w żaden program systemowy na Windows7 x64 (kalkulator, notatnik itp).

Kolejną kwestią o której warto wspomnieć są problemy z otwieraniem procesu w trybie *PROCESS_ALL_ACCESS*. Jeżeli skompilujesz **Inector** na systemie Vista lub Windows7, **nie będziesz w stanie uruchomić go na Windows XP**. Aby temu zapobiec należy zdefiniować w Injectorze:

	#define _WIN32_WINNT _WIN32_WINNT_WINXP

Wynika to z różnic w wielkości flag na poszczególnych systemach, więcej na ten temat można znaleźć na MSDN.