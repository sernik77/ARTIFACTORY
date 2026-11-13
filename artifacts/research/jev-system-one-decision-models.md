---
title: JEV-Type Decision Models (System One)
kind: research
status: stable
summary: "What JEV-type System One models are: non-generative decision models that return a typed choice plus a probability distribution (as of October 2026, in Polish)."
tags: [llm, decision-models, automation]
created: 2026-10-07
updated: 2026-10-07
origin: "https://docs.google.com/document/d/1reeOw4RDLC7d6aH7gpc3KeO0HQEXptENl2TJwbHX7-U"
---

# **Modele typu JEV – modele decyzyjne „System One”**

*Stan na październik 2026*

Krótko: „JEV-type” to nie żadna nowa magia, tylko **modele decyzyjne, które w ogóle nie generują tekstu**. Dajesz stan i pytanie z typowanymi odpowiedziami, dostajesz wybór plus rozkład prawdopodobieństwa.

# **Skąd to się wzięło**

TypeSafe AI wypuściło JEV 15 września 2026 jako swój pierwszy publiczny „System One Model”, czyli klasę modeli zaprojektowaną pod automatyzację maszyna-maszyna. Nazwa nawiązuje do Kahnemana: szybkie, intuicyjne System 1 kontra wolne, przemyślane System 2. LLM-y to w tej narracji System 2. Hype był spory, bo firma zebrała 40 mln dolarów seed i ludzie ustawiali się w kolejce po dostęp.

# **Co to potrafi**

Są trzy prymitywy:

* **Choice** wybiera jedną opcję z enuma do 255 pozycji i zwraca wybraną opcję, pełny rozkład prawdopodobieństwa oraz confidence 0–1.

* **Score** umieszcza stan na skali od 2 do 10 opisanych poziomów i zwraca wartość interpolowaną między nimi, więc wynik może wyjść np. 1.035.

* **Noul** to prawdopodobieństwo, że dane stwierdzenie jest prawdziwe. Nie ma confidence, a dokumentacja ostrzega, że 0.5 oznacza równe szanse na tak i nie, a nie że mierzona wartość leży w połowie skali.

Typowe zastosowania to routing modeli, guardrails, sprawdzanie akcji agenta, klasyfikacja ticketów i retry decisions. W praktyce chodzi o każde miejsce w agent loopie, w którym teraz pytasz LLM-a o coś, do czego nie potrzebujesz prozy. Wzorzec wygląda tak: stały kontekst, potem decyzja AI, potem stała akcja. AI dotyka tylko wąskiego wycinka wymagającego osądu, a konkretną robotę robi kod.

# **Jak to działa**

Zamiast pętli autoregresywnej Parallel Sampler przetwarza kontekst i liczy rozkład prawdopodobieństwa po wszystkich zdefiniowanych opcjach w jednym forward passie. Nie ma dekodowania tokenów, więc nie ma też constrained decodingu ani zepsutego JSON-a. Model fizycznie nie może wymyślić nowej kategorii.

Do tego dochodzi kalibracja: TypeSafe opisuje nową architekturę, parallel sampling i Reinforcement Learning for Calibrated Decisions (RLCD). Szczegółów nie ma, bo trenowali wyłącznie na danych syntetycznych, a liczby parametrów, wag ani pełnej architektury nie ujawnili.

Cennik: 42 dolary za miliard tokenów inputu, output za darmo.

# **Open source**

**Laya (Convai Innovations)** to najpoważniejszy gracz. Jest to nie-autoregresywny model decyzyjny na ModernBERT-large, z osobnym checkpointem mmBERT do zadań wielojęzycznych. Licencja to Apache 2.0, także do użytku komercyjnego. Odpalasz przez pip install laya, a wagi lecą z HF (convaiinnovations/laya). Wersja tylko-angielska waży ok. 808 MB, pełny bundle 2.5 GB.

Jest też drama: autor twierdzi, że ten sam pomysł opublikował jako open source ponad rok wcześniej, a potem patrzył, jak dobrze ufundowany startup sprzedaje go jako „breakthrough” za płatnym API. Był to paper SalesRLAgent z marca 2025, z wagami, datasetem i paczką na PyPI.

**Kev** to rodzina modeli inspirowanych Jevem, zbudowana na Qwen3.5, w wariantach 0.8B, 4B i 9B, z otwartym pipeline’em treningowym.

**Nimble (Bespoke Labs)** pokazuje, że takie zachowanie da się doczepić do istniejącego otwartego LLM-a. To LoRA mergowana z Qwen3.5, bespokelabs/Bespoke-Nimble-9B.

# **Brutalnie szczerze**

1. **To jest w gruncie rzeczy dobrze skalibrowany klasyfikator z ładnym API.** Jeden z tekstów ujmuje to trafnie: nowość polega mniej na przełomie, a bardziej na powrocie do metody. Najpierw wybierasz dozwolone odpowiedzi, mierzysz, jak model sobie na nich radzi, i czytasz liczbę. Cross-encoder z softmaxem nad klasami robiliśmy od lat. Nowe jest to, że jest general-purpose i zero-shot, i do tego skalibrowany.

