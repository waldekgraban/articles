---
title: Subclassing i zwiększanie funkcjonalności aplikacji
category: cpp
thumbnail: subclassing.png
published: 2014-01-03TT00:00:00Z
---
Kolejny artykuł dotyczący ingerencji w aplikacje trzecie, opisujący podstawową technikę **zwiększania funkcjonalności innych procesów**. Głównym wątkiem który poruszę, będzie zastosowanie **subclassingu** w połączeniu z **dll injection**. Jak zwykle na ruszt weźmiemy grę Saper dostępną w systemie Windows XP.

<!--more-->

## Działanie aplikacji Windows

Zanim opiszę **subclassing** wspomnę trochę o zasadzie działania programów napisanych dla systemu Windows. Wszystkie programy składają się ze zbioru **kontrolek** i **form**. Mają one różne cechy i nazwy. Każdą kontrolkę identyfikuje jej unikalny uchwyt nazywany **hwnd**. Jest on potrzebny wielu funkcjom **WinAPI** operującym na tychże kontrolkach.

Podczas używania wszelkich kontrolek generowane są różne **komunikaty**. Aby program (czyli zbiór form i kontrolek) mógł komunikować się z systemem, potrzebna jest obsługa tych komunikatów. Komunikaty wysyłane przez kontrolki zostają przechwytywane przez **pętlę komunikatów**. Pętla komunikatów jest po prostu zwykłą pętlą _while_, odbierającą wiadomości od kontrolek zawartych w programie. Komunikaty są odbierane za pomocą specjalnych funkcji **WinApi** i kierowane do **procedury okna**.

<span class="d"><strong>Procedura obsługi okna</strong> jest specjalną funkcją, która zapewnia obsługę komunikatów dostarczanych przez <strong>pętlę komunikatów</strong>. Funkcja posiada kilka argumentów dzięki którym możemy odróżniać komunikaty i sprawdzać ich treść.</span>

Oto opis argumentów:

- *Hwnd* - unikalny uchwyt kontrolki od jakiej przyszedł komunikat
- *Message* -  wiadomość jaka została wysłana przez kontrolkę
- *wParam* i *lParam* - zawierają parametry charakterystyczne dla wysłanej wiadomości (np. współrzędne kursora)

Najlepszą dostępną dokumentacją dotyczącą WinAPI i komunikatów jest oczywiście strona _msdn.microsoft.com_. W końcu to oni najlepiej powinni wiedzieć, jak działa to co stworzyli.

