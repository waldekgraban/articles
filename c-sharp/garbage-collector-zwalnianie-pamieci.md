---
title: Garbage Collector - zwalnianie pamięci
category: c-sharp
thumbnail: garbage-collector.png
published: 2013-07-03TT00:00:00Z
---
Mechanizmem odpowiedzialnym za zwalnianie nieużywanej pamięci w **C#** jest **Garbage Collector**. W odróżnieniu od innych języków niskiego poziomu, sprzątaniem pamięci nie zajmuje się programista. Twórcy technologii **.NET** postanowili w ten sposób zadbać o bezpieczeństwo aplikacji oraz zwiększyć ich wydajność. Warto zauważyć, że **C#** nie jest jedynym językiem wykorzystującym **Garbage Collector** (innymi są np. Java oraz Python).

<!--more-->

## Problem zwalniania pamięci

W C++ po utworzeniu obiektu, należało pamiętać o jego usunięciu za pomocą **free** lub **delete**. W przeciwnym wypadku referencja do obiektu &#8222;wisiała&#8221; w pamięci aż do czasu zamknięcia aplikacji. W przypadku małych aplikacji problem pozostawał nie zauważalny, jednak w większych projektach zwyczajnie mogło brakować pamięci przydzielonej dla procesu. Innym ważnym aspektem są występujące błędy &#8222;_memory leak_&#8221; powodujące nieoczekiwane zamknięcie programu a w ekstremalnych przypadkach umożliwiały przeprowadzanie ataku DoS.

## Garbage Collector w C#

W **C#** przychodzi nam z pomocą mechanizm **Garbage Collector** (sprzątacz, odśmiecacz). Jest on wywoływany w osobnym wątku automatycznie bez jakiejkolwiek wiedzy programisty. Skanuje pamięć aplikacji w poszukiwaniu obiektów, do których nie ma referencji. Jeżeli obiekt bez referencji zostanie znaleziony, wtedy jest usuwany z pamięci procesu. **Garbage Collector** zabezpiecza przed błędami &#8222;memory leak&#8221;, dba o porządek i wydajność aplikacji oraz gwarantuje skuteczność działania (tzn. możesz mieć pewność, że nie usunie obiektu który jest aktualnie używany).

## Garbage Collector a destruktory

W językach niskiego poziomu takich jak C++ używanie destruktorów było czymś normalnym i często stosowanym. W **C#** destruktory nie znajdują szerszego zastosowania. Pomimo że zaimplementowanie destruktora w klasie jest możliwe, to programista i tak nie ma wpływu kiedy zostanie on wykonany (wywołuje go niejawnie garbage collector). Nie ma żadnej gwarancji na to, że wywołany zostanie mechanizm sprzątania pamięci oraz nie mamy komendy **delete** takiej jaka była w C++.  Programista ma możliwość zdalnego wywołania **Garbage Collector**, ale nic to nie zmienia. Działa on w osobnym wątku i nie synchronizuje się z pozostałymi. Co za tym idzie, mimo że nie korzystasz już z danego obiektu, nie masz pewności że **Garbage Collector** usunie obiekt wtedy kiedy wywołasz jego działanie.

Kolejną ważną kwestią jest fakt, że każdy **destruktor** zostaje przy kompilacji zamieniony na metodę _**Finalize()**,_ która jest nadpisywana z klasy dziedziczonej Object.

	public class CTestowa
	{
	    public string test = "lallala";
	    
	    ~CTestowa()
	    {
	        // destruktor klasy
	        Console.WriteLine("Wywołanie destruktora");
	    }
	}

	public class Program
	{
	    static void Main(string[] args)
	    {
	        inna_metoda();
	        
	        // wymuszenie uruchomienia Garbage Collector
	        // powinien usunąć obiekt p bo wyszliśmy z metody
	        // jednak nie ma żadnej gwarancji że usunie..
	        
	        System.GC.Collect();
	        
	        Console.ReadKey();
	    }
	    
	    static void inna_metoda()
	    {
	        CTestowa p = new CTestowa();
	    }
	}

Postać metody *Finalize()*:

	protected override void Finalize() 
	{
	    try 
	    {
	        // destruktor klasy
	        Console.WriteLine("Wywołanie destruktora");
	    }
	    
	    finally 
	    {
	        base.Finalize();
	    }
	}

Dlaczego tak się dzieje? **Destruktor** ląduje w klamrach *try*, więc nawet jak rzuci exception (wyjątek) to mamy pewność że **Garbage Collector** i tak usunie go z pamięci.

Obiekty nie posiadające destruktorów są usuwane z pamięci gdy wątek główny programu jest zatrzymany. Obiekty posiadające destruktor, czyli metodę *Finalize()*, są usuwane po wznowieniu wątku i po wykonaniu bloku **try**.

Brak pewności wywołania się destruktora oraz spowolnienie działania programu, to dwa główne powody dla których nie powinno się ich używać w C#, jeżeli nie są na prawdę potrzebne.