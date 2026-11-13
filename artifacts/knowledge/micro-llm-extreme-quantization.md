---
title: "Micro LLMs: Optimization and Extreme Quantization"
kind: knowledge
status: stable
summary: Practical take on running modern models on weak hardware through extreme quantization and optimization (in Polish).
tags: [llm, quantization, small-models, edge]
created: 2026-03-26
updated: 2026-03-26
origin: "https://docs.google.com/document/d/1hGxyMjn3U7aCZ5QdxfTC9eBwcSJPvhRVn_iS8jdlFoI"
---

# **Mikro LLMy — Ultimate Knowledge Compendium**

To nie jest miejsce dla miękkiej, korporacyjnej gadki i teoretyzowania o wirtualnych chmurach za miliony dolarów. To ostateczny, brutalny zrzut wiedzy o tym, jak wziąć najnowocześniejsze, potężne modele sztucznej inteligencji, odciąć z nich gigabajty tłuszczu i zmusić do perfekcyjnego działania na sprzęcie o mocy obliczeniowej starego tostera lub zardzewiałego laptopa. Skupiamy się na modelach sub-8B, ekstremalnej kwantyzacji, magii inżynierii pamięci i rzeźnickiej optymalizacji, która pozwala uruchamiać w zaledwie kilku gigabajtach RAM-u to, co normalnie wywaliłoby błąd Out Of Memory na dedykowanych serwerach.

**Compiled on:** March 26, 2026

---

**Spis Treści**

1. ETAP 1: Sekcja Anatomiczna (Jak ciąć, żeby pacjent przeżył)  
   1.1. Rzeźnia na wagach: Formaty i metody kwantyzacji (GGUF, AWQ, EXL2, GPTQ)  
   1.2. Ekstremalna kompresja: Binarne i ternarne sieci (BitNet 1-bit LLMs)  
   1.3. Amputacja i klonowanie umysłu: Pruning i Knowledge Distillation  
   1.4. Najmniejszy logicznie myślący mikrus na świecie  
2. ETAP 2: Wielka Jebitna Tabela (Baza Danych Modeli)  
   2.1. Przegląd ekosystemu Sub-8B w 2026 roku  
   2.2. Baza Danych Modeli (Tabela)  
3. ETAP 3: Narzędzia, Lifehacki i Czarna Magia (Ekosystem)  
   3.1. Silniki inferencyjne na śmieciowy sprzęt (llama.cpp, Ollama, vLLM, AirLLM)  
   3.2. Trening dla biedaków: LoRA, QLoRA i framework Unsloth  
   3.3. Lifehacki na optymalizację VRAM/RAM na granicy możliwości

## ---

**1. ETAP 1: Sekcja Anatomiczna (Jak ciąć, żeby pacjent przeżył)**

Modele językowe z definicji rodzą się obrzydliwie grube – trenowane w formacie FP16 (Half Precision) lub bfloat16, pożerają VRAM z prędkością, która zawiesza każdy konsumencki sprzęt.1 Aby zmieścić logicznie myślący byt w naszym absolutnym limicie 8GB (a najlepiej 4GB lub 2GB), musimy wejść do jego wnętrza z rzeźnickim tasakiem, modyfikując same fundamenty reprezentacji liczb zmiennoprzecinkowych.

## **1.1. Rzeźnia na wagach: Formaty i metody kwantyzacji (GGUF, AWQ, EXL2, GPTQ)**

Kwantyzacja to proces brutalnej redukcji precyzji numerycznej wag modelu, na przykład z 16-bitowych liczb zmiennoprzecinkowych (FP16) do 4-bitowych lub 8-bitowych liczb całkowitych (INT4/INT8), co drastycznie zmniejsza zajętość pamięci i odciąża przepustowość (memory bandwidth).1

**1. GPTQ (Post-Training Quantization for GPT)** Klasyczna i niezwykle popularna metoda kompresji optymalizowana pod kątem akceleratorów graficznych (GPU), pozwalająca ściąć modele do 4 lub nawet 3 bitów na wagę. Algorytm ten wykorzystuje informacje drugiego rzędu (odwrotność macierzy Hessiana), aby kompensować błąd kwantyzacji w locie, waga po wadze.4 Działa to tak, że gdy jedna waga traci precyzję, algorytm modyfikuje sąsiednie, jeszcze nie skwantyzowane wagi, aby zminimalizować całkowity błąd wyjścia warstwy. **Notable Insights & Ciekawostki:** GPTQ było absolutnym pionierem, który udowodnił, że 4-bitowe LLMy mają sens i nie stają się z miejsca bezużytecznymi generatorami losowych znaków.5 Bez wsparcia zoptymalizowanych kerneli (jak Marlin), GPTQ potrafi być jednak potwornie wolne w fazie dekodowania.4

**2. AWQ (Activation-aware Weight Quantization)** Znacznie bardziej inteligentny kuzyn GPTQ, który zakłada, że nie wszystkie wagi są sobie równe. AWQ analizuje aktywacje modelu podczas przetwarzania małego zbioru kalibracyjnego i identyfikuje te wagi, które są absolutnie krytyczne dla zachowania poprawnego rozumowania (tzw. salient weights), utrzymując je w wyższej precyzji (np. FP16), podczas gdy reszta jest brutalnie ścinana do 4 bitów.4 Równanie skalowania w AWQ zakłada ochronę rzędu 1% najważniejszych parametrów. **Notable Insights & Ciekawostki:** AWQ w połączeniu z kernelem Marlin w silniku vLLM wykręca w 2026 roku absurdalne wręcz prędkości (np. 741 tok/s na H200 dla 32B), zachowując znacznie lepszą spójność kodu i logiki niż chamskie cięcie GPTQ.4 Jest to SOTA dla kart Nvidii.

**3. EXL2 (ExLlamaV2)** Format stworzony przez absolutnego magika optymalizacji, zaprojektowany specjalnie pod maksymalną wydajność na GPU przy użyciu zmiennego bitrate'u (Variable Bitrate Quantization). Pozwala on na zdefiniowanie średniej precyzji np. na poziomie 4.25 bpw (bits per weight) lub 3.0 bpw, co umożliwia wciśnięcie modelu z chirurgiczną precyzją pod sam "korek" dostępnego VRAM-u.7 **Notable Insights & Ciekawostki:** Format EXL2 potrafi generować tokeny nawet 3 do 4 razy szybciej niż standardowe ładowanie przez GGUF na tej samej karcie graficznej, pod warunkiem, że model w całości mieści się w VRAM.9 Jego przewaga leży w minimalizacji *perplexity* dla specyficznych limitów pamięciowych.10

