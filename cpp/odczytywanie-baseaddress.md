---
title: Odczytywanie baseaddress
category: cpp
thumbnail: base-adress.png
published: 2012-07-26T00:00:00Z
---
Poruszając temat **adresu bazowego** można by się rozpisać na dziesiątki stron, należało by zacząć od budowy plików wykonywalnych Windows, o nagłówkach i sekcjach (np. PE Header) oraz adresach RVA i VA (relatywnych i bezwzględnych). Informacje na ten temat można znaleźć w internecie. Ja podam kilka najważniejszych faktów.

<!--more-->

## Czym jest baseaddress?

**BaseAddress** (adres bazowy) - adres pierwszego bajtu pliku po wczytaniu go do pamięci przez system Windows.
{: .alert-info} 

W Windows 98/XP adres bazowy jest stały dla aplikacji i może wynosić np. *0x400000*. Problem pojawił się od Windows Vista w górę. Zostało wprowadzone zabezpieczenie **ASLR**. Polega ono na zmienności adresu bazowego. Aplikacja ma inny adres bazowy podczas każdego uruchomienia.


**Adres relatywny** (RVA) - adres w pamięci aplikacji względem *adresu bazowego*.
{: .alert-info} 

Adresy relatywne są najczęściej spotykanymi adresami, pisząc aplikacje przeprowadzające operacje na pamięci musisz się nimi posługiwać. Co za tym idzie, aby odczytać wartość z pamięci musisz posiadać adres relatywny oraz adres bazowy. *Adres = adres bazowy + adres relatywny*.

Adresy relatywne można odczytywać w łatwy sposób np. **za pomocą programu CheatEngine**. Zwróć uwagę, że w momencie gdy chcesz zmienić adres dowolnej wartości, program wyświetla okienko z taką zawartością:

{% include parts/postPicture.html page=page img="ch1" %}

Słowo **winmine.exe** zastępuje adres bazowy. Do tej wartości dodawany jest odpowiedni adres relatywny, który jest już stały. Aby stworzyć program odczytujący wartość czasu na systemie Windows7, musimy odczytać adres bazowy i dodać do niego adres relatywny widoczny wyżej.

Wiemy, że adres dla czasu w Saperze to *1005194*. Ten adres zawiera w sobie adres bazowy + adres relatywny. Adres bazowy wynosi *1000000*. Odejmując otrzymujemy adres bazowy *0005194*.

## Kod programu w C++

Chcę pokazać metodę odczytania **adresu bazowego** w C++. Przyda się to Twórcom trainerów i innych programów korzystających z edycji pamięci.

	#include <iostream>
	#include <windows.h>
	#include <tlhelp32.h>
	#include "psapi.h"
	
	using namespace std;
	
	char* GetExeName (int pid);
	DWORD GetModuleBase(LPSTR lpModuleName, DWORD dwProcessId);
	
	int main ()
	{
	    HWND hSaper = FindWindow("Saper",NULL);
	    DWORD BaseAddr;
	
	    if (hSaper)
	    {
	        DWORD* processID = new DWORD;
	        GetWindowThreadProcessId(hSaper, processID);
	        BaseAddr = GetModuleBase(GetExeName(*processID), *processID);
	    }
	
	    cout << "BaseAddres okna Saper to: " << BaseAddr << endl;
	
	    system ("pause >nul");
	    return 0;
	}
	
	char* GetExeName (int pid)
	{
	    char szProcessName[254];
	    HANDLE hProcess = OpenProcess(PROCESS_QUERY_INFORMATION | PROCESS_VM_READ, FALSE, pid);
	
	    if (NULL != hProcess)
	        GetModuleBaseName(hProcess, NULL, szProcessName, sizeof(szProcessName)/sizeof(char));
	
	    CloseHandle(hProcess);
	    return szProcessName;
	}
	
	DWORD GetModuleBase(LPSTR lpModuleName, DWORD dwProcessId)
	{
	    MODULEENTRY32 lpModuleEntry = {0};
	    HANDLE hSnapShot = CreateToolhelp32Snapshot(TH32CS_SNAPMODULE, dwProcessId);
	
	    if(!hSnapShot)
	        return NULL;
	
	    lpModuleEntry.dwSize = sizeof(lpModuleEntry);
	    BOOL bModule = Module32First(hSnapShot, &lpModuleEntry);
	    while(bModule)
	    {
	        if(!strcmp(lpModuleEntry.szModule, lpModuleName))
	        {
	            CloseHandle(hSnapShot);
	            return (DWORD)lpModuleEntry.modBaseAddr;
	        }
	        bModule = Module32Next(hSnapShot, &lpModuleEntry);
	    }
	    CloseHandle(hSnapShot);
	    return NULL;
	}

Uwaga! Przed kompilacją należy dodać do linkera plik *libpsapi.a*!