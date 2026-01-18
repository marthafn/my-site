---
title: Puljefordeling – Nørdet Forklaring
---
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@4/tex-mml-chtml.js"></script>

En række består af N hold. Vi ønsker H hold i hver pulje og får dermed K = N//H puljer P_1,...,P_K.  

Algoritmen er en blandingsalgoritme. Puljerne initialiseres ved at bestemme de K bedst spredte hold i hver 
pulje (maksimering af afstanden mellem disse initialiseringshold). Gennem en grådig heuristik vælges de nærmeste 6 hold for initialiseringsholdene således at holdet med den længste samlede køretid "vælger" sin første modspiller først og initialiseringsholdene derefter "skiftes" til at vælges en modspiller. Disse indledende puljer gives til en 2-opt improvement algoritme, der
foretager en bi-objektiv optimering, hvor den gennemsnitlige køretid i hver pulje og den (skalerede) varians i køretid mellem puljerne begge minimeres. 2-opt byt sker således på baggrund af en monotont aftagende
funktion, kaldet den globale potentialfunktion:
$$
\Phi(\{P_1,\dots,P_K\}) = (1-\alpha) \cdot \text{mean}(\bar{\textbf{P}}) + \alpha \cdot \text{var}(\bar{\textbf{P}})$$ 

Hvor 

$$\bar{\textbf{P}} = \begin{bmatrix} \bar{P_1} & ... &  \bar{P_K}  \end{bmatrix}$$

 betegner en vektor hvis elementer er den gennemsnitlige køretid for hver pulje
og 

$$\alpha \in [0,1]$$ 
kaldes "fairnessfaktoren" og styrer balancen mellem gennemsnitlig køretid og varians af køretid. Vi anvender en fairnessfaktor på 0.2



2-opt swap-operationer foretages ved:
$$
P_a' = (P_a \setminus \{i\}) \cup \{j\}, \quad
P_b' = (P_b \setminus \{j\}) \cup \{i\}
$$

Swap accepteres kun hvis følgende tre betingelser er opfyldt:

1. Klubbegrænsningen overholdes (ingen pulje får mere end 3 hold fra samme klub).  
2. Enten:  
   - i eller j er et outlier-hold, eller  
   - Hele puljen er outlier i forhold til de andre puljer. 
3. Den globale potentialfunktion forbedres:  

$$
\Phi(\{P_1',\dots,P_a',\dots, P_b',\dots,P_K'\}) < \Phi(\{P_1',\dots,P_a',\dots, P_b',\dots,P_K'\})
$$

Algoritmen fortsætter indtil ingen forbedringer er mulige.