**4. GGUF (GPT-Generated Unified Format)** Niepodważalny król lokalnej, "biednej" inferencji, stworzony przez zespół odpowiedzialny za projekt llama.cpp. GGUF nie jest samą metodą matematyczną, a zunifikowanym formatem pliku, który w środku przechowuje wagi skwantyzowane algorytmami GGML (k-quants lub najnowsze i-quants typu IQ3\_S).1 Format ten błyszczy dzięki obsłudze pamięci mapowanej z dysku (mmap) oraz możliwości płynnego dzielenia obliczeń pomiędzy CPU a GPU.9 **Notable Insights & Ciekawostki:** Kwantyzacja Q4\_K\_M (4 bity) to złoty standard dla hobbystów w 2026 roku, zachowujący ok. 92% oryginalnej jakości modelu przy redukcji rozmiaru o 75%.3 Dzięki GGUF możesz wczytać model 8B na starym Macbooku lub zintegrowanej karcie graficznej i wciąż dostać sensowne odpowiedzi.9

*(Schemat architektury Transformera - każda warstwa uwag \[Attention\] i sieci jednokierunkowej \[FFN\] jest celem agresywnej kwantyzacji wag w formatach AWQ i GPTQ).*

## **1.2. Ekstremalna kompresja: Binarne i ternarne sieci (BitNet 1-bit LLMs)**

Zwykła kwantyzacja to dla nas czasami za mało, gdy schodzimy do środowisk skrajnie ograniczonych. Projekt BitNet b1.58 od Microsoftu to ostateczny środkowy palec wymierzony w matematykę zmiennoprzecinkową – modele te są trenowane od samego zera w logice ternarnej.

**1. Architektura Ternarna i BitNet b1.58** W tradycyjnym modelu każda waga to dokładna liczba (np. ). BitNet b1.58 używa tylko trzech możliwych stanów dla swoich wag: . Aby przechować trzy stany w systemie binarnym, potrzebujemy dokładnie  bita.12 Matematyka tego rozwiązania jest chora, ale genialna – całkowicie eliminuje to kosztowne operacje mnożenia macierzy (FP16/FP32 MACs), zastępując je prostym, sprzętowym dodawaniem i odejmowaniem liczb całkowitych (INT8).14 Aktywacje są kwantyzowane za pomocą funkcji absmax (per-token), a wagi skalowane przez średnią wartość bezwzględną absmean.15 **Notable Insights & Ciekawostki:** BitNet b1.58 potrafi obciąć zużycie pamięci operacyjnej do absurdalnych wartości – wersja 2B zajmuje marne 0.4GB RAM i dekoduje tokeny w 29ms na zwykłym procesorze.15 Energooszczędność w stosunku do Llama 3 1B to różnica rzędu 9-10x mniej dżuli na wygenerowany token.15

**2. Ograniczenia i pułapka bibliotek standardowych** Mimo bycia cudem inżynierii, załadowanie modelu BitNet do standardowej biblioteki transformers w HuggingFace nie daje absolutnie żadnych zysków pamięciowych ani wydajnościowych. Systemy te nie posiadają sprzętowych kerneli zdolnych do ominięcia operacji mnożenia.15 Aby czerpać zyski z logiki 1.58-bitowej, niezbędne jest użycie specjalnie skompilowanego silnika bitnet.cpp, który posiada niskopoziomowe kernele w C/C++ zmuszające CPU i GPU do używania wyłącznie algebry wskaźnikowej i dodawania.15 **Notable Insights & Ciekawostki:** W silniku bitnet.cpp model o rozmiarze 100 miliardów parametrów może zostać uruchomiony na pojedynczym CPU domowego komputera, osiągając prędkość 5-7 tokenów na sekundę, co odpowiada ludzkiej prędkości czytania.16

