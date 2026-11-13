---
title: Micro LLMs 2026 Compendium
kind: knowledge
status: stable
summary: "State of small, heavily quantized LLMs as of March 2026: models, quantization and optimization (in Polish)."
tags: [llm, quantization, small-models, edge]
created: 2026-03-26
updated: 2026-03-26
origin: "https://docs.google.com/document/d/1zLLJ9yWWTwr1s88jvwlICf1EWg7TnrQOUe_U07dpRWQ"
---

# **Mikro LLMy 2026 — Ultimate Knowledge Compendium**

Rozwój wielkich modeli językowych osiągnął w marcu 2026 roku punkt krytyczny, w którym gigantyczne, RAM-ożerne potwory ustępują miejsca ekstremalnie zoptymalizowanym, skwantyzowanym do granic możliwości mikro-architekturom. Dziedzina "Ultra-Light AI" skupia się na brutalnej redukcji precyzji wag (nawet poniżej 1-bita), destylacji wiedzy z modeli klasy R1 oraz zaawansowanym offloadingu, co pozwala uruchamiać potężne algorytmy wnioskowania na sprzęcie, którego specyfikacja brzmi jak żart. To technologiczny przełom, który całkowicie demokratyzuje dostęp do sztucznej inteligencji, pozwalając na odpalenie w pełni funkcjonalnych asystentów, koderów i agentycznych sieci na starych laptopach wyposażonych w zaledwie 8 GB pamięci.

**Compiled on:** March 26, 2026

---

**Table of Contents**

*   
  1. Wprowadzenie i Kluczowe Pytania Startowe  
  * 1.1 Najlżejsze działające modele na rynku  
  * 1.2 Mechanika ekstremalnego odchudzania  
*   
  2. Kompletna Lista i Porównanie Modeli (Poniżej 8GB VRAM/RAM)  
  * 2.1 Tabela główna: Top 21 Mikro LLMów  
  * 2.2 Analiza liderów w segmencie Low-End  
*   
  3. Architektura i Techniki Ekstremalnej Kompresji  
  * 3.1 Kwantyzacja (GGUF, AWQ, EXL2, NF4)  
  * 3.2 Rewolucja 1-bit i Sub-1-bit (BitNet b1.58, BTC-LLM)  
  * 3.3 Pruning i Sparse Networks  
*   
  4. Trening, Fine-Tuning i Knowledge Transfer  
  * 4.1 Distillation i Knowledge Transfer  
  * 4.2 LoRA, QLoRA, DoRA  
  * 4.3 GRPO i Reinforcement Learning  
  * 4.4 Low-resource Fine-tuning (Unsloth)  
*   
  5. Infrastruktura Inferencyjna i Edge Cases  
  * 5.1 Offloading, Paging i PagedAttention (vLLM)  
  * 5.2 Optymalizacja lokalna (llama.cpp, ExLlamaV2, TabbyAPI, Ollama mods)  
  * 5.3 Hybrydowa inferencja i Petals  
  * 5.4 Edge cases: co się dzieje przy 2-bit / 1-bit i ratowanie jakości  
*   
  6. Wnioski i Rekomendacje

## ---

**1. Wprowadzenie i Kluczowe Pytania Startowe**

Analiza rynku z marca 2026 roku bezlitośnie obnaża przestarzałość paradygmatu "im więcej parametrów, tym lepie". Współczesny setup z 8 GB VRAM i 8 GB RAM to z perspektywy mikro LLMów absolutny, potężny top tier, który pozwala na płynne uruchamianie modeli o potężnych możliwościach rezonowania.

## **1.1 Najlżejsze działające modele na rynku**

Zrozumienie granic miniaturyzacji AI wymaga spojrzenia na sam dół drabiny parametrów, gdzie modele o wielkości zaledwie kilkudziesięciu do kilkuset milionów wag zaczynają przejawiać realne zdolności kognitywne.

Odpowiedź na pytanie "jaki jest najmniejszy model, który cokolwiek potrafi" mocno ewoluowała. Jeśli mówimy o absolutnym dnie, to jest to **htmLLM (50M - 124M)**, nazywany w żargonie "Pocket Coderem".1 Mimo komicznie małego rozmiaru (mieści się w ułamku gigabajta pamięci), rozumie on struktury formularzy, tagi semantyczne i instrukcje CSS, nawet jeśli przy skomplikowanych layoutach zaczyna mocno halucynować CSS-y.1

Jednak prawdziwie użyteczne potwory zaczynają się w okolicach 250M - 600M parametrów. **SmolVLM-256M** to wizualno-językowa bestia, która zjada poniżej 1 GB pamięci VRAM podczas inferencji.2 Co najbardziej zrywa kask, ten maluch outperformuje starsze modele o rozmiarach 80 miliardów parametrów (jak Idefics-80B) w zadaniach wizyjnych, co jest możliwe dzięki agresywnej optymalizacji tokenizacji obrazu i wyrzuceniu zbędnego bloatu.2 Na froncie czysto tekstowym, **Qwen3 0.6B** to absolutny game-changer. Daje radę wypruć z siebie 70 tokenów na sekundę na procesorach pokroju Apple M3 Max, a dzięki zaimplementowaniu procesu "myślenia" (dual-mode) osiąga wyniki na listach odporności na halucynacje lepsze niż niektóre płatne modele od OpenAI.4

**Notable Insights & Ciekawostki:**

* Model Qwen3 0.6B jest na tyle bystry, że społeczność powszechnie używa go jako modelu-szkicownika (draft model) do operacji *speculative decoding* przy większych modelach, gigantycznie przyspieszając ich działanie.4  
* SmolVLM-256M nawet przy załadowaniu maksymalnego "batch size" równego 64 pożera zaledwie 15 GB pamięci, co na standardy modeli wizyjnych jest wynikiem wręcz absurdalnie małym.5

