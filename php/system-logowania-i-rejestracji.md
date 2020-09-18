---
title: System logowania i rejestracji
category: php
thumbnail: logowanie-php.png
published: 2013-03-12T00:00:00Z
---
**System logowania i rejestracji** jest bardzo przydatną funkcjonalnością na każdej stronie. Umożliwia sprawną identyfikację i zarządzanie użytkownikami. Strona ze **skryptem logowania** nabiera dużej wartości. **Zarejestrowani** użytkownicy mogą się komunikować, tworzą swoją odrębną społeczność. Często zwiększa to zainteresowania stroną i ruch na niej. Konta mogą mieć dużo funkcjonalności takich jak dodawanie zdjęć, system komentarzy czy ankiety.

<!--more-->

## Baza danych do przechowywania użytkowników

Informacje dotyczące **kont użytkowników** najlepiej przechowywać w bazie **MySQL.** Na prawdę &#8211; nie warto bawić się w pliki tekstowe pisząc tak rozbudowane skrypty. Tabela będzie przechowywać podstawowe informacje takie jak *id*, *login*, *hasło*, *email*, *data rejestracji*, *data logowania* oraz *adres IP*.

	CREATE TABLE IF NOT EXISTS `uzytkownicy` (
	    `id` int(10) NOT NULL AUTO_INCREMENT,
	    `login` varchar(255) NOT NULL,
	    `haslo` varchar(255) NOT NULL,
	    `email` varchar(255) NOT NULL,
	    `rejestracja` int(10) NOT NULL,
	    `logowanie` int(10) NOT NULL,
	    `ip` varchar(15) NOT NULL,
	    PRIMARY KEY (`id`)
	) ENGINE=MyISAM DEFAULT CHARSET=utf8 AUTO_INCREMENT=1;

Posiadając bazę danych proponuję od razu **dodać do niej pierwszego użytkownika** &#8211; administratora. Dzięki temu od razu będziemy widzieć efekty pracy. Dane logowania to:

	Login: admin
	Hasło: haslo

Hasło będziemy przechowywać w bazie danych **w postaci zakodowanego ciągu MD5**. Dzięki temu w razie włamania do serwisu, włamywacz nie będzie posiadał listy haseł, a jedynie ich zakodowane odpowiedniki. Podczas logowania hasło wpisane przez użytkownika także jest od razu kodowane i porównywane z wartością z bazy.

**Uwaga!** Warto wspomnieć, że w obecnych czasach kodowanie MD5 jest bardzo łatwe do złamania (mimo że jest to kodowanie jednostronne). Z tego powodu, pisząc bezpieczny skrypt należy skorzystać z innej funkcji szyfrującej hasła niż **MD5**. Dobrym i często stosowanym rozwiązaniem jest także **użycie &#8222;soli&#8221;** (z ang. salt). Polega to na szyfrowaniu haseł poddanych jakiejś transformacji tekstu.

Dodajmy konto administratora do bazy danych:

	INSERT INTO `uzytkownicy` (`id`, `login`, `haslo`, `email`, `rejestracja`, `logowanie`, `ip`)
	    VALUES (1, 'admin', '207023ccb44feb4d7dadca005ce29a64', 'admin@admin.pl', '1357063200', '1357063200', '127.0.0.1');

## Formularz HTML rejestracji użytkownika

**Formularz rejestracji ma za zadanie pobrać od użytkownika niezbędne dane** wymagane do utworzenia konta. Dane zostaną wysłane do **skryptu PHP** metodą *POST* po kliknięciu w przycisk. Formularz znajduje się w pliku *rejestracja.php*.

	<form method="POST" action="rejestracja.php">
	    <b>Login:</b> <input type="text" name="login"><br><br>
	    <b>Hasło:</b> <input type="password" name="haslo1"><br>
	    <b>Powtórz hasło:</b> <input type="password" name="haslo2"><br><br>
	    <b>Email:</b> <input type="text" name="email"><br>
	    <input type="submit" value="Utwórz konto" name="rejestruj">
	</form>

## Skrypt rejestracji PHP

Skrypt dopisujmy do tego samego pliku co formularz.  Przed utworzeniem konta, **w skrypcie należy wykonać poniższe kroki**:

1. Połączyć się z bazą MySQL
2. Pobrać i przefiltrować dane formularza z tablicy super globalnej *$*POST*.
3. Sprawdzić czy login nie jest zajęty.
4. Zapisać w bazie danych.
5. Zamknąć połączenie MySQL.