!([https://upload.wikimedia.org/wikipedia/commons/2/29/Transformer\_architecture\_-\_Multiheaded\_Attention\_module.png](https://upload.wikimedia.org/wikipedia/commons/2/29/Transformer_architecture_-_Multiheaded_Attention_module.png))

*(W architekturze ternarnej, skomplikowane rzuty wielogłowej atencji \[Multi-head Attention\] bazują niemal w 100% na operacjach dodawania dla wag skwantyzowanych do stanów -1, 0, 1).*

## **1.3. Amputacja i klonowanie umysłu: Pruning i Knowledge Distillation**

Kompresowanie rozkładu wag to jedna droga, ale równie fascynujące jest fizyczne wycinanie kawałków sieci lub destylowanie wiedzy gigantów do ciał liliputów.

**1. Model Pruning (Odcinanie parametrów)** Pruning to brutalna amputacja parametrów po zakończonym treningu. Rozróżniamy *depth-pruning*, gdzie wywalamy całe bloki transformera z architektury (np. ucinamy z 32 warstw do 16), oraz *width-pruning*, polegający na kastrowaniu poszczególnych głów uwagi (attention heads) czy wymiarów warstw liniowych, które algorytm uznał za mało istotne dla funkcji straty.17 **Notable Insights & Ciekawostki:** W roku 2026 środowisko naukowe doszło do brutalnych wniosków (np. poprzez benchmark LLM-KICK) – samo odcięcie 25-30% parametrów w klasycznym pruningu zazwyczaj bezpowrotnie niszczy zdolności rozumowania i "In-context learningu".18 Pruning działa poprawnie tylko wtedy, gdy jest połączony z agresywnym retreningiem i destylacją.

**2. Knowledge Distillation (Destylacja Wiedzy)** Destylacja to proces transferu wiedzy, w którym kolosalny model-nauczyciel (np. DeepSeek V3 mający 671B parametrów) uczy małego model-studenta (np. 8B). Zamiast trenować studenta tylko na surowym tekście, zmuszamy go do naśladowania tzw. "soft targets" – bogatych rozkładów prawdopodobieństwa wypluwanych przez softmax nauczyciela.19 Nauczyciel mówiący "Paryż 92%, Lyon 5%" uczy studenta więcej o geografii niż sucha odpowiedź "Paryż".19 **Notable Insights & Ciekawostki:** Przełomem w mikro LLM-ach w 2025 i 2026 roku jest *Rationale-Based KD*. Nauczyciel nie tylko daje odpowiedzi, ale wypluwa tysiące skomplikowanych ciągów myślowych (Chain-of-Thought). Mały student uczy się całych wzorców logicznych dedukcji, co pozwala mikrusom uderzać w poziom modeli GPT-4.20

## **1.4. Najmniejszy logicznie myślący mikrus w 2026**

Do jakiego ekstremum możemy zejść z parametrami, żeby model sztucznej inteligencji nadal kontaktował z bazą, a nie rzucał przypadkowym słownym śmietnikiem? Limit absurdu z roku na rok się przesuwa.

**1. Architektury Dual-Mode i seria Qwen3 (0.6B / 1.5B)** W 2026 roku modele Qwen3 zdefiniowały na nowo, co znaczy być małym. Wprowadziły one hybrydowe tryby działania (Thinking Mode i Non-thinking Mode) w zunifikowanym modelu. Tryb *Thinking* pozwala modelowi przepalić więcej tokenów na ukryte rozumowanie step-by-step przed podaniem ostatecznej odpowiedzi, co uodparnia model na szybkie potknięcia.22 Qwen3-0.6B – model tak mały, że mógłby być zainstalowany na smartwatchu – z użyciem RAG (Retrieval-Augmented Generation) osiąga porażające 88.12% skuteczności w złożonej klasyfikacji logów systemowych Linuxa.24 **Notable Insights & Ciekawostki:** Wersja Qwen 2.5 i 3 w rozmiarze 1.5B jest niesamowita pod względem wbudowanych hamulców. W testach tool-calling'u mniejszy model 1.5B często pokonywał wersję 3B tylko dlatego, że potrafił zignorować prowokacyjny prompt i odmówić użycia funkcji, gdy nie była ona bezwzględnie potrzebna, podczas gdy większe modele halucynowały.25

**2. Ekstremalnie czyste SFT: SmolLM2 (135M / 360M / 1.7B)** SmolLM2 to seria mikrusów od HuggingFace, wyuczona na rygorystycznie czyszczonych datasetach (FineMath, Stack-Edu, Cosmopedia).26 Wersja 135M została wytrenowana na skromnych 2 bilionach tokenów (koszt zaledwie 18 tysięcy dolarów) i pokonuje wszystkie modele w klasie poniżej 200 milionów parametrów, mieszcząc się fizycznie w cache'u L1/L2 współczesnych procesorów.27 **Notable Insights & Ciekawostki:** SmolLM2 1.7B jest potworem instrukcyjnym (IFeval), deklasując modele 2B z innych korporacji, i wyciąga aż 24% pass@1 w rozwiązywaniu surowego kodu w Pythonie (HumanEval) – to poziom, który w 2023 roku wymagał modelu 30B.28

!([https://upload.wikimedia.org/wikipedia/commons/e/ea/The-Transformer-model-architecture.png](https://upload.wikimedia.org/wikipedia/commons/e/ea/The-Transformer-model-architecture.png))

*(Standardowe podejście "mały model, małe możliwości" przestało obowiązywać dzięki agresywnemu filtrowaniu korpusów syntetycznych używanych w takich architekturach jak SmolLM).*

## ---

**2. ETAP 2: Wielka Jebitna Tabela (Baza Danych Modeli)**

## **2.1. Przegląd ekosystemu Sub-8B w 2026 roku**

Granica między kolosalnymi instancjami chmurowymi a modelami uruchamianymi lokalnie całkowicie się zatarła. Giganci tacy jak Google (Gemma 3), Alibaba (Qwen 3), Microsoft (Phi-4) i DeepSeek wpuścili na rynek "małe" warianty swoich flagowych okrętów, nasycone wiedzą destylowaną z modeli >600B. Dzisiejsze modele sub-8B potrafią bezbłędnie przetwarzać gigantyczne okna kontekstowe (128K+ tokenów), wspierać wywoływanie funkcji (tool-use) jako pełnoprawni agenci oraz analizować kod bez potknięć. Nadeszła era Mixture of Experts (MoE) przeskalowanego w dół, hybrydowej atencji i wbudowanych tokenów \<think>. Poniższa tabela to surowa, chamska baza danych oddzielająca ziarno od marketingowego bełkotu.

## **2.2. Baza Danych Modeli (Tabela)**

Zestawienie zakłada domyślne środowisko zubożałego inżyniera: modele skwantyzowane w formacie **GGUF (Q4\_K\_M)** lub specyficznych wersjach 4-bit, testowane pod kątem realnej utylizacji pamięci. Rozmiary VRAM i RAM obejmują narzut systemu operacyjnego i minimalny bufor KV cache.3

| Twórca / Pełna nazwa modelu (Parametry) | Rozmiar na dysku / RAM (Q4\_K\_M) | Minimalne wymagania (VRAM/RAM/CPU) | Średni czas reakcji (t/s na śmieciowym PC) | Max Context Size | Co potrafi zajebiście / W czym leży i kwiczy (Wady) | Szczyt możliwości (Najlepsze zastosowanie) |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **Alibaba / Qwen3-8B** 31 | ~5.0 GB | 6GB VRAM + 8GB RAM | ~25 - 30 t/s | 131K | **TOP:** Absolutny potwór w 2026. Przełącza się w Thinking Mode. Zjada GPT-3.5 na śniadanie w kodowaniu i matmie. **LEŻY:** Dość ugrzeczniony (censored). Wymaga sporo KV cache. | Niezależny lokalny asystent-agent do automatyzacji shella i kodu Python w VS Code. |
| **DeepSeek / DeepSeek-R1-Distill-Qwen-7B** 31 | ~4.5 GB | 6GB VRAM (GPU) | ~30 - 35 t/s | 32K+ | **TOP:** Rozumowanie. 92.8% w MATH-500. Destylowane, surowe łańcuchy CoT. **LEŻY:** Zawsze "myśli" nawet przy prostej prośbie "napisz cześć", tracąc czas.25 | Piekielnie trudne problemy logiczne, matematyka akademicka, off-grid research. |
| **Microsoft / Phi-4-Mini (3.8B)** 33 | ~2.3 GB | 4GB RAM/VRAM | ~45 t/s | 128K | **TOP:** MMLU 67.3%. Wybitna znajomość faktów i generalnej wiedzy w tak małej bryle. Szybki jak błyskawica. **LEŻY:** Przy złożonym RAG potrafi się zgubić i loopować.24 | Inteligentny router w systemach IoT, wbudowane asystenty w tanich telefonach. |
| **Mistral / Ministral-3-3B-Instruct** 34 | ~2.0 GB | 4GB RAM/VRAM | ~50 t/s | 256K | **TOP:** Gigantyczny kontekst 256K. Wbudowany mały enkoder wizyjny (0.4B). Tool calling wypluwa perfekcyjny JSON. **LEŻY:** Bardzo wąski zakres językowy, słabe widzenie detali. | Analiza gigantycznych logów tekstowych i prosty OCR rachunków offline na edge. |
| **Meta / Llama 3.2 3B** 25 | ~1.9 GB | 2GB - 4GB RAM | ~50 - 55 t/s | 128K | **TOP:** Świetnie wytrenowany "młotek" systemowy. Bardzo szybki, mały footprint. **LEŻY:** Tool calling bez umiaru. Llama spróbuje wywołać funkcję sieciową nawet gdy powiesz "Zignoruj API".25 | Agresywny web-scraping i proste zadania transkrypcyjne na starym laptopie. |
| **Alibaba / Qwen2.5-Coder-3B** 30 | ~1.8 GB | 2GB VRAM | ~55 - 60 t/s | 32K | **TOP:** Bezapelacyjny król autouzupełniania kodu (FIM). Rozumie składnię kilkudziesięciu języków programowania. **LEŻY:** Rozmowy ogólne i encyklopedyczne ssą. Ślepa uliczka poza IDE. | Zastępstwo za GitHub Copilot bezpośrednio na firmowym, zamkniętym komputerze. |
| **Google / Gemma 3 4B** 33 | ~2.5 GB | 4GB RAM/VRAM | ~40 t/s | 128K | **TOP:** Natywne zrozumienie obrazu, ultra-oszczędna architektura prądowa, 140+ języków. **LEŻY:** Sztywny "Google alignment", blokady bezpieczeństwa potrafią irytować. Trudny abliterate. | Osobisty asystent mobilny i wielojęzyczne aplikacje RAG na Androidzie. |
| **HuggingFace / SmolLM2-1.7B** 25 | ~1.1 GB | 2GB RAM | ~70 - 80 t/s | 8K | **TOP:** Cudowny single-stage SFT. Świetny do przepisywania tekstu i IFEval. Idealny na sprzęt bez dedykowanego GPU. **LEŻY:** Krótki kontekst, brak wiedzy specjalistycznej ze względu na rozmiar. | Błyskawiczna korekta gramatyczna tekstu "w locie" zintegrowana z klawiaturą. |
| **H2O.ai / Danube3-4B** 35 | ~2.4 GB | 4GB RAM/CPU | ~45 t/s | 8K | **TOP:** Wyrzuca >80% w HellaSwag. Pełne open-source Apache 2.0, stabilny baseline. **LEŻY:** Angielski only. Zapomnij o logicznym rozumowaniu i agentyce. | Środowiska korporacyjne wymagające czystej licencji bez vendor-lockin. |
| **Alibaba / Qwen3-1.5B** 25 | ~1.0 GB | 2GB RAM/VRAM | ~80 t/s | 32K | **TOP:** Zachowawczy tool-calling. Często mądrzejszy w osądach niż więksi bracia (odmawia użycia złego narzędzia). **LEŻY:** Niewielka baza wiedzy świata (world knowledge). | Lokalny firewall i router przed wywołaniami dużych modeli (Intent classification). |
| **Microsoft / BitNet b1.58-2B-4T** 15 | **~0.4 GB** | 1GB RAM (CPU!) | ~29 ms CPU | 4K | **TOP:** 1.58-bit wagi! Oszczędność energii rzędu 70-80% względem FP16. Nie do zabicia na procesorach ARM. **LEŻY:** Działa *tylko* w bitnet.cpp. PyTorch wyrzuca błędy wydajności. | Instalacja na mikro-płytkach rozwojowych, robotyce, sprzęcie zasilanym z baterii słonecznej. |
| **Alibaba / Qwen3-0.5B** 24 | ~0.4 GB | 1GB RAM | ~100+ t/s | 32K | **TOP:** Posiada Thinking Mode przy 500 milionach wag. Piekielnie szybki w analizie logów systemowych z użyciem RAG (95%+). **LEŻY:** Gubi się bez podanego mu w prompcie kontekstu. | Moduły wbudowane na maszynach wirtualnych skanujące w tle logi dmesg Linuxa. |
| **Zhipu AI / GLM-4-9B-0414** 32 | ~5.8 GB | 8GB VRAM | ~20 t/s | 128K | **TOP:** Znakomity model do generowania grafik SVG, UI webowego, programowania i chińskiego RAG. **LEŻY:** Jest na krawędzi limitu naszych maszyn (8GB RAM to za mało dla wygody). | Tworzenie makiet stron internetowych na potężniejszych laptopach deweloperskich. |

## ---

**3. ETAP 3: Narzędzia, Lifehacki i Czarna Magia (Ekosystem)**

Samo posiadanie pliku z modelem GGUF na dysku nie czyni cię ekspertem. Jeśli spróbujesz go wgrać za pomocą domyślnego, napisanego w Pythonie transformers z HuggingFace na sprzęcie z 4GB RAM, system operacyjny dociśnie cię do ściany i zabije proces OOM Killerem w ułamku sekundy.37 Potrzebujesz bezwzględnej optymalizacji, omijania śmieciowych abstrakcji i pisanych od zera w C/C++ silników wykonawczych. Oto narzędziownik masochisty.

## **3.1. Silniki inferencyjne na śmieciowy sprzęt (llama.cpp, Ollama, vLLM, AirLLM)**

Wybór silnika to 90% sukcesu lub porażki w środowisku z ograniczoną pamięcią.38

**1. llama.cpp** Bezwzględny, niekwestionowany król ekosystemu "bieda-hardware". Silnik napisany w czystym C i C++ przez Georgi Gerganova, pozbawiony absolutnie jakichkolwiek ciężkich zależności w Pythonie.38 Główna mechanika: Używa formatu GGUF i potrafi realizować dynamiczny offloading – jeśli twoje zardzewiałe GPU ma 4GB VRAM, a model wymaga 6GB, llama.cpp wciśnie dokładnie tyle warstw transformera do VRAMu, ile zdoła bezpiecznie zająć, a resztę płynnie oddeleguje do liczenia na CPU (z użyciem akceleracji SIMD czy AVX512).9 Został stworzony specjalnie dla Apple Silicon i konsumenckich procesorów. **Notable Insights & Ciekawostki:** To najlepszy silnik do użycia jednoosobowego. Posiada genialne mechanizmy kwantyzacji KV cache "w locie", co pozwala w 8GB VRAM obsłużyć gigantyczne okna kontekstowe bez zadyszki.40

**2. Ollama** Zbudowane w języku Go, narzędzie działające w filozofii "Docker dla modeli językowych".42 Pod maską to i tak jest fork llama.cpp, ale całkowicie ukrywa ból głowy związany z kompilacją kerneli. Wpisujesz w terminalu ollama run qwen2.5:3b i wszystko dzieje się samo, z obsługą CLI i ustandaryzowanego REST API.42 **Notable Insights & Ciekawostki:** Pomimo wygody, architektoniczny overhead Ollamy i narzut komunikacji w Go powoduje spadki wydajności (tzw. tail latency), gdy spróbujesz wcisnąć więcej niż jeden równoległy request. Dla dewelopera na lokalnym lapku to złoto, dla mikroserwera z 50 klientami – proszenie się o kłopoty.43

**3. vLLM** Jeśli Twoim celem jest serwowanie modelu setkom użytkowników w produkcji, używasz vLLM. Wprowadza on rewolucyjny koncept PagedAttention – w klasycznym podejściu alokacja pamięci dla wygenerowanych tokenów ulega potężnej fragmentacji. vLLM traktuje pamięć KV cache jak strony wirtualnej pamięci w systemie operacyjnym, alokując je nieliniowo blokami. Osiąga do 35x większą przepustowość w systemach multi-user niż llama.cpp.38 **Notable Insights & Ciekawostki:** Brutalna prawda dla posiadaczy ziemniaków jest inna: **vLLM nienawidzi małych zasobów.** Alokacja pre-cache wymaga gigantycznych rezerw (minimum to często kilkanaście GB). Jeśli odpalasz model 7B na karcie z 8GB VRAM – zapomnij o vLLM, to wywali ci system na starcie.41

**4. AirLLM** Chora, perwersyjna abstrakcja oprogramowania, która z powodzeniem omija wszystkie reguły sprzętowe.45 Projekt pozwala uruchomić gigantycznego potwora Llama 3 70B (wymagającego natywnie ~130GB) na karcie graficznej z nędznymi 4GB VRAM, w ogóle nie stosując kwantyzacji obniżającej jakość.46 **Mechanizm:** Zamiast ładować cały model, AirLLM używa podejścia *Layer-wise Inference*. Model siedzi grzecznie na dysku SSD. System ładuje do pamięci GPU wyłącznie pojedynczą warstwę, liczy wynik atencji dla danego tokena, wywala warstwę z GPU do kosza, ładuje kolejną warstwę z dysku, i tak przez 80+ warstw dla jednego zasranego tokena.47 **Notable Insights & Ciekawostki:** To nie działa na HDD. Wymaga absolutnie najszybszych dysków NVMe M.2. Jest potwornie wolne i obciąża szynę PCIe, ale technologicznie demonstruje ominięcie paradygmatu VRAM za wszelką cenę.47 Wczytujesz model do przestrzeni "meta device" tak, że dla OS on ma zero RAMu.48

*(Logotyp llama.cpp – symbol buntu środowiska open-source wobec gigantów chmurowych zamykających modele za murami farm z GPU).*

## **3.2. Trening dla biedaków: LoRA, QLoRA i framework Unsloth**

Trenowanie (Fine-tuning) pełnego modelu od zera to luksus dla bogatych korporacji. Algorytmy optymalizacji takie jak AdamW wymagają przechowywania obok wag także ich gradientów i momentów pierwszego/drugiego rzędu w FP32. Trening modelu 7B pożarłby ponad 84GB VRAM. Nasz limit to 8GB.

**1. LoRA (Low-Rank Adaptation) i QLoRA** Nie ruszamy głównego rdzenia modelu. Zamrażamy wagi bazy i wstrzykujemy w warstwy uwagi małe macierze adapterów o niskim rzędzie . Matematycznie: , gdzie .49 Ilość trenowanych parametrów spada z miliardów do milionów (ok 1% całego rozmiaru).49 W technice **QLoRA** idziemy jeszcze głębiej – wagi bazowe są skwantyzowane i ściśnięte do specjalnego formatu 4-bitowego (NormalFloat4), oszczędzając do 75% VRAMu na start.50 **Notable Insights & Ciekawostki:** Dzięki QLoRA na jednej śmieciowej karcie RTX 3060 (lub darmowym T4 na Google Colab) wytrenujesz wyspecjalizowany model 8B w ciągu godziny lub dwóch.51

**2. Magia frameworku Unsloth** Gdy standardowy skrypt PyTorch/HuggingFace zawodzi, wchodzi Unsloth.50 Zespół napisał od nowa krytyczne mechanizmy matematyczne i uwagowe bezpośrednio w niskopoziomowym języku Triton. Optymalizacje obejmują ręcznie pisane jądra backpropagation, minimalizację "skoków" pamięci (kopii tensorów) na VRAM, oraz chunked cross-entropy (rozbicie kalkulacji funkcji straty na malutkie, niezależne porcje omijające limity pamięci w blokach GPU).52 **Notable Insights & Ciekawostki:** Unsloth powoduje, że cały trening LLM jest od 2x do 5x szybszy i żre o około 70% mniej VRAMu, z zachowaniem 0% straty na jakości gradientów w porównaniu do standardu. Jest to wręcz obowiązkowy framework dla entuzjastów.50 Najnowsze kernele wspierają również natywne pakowanie ciągów (sequence packing) z wyeliminowaniem cross-kontaminacji dokumentów.53

## **3.3. Lifehacki na optymalizację VRAM/RAM na granicy możliwości**

Udało ci się pobrać format i masz silnik. Poniżej surowe komendy, sztuczki na poziomie systemu i sprzętu, by zoptymalizować inferencję na maszynie brzegowej.

**1. Kwantyzacja KV Cache (Pamięci kontekstu)** Z każdym nowym słowem model zapisuje ukryte stany (K i V) do VRAMu. Domyślnie formatuje to w potężnym 16-bitowym standardzie. O ile wagi modelu ważą stałą wartość, długi kontekst pożre każdą pamięć, która została. **Hack:** Użyj komend silnika llama.cpp, aby kwantyzować pamięć podręczną (KV Cache Quantization). Przełączenie pamięci podręcznej na format 8-bit (np. INT8 lub FP8) za pomocą flag --ctk q8\_0 --ctv q8\_0 drastycznie zwiększa twój efektywny kontekst bez utraty stabilności modelu. Pozwala to upchać ponad trzykrotnie większy kontekst do tego samego VRAMu na RTX zaledwie 8GB.55

**2. Memory Mapping (mmap) vs Memory Lock (mlock)** Zrozum, jak twój system zrzuca dane na dysk. Standardowy proces w llama.cpp wczytuje ciężki format GGUF za pomocą funkcji mmap (mapowanie pamięci na dysku). Z systemowego punktu widzenia pozwala to na utrzymanie logiki modelu "w pamięci", mimo że brakuje fizycznych układów RAM (tzw. out-of-core execution), bo system traktuje szybki dysk NVMe jako przedłużenie operacyjnej.55 **Hack:** Jeśli twój model miesci się ledwo, ledwo w RAM-ie i zauważasz koszmarne lagi (page faults), użyj parametru --mlock (Memory Lock). Zablokuje on zrzucone dane w pamięci RAM, zapobiegając bezmyślnemu wypychaniu ważnych warstw modelu do swapa przez system operacyjny, zyskując potężny spadek latencji na każdym nowym promcie.58 Uwaga: wyłącz mlock a zostaw czysty mmap jeśli celowo używasz dysku jako RAM-u, w przeciwnym razie wywołasz panic system.55

**3. Redukcja rozmiaru Batchingu i CPU Offloading** Firmy piszące tutoriale nakazują ustawianie wielkich paczek (Batch Size) aby zwiększyć optymalizację. Gówno prawda dla maszyn ze słabym dostępem do pamięci operacyjnej i z limitowanym VRAM. **Hack:** Zawsze zmuszaj silniki takie jak vLLM lub llama.cpp do działania w trybie pojedynczego zadania. Ustaw parametr concurrency na 1, a batch\_size (często domyślnie na 512 lub więcej w procesie ewaluacji) zredukuj. Odciążysz tym gigantyczną rezerwę pamięci wymaganą na przygotowanie bufora. Dodatkowo umiejętnie określ parametr -ngl (Number of GPU Layers) by przesunąć warstwy gęste (dense) na kartę a embedding i końcowe logity na CPU (np. -ngl 28 na modelach z 32 warstwami).41

Dzięki temu ekosystemowi narodziła się klasa programistów-survivalowców. Uruchamiając skwantyzowane, wydestylowane modele typu Qwen 3 z logiką ternary (1.58b) opartą na bitnet.cpp, modyfikując kernele atencji za pomocą Unsloth i agresywnie optymalizując swój marny sprzęt, jednostka może obecnie przetwarzać dane offline z prędkością, z jaką nie radziły sobie wielkie laby jeszcze kilka lat temu. Limit przestał zależeć od Twojej karty, a od Twojego uporu.

#### **Works cited**

> 1. LLM Quantization Methods: GPTQ, AWQ, GGUF - Cast AI, accessed on March 26, 2026, [https://cast.ai/blog/demystifying-quantizations-llms/](https://cast.ai/blog/demystifying-quantizations-llms/)  
> 2. A Visual Guide to Quantization - Maarten Grootendorst, accessed on March 26, 2026, [https://www.maartengrootendorst.com/blog/quantization/](https://www.maartengrootendorst.com/blog/quantization/)  
> 3. GGUF vs GPTQ vs AWQ Compared: Best Quantization 2026 | Local AI Master, accessed on March 26, 2026, [https://localaimaster.com/blog/quantization-explained](https://localaimaster.com/blog/quantization-explained)  
> 4. LLM Quantization Guide: GGUF vs AWQ vs GPTQ vs bitsandbytes Compared (2026), accessed on March 26, 2026, [https://blog.premai.io/llm-quantization-guide-gguf-vs-awq-vs-gptq-vs-bitsandbytes-compared-2026/](https://blog.premai.io/llm-quantization-guide-gguf-vs-awq-vs-gptq-vs-bitsandbytes-compared-2026/)  
> 5. LLM Quantization Explained: GPTQ, AWQ, QLoRA, GGUF and More - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=WmvZwR4rKJg](https://www.youtube.com/watch?v=WmvZwR4rKJg)  
> 6. The Complete Guide to LLM Quantization - LocalLLM.in, accessed on March 26, 2026, [https://localllm.in/blog/quantization-explained](https://localllm.in/blog/quantization-explained)  
> 7. A detailed comparison between GPTQ, AWQ, EXL2, q4\_K\_M, q4\_K\_S, and load\_in\_4bit: perplexity, VRAM, speed, model size, and loading time. - oobabooga blog, accessed on March 26, 2026, [https://oobabooga.github.io/blog/posts/gptq-awq-exl2-llamacpp/](https://oobabooga.github.io/blog/posts/gptq-awq-exl2-llamacpp/)  
> 8. ‍ LLM Format Comparison/Benchmark: 70B GGUF vs. EXL2 (and AWQ) : r/LocalLLaMA, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/17w57eu/llm\_format\_comparisonbenchmark\_70b\_gguf\_vs\_exl2/](https://www.reddit.com/r/LocalLLaMA/comments/17w57eu/llm_format_comparisonbenchmark_70b_gguf_vs_exl2/)  
> 9. For those who don't know what different model formats (GGUF, GPTQ, AWQ, EXL2, etc.) mean ↓ : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1ayd4xr/for\_those\_who\_dont\_know\_what\_different\_model/](https://www.reddit.com/r/LocalLLaMA/comments/1ayd4xr/for_those_who_dont_know_what_different_model/)  
> 10. A detailed comparison between GPTQ, AWQ, EXL2, q4\_K\_M, q4\_K\_S, and load\_in\_4bit: perplexity, VRAM, speed, model size, and loading time. : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/17fuqr7/a\_detailed\_comparison\_between\_gptq\_awq\_exl2\_q4\_k/](https://www.reddit.com/r/LocalLLaMA/comments/17fuqr7/a_detailed_comparison_between_gptq_awq_exl2_q4_k/)  
> 11. can someone explain all the different quant methods : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1fnwxm7/can\_someone\_explain\_all\_the\_different\_quant/](https://www.reddit.com/r/LocalLLaMA/comments/1fnwxm7/can_someone_explain_all_the_different_quant/)  
> 12. 1.58-bit large language model - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/1.58-bit\_large\_language\_model](https://en.wikipedia.org/wiki/1.58-bit_large_language_model)  
> 13. \[2402.17764\] The Era of 1-bit LLMs: All Large Language Models are in 1.58 Bits - arXiv, accessed on March 26, 2026, [https://arxiv.org/abs/2402.17764](https://arxiv.org/abs/2402.17764)  
> 14. Llama-Bitnet | Training a 1.58 bit LLM | by Zain ul Abideen - Medium, accessed on March 26, 2026, [https://medium.com/@zaiinn440/llama-bitnet-training-a-1-58-bit-llm-3831e517430a](https://medium.com/@zaiinn440/llama-bitnet-training-a-1-58-bit-llm-3831e517430a)  
> 15. microsoft/bitnet-b1.58-2B-4T - Hugging Face, accessed on March 26, 2026, [https://huggingface.co/microsoft/bitnet-b1.58-2B-4T](https://huggingface.co/microsoft/bitnet-b1.58-2B-4T)  
> 16. GitHub - microsoft/BitNet: Official inference framework for 1-bit LLMs, accessed on March 26, 2026, [https://github.com/microsoft/BitNet](https://github.com/microsoft/BitNet)  
> 17. LLM Model Pruning and Knowledge Distillation with NVIDIA NeMo Framework, accessed on March 26, 2026, [https://developer.nvidia.com/blog/llm-model-pruning-and-knowledge-distillation-with-nvidia-nemo-framework/](https://developer.nvidia.com/blog/llm-model-pruning-and-knowledge-distillation-with-nvidia-nemo-framework/)  
> 18. Compressing LLMs: The Truth is Rarely Pure and Never Simple, accessed on March 26, 2026, [https://machinelearning.apple.com/research/compressing-llms](https://machinelearning.apple.com/research/compressing-llms)  
> 19. Model distillation for LLMs: A practical guide to smaller, faster AI - Redis, accessed on March 26, 2026, [https://redis.io/blog/model-distillation-llm-guide/](https://redis.io/blog/model-distillation-llm-guide/)  
> 20. Top 10 Open-source Reasoning Models in 2026 - Clarifai, accessed on March 26, 2026, [https://www.clarifai.com/blog/top-10-open-source-reasoning-models-in-2026](https://www.clarifai.com/blog/top-10-open-source-reasoning-models-in-2026)  
> 21. Knowledge distillation and dataset distillation of large language models: emerging trends, challenges, and future directions - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC12634706/](https://pmc.ncbi.nlm.nih.gov/articles/PMC12634706/)  
> 22. Qwen3: Think Deeper, Act Faster | Qwen, accessed on March 26, 2026, [https://qwenlm.github.io/blog/qwen3/](https://qwenlm.github.io/blog/qwen3/)  
> 23. Qwen3 Technical Report - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2505.09388v1](https://arxiv.org/html/2505.09388v1)  
> 24. Benchmarking Small Language Models and Small Reasoning Language Models on System Log Severity Classification - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2601.07790v1](https://arxiv.org/html/2601.07790v1)  
> 25. I tested 11 small LLMs on tool-calling judgment — on CPU, no GPU. : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1qyg10z/i\_tested\_11\_small\_llms\_on\_toolcalling\_judgment\_on/](https://www.reddit.com/r/LocalLLaMA/comments/1qyg10z/i_tested_11_small_llms_on_toolcalling_judgment_on/)  
> 26. SmolLM2: When Smol Goes Big — Data-Centric Training of a Small Language Model - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2502.02737v1](https://arxiv.org/html/2502.02737v1)  
> 27. SmolLM2: When Smol Goes Big — Data-Centric Training of a Fully Open Small Language Model | OpenReview, accessed on March 26, 2026, [https://openreview.net/forum?id=3JiCl2A14H](https://openreview.net/forum?id=3JiCl2A14H)  
> 28. SmolLM - blazingly fast and remarkably powerful - Hugging Face, accessed on March 26, 2026, [https://huggingface.co/blog/smollm](https://huggingface.co/blog/smollm)  
> 29. Papers Explained 310: SmolLM2 - Ritvik Rastogi, accessed on March 26, 2026, [https://ritvik19.medium.com/papers-explained-310-smollm2-53991a485d7b](https://ritvik19.medium.com/papers-explained-310-smollm2-53991a485d7b)  
> 30. Open-Source LLMs Compared 2026 – 20+ Models You Should Know - Till Freitag, accessed on March 26, 2026, [https://till-freitag.com/en/blog/open-source-llm-comparison](https://till-freitag.com/en/blog/open-source-llm-comparison)  
> 31. Ultimate Guide - The Best Small LLMs Under 10B Parameters in 2026 - SiliconFlow, accessed on March 26, 2026, [https://www.siliconflow.com/articles/en/best-small-LLMs-under-10B-parameters](https://www.siliconflow.com/articles/en/best-small-LLMs-under-10B-parameters)  
> 32. Ultimate Guide - The Best Small LLMs For Personal Projects In 2026 - SiliconFlow, accessed on March 26, 2026, [https://www.siliconflow.com/articles/en/best-small-LLMs-for-personal-projects](https://www.siliconflow.com/articles/en/best-small-LLMs-for-personal-projects)  
> 33. Best Small Language Models (March 2026): Run AI on 4GB RAM — Phi-4, Gemma 3, Qwen 3 | Local AI Master, accessed on March 26, 2026, [https://localaimaster.com/blog/small-language-models-guide-2026](https://localaimaster.com/blog/small-language-models-guide-2026)  
> 34. The Best Open-Source Small Language Models (SLMs) in 2026 - BentoML, accessed on March 26, 2026, [https://www.bentoml.com/blog/the-best-open-source-small-language-models](https://www.bentoml.com/blog/the-best-open-source-small-language-models)  
> 35. Open Source H2O-Danube3-4B | H2O.ai, accessed on March 26, 2026, [https://h2o.ai/platform/danube/](https://h2o.ai/platform/danube/)  
> 36. Zebra-Llama: Towards Extremely Efficient Hybrid Models - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2505.17272v2](https://arxiv.org/html/2505.17272v2)  
> 37. The 2026 Local LLM Hardware Guide: Surviving the RAM Crisis | by James Rien | Medium, accessed on March 26, 2026, [https://medium.com/@jameshugo598/the-2026-local-llm-hardware-guide-surviving-the-ram-crisis-fa67e8c95804](https://medium.com/@jameshugo598/the-2026-local-llm-hardware-guide-surviving-the-ram-crisis-fa67e8c95804)  
> 38. vLLM or llama.cpp: Choosing the right LLM inference engine for your use case, accessed on March 26, 2026, [https://developers.redhat.com/articles/2025/09/30/vllm-or-llamacpp-choosing-right-llm-inference-engine-your-use-case](https://developers.redhat.com/articles/2025/09/30/vllm-or-llamacpp-choosing-right-llm-inference-engine-your-use-case)  
> 39. llama.cpp - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Llama.cpp](https://en.wikipedia.org/wiki/Llama.cpp)  
> 40. Vllm vs Llama.cpp vs Ollama : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1qwwint/vllm\_vs\_llamacpp\_vs\_ollama/](https://www.reddit.com/r/LocalLLaMA/comments/1qwwint/vllm_vs_llamacpp_vs_ollama/)  
> 41. Llama.cpp vs vllm : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1qexkwb/llamacpp\_vs\_vllm/](https://www.reddit.com/r/LocalLLaMA/comments/1qexkwb/llamacpp_vs_vllm/)  
> 42. Ollama vs vLLM: A Comprehensive Guide to Local LLM Serving | by Mustafa Genc - Medium, accessed on March 26, 2026, [https://medium.com/@mustafa.gencc94/ollama-vs-vllm-a-comprehensive-guide-to-local-llm-serving-91705ec50c1d](https://medium.com/@mustafa.gencc94/ollama-vs-vllm-a-comprehensive-guide-to-local-llm-serving-91705ec50c1d)  
> 43. Ollama vs vLLM: Performance Benchmark 2026 - SitePoint, accessed on March 26, 2026, [https://www.sitepoint.com/ollama-vs-vllm-performance-benchmark-2026/](https://www.sitepoint.com/ollama-vs-vllm-performance-benchmark-2026/)  
> 44. vLLM vs TensorRT-LLM vs Ollama vs llama.cpp — Choosing the Right Inference Engine on RTX 5090 - DEV Community, accessed on March 26, 2026, [https://dev.to/soytuber/vllm-vs-tensorrt-llm-vs-ollama-vs-llamacpp-choosing-the-right-inference-engine-on-rtx-5090-2aap](https://dev.to/soytuber/vllm-vs-tensorrt-llm-vs-ollama-vs-llamacpp-choosing-the-right-inference-engine-on-rtx-5090-2aap)  
> 45. Unbelievable! Run 70B LLM Inference on a Single 4GB GPU with This NEW Technique, accessed on March 26, 2026, [https://huggingface.co/blog/lyogavin/airllm](https://huggingface.co/blog/lyogavin/airllm)  
> 46. lyogavin/airllm: AirLLM 70B inference with single 4GB GPU - GitHub, accessed on March 26, 2026, [https://github.com/lyogavin/airllm](https://github.com/lyogavin/airllm)  
> 47. AirLLM and “70B on a 4GB GPU” — What's Actually Going On? | by Rohit Shirke | Mar, 2026, accessed on March 26, 2026, [https://rohit-shirke.medium.com/airllm-and-70b-on-a-4gb-gpu-whats-actually-going-on-3bf0e102252e](https://rohit-shirke.medium.com/airllm-and-70b-on-a-4gb-gpu-whats-actually-going-on-3bf0e102252e)  
> 48. Mind-Blowing: Run a Massive 70B Parameter LLM on Just a 4GB GPU — Without Sacrificing Quality! | by AI Gravity Lab - Medium, accessed on March 26, 2026, [https://medium.com/aiguys/mind-blowing-run-a-massive-70b-parameter-llm-on-just-a-4gb-gpu-without-sacrificing-quality-2c96f6355d61](https://medium.com/aiguys/mind-blowing-run-a-massive-70b-parameter-llm-on-just-a-4gb-gpu-without-sacrificing-quality-2c96f6355d61)  
> 49. LoRA fine-tuning Hyperparameters Guide | Unsloth Documentation, accessed on March 26, 2026, [https://unsloth.ai/docs/get-started/fine-tuning-llms-guide/lora-hyperparameters-guide](https://unsloth.ai/docs/get-started/fine-tuning-llms-guide/lora-hyperparameters-guide)  
> 50. Unsloth Explained (2026 Edition) | by Dewasheesh Rana | Medium, accessed on March 26, 2026, [https://medium.com/@dewasheesh.rana/unsloth-explained-2026-edition-c2678f23cca3](https://medium.com/@dewasheesh.rana/unsloth-explained-2026-edition-c2678f23cca3)  
> 51. Fine-Tune Local LLMs 2026 | Practical Guide - SitePoint, accessed on March 26, 2026, [https://www.sitepoint.com/fine-tune-local-llms-2026/](https://www.sitepoint.com/fine-tune-local-llms-2026/)  
> 52. Hacks to make LLM training faster guide : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1fk0acj/hacks\_to\_make\_llm\_training\_faster\_guide/](https://www.reddit.com/r/LocalLLaMA/comments/1fk0acj/hacks_to_make_llm_training_faster_guide/)  
> 53. 3x Faster LLM Training with Unsloth Kernels + Packing, accessed on March 26, 2026, [https://unsloth.ai/docs/blog/3x-faster-training-packing](https://unsloth.ai/docs/blog/3x-faster-training-packing)  
> 54. Chronicals: A High-Performance Framework for LLM Fine-Tuning with 3.51x Speedup over Unsloth - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2601.02609v1](https://arxiv.org/html/2601.02609v1)  
> 55. Running/Evaluating Models Larger Than RAM + VRAM Capacity (with SSD) - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1r7bh8k/runningevaluating\_models\_larger\_than\_ram\_vram/](https://www.reddit.com/r/LocalLLaMA/comments/1r7bh8k/runningevaluating_models_larger_than_ram_vram/)  
> 56. LLM Quantization: BF16 vs FP8 vs INT4 - AIMultiple, accessed on March 26, 2026, [https://aimultiple.com/llm-quantization](https://aimultiple.com/llm-quantization)  
> 57. Questions about KV Cache Quantization - General - vLLM Forums, accessed on March 26, 2026, [https://discuss.vllm.ai/t/questions-about-kv-cache-quantization/966](https://discuss.vllm.ai/t/questions-about-kv-cache-quantization/966)  
> 58. Feasibility and Trade-offs of Language Model Inference at the Edge - arXiv, accessed on March 26, 2026, [https://arxiv.org/pdf/2503.09114](https://arxiv.org/pdf/2503.09114)  
> 59. How to Optimize LLM Inference ?. In this blog, we'll explore how to… | by Maninder Singh | Medium, accessed on March 26, 2026, [https://medium.com/@manindersingh120996/how-to-optimize-llm-inference-0076d9f3dd1b](https://medium.com/@manindersingh120996/how-to-optimize-llm-inference-0076d9f3dd1b)
