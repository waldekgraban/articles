---
title: Używanie timerów
category: cpp
thumbnail: timer-1.png
published: 2013-01-06T00:00:00Z
---
**Timer** to kontrolka odpowiedzialna za wykonywanie określonego kodu, co określoną ilość sekund. Timery znane są przede wszystkim użytkownikom środowisk takich jak **Delphi**, **C++ Bulider**, **Visual Basic** oraz **platformy .NET**. W ich przypadku utworzenie **kontrolki timera** ogranicza się wyłącznie do przeciągnięcia go na formę i dodania odpowiedniego kodu. Timery są na tyle wygodne, że często są wręcz nadużywane.

<!--more-->

## Wstęp

W internecie można spotkać wiele przykładów kodów źródłowych gdzie timery użyte są po prostu z lenistwa, a problem można było rozwiązać w zwykłej pętli.

Programistom nie przyzwyczajonym do programowania w C++ często przeszkadza brak **timerów**. Spotkałem się z przykładami tworzenia pseudo **timerów** polegających na zapętlonej pętli *while (true)*, opóźnianej funkcją *sleep()*. Jest to rozwiązanie w miarę skuteczne, jednak niepotrzebne i zużywające dużo zasobów. Na szczęście do timerów mamy dostęp zawsze, sięgając po *WinAPI*. Windows przygotował zestaw gotowych funkcji służących do tworzenia i usuwania timerów w programie.

## Uruchomienie timera

Do obsługi timerów musimy znać kilka podstawowych rzeczy:


- *SetTimer* - funkcja tworząca timer
- *KillTimer* - funkcja usuwająca timer
- *TimerProc* - główna funkcja, obsługująca komunikaty *WM_TIMER*

Timer tworzymy w dowolnym fragmencie kodu. Ja utworzę go podczas startu w funkcji *main()*. Oto procedura startowa:

	UINT TimerId = SetTimer(NULL, 1, 3000, &TimerProc);

Utworzyliśmy Timer. Jego identyfikator to  *1*. Co *3* sekundy *(3000 ms)* będzie wykonywał funkcję *TimerProc*. W dowolnym miejscu w kodzie musimy dołączyć główną funkcję timera. Wygląda ona następująco`:`

	void CALLBACK TimerProc(HWND hWnd, UINT nMsg, UINT nIDEvent, DWORD dwTime)
	{
	    cout << "Timer dziala" << endl;
	}

Nie zapomnij zadeklarować funkcji *TimerProc*, jeżeli znajduje się ona za funkcją *main*. Naturalnie musimy dołączyć do programu nagłówek *<windows.h>*.

Nasz timer w chwili obecnej działać nie będzie. Każda kontrolka w Windowsie wysyła komunikaty określonego typu. Jak napisałem wyżej, timer wysyła komunikaty *WM_TIMER*, te komunikaty muszą zostać odebrane przez funkcję *GetMessage*. Następnie wywołana zostaje funkcja *DispatchMessage*, która wyśle je do **procedury okna**. Całość wygląda tak:

	while (1)
	{
	    GetMessage(&Msg, NULL, 0, 0);
	    DispatchMessage(&Msg);
	}

Jak widzimy obsługa komunikatów znajduje się w zapętlonej pętli. Z tego powodu umieszczamy ją na końcu funkcji *main()*. Obsługa komunikatów będzie Ci potrzebna w przyszłości do odbierania wszelkich wiadomości z innych kontrolek, takich jak Buttony, Textboxy i wiele innych. Bez obsługi komunikatów nie mogła by funkcjonować żadna aplikacja napisana w Windowsie. Ostatecznie kod naszego programu wygląda następująco:

	#include <windows.h>
	#include <iostream>
	#include <cstdlib>
	
	using namespace std;
	
	void CALLBACK TimerProc(HWND hWnd, UINT nMsg, UINT nIDEvent, DWORD dwTime);

	int main()
	{
	    UINT TimerId = SetTimer(NULL, 1, 3000, &TimerProc); //tworzenie timera
	    MSG Msg;
	    
	    while (1)
	    {
	        GetMessage(&Msg, NULL, 0, 0);
	        DispatchMessage(&Msg); //obsługa wiadomości
	    }
	    
	    return 0;
	}
	
	void CALLBACK TimerProc(HWND hWnd, UINT nMsg, UINT nIDEvent, DWORD dwTime)
	{
	    cout << "Timer dziala" << endl; //procedura timera
	}

## Usunięcie timera

Po uruchomieniu programu, co 3 sekundy wyświetli się napis **Timer działa**. Uruchomiony timer jest niezależny od programu. Aby zakończyć jego działanie korzystamy z funkcji *KillTimer()*:

	KillTimer(NULL, 1);