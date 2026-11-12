Na początku korzystałem z Claude Opus 4.6 (Thinking) - Default, ale wyczerpałem limit więc w połowie etapu drugiego przełączyłem się na Gemini 3.8 Flash (High) - Default
Link do repozytorium: https://github.com/kamilosomarmur/MIOB.git

1. Cel i zakres. Co prototyp otrzymuje, co zwraca i do czego ma służyć?
2. Wybrane podejście i uzasadnienie. Opisz potrzebne przygotowanie danych, metodę reprezentacji tekstu i rodzaj modelu. Wyjaśnij, dlaczego wybrano to podejście. Porównaj wybrany model z jedną alternatywą. Przy najważniejszych wyborach wskaż obserwację danych, informację z dokumentacji lub porównanie, które przemówiło za decyzją.
3. Kroki planu i ich zależności. Prześledź drogę od dostarczonych wiadomości do oceny odpowiedzi modelu. Wskaż kolejność czynności, potrzebne dane i wyniki, które trzeba zachować. Wyjaśnij, co powstaje w każdym kroku i do czego jest potrzebne dalej.
4. Planowana ocena. Wskaż dane do oceny, sposób porównania przewidywań z etykietami oraz wybrane miary. Wyjaśnij, co miary pokażą o przeoczonym spamie i prawidłowych wiadomościach uznanych za spam.
5. Niewiadome i możliwe korekty. Wskaż, które założenia wymagają jeszcze sprawdzenia. Opisz, jak je sprawdzisz i co należałoby zmienić w planie, gdyby okazały się błędne.
1. Prototyp jest klasyfikatorem spamu, otrzymuje dane z pliku csv i zwraca etykietę spam lub ham.
2. Przygotowanie danych: należało wyczyścić wiadomości tzn. zamienić duże litery na małe, usunąć urle, adresy email, znaki specjalne itp. Wybrano metodę reprezentacji TF-IDF i model Multinomial Naive Bayes - dobrze sprawdza się na małych zbiorach danych. Porównując Naive Bayes vs Regresja Logistyczna, Bayes okazał się lepszy we wszystkich metrykach w poniższym zbiorze.
| Cecha | Naive Bayes | Regresja Logistyczna |
|-------|:-----------:|:--------------------:|
| **Accuracy** | **90.0%** | 86.7% |
| **Precision** | **100.0%** | 100.0% |
| **Recall** | **70.0%** | 60.0% |
| **F1-score** | **82.4%** | 75.0% |
3. 	1. Czyszczenie tekstu - wejście plik csv, wynik lista słów po normalizacji
	2. TF-IDF - wejście plik z kroku pierwszego po normalizacji, wynik słownik wag IDF na danych treningowych
	3. Uczenie modelu Naive Bayes - wejście słownik wag + etykiety, wyjsce wytrenowany modl
	4. Klasyfikacja wiadomości z pliku validation.csv wejście słownik wag, wynik przewidywane etykiety dla 		wiadomości z pliku csv.
	5. Ocena jakości - wejście przewidywania modelu, wynik macierz metryk
4. Dane do oceny to plik validation.csv, dla każdej wiadomości porównujemy przewidywaną etykietę z tą prawdziwą.
| Miara | Co pokazuje? |
|-------|-------------|
| **Accuracy** | Ogólny procent poprawnych klasyfikacji |
| **Precision (spam)** | Spośród wiadomości oznaczonych jako spam — ile faktycznie jest spamem? Niska precision → ważna poczta trafia do spamu |
| **Recall (spam)** | Spośród prawdziwego spamu — ile model wykrył? Niski recall → spam prześlizguje się do skrzynki |
| **F1-score** | Średnia harmoniczna precision i recall — równowaga między nimi |
5. | Niewiadoma | Jak sprawdzić? | Co zmienić, jeśli wynik jest niekorzystny? |
|-----------|---------------|-------------------------------------------|
| **Podobne wiadomości między train i validation** (np. wspólne wątki, ten sam nadawca) | Porównać tematy i nadawców w obu zbiorach | Jeśli występują — wynik na validation może być zawyżony; odnotować jako ograniczenie oceny |
| **Wpływ usuwania stop-words i stemmingu** | Porównać wyniki modelu z tymi krokami i bez nich | Pominąć krok, który pogarsza wyniki |
| **Czy 180 wiadomości treningowych wystarczy?** | Sprawdzić, czy accuracy na train jest dużo wyższa niż na validation (sygnał przeuczenia) | Mała próba: accuracy na train = 100%, na val = 90% — pewien stopień przeuczenia istnieje; rozważyć silniejsze wygładzanie (parametr alpha w NB) |
| **Obcojęzyczny spam** | Sprawdzić, ile wiadomości spamowych jest w językach innych niż angielski | Rozszerzyć listę stop-words o inne języki lub dodać cechę „język wiadomości" |
| **Phishing imitujący ważną pocztę** | Przejrzeć przeoczone spamy i zidentyfikować wzorce | Dodać cechy strukturalne (np. obecność linków, wielkie litery w temacie) |
| **Historyczność danych (2003 r.)** | Nie da się zweryfikować na tym zbiorze | Odnotować jako ograniczenie — współczesny spam (np. spear-phishing, AI-generated) wygląda inaczej i wymagałby nowszych danych |
| **Proporcje klas (2:1) a rzeczywistość** | Sprawdzić, czy model faworyzuje klasę większościową | Proporcje w zbiorze nie odzwierciedlają rzeczywistego udziału spamu; przy wdrożeniu w realnym scenariuszu wyniki mogą się różnić |
