---
layout: post
title: 'JIT - Co każdy developer powinien o nim wiedzieć'
tags: [JIT, JVM]
---
Dzisiejszy wpis chciałbym poświęcić zagadnieniu związanym z JVM a dokładniej JIT. Każda aplikacja napisana
na wirtualną maszynę Javy korzysta z tego dobrodziejstwa a nie każdy wie jak to dokładnie działa i jaki może mieć
wpływ na naszą aplikację.

![](../assets/images/posts/2021/jvm_life_cycle.png)

Java znalazła kompromis pomiędzy językami kompilowanymi a interpretowanymi tworząc bytecode. Jest to
zbiór instrukcji do wykonania przez wirtualną maszynę Javy. Takie podejście umożliwiło uruchamianie
aplikacji na różnych platformach oraz dało duże możliwości optymalizacji, którymi zajmuje się właśnie JIT.
JIT(ang. Just-In-Time) jest to serce maszyny JVM, którego zadaniem jest poprawienie wydajności
aplikacji w czasie jej działania. Aplikacja uruchamiana na maszynie JVM w pierwszej fazie jest interpretowana
a nie kompilowana od razu do kodu natywnego. Dzieje się tak z dwóch powodów:
* (1) brak informacji, które miejsca będą najczęściej wykorzystywane (tzw. hotspoty)
* (2) brak informacji jak jest wykorzystywany dany kod

(1) kompilacja do kodu natywnego jest kosztowna i jest to nieopłacalne dla metod rzadko wykorzystywanych\
(2) większa liczba informacji jak jest wykrozystywany kod pozwala na dobieranie bardziej dopasowanych optymalizacji.

W starszych wersjach Java (przed wersją 8) to developer musiał wskazać jaka wersja kompilatora ma
zostać użyta. Wyróżniało się dwie wersje:
* Kliencką -client (C1) wykorzystywaną przy ograniczonych zasobach i aplikacjach gdzie kluczową kwestią
jest czas uruchomienia.
* Serwerową -server (C2), która umożliwiała stosowanie bardziej zaawansowanych technik optymalizacji.
Wymagała jednak większych zasób oraz dłuższego działania aplikacji.

W wersji 8 powyższe flagi nie mają już żadnego znaczenia i są ignorowane, a od wersji 11 nie są rozpoznawalne
i wykorzystanie ich wywoła błąd. Obecnie wszystkie maszyny wykorzystują kompilacje etapową (ang. Tiered Compilation),
która łączy zalety wersji klienckiej i serwerowej. Składa się ona z etapów: 
* Interpretacja kodu
* Kompilacja C1 (protsta)
* Ograniczona kompilacja C1
* Pełna kompilacja C1
* Kompilacja C2

JIT w celu optymalizacji kodu stosuje wiele zaawansowanych technik, które umożliwiają przyspieszenie
działania aplikacji. 
### Przykładowe metody optymalizacji
#### Zagnieżdżanie metod (Method inlining)
Optymalizacja polega na zastąpieniu wywołania funkcji ciałem wywoływanej metody
#### Kompilacja do kodu natywnego (Native code)
Jest to jedna z najbardziej efektywnych metod optymalizacji, która pozwala uzyskać nawet 20-krotne przyspieszenie.
Optymalizacja polega na zastąpieniu interpretora kodu bajtowego na kod natywny w trakcie pracy aplikacji. 
#### Usuwanie martwego kodu (Dead code elimination)
Usuwa nieużywany kod zachowując funkcjonalność przykładem może być usunięcie kodu po wykonaniu optymalizacji
zagnieżdżania metod.

### Przydatne flagi
* -XX:ReservedCodeCacheSize - maksymalna wielkość pamięci podręcznej. Pamięć ta jest wykorzystywana przez JIT.
* -XX:+PrintCompilation (domyślna wartość false) - umożliwia wyświetlanie informacji o każdej kompilacji,
która jest wykonywana w naszej aplikacji. 
* XX:-TieredCompilation (domyślna wartość true) - włączenie/wyłączenie kompilacji etapowej

### Profilowanie aplikacji
Wykonując profilowanie swojej aplikacji warto zwrócić uwagę na kilka kwesti związanych z JIT:
* czy zdefinowana pamięć podręczna jest wystarczająca dla naszej aplikacji i czy czasem nie dostajemy
komunikatów: "CodeCache is full"
* wykorzystać flagę -XX:+PrintCompilation w celu weryfikacji jakie elementy są kompilowane.

Mam nadzieję, że dzięki temu wpisowi udało Ci się dowiedzieć jak działa JIT i jaki może mieć wpływ na twoją 
aplikację. W kolejnych wpisach omówię bardziej szczegółowo i z przykładami takie elementy jak: 
* kompilacja etapowa
* kolejki i wątki
 

Jeżeli chcielibyście jeszcze bardziej zgłębić wiedzę na temat JIT i samego JVM to zachęcam do przeczytania
książki Scotta Oaks "Java Performance: In-Depth Advice for Tuning and Programming Java 8, 11, and Beyond,
2nd Edition" jest to kopalnia wiedzy jeżeli chodzi o JVM.