Na przykład, spójrzmy na komunikat [WM_COMMAND](http://msdn.microsoft.com/en-us/library/windows/desktop/ms647591%28v=vs.85%29.aspx). Jak wynika z dokumentacji jest on wysyłany przez opcje menu oraz przyciski na formie. W zmiennej _wParam_ znajduje się  ID kontrolki lub opcji menu. _lParam_ dla kontrolki zwraca uchwyt dla jej okna, a dla pozycji menu zwraca 0.

Przykładowy szkielet **procedury obsługi okna** wygląda następująco:

	LRESULT CALLBACK WndProc(HWND Hwnd, UINT Message, WPARAM wParam, LPARAM lParam)
	{
	    switch(Message)
	    {
	        case WM_COMMAND:
	            break;
	        
	        case WM_CLOSE:
	            DestroyWindow(Hwnd);
	            break;
	    }
	    
	    return DefWindowProc(Hwnd, Message, wParam, lParam);
	}

Zauważ, że w **procedurze obsługi okna** powyżej obsłużyliśmy tylko 2 komunikaty. Rodzajów komunikatów są setki, odpowiadają za każdą operację związaną z programem (nawet za &#8222;namalowanie&#8221; okna). Gdybyśmy chcieli obsłużyć wszystkie komunikaty kod stałby się niesamowicie długi. Z tego powodu przy wyjściu z funkcji, przekazujemy argumenty do **domyślnej procedury okna** za pomocą funkcji _DefWindowProc_. Nie jest to konieczne, ale za to bardzo wygodne. Jest ona odpowiedzialna za standardową obsługę komunikatów w domyślny sposób.**``**

### Inna postać procedury okna

W większości przykładów w internecie, spotkasz nieco inną postać **procedury obsługi okna**. Przeważnie na końcu zwraca ona _returnem_ wartość 0, a wywołanie **domyślnej procedury okna** znajduje się w _switchu_ w _default_. Wygodniejsza dla nas będzie powyższa postać.

## Czym jest subclassing?

**Subclassing** polega na zmianie wskaźnika do **procedury obsługi okna**. Wskaźnik ten jest zapisany w strukturze *WNDCLASS*.
{: .alert-info :}

**Subclassing** jest niczym innym jak po prostu zamianą domyślnej procedury okna. Zamiany można dokonać za pomocą funkcji _SetWindowLong_ z parametrem _GWL_WNDPROC_**.** Ogólnie rzecz biorąc nic ciekawego, ponieważ zmienić **procedurę obsługi okna** możemy tylko dla procesu, w obrębie wykonywanego wątku.

Ciekawy efekt można uzyskać po połączeniu **subclassingu** z techniką **dll injection**. Funkcja _SetWindowLong_ zwraca wskaźnik na poprzednią procedurę okna. Dzięki temu powstaje ciekawe narzędzie przypominające **hookowanie funkcji**. Możemy śledzić parametry interesujących nas komunikatów, zmieniać je, lub w ogóle nie dopuścić do wykonania poszczególnych funkcji.

### Subclassing i menu

Wstrzykiwać DLL będziemy standardowo za pomocą programu Winject. Po wstrzyknięciu pliku DLL poszerzymy menu o kilka pozycji. Następnie utworzymy własną procedurę okna, aby móc obsłużyć komunikat _WM_COMMAND_. <code class="cpp"></code>

	#include <windows.h>
	
	LONG StaraProcOkna;
	
	LRESULT CALLBACK NowaProcOkna(HWND Hwnd, UINT Message, WPARAM wParam, LPARAM lParam)
	{
	    switch(Message)
	    {
	        case WM_COMMAND:
	            switch(wParam)
	            {
	                case 1234:
	                    MessageBox(0,"Klikniecie :)","wiadomosc",16);
	                    break;
	            }
	            break;
	    }
	    
	    // wywolujemy oryginalna procedure okna
	    return CallWindowProc((WNDPROC)StaraProcOkna, Hwnd, Message, wParam, lParam);
	}
	
	extern "C" BOOL __stdcall DllMain(HMODULE hDLL, DWORD Reason, LPVOID Reserved)
	{
	    switch(Reason)
	    {
	        case DLL_PROCESS_ATTACH:
	            HWND Hwnd = FindWindow("Saper",0);
	            
	            HMENU MenuStare = GetMenu(Hwnd);
	            HMENU MenuNowe = CreateMenu();
	            AppendMenu(MenuStare, MF_STRING | MF_POPUP, (int)MenuNowe, "Tutorial");
	            AppendMenu(MenuNowe, MF_STRING, 1234, "Kliknij mnie"); // 1234 to ID menu
	            DrawMenuBar(Hwnd);
	            
	            StaraProcOkna = SetWindowLong(Hwnd, GWL_WNDPROC, (long)NowaProcOkna);
	            
	            break;
	    }
	    return 1;
	}

Zmodyfikowaliśmy menu, dodaliśmy do niego jedną pozycję. _ID_ menu to _1234_, a więc w procedurze obsługi okna musimy obsłużyć _WM_COMMAND_ właśnie dla tego _ID_.

{% include parts/postPicture.html page=page img="tta1" %}

W naszej nowej procedurze okna spróbujmy obsłużyć istniejące już elementy menu (np. po to aby zmienić ich funkcjonalność). W tym celu potrzebujemy ich _ID_. Można go zdobyć na kilka sposobów, najłatwiejszym z nich jest program **Resource Hacker**. Podejrzymy w nim budowę menu:

{% include parts/postPicture.html page=page img="tta2" %}

_ID_ pozycji &#8222;nowa gra&#8221; to _510_. Obsłużmy komunikat _WM_COMMAND_ w naszej procedurze obsługi okna:

	LRESULT CALLBACK NowaProcOkna(HWND Hwnd, UINT Message, WPARAM wParam, LPARAM lParam)
	{
	    switch(Message)
	    {
	        case WM_COMMAND:
	            switch(wParam)
	            {
	                case 1234:
	                MessageBox(0,"Klikniecie :)","wiadomosc",16);
	                break;
	                
	                case 510:
	                MessageBox(0,"Zablokowane","wiadomosc",16);
	                return 0;
	                break;
	            }
	            break;
	    }
	    
	    // wywolujemy oryginalna procedure okna
	    return CallWindowProc((WNDPROC)StaraProcOkna, Hwnd, Message, wParam, lParam);
	}

Zauważ, że po funkcji _MessageBox_ znajduje się wyjście z funkcji _(return 0)_. Oznacza to, że po kliknięciu w menu &#8222;nowa gra&#8221;, nie zostanie wywołana oryginalna procedura obsługi okna Sapera, z tego też powodu nie możliwe stanie się rozpoczęcie nowej gry.

### Subclassing i okienka

Spróbujmy pójść dalej tropem **subclassingu**. Modyfikacja menu nie dostarcza nam zbyt wielu funkcjonalności. Fajnym pomysłem może okazać się stworzenie nowego okienka, oczywiście otwieranego w obrębie procesu Sapera (a nie jako osobny program).

Stworzenie najprostszego okna dialogowego wymaga następujących kroków:

  * wypełnienie struktury _WNDCLASSEX_
  * rejestracja klasy funkcją _RegisterClassEx()_
  * utworzenie okienka za pomocą funkcji _CreateWindowEx()_

Okienko zostanie wyświetlone po kliknięciu dodanej przez nas pozycji menu. Po utworzeniu **dialogboxa** tworzymy odpowiednie kontrolki. Kilka etykiet, przycisk i timer. Etykiety będą wyświetlały aktualne informacje pobrane z pamięci, przycisk będzie odpowiadał za reset aktualnego czasu. Timer będzie odświeżał informacje i za pomocą _SendMessage_ wyświetlał je na etykietach.

Niezbędne będzie odczytywanie wartości odpowiednich adresów w pamięci, a także ich **modyfikacja**. Ponieważ po wykonaniu **Dll Injection** będziemy znajdować się w wątku w obrębie procesu gry, nie będziemy używać _Read/WriteProcessMemory_. [Będziemy modyfikować pamięć posługując się wskaźnikami generycznymi.](https://www.p-programowanie.pl/cpp/odczytywanie-pamieci-wskaznikami-generycznymi/ "edycja pamięci C++")<code class="cpp"></code>

	#include <windows.h>
	
	LONG StaraProcOkna;
	HWND lab1, lab2;
	UINT TimerId;
	
	void CALLBACK TimerProc(HWND hWnd, UINT nMsg, UINT nIDEvent, DWORD dwTime)
	{
	    // timer wyswietla aktualna ilosc min i czasu
	    
	    void* wsk_miny = (void*)0x1005194; //adres min
	    void* wsk_czas = (void*)0x100579C; //adres czasu
	    
	    // możemy rzutowac na int* BYTE* lub unsigned char*
	    int miny = *(int*)wsk_miny;
	    int czas = *(int*)wsk_czas;
	    
	    char *buffer = new char;
	    
	    itoa(miny,buffer,10);
	    SendMessage(lab1, WM_SETTEXT, 0, (LPARAM)buffer);
	    
	    itoa(czas,buffer,10);
	    SendMessage(lab2, WM_SETTEXT, 0, (LPARAM)buffer);
	}
	
	LRESULT CALLBACK DialogProc(HWND Hwnd, UINT Message, WPARAM wParam, LPARAM lParam)
	{
	    switch(Message)
	    {
	        case WM_CREATE:
	            // tworzenie kontrolek okna dialogowe
	            CreateWindow("static", "Ilosc min: ", WS_VISIBLE | WS_CHILD , 10, 20, 80, 25, Hwnd, (HMENU) 1, NULL, NULL);
	            CreateWindow("static", "Ilosc czasu: ", WS_VISIBLE | WS_CHILD , 10, 45, 80, 25, Hwnd, (HMENU) 1, NULL, NULL);
	            CreateWindow("button", "Resetuj czas", WS_VISIBLE | WS_CHILD , 20, 70, 120, 25, Hwnd, (HMENU) 1, NULL, NULL);
	            lab1 = CreateWindow("static", "124", WS_VISIBLE | WS_CHILD , 98, 20, 80, 25, Hwnd, (HMENU) 1, NULL, NULL);
	            lab2 = CreateWindow("static", "421", WS_VISIBLE | WS_CHILD , 98, 45, 80, 25, Hwnd, (HMENU) 1, NULL, NULL);
	            TimerId = SetTimer(NULL, 1, 300, &TimerProc); //tworzenie timera
	            break;
	        
	        case WM_COMMAND:
	            // resetowanie czasu
	            void* wsk_czas = (void*)0x100579C;
	            *(int*)wsk_czas = 0;
	            break;
	        
	    }
	    return (DefWindowProc(Hwnd, Message, wParam, lParam));
	}
	
	LRESULT CALLBACK NowaProcOkna(HWND Hwnd, UINT Message, WPARAM wParam, LPARAM lParam)
	{
	    switch(Message)
	    {
	    case WM_COMMAND:
	        switch(wParam)
	        {
	            case 1234:
	            {
	                // tworzenie okna dialogowego, rejestracja klasy i wywolanie okna
	                
	                WNDCLASSEX wc = {0};
	                wc.cbSize        = sizeof(WNDCLASSEX);
	                wc.lpfnWndProc   = (WNDPROC) DialogProc;
	                wc.hInstance     = 0;
	                wc.hbrBackground = GetSysColorBrush(COLOR_3DFACE);
	                wc.lpszClassName = TEXT("NazwaKlasy");
	                
	                RegisterClassEx(&wc);
	                
	                CreateWindowEx(WS_EX_DLGMODALFRAME | WS_EX_TOPMOST,  "NazwaKlasy",
	                    "Tajne okienko", WS_VISIBLE | WS_SYSMENU | WS_CAPTION,
	                    400, 400, 170, 130, NULL, NULL, 0, NULL);
	            }
	            break;
	        }
	        break;
	    }
	    
	    // wywolujemy oryginalna procedure okna
	    return CallWindowProc((WNDPROC)StaraProcOkna, Hwnd, Message, wParam, lParam);
	}
	
	extern "C" BOOL __stdcall DllMain(HMODULE hDLL, DWORD Reason, LPVOID Reserved)
	{
	    switch(Reason)
	    {
	    case DLL_PROCESS_ATTACH:
	        
	        HWND Hwnd = FindWindow("Saper",0);
	        
	        HMENU MenuStare = GetMenu(Hwnd);
	        HMENU MenuNowe = CreateMenu();
	        AppendMenu(MenuStare, MF_STRING | MF_POPUP, (int)MenuNowe, "Tutorial");
	        AppendMenu(MenuNowe, MF_STRING, 1234, "Otworz ukryte opcje"); // 1234 to ID menu
	        DrawMenuBar(Hwnd);
	        
	        StaraProcOkna = SetWindowLong(Hwnd, GWL_WNDPROC, (long)NowaProcOkna);
	        
	        break;
	    }
	    return 1;
	}

Zauważ, że okno dialogowe, posiada swoją własną **procedurę obsługi okna**. Oto efekt po wstrzyknięciu DLLki do pamięci Sapera:

{% include parts/postPicture.html page=page img="tta3" %}