# Mini-projet-Planification-robuste-sur-grille-A-Cha-nes-de-Markov

> **Modélisation, Planification Déterministe et Analyse Stochastique d'un Agent de Navigation.**

Ce projet explore la navigation autonome d'un agent dans un environnement spatialisé (grille 2D). Il met en évidence les limites de la **planification déterministe** classique lorsqu'elle est confrontée à la **réalité stochastique** (incertitude des mouvements, capteurs bruités, dérapages).

##  Table des Matières
1. [Problématique](#-problématique)
2. [Modélisation Mathématique](#-modélisation-mathématique)
3. [Algorithmes Implémentés](#-algorithmes-implémentés)
4. [Résultats et Expériences](#-résultats-et-expériences)
5. [Installation et Utilisation](#-installation-et-utilisation)
6. [Perspectives](#-perspectives)

---

##  Problématique

Dans un environnement parfait, un algorithme comme **A*** trouve le chemin le plus court avec une efficacité redoutable. Cependant, si les actionneurs du robot ont un taux d'erreur $\varepsilon$ (ex: glissement latéral au lieu d'avancer tout droit), une trajectoire frôlant les murs devient extrêmement dangereuse et sous-optimale en temps réel. 

L'objectif de ce projet est de :
1. Générer une politique déterministe optimale.
2. Modéliser l'incertitude via une Chaîne de Markov Absorbante.
3. Quantifier (théoriquement et empiriquement) l'impact de $\varepsilon$ sur le temps d'arrivée.

---

##  Modélisation Mathématique

### 1. Planification Déterministe
Nous utilisons une fonction d'évaluation heuristique avec la distance de Manhattan :
$$f(n) = g(n) + h(n)$$
Où $g(n)$ est le coût exact depuis le départ et $h(n)$ est l'estimation (admissible et cohérente) jusqu'au but.

### 2. Chaînes de Markov à temps discret
L'incertitude est paramétrée par un taux $\varepsilon$. L'espace d'états $S$ est divisé en **états transitoires** (cases libres) et en un **état absorbant** (le But).
La dynamique est régie par une matrice stochastique $P$ où l'évolution de la distribution spatiale de l'agent suit l'équation de Chapman-Kolmogorov :
$$\pi^{(n)} = \pi^{(0)}P^n$$

### 3. Analyse de l'Absorption (Temps de parcours)
Pour obtenir l'espérance mathématique du nombre d'étapes avant d'atteindre le but, nous mettons la matrice $P$ sous forme canonique et calculons la **matrice fondamentale $N$** :
$$N = (I - Q)^{-1}$$
La somme de la ligne correspondant à l'état initial dans $N$ donne le temps moyen théorique de parcours.

---

##  Algorithmes Implémentés

- **Moteur de recherche unifié :** Comparaison de Uniform Cost Search (UCS), Greedy Best-First, et A*.
- **Extraction de Politique :** Conversion d'un chemin statique en un dictionnaire d'actions (mapping État $\rightarrow$ Action).
- **Générateur de Matrice $P$ :** Algorithme transformant la topologie de la grille et la politique en probabilités de transition markoviennes tenant compte des collisions contre les murs.
- **Simulateur de Monte-Carlo :** Exécution de milliers de trajectoires aléatoires pour valider empiriquement le calcul matriciel.

---

##  Résultats et Expériences

### Expérience 1 : A* vs UCS vs Greedy
Comparaison de l'efficacité mémoire (nœuds explorés) et de l'optimalité du chemin :
| Algorithme | Coût (Optimalité) | Nœuds explorés |
| :--- | :---: | :---: |
| **UCS** ($h=0$) | Optimal | Élevé (Recherche aveugle) |
| **Greedy** ($g=0$) | Sous-optimal (Pièges) | Très faible |
| **A\*** ($g+h$) | **Optimal** | **Faible (Efficace)** |

<img width="693" height="676" alt="image" src="https://github.com/user-attachments/assets/99f8cef0-a09b-4c66-ab8a-1094f18f789b" />


### Expérience 2 : L'explosion combinatoire de l'incertitude
L'analyse de sensibilité faisant varier $\varepsilon$ de 1% à 40% montre deux régimes :
1. **Régime linéaire ($\varepsilon < 15\%$)** : L'agent dévie peu, la politique A* reste acceptable.
2. **Explosion exponentielle ($\varepsilon > 15\%$)** : L'agent heurte constamment les murs en essayant de raser les obstacles selon la politique A*. Le temps de parcours explose.

<img width="945" height="586" alt="image" src="https://github.com/user-attachments/assets/e2d0d3f0-bb57-41d5-b594-ebec627e50ef" />


---

##  Installation et Utilisation

### Prérequis
- Python 3.8+
- Bibliothèques scientifiques : `numpy`, `matplotlib`