2. **„Zero halucynacji” to marketingowa ściema.** Gwarancja jest na poziomie typu, nie prawdy. Model nie zwróci złego JSON-a, ale spokojnie zwróci zły wybór. Ktoś to dobrze nazwał: Jev daje pierwszą gwarancję, a TypeSafe pakuje ją tak, jakby dawał też drugą.

3. **Benchmarki Jeva wyglądają mocno, tylko na łatwych setach**: 95–99% na IMDB, SST-2, HellaSwag i ARC. Na czymś trudniejszym, np. rozpoznawaniu aktywności z sensorów, klasyfikatory nadzorowane dalej mocno go wyprzedzają. TypeSafe sam radzi, żeby arytmetykę trzymać w kodzie.

4. **Laya jest szybka, ale zero-shot wypada słabo.** Niezależny test wykazał 6–7x niższą latencję, ale accuracy zero-shot 0.36, czyli gorzej niż majority class (0.46), dopóki nie zrobisz fine-tune’u na własnych danych. Do tego 63 ms warm na CPU i 20 s cold startu. Lepiej wypada za to kalibracja: ECE 0.081 dla Layi kontra 0.246 dla Jeva. Wniosek: Laya to raczej świetna baza do fine-tune’u niż gotowy zamiennik.

Jeśli chcesz coś self-hosted do routingu albo guardrails w swoich agentach, wybór wygląda tak. Laya, jeśli masz dane do fine-tune’u i zależy ci na latencji. Kev lub Nimble, jeśli chcesz zero-shot i masz GPU pod 4–9B.

# **Źródła**

1. Deußer et al. – Evaluating and Benchmarking the System One Model Jev (arXiv) – [https://arxiv.org/pdf/2609.37647](https://arxiv.org/pdf/2609.37647)

2. More Features Are Not More Evidence: Training-Free HAR with Jev (arXiv) – [https://arxiv.org/pdf/2609.36154](https://arxiv.org/pdf/2609.36154)

3. DataNorth – Jev: What it is and why this new model matters – [https://datanorth.ai/blog/jev-what-it-is-and-why-this-new-model-matters](https://datanorth.ai/blog/jev-what-it-is-and-why-this-new-model-matters)

4. YourStory – Introducing Jev – [https://yourstory.com/ai-story/what-is-jev-typesafe-ai-decision-model](https://yourstory.com/ai-story/what-is-jev-typesafe-ai-decision-model)

5. x-cmd – Jev, Laya Go Viral – [https://x-cmd.com/blog/260924/](https://x-cmd.com/blog/260924/)

6. lilting.ch – TypeSafe Jev vs autoregressive LLMs and MDLM – [https://lilting.ch/en/articles/typesafe-ai-jev-system-one-model](https://lilting.ch/en/articles/typesafe-ai-jev-system-one-model)

7. noze.it – Jev and System One models: typed decisions – [https://www.noze.it/en/insights/jev-system-one-typed-decisions/](https://www.noze.it/en/insights/jev-system-one-typed-decisions/)

8. Level Up Coding – Jev as an SDD validation gate – [https://levelup.gitconnected.com/typesafes-jev-as-an-sdd-validation-gate-type-guarantee-vs-truth-guarantee-afc149889058](https://levelup.gitconnected.com/typesafes-jev-as-an-sdd-validation-gate-type-guarantee-vs-truth-guarantee-afc149889058)

9. rywalker.com – TypeSafe research notes – [https://rywalker.com/research/typesafe](https://rywalker.com/research/typesafe)

10. MindStudio – Jev AI model articles – [https://www.mindstudio.ai/blog/tag/jev-ai-model](https://www.mindstudio.ai/blog/tag/jev-ai-model)

11. youmind – Jev Model: A Beginner's Guide – [https://youmind.com/landing/x-viral-articles/jev-model-beginner-guide](https://youmind.com/landing/x-viral-articles/jev-model-beginner-guide)

12. mer.vin – Laya, the 33ms open source decision model – [https://mer.vin/news/laya-the-33ms-open-source-decision-model-beating-jev/](https://mer.vin/news/laya-the-33ms-open-source-decision-model-beating-jev/)

13. DataCamp – Top 7 Open-Source TypeSafe Jev Alternatives – [https://www.datacamp.com/blog/top-open-source-jev-alternatives](https://www.datacamp.com/blog/top-open-source-jev-alternatives)

14. Layer3 Labs – Laya Decision Model, Explained – [https://www.layer3labs.io/guides/laya-explained](https://www.layer3labs.io/guides/laya-explained)

15. besthub.dev – laya vs Jev: Speed Wins, Zero-Shot Fails – [https://www.besthub.dev/articles/open-source-decision-model-laya-vs-jev-speed-wins-zero-shot-fails-befced0a2228](https://www.besthub.dev/articles/open-source-decision-model-laya-vs-jev-speed-wins-zero-shot-fails-befced0a2228)
