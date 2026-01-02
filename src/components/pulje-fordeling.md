---
title: Puljefordeling – Nørdet Forklaring
---
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@4/tex-mml-chtml.js"></script>
Algoritmen er en blandingsalgoritme, der starter med en grådig heuristik, der grådigt vælger de nærmeste
hold for tilfældigt seedede klubber. Disse puljer gives til en 2-opt improvement algoritme, der
foretager en bi-objektiv optimering, hvor den gennemsnitlige køretid i en pulje og den (skalerede) maksimale 
gennemsnitlige køretid for et hold begge minimeres. 2-opt byt sker således på baggrund af en monotont aftagende
funktion, kaldet den globale potentialfunktion.
Improvement algoritmen sker under hensyntagen til
en række begrænsninger, særligt at der kun ønskes 1, maksimalt 2, hold fra samme klub i en pulje.
Denne begrænsning balanceres mod et ønske om fairhed i puljefordelingen, hvor et 2-opt
swap kan foretages for puljer/hold hvis gennemsnitlige køretid overstiger den gennemsnitlige køretid
for øvrige hold/puljer med en faktor 1.6.

En stringent matematisk beskrivelsen forefindes herunder:

En række består af N hold. Vi ønsker h hold i hver pulje og får dermed K = N//h puljer P_1,...,P_K.  
Hvert hold i har koordinater (x_i, y_i), og køretiden mellem hold i og hold j er d_{ij}.


### Puljekøretid

Den samlede køretid for en pulje P_k er summen af køretiderne mellem alle hold i puljen:

$$
C(P_k) = \sum_{i\in P_k} \sum_{j>i} d_{ij}
$$

Den gennemsnitlige køretid pr. hold i puljen er:

$$
\bar{d}(P_k) = \frac{1}{|P_k|} \sum_{i\in P_k} \sum_{\substack{j\in P_k\\ j\neq i}} d_{ij}
$$

Den maksimale køretid for et hold i puljen:

$$
d_\mathrm{max}(P_k) = \max_{i\in P_k} \sum_{\substack{j\in P_k\\ j\neq i}} d_{ij}
$$


### Global potentialfunktion

Puljernes samlede potentiale udregner et kompromis mellem gennemsnitlig køretid og worst-case køretid:

$$
\Phi(\{P_1,\dots,P_K\}) = (1-\alpha) \cdot \mathrm{mean}(\bar{d}(P_k)) + \alpha \cdot \frac{\max(d_\mathrm{max}(P_k))}{\mathrm{mean}(\bar{d}(P_k))}
$$

Hvor
$$
\alpha \in [0,1]
$$
styrer balancen mellem gennemsnitlig køretid og worst-case køretid.


### 2-opt swap-operationer

Byt hold i fra P_a med hold j fra P_b:  

$$
P_a' = (P_a \setminus \{i\}) \cup \{j\}, \quad
P_b' = (P_b \setminus \{j\}) \cup \{i\}
$$

Ændringen i total pulje køretid:

$$
\Delta = \sum_{x \in P_a \setminus \{i\}} (d_{jx} - d_{ix}) + \sum_{y \in P_b \setminus \{j\}} (d_{iy} - d_{jy})
$$

Swap accepteres kun hvis følgende tre betingelser er opfyldt:

1. Klubbegrænsningen overholdes (ingen pulje får mere end 2 hold fra samme klub).  
2. Enten:  
   - i eller j er et outlier-hold, eller  
   - Hele puljen er outlier i forhold til de andre puljer. 
3. Den globale potentialfunktion forbedres:  

$$
\Phi(\{P_1',\dots,P_K'\}) < \Phi(\{P_1,\dots,P_K\})
$$



### Algoritme

1. **Initialisering:**  
   - Vælg jævnt fordelte “seed”-hold geografisk.

2. **Grådig fordeling:**  
   - Fordel de resterende hold til puljer for at minimere køretid til seed-hold.

3. **Iterativ forbedring (2-opt):**  
   - Prøv alle 2-opt swaps mellem puljer.  
   - Accepter swaps hvis betingelserne ovenfor opfyldes.  
   - Gentag indtil ingen swap kan forbedre globalt potentiale.
