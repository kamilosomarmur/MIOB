Na początku korzystałem z Claude Opus 4.6 (Thinking) - Default, ale wyczerpałem limit więc w połowie etapu drugiego przełączyłem się na Gemini 3.8 Flash (High) - Default
Link do repozytorium: https://github.com/kamilosomarmur/MIOB.git

1. Jaki wynik zlecono, jakie zmiany były dozwolone i o jakie informacje do odbioru poproszono agenta?
2. Jak powstał raport? Wskaż wejście, polecenie, narzędzie, środowisko wykonania i wynik procesu. Dodaj link do końcowego raportu i wyjaśnij jego liczebności.
3. Który fragment dokumentacji lub kodu wyjaśniłeś? Podaj plik i miejsce, własne wyjaśnienie oraz mały przypadek: przewidywanie, sposób sprawdzenia i wniosek.
4. Jak uruchomiono gotowy test, jaki był wynik i czego dotyczyło sprawdzenie?
5. Jaką etykietę zmieniono w etapie 2? Jakiej zmiany liczebności oczekiwałeś, jakie operacje wybrałeś i dlaczego? Porównaj wynik przed zmianą i po nowym wykonaniu.

1. Oczekiwano wyjaśnienia działania skryptu describe_data.py, instrukcję jego uruchomienia, przedstawienie jednej wiadomości z pliku exercise.csv, wyjaśnienie pól w tym pliku oraz wygenerowania raportu dla pliku excercise.csv i zapisania go w exercise-summary.json.
W pierwszym etapie nie były dozwolone żadne zmiany, w drugim jedyną dozwoloną zmianą była modyfikacja jednej etykiety z "ham" na "spam".
Agenta poproszono o wskazanie miejsca zapisu raportu, odczytanie jego zawartości, wskazanie użytych danych, wskazanie użytego polecenia i przedstawienie wyniku testu.
2. Wejście to plik exercise.csv. 
Polecenie: python describe_data.py --data data/exercise.csv --out results/exercise-summary.json
Narzędzie: Python, środowisko udostępnione przez Antigravity
Wynik procesu: wygenerowanie raportu do pliku exercise-summary.json
Link: https://github.com/kamilosomarmur/MIOB/blob/main/lab1/exercise-summary.json
Wyjaśnienie liczebności: skrypt zliczył wystąpienia ham i spam w pliku csv. Ham: 5. Spam: 3.
3. Wyjaśniłem linię 19. counts = {"ham": 0, "spam": 0} w skrypcie describe_data.py. 
Własne wyjaśnienie: Linia ta odpowiada za zliczanie wystąpień ham i spam w pliku csv.
Mały przypadek: Jeżeli zmienimy "ham" na np. "tam" to skrypt po próbie uruchomienia na pliku csv zwróci błąd, ponieważ skrypt napotka "ham" i nie znajdzie go w counts i rzuci błąd: raise ValueError(f"Record {number}: label must be ham or spam."). Agent odpowiedział zgodnie z moimi oczekiwaniami po zadaniu mu tego pytania.
4. Zleciłem agentowi wykonanie testu poleceniem wskazanym w pliku README, polecenie to: python -m unittest discover -s tests -v.
Test przebiegł pomyślnie i zwrócił wynik: test_cli_writes_expected_counts ... ok
Ran 1 test in 0.503s — OK
Test korzystał z pliku messages.csv, uruchamia na nim skrypt describe_data.py i sprawdza czy w pliku znajdują się dokładnie 3 wiadomości: 2 oznaczone jako ham i 1 oznaczona jako spam.
5. Zmieniono etykietę dla exercise-001 z ham na spam.
Oczekiwałem, że po zmianie tej etykiety raport będzie zawierał 4 wystąpienia ham i 4 wystąpienia spam.
Zleciłem agentowi ponowne wykonanie skryptu, aby wygenerować nowy raport. Następnie poprosiłem o odczytanie wygenerowanego raportu, aby potwierdzić moje oczekiwania. Na koniec zleciłem agentowi porównanie obu raportów.
Wynik porównanie potwierdził moje oczekiwania, po zmianie etykiety na jednej z wiadomości raport zawiera 4 ham i 4 spam.