Zawsze przed zapisem jakichkolwiek danych do bazy danych, **należy odpowiednio przygotować zapisywaną zmienną**. Inaczej skrypt będzie źle zabezpieczony. Ataki **SQL Injection** to ciągle jedne  z najczęstszych ataków. Ja utworzyłem specjalną funkcję, która zabezpiecza dane pochodzące od użytkownika:

	<?php
	    mysql_connect("localhost","admin","haslo");
	    mysql_select_db("baza");
	    
	    function filtruj($zmienna)
	    {
	        if(get_magic_quotes_gpc())
	            $zmienna = stripslashes($zmienna); // usuwamy slashe
	        
	        // usuwamy spacje, tagi html oraz niebezpieczne znaki
	        return mysql_real_escape_string(htmlspecialchars(trim($zmienna)));
	    }
	    
	    if (isset($_POST['rejestruj']))
	    {
	        $login = filtruj($_POST['login']);
	        $haslo1 = filtruj($_POST['haslo1']);
	        $haslo2 = filtruj($_POST['haslo2']);
	        $email = filtruj($_POST['email']);
	        $ip = filtruj($_SERVER['REMOTE_ADDR']);
	        
	        // sprawdzamy czy login nie jest już w bazie
	        if (mysql_num_rows(mysql_query("SELECT login FROM uzytkownicy WHERE login = '".$login."';")) == 0)
	        {
	            if ($haslo1 == $haslo2) // sprawdzamy czy hasła takie same
	            {
	                mysql_query("INSERT INTO `uzytkownicy` (`login`, `haslo`, `email`, `rejestracja`, `logowanie`, `ip`)
	                    VALUES ('".$login."', '".md5($haslo1)."', '".$email."', '".time()."', '".time()."', '".$ip."');");
	                
	                echo "Konto zostało utworzone!";
	            }
	            else echo "Hasła nie są takie same";
	        }
	        else echo "Podany login jest już zajęty.";
	    }
	?>

## Formularz HTML logowania użytkownika

Mając gotową rejestrację i strukturę bazy danych, **można wziąć się za skrypt logowania**. Będzie w nim wymagane użycie **sesji**. Sesje startuje się na samym początku dokumentu, jeszcze przed kodem HTML. Podobnie jak przy rejestracji, dane są przesyłane do bazy metodą *POST*. Warto dodać też **połączenie z bazą danych** na górze dokumentu, tam gdzie startujemy sesję. Nazwa pliku to *logowanie.php*.

	<?php
	    session_start();
	    mysql_connect("localhost","admin","haslo");
	    mysql_select_db("baza");
	?>
	
	<form method="POST" action="logowanie.php">
	    <b>Login:</b> <input type="text" name="login"><br>
	    <b>Hasło:</b> <input type="password" name="haslo"><br>
	    <input type="submit" value="Zaloguj" name="loguj">
	</form>

**Skrypt PHP** odpowiedzialny za logowanie umieścimy w tym samym pliku co formularz. **Logując się filtrujemy dane**. Następnie sprawdzamy czy określony login i hasło występują w bazie, jeżeli tak &#8211; logujemy użytkownika. W momencie logowania należy uaktualnić **informacje o użytkowniku** czyli datę logowania oraz aktualny adres IP.

	<?php
	    function filtruj($zmienna)
	    {
	        if(get_magic_quotes_gpc())
	            $zmienna = stripslashes($zmienna); // usuwamy slashe
	        
	        // usuwamy spacje, tagi html oraz niebezpieczne znaki
	        return mysql_real_escape_string(htmlspecialchars(trim($zmienna)));
	    }
	    
	    if (isset($_POST['loguj']))
	    {
	        $login = filtruj($_POST['login']);
	        $haslo = filtruj($_POST['haslo']);
	        $ip = filtruj($_SERVER['REMOTE_ADDR']);
	        
	        // sprawdzamy czy login i hasło są dobre
	        if (mysql_num_rows(mysql_query("SELECT login, haslo FROM uzytkownicy WHERE login = '".$login."' AND haslo = '".md5($haslo)."';")) > 0)
	        {
	            // uaktualniamy date logowania oraz ip
	            mysql_query("UPDATE `uzytkownicy` SET (`logowanie` = '".time().", `ip` = '".$ip."'') WHERE login = '".$login."';");
	            
	            $_SESSION['zalogowany'] = true;
	            $_SESSION['login'] = $login;
	            
	            // zalogowany
	        }
	        else echo "Wpisano złe dane.";
	    }
	?>

## Uwagi na koniec

Skrypt jest okrojony. Nie ma sensu ubierać go w bajery ponieważ jest on napisany do **celów naukowych**. Starałem się napisać jak najkrótszy kod, który będzie w stanie rejestrować/logować oraz który będzie miał **jakiekolwiek zabezpieczenia**.

**W internecie można znaleźć wiele długich gotowców z wieloma funkcjami**, ale nie tędy droga. 20 stron a4 przeplatanych kodem HTML trudno jest zrozumieć i przyswoić, a kopiować takich rzeczy nie ma sensu &#8211; jeżeli chcesz kopiować to **przerzuć się na gotowy system CMS**.

Jeżeli chcesz wykorzystać ten skrypt u siebie musisz go ulepszyć. Skrypty PHP trzyma się w osobnych plikach a formularze w osobnych. To samo dotyczy obsługi połączenia z bazą danych. Dodatkowo **trzeba bezwzględnie sprawdzać długość wprowadzanych danych**! Zabezpieczyć je zarówno od strony HTML ograniczając długość pół formularzy, ale przede wszystkim od strony PHP (funkcja *strlen*).

Cały skrypt jest dostępny do pobrania na samym dole strony. Jest w nim kompletna rejestracja i logowanie. Po zalogowaniu wyświetla się powitanie i możliwość wylogowania. W tym artykule fragmentu kodu odpowiedzialnego za moment **po zalogowaniu i wylogowanie** nie umieściłem, ponieważ niepotrzebnie by wszystko skomplikował (a jest bardzo prosty). Podczas testowania skryptu zacznij od utworzenia bazy danych.
