---
title: Ukrywanie funkcji WinApi
category: cpp
thumbnail: ukrywanie-funkcji-winapi.png
published: 2012-08-02T00:00:00Z
author: karol-trybulec
---
**Maskowanie funkcji WinAPI** może się nam przydać kiedy antywirusy internetowe czepiają się naszego programu. Dla testów napisałem program w C++, który podczas startu dodaje się do autostartu (rejestr) a następnie łączy się z losowym serwerem przez *winsock*. Po tych czynnościach program się zamykał. Przeskanowałem go na skanerze internetowym, wynik skanu to **8/43**. Aż 8 antywirusów wykryło zagrożenie.

<!--more-->

## Definiowanie wskaźnika funkcji

Aby ukryć wywołanie funkcji niezbędne będzie utworzenie **definicji wskaźnika funkcyjnego**. Jest to zabieg dość prosty, zaprezentuję go na prostym przykładzie programu obliczającego potęgę liczby:

	long int potega(long int podstawa, int wykladnik)
	{
	    for (int i = 1; i<wykladnik;i++)
	        podstawa*=podstawa;

	    return podstawa;
	}

Oto oryginalny kod funkcji skopiowanej z innej podstrony naszego serwisu. Zajmijmy się definicją wskaźnika. Interesuje nas deklaracja funkcji a więc tylko pierwsza linijka. Usuwamy z niej nazwy argumentów, wymyślamy nową nazwę oraz poprzedzamy słowem kluczowym *typedef*. Ostateczna postać:

    typedef long int (* pt)(long int, int);

Mamy definicję wskaźnika, musimy jej teraz użyć do wskazania na naszą funkcję o nazwie *potega()*. Nazwa może być dowolna. Całość wygląda tak:

	long int potega(long int podstawa, int wykladnik)
	{
	    for (int i = 1; i<wykladnik;i++)
	        podstawa*=podstawa;

	    return podstawa;
	}

	typedef long int (* pt)(long int, int);
	pt licz = (pt)potega;

Zamiast używać funkcji _potega()_ możemy odnieść się do wskaźnika funkcji który utworzyliśmy, a więc:

    cout << licz(5,2);

## Maskowanie funkcji WinAPI

Aby ukryć daną funkcję *WinAPI*, musimy znaleźć opis jej deklaracji na *MSDN*. Przykładowo rozważmy funkcję *ReadProcessMemory*. Jest ona używana bardzo często kiedy chcemy napisać aplikację, która bezpośrednio modyfikuje pamięć innej aplikacji. Deklaracja wygląda następująco:

	BOOL WINAPI ReadProcessMemory(
	    __in   HANDLE hProcess,
	    __in   LPCVOID lpBaseAddress,
	    __out  LPVOID lpBuffer,
	    __in   SIZE_T nSize,
	    __out  SIZE_T *lpNumberOfBytesRead
	);

Widzimy, jaką wartość zwraca funkcja oraz wszystkie jej parametry wejściowe i wyjściowe. Ze strony *MSDN* odczytujemy jeszcze z jakiej biblioteki korzysta:

    Kernel32.dll

Zamiast wstawić funkcję w kodzie naszego programu, utworzymy alias wskaźnika a następnie wskaźnik do funkcji używając *GetProcAddress* oraz *GetModuleHandle*. Nazwy funkcji będą zapisane w osobnych zmiennych dzięki czemu antywirusy nie wykryją wywołania. Można je jeszcze zahashować jednak tego tutaj nie robiłem. Całość powinna wyglądać tak:

	typedef BOOL (WINAPI *__ReadProcessMemory)
	(
	    HANDLE hProcess,
	    LPCVOID lpBaseAddress,
	    LPVOID lpBuffer,
	    SIZE_T nSize,
	    SIZE_T *lpNumberOfBytesRead
	);

	const char funkcja[] = "ReadProcessMemory";
	const char biblioteka[] = "kernel32.dll";

	__ReadProcessMemory CzytajPamiec = (__ReadProcessMemory)GetProcAddress(GetModuleHandle(biblioteka), funkcja);

Teraz możemy korzystać z funkcji *ReadProcessMemory* pod aliasem *CzytajPamiec* ale żaden antywirus nie wykryje importu funkcji. Poniżej podaję przykładowy program korzystający z "ukrytej" funkcji *ReadProcessMemory*:

	#include <iostream>
	#include <windows.h>
	
	using namespace std;
	
	typedef BOOL (WINAPI *__ReadProcessMemory)
	(
	    HANDLE hProcess,
	    LPCVOID lpBaseAddress,
	    LPVOID lpBuffer,
	    SIZE_T nSize,
	    SIZE_T *lpNumberOfBytesRead
	);
	
	const char funkcja[] = "ReadProcessMemory";
	const char biblioteka[] = "kernel32.dll";
	
	__ReadProcessMemory CzytajPamiec = (__ReadProcessMemory)GetProcAddress(GetModuleHandle(biblioteka), funkcja);
	
	int main()
	{
	    int wartosc = 0x400000;
	    int wynik = 0;
	    
	    DWORD processId;
	    HANDLE hProcess;
	    
	    HWND gadu = FindWindow("Gadu-Gadu", NULL);
	    
	    if(gadu)
	    {
	        GetWindowThreadProcessId(gadu,&processId);
	        hProcess = OpenProcess(PROCESS_VM_READ, false, processId);
	        CzytajPamiec(hProcess, (LPVOID)wartosc, &wynik, sizeof(int), 0);
	        CloseHandle(hProcess);
	    }
	    
	    system("pause");
	    return 0;
	}

W ten sposób możemy korzystać ze wszystkich funkcji WinAPI32, wystarczy zerknąć na *MSDN* aby zobaczyć ich definicję, oraz biblioteki, z jakich można je wczytać.

Dzięki wywoływaniu funkcji przez wskaźniki funkcji, wykrywalność programu spadła z *8/43* do *1/43*. Kilka poprawek w kodzie pozwoliło prawie całkiem wyeliminować problem.