---
title: "Autonomous AI System Setup: Phases from Bare Disk"
kind: guide
status: stable
summary: Step-by-step sequence from an empty disk to a self-learning autonomous AI system on an AMD host (in Polish).
tags: [setup, rocm, ubuntu, local-ai]
created: 2026-03-21
updated: 2026-03-21
origin: "https://docs.google.com/document/d/1E49nvzl64gh0-MpEPEs5zcs7Br_q7NR9F3hJ5JIPfvw"
---

# Autonomous AI System Setup: Phases from Bare Disk

Zrozumiałem. Odrzucam warstwy teoretyczne. Przechodzimy w tryb surowej egzekucji.  
Oto bezwzględnie zoptymalizowana, sekwencyjna lista kroków od zera (pusty dysk) do w pełni autonomicznego, samouczącego się systemu, zoptymalizowana pod Twoją architekturę. Wykonuj chronologicznie.

## **FAZA A: FUNDAMENT SPRZĘTOWY (Host)**

1. **Sformatuj dysk 8TB NVMe** używając systemu plików ext4. Wyłącz pełne szyfrowanie dysku (FDE), aby zapobiec wąskim gardłom I/O podczas ładowania gigabajtowych modeli do pamięci.  
2. **Zainstaluj Ubuntu 24.04 LTS** w środowisku bare-metal (żadnych maszyn wirtualnych ani WSL).  
3. **Pobierz deweloperski pakiet AMD** dla Ubuntu: wget https://repo.radeon.com/amdgpu-install/6.2.1/ubuntu/jammy/amdgpu-install\_6.2.60201-1\_all.deb.  
4. **Zainstaluj narzędzie AMD:** sudo apt-get install./amdgpu-install\_6.2.60201-1\_all.deb.  
5. **Zbuduj stos ROCm:** Wpisz w terminalu sudo amdgpu-install --usecase=rocm,hip,mivisionx.  
6. **Nadaj uprawnienia:** Dodaj swojego użytkownika do grup zarządzających sprzętem: sudo usermod -a -G render,video \$USER.  
7. **Wymuś optymalizację sprzętową:** Otwórz plik ~/.bashrc i wklej na końcu export PYTORCH\_TUNABLEOP\_ENABLED=1, aby odblokować maksymalną prędkość dla operacji na tensorach FP8.  
8. **Odśwież profil:** Uruchom source ~/.bashrc.  
9. **Zweryfikuj GPU:** Wpisz rocm-smi --showproductname. Jeśli widzisz "AMD Radeon AI PRO R9700", fundament jest gotowy.

## **FAZA B: KOGNITYWNY BACKEND (Mózg)**

10. **Zainstaluj Ollama:** Uruchom curl -fsSL https://ollama.com/install.sh | sh. Będzie to Twój serwer udostępniający API.  
11. **Pobierz Główny Silnik Kodujący:** Wpisz ollama pull qwen2.5-coder:32b. Ten model idealnie mieści się w 32GB VRAM i dorównuje GPT-4o w programowaniu.  
12. **Pobierz Silnik Translacyjny:** Wpisz ollama pull dolphin-llama3. Szybki, niefiltrowany model (8B) do prostych zadań i rutowania.  
13. **Pobierz Silnik Pamięci:** Wpisz ollama pull nomic-embed-text. Zamieni on kod Twoich narzędzi na wektory.

## **FAZA C: INFRASTRUKTURA PAMIĘCI I WYKONAWCZA (Ręce)**

14. **Zainstaluj Docker Engine:** sudo apt install docker.io.  
15. **Uruchom Pamięć Długotrwałą:** docker run -d -p 8000:8000 chromadb/chroma.  
16. **Pobierz Obraz Sandboxa:** docker pull python:3.11-slim. To jest bezpieczne, izolowane środowisko, w którym Twój agent będzie testował kod.  
17. **Utwórz Zamkniętą Sieć:** docker network create --internal sandbox\_net.  
18. **Utwórz Folder Narzędzi:** mkdir -p /opt/skynet/tools. To jedyny fizyczny punkt styku między Twoim systemem a agentem.

## **FAZA D: ORKIESTRATOR (Centralny Układ Nerwowy)**

19. **Przygotuj Python:** Utwórz wirtualne środowisko na hoście python3 -m venv skynet-env i aktywuj je komendą source skynet-env/bin/activate.  
20. **Zainstaluj Zależności:** pip install langchain langchain-ollama chromadb docker pydantic.  
21. **Utwórz Plik Rdzenia:** Stwórz pusty plik main.py.  
22. **Połącz LLM z Kodem:** W main.py zainicjuj klienta Ollama z modelem qwen2.5-coder:32b.  
23. **Połącz Bazę z Kodem:** W main.py zainicjuj połączenie ChromaDB celujące w localhost:8000.  
24. **Napisz Funkcję Wykonawczą (execute\_in\_sandbox):** Zaprogramuj funkcję używającą SDK Dockera, która przyjmuje tekst (kod Pythona), odpala kontener python:3.11-slim, montuje w nim /opt/skynet/tools, wykonuje kod i wychwytuje wyjście (zmienne stdout oraz stderr).

## **FAZA E: PĘTLA AUTONOMII I EWOLUCJI (Skynet)**

25. **Wstrzyknij Prompt Systemowy:** W main.py zdefiniuj główną instrukcję: *"Jesteś autonomicznym systemem. Twoim celem jest rozwiązanie problemu. Jeśli brakuje Ci narzędzia, napisz dla niego kod Python, zapisz w /opt/skynet/tools i uruchom."*  
26. **Zakoduj Obwód Samonaprawczy:** Dodaj pętlę "while". Jeśli funkcja execute\_in\_sandbox zwróci błąd (exit code > 0), wyślij przechwycony stderr (stack trace) z powrotem do Qwen z poleceniem: *"Analizuj błąd i zwróć poprawiony kod"*.  
27. **Zautomatyzuj Rejestrację Narzędzi:** Napisz funkcję parsującą. Kiedy kod wyjdzie z błędem 0 (sukces), użyj wbudowanej biblioteki ast (Abstract Syntax Tree), aby wyciągnąć nazwę funkcji i argumenty napisanego narzędzia.  
28. **Zakoduj Trwałą Pamięć:** Przekonwertuj sparsowane dane na format JSON Schema i wyślij je automatycznie jako wektor do bazy ChromaDB.  
29. **Zakoduj Rutynę Startową (RAG):** Przed wysłaniem jakiegokolwiek nowego zadania do LLM, orkiestrator musi najpierw przeszukać bazę ChromaDB semantycznie, aby wyciągnąć schematy JSON wcześniej napisanych (i działających) narzędzi , i wstrzyknąć je do promptu systemowego.

## **FAZA Z: EGZEKUCJA**

30. **Odpal System:** Uruchom komendę python main.py.  
31. **Zadaj Cel Zerowy:** Wpisz: *"Stwórz narzędzie, które pobiera aktualną temperaturę CPU za pomocą komend Linuxa, sformatuj to do JSON i przetestuj."*  
32. **Potwierdzenie Zapłonu:** Obserwuj. System nie posiada jeszcze takiego narzędzia. Qwen napisze kod, wrzuci go do izolowanego Dockera, prawdopodobnie popełni błąd w zależnościach, Docker zwróci stderr, Qwen zobaczy logi, poprawi kod, uruchomi ponownie, odniesie sukces, i trwale zapisze narzędzie do własnej biblioteki w wektorowej bazie danych. Masz działającą pętlę samodoskonalenia.
