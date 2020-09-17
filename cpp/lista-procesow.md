---
title: Lista procesów
category: cpp
thumbnail: lista-procesow.png
published: 2013-08-04T00:00:00Z
---
Podczas pisania różnego typu trainerów do gier lub programów monitorujących system, zachodzi potrzeba wyświetlania **listy procesów** uruchomionych w systemie. **Lista procesów** dostarcza nam wielu informacji takich  jak np nazwa, identyfikator czy ID. Istnieje kilka metod na **wyświetlenie uruchomionych procesów**. Przedstawię wszystkie metody które znam i opiszę jedną której zawsze używam.

<!--more-->

## Funkcje wyświetlające procesy

Znam 3 sposoby umożliwiające **wyświetlenie listy procesów** w systemie Windows. Jeden jest funkcją konsolową, dlatego go nie używam. Pozostaje dwa to funkcje API.

- **Qprocess** - jest to funkcja konsolowa, która wyświetla pełną listę uruchomionych procesów. Nie używam jej ponieważ przechwycenie strumienia wyjścia z konsoli na pewno nie jest optymalnym czasowo rozwiązaniem.
- **EnumProcesses** -  nie używam tej funkcji i nie wiem o niej zbyt dużo. Kiedyś miałem z nią problem i program ciągle się sypał, gdy przepisałem wszystko na *CreateToolhelp32Snapshot* program zadziałał. Od tego czasu funkcja odeszła na drugi plan. Czasem występują w niej problemy związane z różnicą w systemamach 32-bit oraz 64-bit.
- **CreateToolhelp32Snapshot** -  moja ulubiona funkcja. Jest prosta i skuteczna. Zapisuje wszystkie informacje o procesach do struktury *PROCESSENTRY32*. Strukturę można dowolnie przeglądać uzyskując odpowiednie informacje.

## Działanie CreateToolhelp32Snapshot

Funkcja *CreateToolhelp32Snapshot* listuje **wszystkie uruchomione procesy** wraz ze wszystkimi informacjami. Ich obraz zostaje zapisany do specjalnej struktury *PROCESSENTRY32*. Strukturę można przeglądać za pomocą funkcji *Process32First* i *Process32Next*.

{% include parts/postPicture.html page=page img="create-toolhelp32-snapshot" %}

W artykule opisuję w jaki sposób uzyskać listę procesów systemu. Jeżeli użyjemy odpowiednich argumentów podczas wywoływania *CreateToolhelp32Snapshot*, możemy uzyskać nie tylko listę procesów, ale także listę wątków i modułów.

## Listowanie procesów

Aby móc użyć funkcji, musisz zaimportować bibliotekę *tlhelp32.h* i *windows.h*. Po zadeklarowaniu struktury *PROCESSENTRY32* oraz uchwytu dla *CreateToolhelp32Snapshot*, wywołujemy funkcję z parametrem *TH32CS_SNAPPROCESS*, aby uzyskać listę procesów. Następnym krokiem jest ustawienie pola struktury *PROCESSENTRY32* o nazwie *dwSize*. Określa ono rozmiar struktury. Należy to zrobić przed wywołaniem *Process32First*, inaczej program się wykrzaczy.

Za pierwszym razem wywołujemy funkcję *Process32First*, zwraca ona nazwę pierwszego procesu na liście. Z mojego doświadczenia wynika że zawsze jest to tytuł *[System Process]*. Dobrym nawykiem jest wywołanie tej funkcji w instrukcji warunkowej. Jeżeli wszystko przebiegnie pomyślnie możemy wywoływać *Process32Next* w pętli *while*, po spełnieniu warunku.

Argumentami *Process32First* i *Process32Next* **są w pierwszej kolejności: uchwyt zwrócony przez *CreateToolhelp32Snapshot* zawierający obraz procesów, oraz wskaźnik na strukturę *PROCESSENTRY32***.

W pętli możemy w dowolny sposób przetwarzać rekordy struktury.

	#include <windows.h>
	#include <tlhelp32.h>
	#include <iostream>
	
	using namespace std;
	
	int main()
	{
	    PROCESSENTRY32 proc32;  //deklaracja struktury
	    HANDLE hSnapshot;       //uchwyt na CreateToolhelp32Snapshot
	    
	    hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
	    
	    //ustawiamy rozmiar struktury
	    proc32.dwSize = sizeof(PROCESSENTRY32);
	    
	    //pierwsze wywolanie Process32First
	    if(Process32First(hSnapshot, &proc32))
	    {
	        //wyświetlamy Process32First, czyli napis "[System Process]"
	        cout << proc32.szExeFile << endl;
	        
	        //glowna petla wyświetlająca procesy przez Process32Next
	        while(Process32Next(hSnapshot, &proc32))
	        {
	            cout << proc32.szExeFile << endl;
	        }
	    }
	    
	    CloseHandle(hSnapshot);
	    
	    system ("pause >nul");
	    return 0;
	}

Powyższy przykład wyświetli **nazwy plików wykonywalnych wszystkich procesów**, czyli na dobrą sprawę ich nazwy. Jest za to odpowiedzialne pole *szExeFile*. Oto wszystkie dostępne pola w strukturze *PROCESSENTRY32*:

- *dwSize* &#8211; rozmiar struktury, zawsze ustawimy na *sizeof(PROCESSENTRY32)*, jeszcze przed użyciem jakiejkolwiek funkcji, inaczej nie odpali Process32First.
- *th32ProcessID* &#8211; ID procesu czyli jego unikalny identyfikator. Przy każdym uruchomieniu procesu zmienia się.
- *cntThreads* &#8211; ilość wątków używanych przez dany proces.
- *th32ParentProcessID* &#8211; ID procesu macierzystego, czyli tego który uruchomił aktualny proces. Dla programów, które uruchamiasz kliknięciem myszką przeważnie będzie to ID procesu explorera.
- *pcPriClassBase* &#8211; priorytet procesu. Priorytety można ustawiać np. w Menedżerze Zadań Windows klikając na proces prawym klawiszem myszy.
- *szExeFile* &#8211; nazwa pliku wykonywalnego procesu (użyte w przykładzie powyżej).

Jak widać bardzo prosto jest stworzyć swój menadżer procesów podobny jak w Windowsie. Musisz pamiętać, że struktura zawierająca informacje o procesach jest **tylko do odczytu**, nie możesz zmieniać jej zawartości.