!([https://upload.wikimedia.org/wikipedia/commons/4/4c/Machine\_learning\_parameters\_evolution.svg](https://upload.wikimedia.org/wikipedia/commons/4/4c/Machine_learning_parameters_evolution.svg))

## **1.2 Mechanika ekstremalnego odchudzania**

Zbudowanie tak cholernie małych modeli bez całkowitego zniszczenia ich zdolności poznawczych to nie magia, ale brutalna matematyka, inżynieria i procesy destylacji wiedzy.

Aby skurczyć gigantyczny LLM do rozmiarów "kieszonkowych", ekosystem z 2026 roku stosuje kombinację kilku bezlitosnych technik. Po pierwsze: **Native Low-Bit Quantization** (np. BitNet b1.58). Nie mówimy tu o chamskim obcinaniu wag po fakcie. Modele są trenowane od samego zera z użyciem wag przyjmujących tylko wartości  (ternary weights).6 Wyrzuca to całkowicie operacje zmiennoprzecinkowe przez okno, zastępując je prostym dodawaniem w CPU.

Po drugie: **Distillation (Destylacja Wiedzy)**. Zamiast uczyć mały model świata na surowych danych z internetu, używa się potężnych bestii (jak DeepSeek-R1 z 671B parametrów) do wygenerowania perfekcyjnych, rozpisanych krok po kroku łańcuchów myślowych (Chain of Thought). Następnie uczy się tego małego 1.5-miliardowego knypka, by naśladował ten konkretny tor rozumowania.8 Do tego wchodzi **GRPO (Group Relative Policy Optimization)**. Przestaliśmy używać pamięciożernego PPO, bo zżerał VRAM na model Krytyka.9 Teraz wypluwamy kilka wariantów odpowiedzi i nagradzamy model za te, które relatywnie (względem reszty grupy) poradziły sobie najlepiej.9

**Notable Insights & Ciekawostki:**

* Badania pokazują, że trenowanie małych modeli metodą destylacji przy pomocy GRPO pozwala modelowi o wielkości zaledwie 1.5B (DeepSeek-R1-Distill-Qwen) przeskoczyć w benchmarkach matematycznych AIME24 potężne modele o1-preview.10  
* Całkowity koszt fine-tuningu małego modelu metodą destylacji na własnych danych zamyka się obecnie w okolicach 42 dolarów na czterech kartach NVIDIA A40 (48GB VRAM).10

!([https://upload.wikimedia.org/wikipedia/commons/9/91/Full\_Transformer\_Architecture.png](https://upload.wikimedia.org/wikipedia/commons/9/91/Full_Transformer_Architecture.png))

## ---

**2. Kompletna Lista i Porównanie Modeli (Poniżej 8GB VRAM/RAM)**

Rynek w połowie 2026 roku zalała fala modeli open-source zoptymalizowanych pod formaty GGUF, AWQ czy EXL2, które działają płynnie na najbardziej upośledzonych maszynach. Poniższa tabela zbiera 21 najważniejszych perełek poniżej 8 GB VRAM.

## **2.1 Tabela główna: Top 21 Mikro LLMów**

Stworzenie tego zestawienia wymagało przekopania repozytoriów HuggingFace, testów społeczności z r/LocalLLaMA oraz dokumentacji llama.cpp, by wybrać te algorytmy, które realnie dowożą wartość na starym krzemie.

| Twórca / Model + Link HF | Rozmiar / Plik (Q4\_K\_M) | Minimalne Wymagania | Real Perf. (8/8GB CPU/GPU) | Max Context | Co potrafi / Czego nie potrafi (Szczerze) | Szczyt możliwości / Supermoce | Najlepiej się nadaje do... |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **Open Source** htmLLM-v2 | 124M / ~0.1 GB | CPU-only | 100+ t/s | 2K | Potrafi HTML/CSS. Nie potrafi logiki JS.1 | Ultraszybki "pocket coder" dla frontendu. | Generowania prostych UI on-the-fly. |
| **HuggingFace** SmolVLM-256M | 256M / \<1.0 GB | CPU/GPU (dowolne) | ~40 t/s | 4K | Potrafi analizować obrazy. Nie potrafi długich esejów.2 | Zjada modele 300x większe w OCR na Edge'u.2 | Mobilnych aplikacji ze skanerem dokumentów. |
| **Alibaba** Qwen2.5 0.5B Instruct | 0.5B / ~0.4 GB | CPU-only | ~60 t/s | 32K | Potrafi RAG. Nie potrafi trzymać trudnych ról.12 | Stabilny RAG za zerowy koszt obliczeniowy. | Embeddings i prosty, lokalny RAG. |
| **Alibaba** Qwen3 0.6B | 0.6B / ~0.5 GB | CPU/Integra | ~70 t/s (M3 Max) 4 | 32K | Potrafi myśleć logicznie. Nie potrafi ciężkiego backendu.4 | Dual-mode (myślący), najmniej halucynujący w klasie.4 | Speculative decoding i nano-agenci.4 |
| **Google** Gemma-3-1B-IT | 1B / ~0.8 GB | GPU 2GB / CPU | ~40 t/s | 32K | Potrafi świetny angielski. Nie potrafi innych języków, brak wizji.14 | Ekstremalna optymalizacja Keras/JAX.14 | Szybkiego NLP w zamkniętych apkach. |
| **Liquid/Apple** LFM2.5-thinking | 1.2B / ~0.9 GB | CPU/Apple NPU | ~45 t/s | 16K | Potrafi działać non-stop bez zapychania cache'u. Nie błyszczy wiedzą.15 | Omija limity KV Cache dzięki architekturze hybrydowej. | Ciągłego strumieniowania tekstu na smartfony. |
| **DeepSeek** R1-Distill-Qwen-1.5B | 1.5B / ~1.1 GB | GPU 2GB | ~35-40 t/s | 128K | Potrafi trudną matematykę. Nie potrafi poezji.10 | Rozwala benchmark AIME24 bijąc o1-preview.10 | Zadań wymagających surowej, czystej dedukcji. |
| **OpenBMB** MiniCPM-V 2.0 | 2B / ~1.8 GB | GPU 4GB | ~35 t/s | 8K | Potrafi high-res OCR. Nie potrafi luźnej rozmowy.11 | Percepcja obrazów do 1.8M pikseli na starym telefonie.11 | Wyciągania tabel i tekstu z rozmazanych zdjęć. |
| **Microsoft** BitNet b1.58 2B4T | 2B / ~0.4 GB | CPU-only (bitnet.cpp) | 29 ms latency / ~30 t/s | 4K | Potrafi szybkie QA. Nie potrafi działać na standardowym HF transformers.18 | Natywne 1.58-bita, pożera ułamek prądu.6 | Skrajnego Edge AI, np. Raspberry Pi czy tostery. |
| **IBM** Granite-4.0-H-Micro | 3B / ~2.2 GB | GPU 4GB | ~25 t/s | 131K | Potrafi używać narzędzi (tools). Nie umie żartować.13 | Wybitny w long context tool-calling.13 | Agentów enterprise wywołujących zewnętrzne API. |
| **Mistral** Ministral 3B | 3B / ~2.2 GB | GPU 4GB | ~30 t/s | 32K | Potrafi konkretny czat. Nie potrafi wyrafinowanego kodu.16 | Działa bez zająknięcia na smartfonach, świetny instruktaż.16 | Wbudowanych, off-linowych asystentów głosowych. |
| **Microsoft** Phi-4-mini | 3.8B / ~3.5 GB | GPU 6GB / Mac 8GB | ~15-20 t/s | 16K | Potrafi niemal wszystko. Gubi się przy 16K skomplikowanego promptu.20 | Jedyny w pełni wszechstronny model, który gładko wchodzi w 8GB RAM.20 | Najlepszy all-rounder dla bieda-laptopów.20 |
| **Alibaba** Qwen3 4B | 4B / ~3.0 GB | GPU 6GB | ~28 t/s | 131K | Potrafi gigantyczny kontekst i logikę. Wolno przetwarza długi prompt.13 | Dual-mode (thinking i non-thinking) na zawołanie.13 | Podsumowywania grubych raportów na Low-Endzie. |
| **Nvidia** Nemotron-3-Nano | 4B / ~2.9 GB | GPU 6GB | ~25 t/s | 32K | Potrafi stabilnie dowozić wyniki. Nudny, korporacyjny styl.22 | Całkowicie otwarta licencja, brak praw Nvidii do zmian.22 | Korporacyjnych, bezpiecznych wdrożeń self-hosted. |
| **Google** Gemma-3-4B-IT | 4B / ~3.2 GB | GPU 6GB / Mac 8GB | ~20 t/s | 128K | Potrafi czytać obrazy i 140 języków. Silnie cenzurowany.14 | Bije modele 27B z zeszłego roku na wielu benchmarkach.14 | Wielojęzycznych aplikacji ze wsparciem obrazków. |
| **Alibaba** Qwen3-VL 4B | 4B / ~3.2 GB | GPU 6GB | ~20 t/s | 32K | Potrafi czytać wideo/obrazy z wielkim detalem. Wolny przy video batchach.16 | Najsilniejsza wizja w tym segmencie po chińsku i angielsku.16 | Systemów monitoringu domowego analizujących klatki wideo. |
| **Mistral** Mistral Small 3 7B | 7B / ~5.0 GB | GPU 8GB / Mac 16GB | ~50 t/s (lepszy CPU), ~12 t/s tutaj | 32K | Potrafi być niesamowicie szybki. Odstaje z kodem od Qwena.20 | Po prostu masakruje konkurencję w tokens/sec.20 | Botów gdzie liczy się ultra-niska latencja. |
| **AllenAI** Olmo Hybrid 7B | 7B / ~5.0 GB | GPU 8GB | ~10 t/s | 32K | Potrafi oszczędzać pamięć hybrydą. Miewa niestabilności (eksperyment).22 | Architektura Gated DeltaNet (Transformer + linear RNN), 100% open.22 | Badań naukowych i modyfikacji mechanizmów atencji. |
| **Alibaba** Qwen3 8B | 8B / ~5.5 GB | GPU 8GB | ~10-15 t/s | 131K | Potrafi perfekcyjnie kodować. Zapycha 8GB VRAM jeśli masz włączony system.20 | Zdecydowany król w teście HumanEval w klasie 8B.20 | Uruchamiania zaawansowanych koderów (Aider) w tle.23 |
| **Meta** Llama-3.3-8B | 8B / ~6.0 GB | GPU 8GB | ~10 t/s | 128K | Potrafi pięknie i wszechstronnie pisać. Na 8GB RAM wywala częste błędy OOM.20 | Niedościgniony ekosystem społeczności (najlepszy all-rounder).20 | RAG pipelines i codziennych asystentów, o ile zoptymalizujesz RAM. |
| **Społeczność** Llama-3.1-8B-Abliterated | 8B / ~5.5 GB | GPU 8GB | ~12 t/s | 128K | Potrafi absolutnie wszystko, brutalnie szczery. Częściej wpada w halucynacje.25 | Kompletny brak cenzury dzięki usunięciu wektora odmowy (Abliteration).26 | Zadań "uncensored", red-teamingu i luźnego gadania bez guardraili. |

**Notable Insights & Ciekawostki:**

* Zablokowanie wektora odmowy (abliteration) znacznie gorzej znosiły nowsze architektury Mixture of Experts (MoE) takie jak Qwen3 30B, gdzie traciły na logice i agentowości. Gęste modele takie jak Llama 8B radzą sobie z wycięciem moralności znacznie bardziej spójnie.26  
* Zapakowanie modelu klasy 8B (jak Llama 3.3) w formacie Q4\_K\_M zostawia w 8-gigabajtowym środowisku tak mało wolnej przestrzeni VRAM/RAM, że samo odpalenie przeglądarki nierzadko dławi system operacyjny.20

!([https://upload.wikimedia.org/wikipedia/commons/e/ea/Elo\_rating\_graph.png](https://upload.wikimedia.org/wikipedia/commons/e/ea/Elo_rating_graph.png))

## **2.2 Analiza liderów w segmencie Low-End**

Kiedy system dysponuje budżetem 8 GB RAM (lub 8 GB zunifikowanej pamięci jak w MacBookach M1/M2), hardware błaga o litość. Realia są brutalne: sam OS, środowisko programistyczne i serwer inferencyjny (np. llama.cpp) potrafią pożreć od 2 do 3 GB zasobów zanim model w ogóle załaduje wagi do pamięci.20

**Absolutny Sweet-Spot: Phi-4-mini (3.8B)** Dla stacji z 8GB ten model z 2025/2026 to, krótko mówiąc, dar niebios. Użytkownicy wprost nazywają go jedynym praktycznym wyborem w tym budżecie pamięci.20 W wersji skwantyzowanej do Q4\_K\_M waży on około 3.5 GB, co zostawia wystarczająco dużo przestrzeni oddechowej na tzw. KV Cache (potrzebny do długich rozmów) i system operacyjny.20 Jego osiągi są wręcz śmiesznie wysokie jak na taki rozmiar – MMLU na poziomie 68.5 oraz HumanEval wynoszący 64.0 pozwalają mu realnie rywalizować z gigantami z poprzedniego roku.20 Dowozi na Apple Silicon około 15-20 tokenów na sekundę, co czyni go superpłynnym.20

**Wojna o Prędkość i Kodowanie: Qwen3 vs Mistral** Jeśli jednak chcemy ryzykować z modelem rzędu 7-8 miliardów parametrów, wkraczamy w strefę kompromisów. **Mistral Small 3 7B** to król prędkości. Jeśli wsadzisz go w lepszy hardware z większą przepustowością pamięci, potrafi pruć nawet 50 tokenów na sekundę.20 Z kolei **Qwen3 8B** stał się głównym wyborem dla koderów. Zmiażdżył konkurencję w benchmarkach generowania kodu (HumanEval na poziomie 72+), stając się fundamentem dla lokalnych agentów programistycznych podłączonych do VSCode (np. w narzędziu Aider).20 Pamiętaj jednak o jednym: modele 8B zapchają twój 8-gigowy komputer pod kurek, często wymagając mocnego ubijania innych apek w tle.28

**Notable Insights & Ciekawostki:**

* Model Qwen3 8B i 4B wprowadzają niesamowitą nowinkę zwaną *Dual-Mode Architecture*. Model potrafi dynamicznie, w locie przełączać się między trybem standardowego szybkiego plucia tekstem, a trybem głębokiego "myślenia" (reasoning-intensive), rozwiązując ciężkie logiczne zagadki na żądanie.13  
* Apple Silicon (układy z serii M) deklasuje układy x86 w kategorii "Low-End" dzięki zunifikowanej architekturze pamięci, co oznacza, że procesor graficzny ma bezpośredni dostęp do całego RAMu bez konieczności powolnego offloadingu przez szynę PCIe, co drastycznie podbija osiągi modeli wielkości 7B.11

## ---

**3. Architektura i Techniki Ekstremalnej Kompresji**

Skurczenie modelu rozmiaru 70 miliardów parametrów do czegoś, co odpala się szybciej niż Pasjans na Windowsie, wymagało porzucenia tradycyjnych metod.

## **3.1 Kwantyzacja (GGUF, AWQ, EXL2, NF4)**

Kwantyzacja to w wielkim skrócie sztuka upychania gładkich, płynnych wartości o wysokiej rozdzielczości (często 16-bitowych) w twarde, kanciaste pojemniki 4-bitowe, a nawet mniejsze.

W 2026 roku mamy kilka dominujących formatów, z których każdy celuje w nieco inny sprzęt i use-case:

* **GGUF:** Król otwartego oprogramowania i wnioskowania opartego na CPU/MacBookach (Unified Memory). Korzysta z zaawansowanych technik takich jak K-Quants (np. Q4\_K\_M), gdzie nie każda waga modelu obcinana jest tak samo. Kluczowe tensory ucinane są do 5 lub 6 bitów, podczas gdy te nieważne spadają do 3 lub 4 bitów. To oszczędza jakość (Perplexity) przy drastycznym cięciu rozmiaru.20  
* **AWQ / GPTQ:** Typowe algorytmy pod stajnię zielonych (karty NVIDIA). Skupiają się one na identyfikacji ułamka promila najważniejszych wag w sieci, tzw. *outlierów*, które zachowują w 16-bitach, kwantyzując brutalnie całą resztę do 4-bitów.6  
* **EXL2 (ExLlamaV2):** Ewolucja wymyślona przez turboderpa. Narzędzie pozwala na ułamkową kwantyzację (np. równe 3.5 bpw - bitów na wagę). Dzięki mieszaniu kwantyzacji w obrębie jednej warstwy na poziomie podgrup, wyciąga absurdalne prędkości na nowoczesnych GPU.31  
* **NF4 (NormalFloat 4):** Standard wykorzystywany głównie w trakcie fine-tuningu algorytmem QLoRA. Bazuje na matematycznym dowodzie, że wagi dobrze wyuczonej sieci rozkładają się na krzywej rozkładu normalnego (Gaussa). NF4 optymalnie alokuje wartości binarne do tej krzywej, zachowując więcej informacji.32

**Notable Insights & Ciekawostki:**

* Zejście z precyzji 16-bit na 4-bit to nie tylko redukcja zajmowanego miejsca na dysku. Największym "wąskim gardłem" podczas inferencji LLM-ów nie jest wcale układ liczący (ALU), ale przepustowość pamięci (Memory Bandwidth). Kwantyzacja sprawia, że przez szynę przelewa się 4x mniej śmieci na wygenerowany token, diametralnie przyspieszając wnioskowanie.11  
* Zbyt agresywna kwantyzacja (np. wchodząca w strefę 2 bitów) bez wczesnej adaptacji powoduje u modelu "afazję" – buduje on prawidłowe gramatycznie zdania, ale kompletnie gubi sens merytoryczny.33

## **3.2 Rewolucja 1-bit i Sub-1-bit (BitNet b1.58, BTC-LLM)**

To jest punkt, w którym matematyka staje na głowie. Jeszcze parę lat temu wydawało się, że 1-bitowe sieci neuronowe to akademicki żart. Dzisiaj Microsoft ze swoim **BitNet b1.58** zdemolował ten pogląd.34

Zamiast ciąć wagi po wyuczeniu modelu (tzw. post-training quantization), w modelach 1.58-bitowych sieć uczy się od zera w środowisku, w którym jej neurony mogą przyjąć jedynie trzy stany: . Informacja niesiona przez taki stan to dokładnie  bita.6

Tajemnica kryje się w operacji zwanej BitLinear, która wykorzystuje *absmean quantization* 6:

  
gdzie  to średnia wartość bezwzględna wagi. Ponieważ wagi składają się tylko z jedynek, zer i minus jedynek, procesor podczas wnioskowania w ogóle nie wykonuje prądożernych operacji mnożenia! Cała odpowiedź modelu liczona jest na tanich operacjach dodawania. W rezultacie model 2-miliardowy (BitNet 2B4T) zajmuje tylko **400 Megabajtów** RAMu, a CPU wypluwa odpowiedź szybciej niż standardowy procesor graficzny.18

A co powiesz na modele, które schodzą poniżej 1 bita? W 2026 weszliśmy w erę **Sub-1-bit** dzięki frameworkom takim jak **BTC-LLM** i NanoQuant. Kompresują one wagi do rozmiarów rzędu 0.8 bita na wagę. Robią to, grupując binarne patenty zachowania sieci (Binary Pattern Clustering) w tzw. Flash and Accurate Binary Codebook.36 Wprowadza to wyuczalną transformację zmniejszającą ilość outlierów bez stosowania pamięciożernych masek rzadkości (sparse masks).36

**Notable Insights & Ciekawostki:**

* Oparty na 1.58 bicie BitNet 2B4T żre absurdalnie małe 0.028 dżula na jedną inferencję, co predysponuje go do działania bezpośrednio na malutkich systemach embedded i urządzeniach na baterie.18  
* W architekturze BTC-LLM przy zejściu do poziomu 0.8 bita na wagę, model potężnej wielkości Llama-2-13B traci na jakości w teście zero-shot MMLU zaledwie 3.1%, przy jednoczesnym zanotowaniu przyspieszenia rzędu 1.6x względem natywnego FP16.38

## **3.3 Pruning i Sparse Networks**

Pruning (przycinanie sieci) to z kolei fizyczna lobotomia modelu. Brutalne usuwanie słabych połączeń z "mózgu" modelu w taki sposób, by ten nawet nie zorientował się, że połowa jego tkanki wylądowała w śmietniku.

Prymitywny *Magnitude Pruning*, gdzie wycinano po prostu wagi najbliższe zeru, dawno wymarł z powodu strat w logice. W 2026 królują metody strukturalne inspirowane funkcjonalnymi sieciami w mózgu człowieka.39 LLM jest w nich traktowany jak "cyfrowy mózg", a pruning polega na identyfikacji kluczowych sieci funkcjonalnych (np. "obszaru odpowiedzialnego za czasowniki angielskie") i wycinaniu zbędnego balastu z ominięciem tych właśnie węzłów.39 Pożenienie tego z mechanizmem *weight tying* (współdzielenia wag w warstwach) pozwala redukować modele nawet o 90-95%, tak jak w przypadku mikrobów rzędu 50-100M parametrów.40

**Notable Insights & Ciekawostki:**

* Modele wycinane starszymi metodami pół-strukturalnymi często wymagały narzutu pamięci rzędu 0.25 bita na każdą wagę tylko po to, by przechować "mapę" usuniętych połączeń (maskę), co nierzadko dławiło sprzęt i niwelowało korzyści kompresji.37  
* Bardzo agresywny pruning bez ponownego wyregulowania pozostałych wag potrafi sprawić, że sieć idealnie zliczy wyrażenie matematyczne, ale w ogóle nie potrafi złożyć zdania w naturalnym języku po ludzku.

## ---

**4. Trening, Fine-Tuning i Knowledge Transfer**

Stworzenie małego geniusza o wielkości 1.5-miliarda parametrów nie odbywa się dzisiaj przez żmudne karmienie go Terabajtami losowych śmieci z całego internetu. Koszty są obcinane, a jakość pompowana do granic dzięki sprytnym metodom optymalizacji.

## **4.1 Distillation i Knowledge Transfer**

Metoda na "Kopiuj-Wklej Umysłu". Zamiast zmuszać mały model do wymyślania koła na nowo, podpinamy go pod gigantycznego potwora (np. 671B parametrów) i każemy uczyć się od mistrza.

To zjawisko w pełni uwypuklił **DeepSeek-R1-Distill-Qwen-1.5B**. Potężny model-nauczyciel (R1) generuje zbiory danych z ekstremalnie szczegółowymi krokami rozumowania (Chain of Thought). Następnie nasz mały, 1.5B "uczeń" jest trenowany wyłącznie na tych doskonałych trajektoriach myślowych.8 Powstaje model, który matematykę liczy lepiej niż ogromne systemy o1-preview z zeszłych lat, mimo że ma ułamek ich pojemności.10 Uczeń nie musi wiedzieć *dlaczego* algorytm działa na głębokim poziomie, on perfekcyjnie naśladuje wzorzec analityczny wpojony mu przez starszego brata.

**Notable Insights & Ciekawostki:**

* Koszt wytrenowania modelu R1-Distill-1.5B przez destylację przy użyciu 7000 wysokiej jakości próbek zajął naukowcom raptem poniżej 24 godzin na czterech kartach A40, pożerając rachunek za infrastrukturę rzędu... 42 dolarów.10  
* Zauważalnym mankamentem jest to, że małe destylowane modele nierzadko wpadają w tzw. *Optimization Instability*, w którym przy długim generowaniu odpowiedzi zapętlają się w tagach \<think>, udając, że wciąż przeprowadzają proces rozumowania, choć tak naprawdę straciły już wątek.

## **4.2 LoRA, QLoRA, DoRA**

Gdy chcesz samemu dotrenować LLMa na domowym RTXie bez wysadzania go w powietrze, z pomocą przychodzi **LoRA (Low-Rank Adaptation)** i jej wariacje.

Trening całego wielomiliardowego modelu (*Full Fine-Tuning*) wymusza trzymanie olbrzymich stanów optymalizatora dla każdej wagi w modelu. LoRA ten problem obchodzi brutalnie – zamraża oryginalne wagi modelu (żeby nie ruszały się z miejsca) i dokleja do nich malutkie, macierzowe "protezy" (adaptery).32 Matematycznie ujęte to po prostu wstrzyknięcie dwóch macierzy  i  o skrajnie niskim rzędzie:

  
Zamiast trenować 8 miliardów wag, trenujesz ich... ułamek procenta. Wersja **QLoRA** poszła jeszcze dalej. Bazowy model pakowany jest do 4 bitów za pomocą magicznego formatu NormalFloat 4 (NF4), a trenujesz jedynie ten ułamek rzucony w pełnych 16-bitach. W 2026 adapter uczy się też oceniać jak bardzo wektor wagi powinien zmienić "długość" i "kierunek" oddzielnie, co wprowadziło metodę zwaną **DoRA** (Weight-Decomposed Low-Rank Adaptation).

**Notable Insights & Ciekawostki:**

* Dzięki metodom QLoRA adapter potrafi zajmować na dysku od głupich 50 do 200 Megabajtów. Plik ten potem scalamy w locie z wagami oryginalnego modelu na domowym PC.32  
* Fine-tuning QLoRA na RTX 4090 dla modeli do ok. 34B parametrów trwa przeważnie jedną noc, dając wynik lepszy niż chamskie upychanie miliona wyrazów do promptu za pomocą inżynierii wejściowej.42

!([https://upload.wikimedia.org/wikipedia/commons/2/2f/LoRA\_diagram.png](https://upload.wikimedia.org/wikipedia/commons/2/2f/LoRA_diagram.png))

## **4.3 GRPO i Reinforcement Learning**

Klasyczne Reinforcement Learning (RLHF) korzystające z powolnego algorytmu PPO wykitowało, bo do jego działania potrzebne były w VRAMie na raz aż trzy modele: bazowy, trenujący i... tak zwany Model Krytyka. To pożerało pamięć szybciej niż Chrome.9

Wkroczyło **GRPO (Group Relative Policy Optimization)**. Wykopano z równania Krytyka. Zamiast liczyć bezwzględną wartość nagrody za wygenerowaną odpowiedź, GRPO rzuca modelowi komendę: "Generuj od razu 5 odpowiedzi na to samo pytanie". Następnie algorytm sprawdza, która odpowiedź z tej wygenerowanej grupy radzi sobie najlepiej relatywnie w stosunku do reszty.9 Dzięki takiemu równaniu różnicującemu, proces uczy się znacznie stabilniej, promuje czystą logiczną jasność, i przede wszystkim – pożera ułamek obliczeń starszych modeli.9 Na peryferiach zyskuje też podejście ES (Evolution Strategies), traktujące sieć jako czarną skrzynkę nagradzaną w locie.44

**Notable Insights & Ciekawostki:**

* GRPO potężnie wspiera rozwój tzw. modeli o1-style (thinking models), bo bazując na nagradzaniu za czysty i jasny *proces logiczny*, faworyzuje układanie danych "krok-po-kroku" i autokorektę w trakcje generowania.9  
* Choć GRPO jest wydajniejsze pamięciowo od PPO, w początkowej fazie treningu miewa większe kłopoty z fluktuacjami wydajności i wymaga drobiazgowego tuningu "temperatury" wyjścia.10

## **4.4 Low-resource Fine-tuning (Unsloth)**

Zwykłe biblioteki Transformers na HuggingFace potrafią trenować modele z prędkością żółwia z astmą. Zbawicielem domowych deweloperów z 8 GB na pokładzie stał się framework **Unsloth**.32

Unsloth zredefiniował całe zaplecze CUDA i napisał własne kernele w Tritonie, omijając niewydajności w implementacji klasycznego PyTorcha.46 Przez to, że framework unika zbędnego kopiowania wielkich tensorów z pamięci tam i z powrotem na kartę podczas propagacji wstecznej, pamięć ulega drastycznie mniejszej fragmentacji. Efekt to trening, który jest nawet 2 do 5 razy szybszy i zżera ok. 75% mniej pamięci VRAM.46 Najnowsze kernele dla modeli Mixture of Experts (MoE) wyrzeźbiły kolejne 35% urobku.47

**Notable Insights & Ciekawostki:**

* Najczęstszym błędem powodującym "Out of Memory" (OOM) przy domowym fine-tuningu za pomocą Unsloth jest rzucanie się z motyką na słońce w postaci wielkiego *Batch Size*. Na małych układach zbicie go twardo do 1, 2 lub 3 w 99% przypadków rozwiązuje problem wywalania VRAMu.48  
* Zamiast żonglować błędami w zależnościach bibliotek, Unsloth potrafi inteligentnie zaplanować optymalne użycie bloków na GPU unikając niepotrzebnego "rozchlapywania" aktywacji po układzie ALU.46

## ---

**5. Infrastruktura Inferencyjna i Edge Cases**

Nawet najlepsza waga skurczona do 1-bita nie pomoże, jeśli środowisko uruchomieniowe leży i kwiczy. Inferencja na małych maszynach w 2026 to w zasadzie zarządzanie rurami z przepychaniem danych w VRAMie i omijanie pułapek architektury PC.

## **5.1 Offloading, Paging i PagedAttention (vLLM)**

Modele zajmują pamięć na dwie rzeczy: wagi sieci oraz tzw. KV Cache. Ten drugi to stany atencji (pamięć podręczna) trzymające to, o czym już przed chwilą rozmawiano w prompcie. Wielki kontekst potrafi pożreć więcej pamięci niż same wagi modelu!

Serwer **vLLM** rozwalił system swoim algorytmem **PagedAttention**.49 Zamiast tworzyć jeden długi i ciągły bufor w pamięci VRAM dla każdego usera, podzielono KV Cache na małe, odizolowane paczki zwane "stronami" (jak page w systemach Windows/Linux). Karty GPU wypełniają bloki pamięci strona po stronie w sposób nieciągły. Skutkuje to zniwelowaniem marnowania pamięci przez wewnątrz-fragmentacyjną pustkę. Odpowiedzi na ten sam początkowy tekst (np. długi system prompt) mogą nawet współdzielić te same klocki pamięci!49

Jeśli i to nie starcza, programy jak LM Studio czy Jan.ai potrafią robić **RAM Offloading**. Silniki (np. llama.cpp) biorą model, ładują pierwsze kilkanaście najgorętszych warstw na twoje GPU, a resztę syfu wywalają do zwykłego systemowego RAMu i każą przeliczać powolnemu procesorowi x86. Zabija to prędkość, zbijając tokens/s nierzadko trzykrotnie, ale pozwala działać modelom, które inaczej nigdy by nie ruszyły.23

**Notable Insights & Ciekawostki:**

* Zunifikowana pamięć w Macach (Apple Silicon) całkowicie eliminuje karę za Offloading, bo i procesor graficzny, i główny CPU ciągną dane z tej samej zunifikowanej kupki RAMu, przez co modele rzędu Llama 3.3 8B na 16GB zjadają PCty x86 na śniadanie.20  
* W ustawieniach vLLM można natrafić na opcje *Chunked Prefill*. Dzieli ona wielkie, gigantyczne prompkty wejściowe na zjadliwe chunki, dzięki czemu serwer nie zacina się blokując inne generowane strumienie.50

!([https://upload.wikimedia.org/wikipedia/commons/3/36/Memory\_management\_paging\_diagram.svg](https://upload.wikimedia.org/wikipedia/commons/3/36/Memory_management_paging_diagram.svg))

## **5.2 Optymalizacja lokalna (llama.cpp, ExLlamaV2, TabbyAPI, Ollama mods)**

Na poziomie systemowym wojnę wygrywają twarde C++ i ułamkowe paczki bitowe.

Jeśli masz cokolwiek ze stajni NVIDII, pakiet **ExLlamaV2** z podpiętym serwerem **TabbyAPI** (kompatybilnym z poleceniami OpenAI) masakruje wszystko.31 Generatory ExLlamy uodporniły się na gubienie wydajności przez *Dynamic Batching* oraz innowacyjną kompresję cache'u Q4 (pakującą trwający wątek dyskusji do czterech bitów), dzięki czemu zapas VRAMu drastycznie rośnie bez utraty szybkości.31

Dla entuzjastów czystego i wbudowanego UX, **Ollama** zaimplementowała w 2026 w pełni natywne modyfikacje dla asystentów, umożliwiając m.in. twarde wpięcie w logikę *Tool Calling* (wywoływania narzędzi, czytania kalendarza, szukania w sieci), co sprawia, że nawet niszowe Qweny stają się lokalnymi agentami bez potrzeby dłubania w JSON-ach w Pythonie.15

**Notable Insights & Ciekawostki:**

* Podpięcie TabbyAPI w duecie z ExLlamaV2 dla modelu 3-miliardowego (np. OpenLlama 3B) na bardzo mocnej karcie potrafiło wyciągnąć bezlitosne 250-290 tokenów na sekundę.31  
* Zaznaczenie opcji mmap w menedżerach pokroju LM Studio decyduje, czy model mapuje całe swoje wagi bezpośrednio jako plik w pamięci. Jej poprawne ustawienie potrafi wyeliminować denerwujące wciąganie nieużywanego RAMu w zasobach Windowsa, gdzie stary błąd potrafił pompować zużycie o 5 gigabajtów ponad stan wirtualny.53

## **5.3 Hybrydowa inferencja i Petals**

A co, jeśli nie mieści się u ciebie nawet najskromniejszy model 8B, a marzy ci się praca na gigancie 405B ze skomplikowaną architekturą? Z pomocą przychodzi peer-to-peer AI.

System **Petals** (przypominający w działaniu bittorrent dla modeli LLM) tnie gigantyczne wagi na bloki.54 Uruchamiając to u siebie, ładujesz na przykład tylko warstwy 50 do 55 wielkiego modelu Llama 3 405B. Ty rozwiązujesz mały ułamek zadania, rzucasz go do internetu pod inny IP na świecie, gdzie inny kumpel liczy ciąg dalszy.56 Dynamiczne balansowanie i wbudowana odporność na wylatywanie hostów gwarantuje, że zapytanie powróci do ciebie wygenerowane niemal w czasie rzeczywistym.

**Notable Insights & Ciekawostki:**

* Dzięki sieci Petals, w inferencji modelu rzędu 70 miliardów parametrów single-batch, można wyciągnąć z sieci rzędu 6 tokenów na sekundę kompletnie nie obciążając własnego zardzewiałego układu ALU ponad parę malutkich warstw ukrytych.56  
* Petals przesyła między urządzeniami wyłącznie "stany ukryte" i aktywacje na warstwach (hidden states), co powoduje absurdalnie mały narzut na transfer łącza sieciowego – nie musisz zaciągać całych Gigabajtów wagi modelu podczas rozmowy z botem.55

## **5.4 Edge cases: co się dzieje przy 2-bit / 1-bit i ratowanie jakości**

Poniżej granicy 4 bitów świat modeli często upada na kolana. Ekstremalna kwantyzacja (np. K-Quants w GGUF rzędu Q2\_K) brutalnie deformuje przestrzeń wektorów w warstwach nieliniowych.29 Zjawisko to potrafi ukatrupić zdolność dedukcji – model w mgnieniu oka wypisze piękny wiersz o deszczu, ale zapytany o poprawną wagę dwóch arbuzów w zadaniu logicznym zgubi pojęcie ilości.

Ukojem na te straty i sposobem na ratowanie uszkodzonej kwantyzacją tkanki jest technika **LoRA recovery**.58 Polega ona na doczepieniu dodatkowego, maleńkiego ułamka 16-bitowego adaptera u wylotu skwantyzowanej, połamanej i sprasowanej sieci. Trening na tym ułamku z dużą wagą kary (Hessian-weighted distortion) rekalibruje sieć i "prostuje" błędy binarne, pozwalając np. architekturze NanoQuant i Sub-1-bit wrócić do wskaźników precyzji sprzed zgniecenia modelu do 0.8 bita na wagę.58 Podobnie jest z wariantami *Abliterated* (nienarażonymi na cenzurę) – te poddane ablacji zazwyczaj traciły na lotności, aż odkryto, że wycięcie wektora plus lekka kroplówka z fine-tuningu pozycjonowania (DPO) na zaledwie kilkuset przykładach (np. projekt *NeuralDaredevil*) kompletnie ratuje mózg sieci.26

## ---

**6. Wnioski i Rekomendacje**

Przełom roku 2025/2026 i gigantyczny spadek zapotrzebowania na potężne karty rzędu 24 GB VRAM zmienił całkowicie paradygmat uruchamiania AI u hobbystów, deweloperów i start-upów. Budżet na poziomie najgorszych dzisiejszych standardów, jakim jest 8 GB połączonej pamięci RAM/VRAM, absolutnie przestał być wymówką.

**Co realnie warto odpalić (i jest absolutnym game-changerem):**

1. Jeśli dysponujesz zunifikowanym systemem lub po prostu ciasnym VRAMem u Nvidii i szukasz inteligentnego kompana od wszystkiego, jedynym prawowitym królem wydajnościowym jest w tej chwili **Phi-4-mini (3.8B)** w dobrej kwantyzacji (GGUF Q4\_K\_M). Wbija się pod próg pożerania RAMu z precyzją skalpela i posiada genialną instrukcyjność.20  
2. Poszukujący koderów powinni wrzucić na warsztat stację z **Qwen3 8B**. Wpięty jako wirtualny agent VSCode rozstawia po kątach modele od OpenAI sprzed dwóch lat, choć wymaga pozamykania zbędnych procesów by oszczędzić 8-gigowe układy.20  
3. Jeżeli interesuje cię tylko i wyłącznie totalna wolność słowa, pisanie zbereźnych powieści albo analizy Red-Team w obszarze Cybersecurity, pobierz modele wariacji **Llama-3.1-8B-Abliterated** – absolutnie wyprane z filtra odmawiania dzięki usunięciu wektora z warstw.26  
4. Koderzy Enterprise szukający solidnych wywołań baz danych powinni pobawić się modelem **Granite-4.0-H-Micro (3B)**. Genialny w tool-calling'u omijającym denerwujące pomyłki w składni JSONów.13

**Czego omijać szerokim łukiem:** Przestańcie na siłę pchać w słabe układy architektury typu MoE (Mixture of Experts) jak stare Mixtrale czy monstrualne Qweny ucięte do potężnych ułamków. Mimo że w locie ładują mało "aktywnych" wag, ich przepychanie przez rurę PCIe do szyny RAM potrafi udusić sprzęt. Nie schodźcie też poniżej magicznej liczby GGUF Q4 w tradycyjnych sieciach (unikać K-quants K\_S rzędu 2 bitów) – oszczędzicie 800 Megabajtów pamięci, a w zamian dostaniecie sieć, która z kretynem mogłaby grać w ping-ponga na argumenty.20 Zamiast tego, wchodząc w mniejsze progi, bierzcie modele, które od korzeni były budowane pod skrajne środowisko – jak **BitNet b1.58** trenowane natywnie od razu do działania na ułamku bitów za pomocą bitnet.cpp na bieda CPU.

Sprzęt przestał limitować deweloperów. Limitem została wyłącznie brutalna kreatywność w fine-tuningu tych komicznie małych potworów.

#### **Works cited**

> 1. \[Project\] htmLLM-50M base: Can a tiny specialist actually code? + Weights & Code (124M v2 in training!) : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1rrrvqs/project\_htmllm50m\_base\_can\_a\_tiny\_specialist/](https://www.reddit.com/r/LocalLLaMA/comments/1rrrvqs/project_htmllm50m_base_can_a_tiny_specialist/)  
> 2. SmolVLM: Redefining small and efficient multimodal models - OpenReview, accessed on March 26, 2026, [https://openreview.net/forum?id=qMUbhGUFUb](https://openreview.net/forum?id=qMUbhGUFUb)  
> 3. SmolVLM - small yet mighty Vision Language Model - Hugging Face, accessed on March 26, 2026, [https://huggingface.co/blog/smolvlm](https://huggingface.co/blog/smolvlm)  
> 4. Running Qwen3 on your macbook, using MLX, to vibe code for free | Hacker News, accessed on March 26, 2026, [https://news.ycombinator.com/item?id=43856489](https://news.ycombinator.com/item?id=43856489)  
> 5. SmolVLM: Redefining small and efficient multimodal models - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2504.05299v1](https://arxiv.org/html/2504.05299v1)  
> 6. The Era of 1-bit LLMs: All Large Language Models are in 1.58 Bits | by Khushi - Medium, accessed on March 26, 2026, [https://medium.com/@kkhushi/the-era-of-1-bit-llms-all-large-language-models-are-in-1-58-bits-2f113032a9fe](https://medium.com/@kkhushi/the-era-of-1-bit-llms-all-large-language-models-are-in-1-58-bits-2f113032a9fe)  
> 7. The Era of 1-bit LLMs: All Large Language Models are in 1.58 Bits - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2402.17764v1](https://arxiv.org/html/2402.17764v1)  
> 8. models/deepseek-r1-distill-qwen-1.5b/README.md at main · instill-ai/models · GitHub, accessed on March 26, 2026, [https://github.com/instill-ai/models/blob/main/deepseek-r1-distill-qwen-1.5b/README.md](https://github.com/instill-ai/models/blob/main/deepseek-r1-distill-qwen-1.5b/README.md)  
> 9. GRPO in Reinforcement Learning Explained - DigitalOcean, accessed on March 26, 2026, [https://www.digitalocean.com/community/conceptual-articles/group-relative-policy-optimization-reinforcement-learning](https://www.digitalocean.com/community/conceptual-articles/group-relative-policy-optimization-reinforcement-learning)  
> 10. Reinforcement Learning for Reasoning in Small LLMs: What Works and What Doesn't, accessed on March 26, 2026, [https://arxiv.org/html/2503.16219v2](https://arxiv.org/html/2503.16219v2)  
> 11. On-Device LLMs: State of the Union, 2026 – Vikas Chandra – AI ..., accessed on March 26, 2026, [https://v-chandra.github.io/on-device-llms/](https://v-chandra.github.io/on-device-llms/)  
> 12. Small llm model within 100M to 1B parameter : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/15v9gdc/small\_llm\_model\_within\_100m\_to\_1b\_parameter/](https://www.reddit.com/r/LocalLLaMA/comments/15v9gdc/small_llm_model_within_100m_to_1b_parameter/)  
> 13. Models | OpenRouter, accessed on March 26, 2026, [https://openrouter.ai/models](https://openrouter.ai/models)  
> 14. Welcome Gemma 3: Google's all new multimodal, multilingual, long context open LLM, accessed on March 26, 2026, [https://huggingface.co/blog/gemma3](https://huggingface.co/blog/gemma3)  
> 15. library - Ollama, accessed on March 26, 2026, [https://ollama.com/library?sort=newest](https://ollama.com/library?sort=newest)  
> 16. Tools models - Ollama, accessed on March 26, 2026, [https://ollama.com/search?c=tools](https://ollama.com/search?c=tools)  
> 17. DeepSeek-R1-Distill-Qwen-1.5B - m5-docs, accessed on March 26, 2026, [https://docs.m5stack.com/en/stackflow/models/deepseek-r1-distill-qwen-1.5b](https://docs.m5stack.com/en/stackflow/models/deepseek-r1-distill-qwen-1.5b)  
> 18. BitNet b1.58 2B4T : The 1st 1-Bit LLM is here | by Mehul Gupta - Medium, accessed on March 26, 2026, [https://medium.com/data-science-in-your-pocket/bitnet-b1-58-2b4t-the-1st-1-bit-llm-is-here-35f0315089c6](https://medium.com/data-science-in-your-pocket/bitnet-b1-58-2b4t-the-1st-1-bit-llm-is-here-35f0315089c6)  
> 19. BitNet - Hugging Face, accessed on March 26, 2026, [https://huggingface.co/docs/transformers/en/model\_doc/bitnet](https://huggingface.co/docs/transformers/en/model_doc/bitnet)  
> 20. Best Local LLM Models 2026 | Developer Comparison - SitePoint, accessed on March 26, 2026, [https://www.sitepoint.com/best-local-llm-models-2026/](https://www.sitepoint.com/best-local-llm-models-2026/)  
> 21. microsoft/phi-4-gguf - Hugging Face, accessed on March 26, 2026, [https://huggingface.co/microsoft/phi-4-gguf](https://huggingface.co/microsoft/phi-4-gguf)  
> 22. A List of Large Language Models | IBM, accessed on March 26, 2026, [https://www.ibm.com/think/topics/large-language-models-list](https://www.ibm.com/think/topics/large-language-models-list)  
> 23. Best Local Coding AI for 8GB VRAM (2026 Benchmark) - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=m3PQd11aI\_c](https://www.youtube.com/watch?v=m3PQd11aI_c)  
> 24. vectara/hallucination-leaderboard - GitHub, accessed on March 26, 2026, [https://github.com/vectara/hallucination-leaderboard](https://github.com/vectara/hallucination-leaderboard)  
> 25. mlabonne/Meta-Llama-3.1-8B-Instruct-abliterated-GGUF - Hugging Face, accessed on March 26, 2026, [https://huggingface.co/mlabonne/Meta-Llama-3.1-8B-Instruct-abliterated-GGUF](https://huggingface.co/mlabonne/Meta-Llama-3.1-8B-Instruct-abliterated-GGUF)  
> 26. IMPORTANT: Why Abliterated Models SUCK. Here is a better way to uncensor LLMs., accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1nq0cp9/important\_why\_abliterated\_models\_suck\_here\_is\_a/](https://www.reddit.com/r/LocalLLaMA/comments/1nq0cp9/important_why_abliterated_models_suck_here_is_a/)  
> 27. Llama 3.1 8B Instruct abliterated GGUF! : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1ebga83/llama\_31\_8b\_instruct\_abliterated\_gguf/](https://www.reddit.com/r/LocalLLaMA/comments/1ebga83/llama_31_8b_instruct_abliterated_gguf/)  
> 28. LM Studio - Why does my system RAM fill up and go OOM if the model says Full GPU Offload Possible? : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1qmo3do/lm\_studio\_why\_does\_my\_system\_ram\_fill\_up\_and\_go/](https://www.reddit.com/r/LocalLLaMA/comments/1qmo3do/lm_studio_why_does_my_system_ram_fill_up_and_go/)  
> 29. LLM Quantization Methods: GPTQ, AWQ, GGUF - Cast AI, accessed on March 26, 2026, [https://cast.ai/blog/demystifying-quantizations-llms/](https://cast.ai/blog/demystifying-quantizations-llms/)  
> 30. Awesome list for LLM quantization - GitHub, accessed on March 26, 2026, [https://github.com/pprp/awesome-llm-quantization](https://github.com/pprp/awesome-llm-quantization)  
> 31. GitHub - turboderp-org/exllamav2: A fast inference library for running LLMs locally on modern consumer-class GPUs, accessed on March 26, 2026, [https://github.com/turboderp-org/exllamav2](https://github.com/turboderp-org/exllamav2)  
> 32. QLoRA Fine-Tuning with Unsloth: A Complete Guide - Medium, accessed on March 26, 2026, [https://medium.com/@matteo28/qlora-fine-tuning-with-unsloth-a-complete-guide-8652c9c7edb3](https://medium.com/@matteo28/qlora-fine-tuning-with-unsloth-a-complete-guide-8652c9c7edb3)  
> 33. Sub-1-Bit LLM Quantization : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1r15qqc/sub1bit\_llm\_quantization/](https://www.reddit.com/r/LocalLLaMA/comments/1r15qqc/sub1bit_llm_quantization/)  
> 34. GitHub - microsoft/BitNet: Official inference framework for 1-bit LLMs, accessed on March 26, 2026, [https://github.com/microsoft/BitNet](https://github.com/microsoft/BitNet)  
> 35. 1.58-bit large language model - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/1.58-bit\_large\_language\_model](https://en.wikipedia.org/wiki/1.58-bit_large_language_model)  
> 36. \[2506.12040\] BTC-LLM: Efficient Sub-1-Bit LLM Quantization via Learnable Transformation and Binary Codebook - arXiv, accessed on March 26, 2026, [https://arxiv.org/abs/2506.12040](https://arxiv.org/abs/2506.12040)  
> 37. BTC-LLM: EFFICIENT SUB-1-BIT LLM QUANTIZA- - OpenReview, accessed on March 26, 2026, [https://openreview.net/pdf/de5c1665ed9f6cc22b159106eab77ece159fa98d.pdf](https://openreview.net/pdf/de5c1665ed9f6cc22b159106eab77ece159fa98d.pdf)  
> 38. BTC-LLM: Efficient Sub-1-Bit LLM Quantization via Learnable Transformation and Binary Codebook | OpenReview, accessed on March 26, 2026, [https://openreview.net/forum?id=p5cUQGrx3n](https://openreview.net/forum?id=p5cUQGrx3n)  
> 39. Pruning Large Language Models by Identifying and Preserving Functional Networks - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2508.05239v1](https://arxiv.org/html/2508.05239v1)  
> 40. Super Tiny Language Models, accessed on March 26, 2026, [https://arxiv.org/html/2405.14159v1](https://arxiv.org/html/2405.14159v1)  
> 41. Fine-tuning LLMs Guide | Unsloth Documentation, accessed on March 26, 2026, [https://unsloth.ai/docs/get-started/fine-tuning-llms-guide](https://unsloth.ai/docs/get-started/fine-tuning-llms-guide)  
> 42. How to Fine-Tune LLMs in 2026: The Practical Guide That Skips the Hype | Spheron Blog, accessed on March 26, 2026, [https://www.spheron.network/blog/how-to-fine-tune-llm-2026/](https://www.spheron.network/blog/how-to-fine-tune-llm-2026/)  
> 43. A comprehensive overview of everything I know about fine-tuning. : r/LocalLLaMA - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1ilkamr/a\_comprehensive\_overview\_of\_everything\_i\_know/](https://www.reddit.com/r/LocalLLaMA/comments/1ilkamr/a_comprehensive_overview_of_everything_i_know/)  
> 44. Evolution Strategies vs GRPO for LLM Fine-Tuning | alphaXiv, accessed on March 26, 2026, [https://www.alphaxiv.org/blog/es-for-fine-tuning-llms](https://www.alphaxiv.org/blog/es-for-fine-tuning-llms)  
> 45. Fine-Tune Local LLMs 2026 | Practical Guide - SitePoint, accessed on March 26, 2026, [https://www.sitepoint.com/fine-tune-local-llms-2026/](https://www.sitepoint.com/fine-tune-local-llms-2026/)  
> 46. Unsloth Explained (2026 Edition) | by Dewasheesh Rana | Medium, accessed on March 26, 2026, [https://medium.com/@dewasheesh.rana/unsloth-explained-2026-edition-c2678f23cca3](https://medium.com/@dewasheesh.rana/unsloth-explained-2026-edition-c2678f23cca3)  
> 47. Fine-tune MoE Models 12x Faster with Unsloth, accessed on March 26, 2026, [https://unsloth.ai/docs/new/faster-moe](https://unsloth.ai/docs/new/faster-moe)  
> 48. Unsloth Requirements | Unsloth Documentation, accessed on March 26, 2026, [https://unsloth.ai/docs/get-started/fine-tuning-for-beginners/unsloth-requirements](https://unsloth.ai/docs/get-started/fine-tuning-for-beginners/unsloth-requirements)  
> 49. Paged Attention - vLLM, accessed on March 26, 2026, [https://docs.vllm.ai/en/latest/design/paged\_attention/](https://docs.vllm.ai/en/latest/design/paged_attention/)  
> 50. GitHub - vllm-project/vllm: A high-throughput and memory-efficient inference and serving engine for LLMs, accessed on March 26, 2026, [https://github.com/vllm-project/vllm](https://github.com/vllm-project/vllm)  
> 51. Settings - Jan.ai, accessed on March 26, 2026, [https://www.jan.ai/docs/desktop/settings](https://www.jan.ai/docs/desktop/settings)  
> 52. GitHub - theroyallab/tabbyAPI: The official API server for Exllama. OAI compatible, lightweight, and fast., accessed on March 26, 2026, [https://github.com/theroyallab/tabbyAPI](https://github.com/theroyallab/tabbyAPI)  
> 53. Excessive RAM Allocation When Loading Models in LM Studio 0.3.31 (Build 7) - GitHub, accessed on March 26, 2026, [https://github.com/lmstudio-ai/lmstudio-bug-tracker/issues/1198](https://github.com/lmstudio-ai/lmstudio-bug-tracker/issues/1198)  
> 54. Petals: Collaborative Inference and Fine-tuning of Large Models - ACL Anthology, accessed on March 26, 2026, [https://aclanthology.org/2023.acl-demo.54/](https://aclanthology.org/2023.acl-demo.54/)  
> 55. Petals: decentralized inference and finetuning of large language models - Yandex Research, accessed on March 26, 2026, [https://research.yandex.com/blog/petals-decentralized-inference-and-finetuning-of-large-language-models](https://research.yandex.com/blog/petals-decentralized-inference-and-finetuning-of-large-language-models)  
> 56. Petals – Run LLMs at home, BitTorrent-style, accessed on March 26, 2026, [https://petals.dev/](https://petals.dev/)  
> 57. GitHub - bigscience-workshop/petals: Run LLMs at home, BitTorrent-style. Fine-tuning and inference up to 10x faster than offloading, accessed on March 26, 2026, [https://github.com/bigscience-workshop/petals](https://github.com/bigscience-workshop/petals)  
> 58. NanoQuant: Efficient Sub-1-Bit Quantization of Large Language Models - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2602.06694v1](https://arxiv.org/html/2602.06694v1)
