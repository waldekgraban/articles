---
title: Edycja pamięci procesów
category: cpp
thumbnail: edycja-pamieci-procesow.png
published: 2012-07-25T00:00:00Z
author: karol-trybulec
---
Windows udostępnia nam funkcje pozwalające odczytywać i zapisywać pamięć innych procesów. Służą do tego **ReadProcessMemory** i **WriteProcessMemory**. Przed odczytaniem pamięci procesu musimy uzyskać jego uchwyt (HANDLE) i posiadać odpowiednie prawa dostępu. Najlepszym darmowym programem do operacji na pamięci procesu jest **CheatEngine** oraz **TSearch**. Naszą aplikacją testową będzie Saper, ponieważ tę grę każdy posiada.

<!--more-->

## Znajdywanie adresów

Jest to artykuł skierowany raczej do początkujących, pominę sprawę ASLR oraz wskaźników. Zajmiemy się adresami statycznymi.

Każdy bajt aplikacji jest zapisany w pamięci procesu i posiada swój unikalny adres. Aby odczytać np. czas z gry saper musimy wiedzieć pod jakim adresem się znajduje. Otwieramy grę Saper a następnie program CheatEngine. Wybieramy z listy proces o nazwie *winmine.exe*. Teraz możemy przeprowadzać dowolne operacje na pamięci Sapera. Standardowo ilość min w grze Saper to *10* - a więc musimy znaleźć tą wartość. W polu *Value* wpisujemy wartość *10*, *Scan type* należy ustawić na *Exact Value*, *Value type* proponuję ustawić na *4Bytes* więcej nie będzie potrzebne. Skanujemy proces w poszukiwaniu wpisanej wartości, klikając na przycisk *First Scan*. 

Prawdopodobnie adresów pojawi się dość dużo, dlatego przełączamy się na okienko sapera i zmniejszamy ilość min (klikamy prawym na dowolne pole). Ilość min została zmniejszona z *1* na *9*. Powtarzamy czynność wyszukania wartości, wpisując w pole *Value* liczbę *9* (aktualna ilość min) i klikając w przycisk *Next Scan*. Czynność powtarzamy aż na liście nie zostanie tylko jeden adres (pamiętaj aby za każdym razem klikać *Next Scan*). Gdy mamy już tylko jeden adres, jest to z pewnością adres min w grze. Wartość możemy dowolnie modyfikować poprzez dwukrotne kliknięcie i wpisanie nowej wartości min. W celu znalezienia adresu odpowiedzialnego za czas, robimy dokładnie to samo jak wyżej. Skanujemy proces aż zostanie tylko jeden adres.

Znaleziony przeze mnie adres wskazujący na ilość min w Saperze to *1005194*, Twój adres może się różnić jeżeli posiadasz inną wersję gry.

## Odczytywanie wartości z pamięci

Znaleźliśmy interesujące nas adresy pamięci. Deklarujemy je w kodzie programu, należy pamiętać że są one w formacie heksadecymalnym, więc aby zadeklarować je w C++ należy dodać przedrostek *0x*, w VisualBasic *&H* a w delphi *$*.

Szukamy okna o tytule &#8222;Saper&#8221; za pomocą funkcji *FindWindow*, zwraca ona hwnd okna. Jeżeli nie znajdzie okna (tzn aplikacja jest zamknięta) zwraca *0*. Za pomocą funkcji *GetWindowThreadProcessId* pobieramy uchwyt *(HANDLE)* aplikacji, tutaj wymagany jest wcześniej odczytany *hwnd*. Następnie za pomocą *OpenProcess* otwieramy proces Sapera, określając prawa dostępu. Prawa dostępu można znaleźć w MSDN więc nie będę ich kopiował. Nas interesuje jedynie *PROCESS_VM_READ*, co pozwoli na odczyt.

**Uwaga!** Bądź precyzyjny używając flag dostępu w funkcji *OpenProcess*. Jeżeli chcesz odczytywać wartości użyj *PROCESS_VM_READ*, jeżeli zapisywać *PROCESS_VM_WRITE \| PROCESS_VM_OPERATION*. Nigdy nie używaj *PROCESS_ALL_ACCESS*, jeżeli nie jest to wymagane! Flaga ta nadaje wszystkie uprawnienia i nie działa na wszystkich systemach bez praw do debugowania (to już funkcja *SeDebugPrivilege*). Konsekwencją tego będzie np. brak możliwości odczytywania pamięci na koncie ograniczonym w Windows Vista/7.
{: .alert-info :}

Kolejnym krokiem jest użycie naszej właściwej funkcji *ReadProcessMemory*. Jej najważniejsze argumenty to **uchwyt otwartego procesu**, adres bazowy, bufor do którego zapisujemy wartość i wielkość bufora. Funkcja jest typu *BOOL* tzn zwraca *true* lub *false* w razie niepowodzenia.

	#include <iostream>
	#include <windows.h>

	using namespace std;

	int main()
	{
		int miny = 0x1005194; //adres min
		int czas = 0x100579C; //adres czasu
		
		long ilosc_czas = 0;
		long ilosc_miny = 0;
		
		DWORD processId;
		HANDLE hProcess;
		
		HWND hSaper = FindWindow("Saper", NULL); //hwnd okna
		
		if(hSaper) //jezeli gra jest uruchomiona
		{
			GetWindowThreadProcessId(hSaper, &processId); //pobieramy uchwyt
			hProcess = OpenProcess(PROCESS_VM_READ, false, processId);
			ReadProcessMemory(hProcess, (LPVOID)czas, &ilosc_czas, sizeof(long), 0); //odczytujemy
			ReadProcessMemory(hProcess, (LPVOID)miny, &ilosc_miny, sizeof(long), 0); //odczytujemy
			CloseHandle(hProcess);
		}
		
		cout << "Aktualna ilosc min: " << ilosc_miny << endl;
		cout << "Aktualna wartosc czasu: " << ilosc_czas << endl;
		
		system("PAUSE >nul");
		return(0);
	}

## Zapisywanie wartości do pamięci

Wszystko odbywa się tak jak w przypadku odczytywania. Używamy funkcji *WriteProcessMemory*. Określamy adres oraz wartość jaką chcemy zapisać. Zwróć uwagę na flagi *OpenProcess*, w przeciwieństwie do *ReadProcessMemory* tutaj wymagana jest jeszcze *PROCESS_VM_OPERATION*.

	#include <iostream>
	#include <windows.h>

	using namespace std;

	int main()
	{
		int czas = 0x100579C; //adres czasu

		int nowy_czas = 3;

		DWORD processId;
		HANDLE hProcess;

		HWND hSaper = FindWindow("Saper", NULL); //hwnd okna

		if(hSaper) //jezeli gra jest uruchomiona
		{
			GetWindowThreadProcessId(hSaper, &processId); //pobieramy uchwyt
			hProcess = OpenProcess(PROCESS_VM_WRITE | PROCESS_VM_OPERATION, false, processId);
			WriteProcessMemory(hProcess, (LPVOID)czas, &nowy_czas, sizeof(int), 0); //zapisujemy
			CloseHandle(hProcess);
		}

		cout << "Czas zostal ustawiony na 3 sekundy" << endl;

		system("PAUSE >nul");
		return(0);
	}

## Podsumowanie

Poznanie działania funkcji służących do operacji na pamięci danego procesu, jest absolutną podstawą. Do bardziej zaawansowanych działań potrzebny Ci będzie debbuger oraz znajomość Assemblera. Nie musisz umieć programować w asmie, ja też nie umiem. Wystarczy znać podstawowe rozkazy, wiedzieć czym jest stos, rejestry oraz przerwania.