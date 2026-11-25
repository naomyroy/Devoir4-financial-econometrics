<div align="center">

# MATH 60231 : Devoir #4

**Professeur Anthony Sanford**

</div>

**Instructions :** Pour ce travail, vous devez travailler en groupes de trois. Je ne m'occuperai pas de la formation des groupes. Il est de votre responsabilité de trouver des membres d'équipe. Si vous avez des difficultés à constituer une équipe, vous pouvez me contacter par courriel et j'essaierai de vous mettre en contact avec d'autres personnes du cours.

Ce devoir comprend deux éléments à soumettre séparément :

1. Une rédaction en format PDF. Il s'agit de votre analyse, discussion et interprétation des résultats. Elle doit être tapée (non manuscrite) et mise en forme comme un rapport, avec des tableaux et des graphiques clairement étiquetés intégrés dans le texte. Votre rédaction ne doit contenir **aucun code**.
2. Votre code, dans le format de votre choix (fichiers `.py`, un carnet Jupyter, un fichier Markdown, etc.). Votre code doit s'exécuter du début à la fin sans erreur et doit reproduire les résultats présentés dans votre rédaction. Il doit être clairement annoté afin qu'un lecteur puisse suivre ce que vous faites et pourquoi.

Le fait de ne pas soumettre les deux éléments sera considéré comme une soumission incomplète. Si vous avez utilisé des ressources en dehors du matériel assigné au cours (y compris du code trouvé en ligne), vous devez citer la source. L'utilisation non citée de code ou de texte externe constitue du plagiat académique. Les règles de HEC Montréal concernant le plagiat s'appliquent à la fois à vos réponses écrites et à votre code.

Pour chaque résultat numérique présenté dans votre rédaction, vous devez fournir une interprétation substantielle dans le contexte de l'économétrie financière. Des énoncés tels que « la réponse est 0,043 » sans discussion supplémentaire ne recevront aucun crédit pour l'interprétation.

Il y a 3 questions en plusieurs parties. Les points pour chaque partie sont indiqués ci-dessous. Pour chaque partie, il y a trois notes possibles :

- **Crochet moins (✓−) :** Vous avez soumis quelque chose, mais cela démontre peu de compréhension, est matériellement incomplet, ou ne consiste qu'en des résultats bruts sans interprétation. Vaut 50 %.
- **Crochet (✓) :** Votre réponse est correcte et complète. Ce sera la note la plus courante. Vaut 80 %.
- **Crochet plus (✓+) :** Votre réponse est aussi bonne (ou meilleure) que la mienne — elle va au-delà de ce qui était demandé, démontre une perspicacité exceptionnelle, ou est particulièrement élégante. Vaut 100 %. Attribuée à moins de 10 % des soumissions.

Si vous ne soumettez rien pour une partie, vous recevez un zéro pour cette partie.

**Remarque sur la qualité du code :** Les commentaires dans votre code qui m'aident à comprendre ce que vous tentiez de faire peuvent améliorer votre note. Un code Python particulièrement laid ou inélégant (p. ex. du copier-coller excessif là où une boucle ou une fonction ferait l'affaire, l'absence d'opérations vectorielles) peut réduire votre note de 10 %. Vous pouvez également perdre des points si votre code ne s'exécute pas correctement ou produit des résultats incompatibles avec votre rédaction.

---

## Problème 1 [50 pts], Tests de modèles d'évaluation d'actifs

**a.** Écrivez une fonction `Q1_KenFrenchData()` qui ne prend aucun argument et retourne deux objets : **[10 pts]**

- `factors` : un DataFrame contenant six colonnes de données mensuelles de janvier 1980 à décembre 2021, dans l'ordre suivant, tirées de la Bibliothèque de données du Pr. Ken French :
  - les cinq facteurs de `F-F_Research_Data_5_Factors_2x3` (Mkt-RF, SMB, HML, RMW, CMA)
  - le facteur momentum de `F-F_Momentum_Factor`
- `portfolios` : un DataFrame contenant les rendements mensuels des 17 Industry Portfolios

Assurez-vous que toutes les séries dans `factors` et `portfolios` sont des excédents de rendement (c'est-à-dire nets du taux sans risque). Vous pouvez utiliser `pandas_datareader` ou toute autre méthode. Présentez et interprétez les cinq premières lignes et les statistiques descriptives de chaque objet.

**b.** Fournissez des statistiques descriptives et des graphiques appropriés pour vos données. **[10 pts]**

Vos graphiques doivent inclure au minimum : (i) un graphique de séries temporelles des six facteurs ; (ii) un graphique de séries temporelles des excédents de rendement des 17 portefeuilles sectoriels ; (iii) une carte de chaleur des corrélations entre les facteurs. Assurez-vous que tous les graphiques sont clairement étiquetés. Dans votre discussion, abordez : quels facteurs sont les plus volatils ? Quels portefeuilles sectoriels ont les excédents de rendement moyens les plus élevés et les plus bas ? Que suggère la structure de corrélation des facteurs quant à la colinéarité potentielle dans les régressions d'évaluation d'actifs ?

**c.** Écrivez une fonction `Q1_AssetPricingModel()` qui accepte : **[20 pts]**

- un DataFrame `Factors` ($T \times K$) de rendements de facteurs
- un DataFrame `Assets` ($T \times N$) d'excédents de rendement sur des actifs ou des portefeuilles
- un booléen `Traded` indiquant si les facteurs sont des excédents de rendement sur des actifs négociés

La fonction estime le modèle linéaire d'évaluation d'actifs en deux passes :

- **(i) Passe en séries temporelles :** Pour chaque actif $i = 1, \ldots, N$, régresser $r_{it}$ sur une constante et les $K$ facteurs :

$$
r_{it} = \alpha_i + \boldsymbol{\beta}_i' \mathbf{F}_t + \varepsilon_{it}
$$

  pour obtenir les expositions estimées $\hat{\boldsymbol{\beta}}_i$ et les interceptes $\hat{\alpha}_i$.

- **(ii) Passe transversale :** Régresser les $N$ moyennes temporelles des excédents de rendement $\bar{r}_i$ sur les expositions estimées $\hat{\boldsymbol{\beta}}_i$ pour obtenir les primes de risque $\hat{\boldsymbol{\lambda}}$.

La fonction affiche un résumé des résultats et retourne :

- un tableau $K \times 1$ de primes de risque estimées $\hat{\boldsymbol{\lambda}}$
- un tableau $K \times 1$ de leurs statistiques $t$ robustes à l'hétéroscédasticité
- un tableau $N \times K$ d'expositions aux facteurs estimées $\hat{\boldsymbol{\beta}}$
- un tableau $N \times K$ de leurs statistiques $t$ robustes à l'hétéroscédasticité
- un scalaire $R^2$ de la régression transversale
- un tableau $3 \times 1$ contenant : la statistique du test $J$, sa valeur $p$, et les degrés de liberté de sa distribution sous $H_0$, pour $H_0$ que le modèle explique adéquatement toutes les différences transversales en excédents de rendement espérés

Appliquez votre fonction aux données de la partie (a) avec `Traded=True` et `Traded=False`. Présentez et interprétez les résultats des deux appels.

**d.** Utilisez vos résultats de la partie (c) pour répondre à chacune des questions suivantes en 2–3 phrases. **[10 pts]**

- Quels facteurs (le cas échéant) obtiennent une prime de risque statistiquement significative lorsqu'on considère les facteurs comme des excédents de rendement sur des actifs négociés ?
- Le test $J$ suggère-t-il que le modèle est bien spécifié, ou certains portefeuilles commandent-ils une prime non expliquée par les six facteurs ?
- Pourquoi la distribution nulle de la statistique du test $J$ diffère-t-elle selon que `Traded` est `True` ou `False` ? Fournissez l'intuition économique.
- Étant donné que les six facteurs (Mkt-RF, SMB, HML, RMW, CMA, Mom) sont des excédents de rendement sur des portefeuilles négociés autofinancés, quelle version du modèle — `Traded=True` ou `Traded=False` — est théoriquement appropriée ici, et pourquoi ?

---

## Problème 2 [35 pts], Estimation par maximum de vraisemblance

Pour ce problème, utilisez l'excédent de rendement du marché (**Mkt-RF**) comme variable dépendante et les cinq autres facteurs (**SMB, HML, RMW, CMA, Mom**) comme régresseurs, en utilisant le même jeu de données mensuel 1980–2021 construit au Problème 1.

**a.** Écrivez une fonction `Q2_OLSStudentT()` qui accepte : **[8 pts]**

- un DataFrame `df` (la première colonne est la variable dépendante, les colonnes restantes sont les régresseurs)
- un entier `dof` $> 0$

et effectue les étapes suivantes :

1. (i) effectue une régression MCO de la première colonne de `df` sur une constante et les autres colonnes
2. (ii) calcule les résidus de la régression $\hat{e}_t$ et leur écart-type estimé $\hat{\sigma}$
3. (iii) calcule la log-vraisemblance du modèle sous l'hypothèse que les résidus standardisés $\hat{e}_t / \hat{\sigma}$ sont i.i.d. $t(\texttt{dof})$

et retourne la log-vraisemblance `llf`.

Appliquez votre fonction avec `dof` $= 5$ et `dof` $= 30$. Présentez les deux log-vraisemblances et expliquez la différence : qu'est-ce qu'une log-vraisemblance plus élevée à `dof` $= 5$ par rapport à `dof` $= 30$ (ou vice versa) vous indique sur le comportement des queues des résidus MCO ?

**b.** Écrivez une fonction `Q2_TestRV()` qui accepte : **[9 pts]**

- un DataFrame `df`
- un entier `dof` $> 0$

et utilise `Q2_OLSStudentT(df, dof)` pour calculer la statistique du rapport de vraisemblance testant $H_0$ que tous les coefficients autres que la constante sont égaux à zéro. La fonction retourne :

- la statistique RV
- sa valeur $p$

Appliquez votre fonction avec `dof` $= 5$. Présentez et interprétez : (i) quel est le modèle contraint correspondant à $H_0$, et comment sa log-vraisemblance se compare-t-elle au modèle non contraint ? (ii) quelle est la conclusion du test RV quant à savoir si les cinq facteurs prédisent conjointement l'excédent de rendement du marché ? (iii) la statistique RV change-t-elle si vous utilisez `dof` $= 30$ à la place ? Expliquez pourquoi ou pourquoi pas.

**c.** Écrivez une fonction `Q2_VraisemblanceProf()` qui accepte : **[9 pts]**

- un DataFrame `df`

et évalue `Q2_OLSStudentT(df, dof)` pour $\texttt{dof}_j = e^{j/12}$, $j = 0, 1, \ldots, 50$, en produisant un graphique de vraisemblance profilée de la log-vraisemblance en fonction des degrés de liberté. La fonction retourne un tableau $51 \times 2$ dont la première colonne est $\texttt{dof}_j$ et la deuxième colonne est $\texttt{llf}_j$.

Appliquez votre fonction et incluez le graphique dans votre rédaction. Présentez et interprétez : (i) décrivez la forme de la courbe de vraisemblance profilée — est-elle monotone, unimodale, ou présente-t-elle plusieurs pics ? (ii) approximativement où la vraisemblance profilée atteint-elle son maximum, et qu'est-ce que cela implique pour l'estimateur du maximum de vraisemblance des degrés de liberté ?

**d.** En utilisant la courbe de vraisemblance profilée de la partie (c), identifiez l'EMV approximatif des degrés de liberté et fournissez une interprétation complète. **[9 pts]**

Votre réponse doit aborder les quatre points suivants :

- **(i) L'estimateur lui-même :** Lisez l'EMV approximatif de `dof` sur votre graphique. La réponse est l'une des suivantes : $\approx -1\,370$ ; $\approx 1$ ; $\approx 15$ ; ou $> 60$. Indiquez laquelle et expliquez comment vous la lisez sur la vraisemblance profilée.
- **(ii) Comportement des queues :** Une distribution $t(\nu)$ converge vers la loi normale standard lorsque $\nu \to \infty$ et devient de plus en plus à queues épaisses lorsque $\nu \to 2^+$ (avec une variance indéfinie pour $\nu \le 2$ et une espérance indéfinie pour $\nu \le 1$). Que signifie votre $\nu$ estimé pour l'épaisseur des queues de la distribution conditionnelle des rendements mensuels des facteurs boursiers ? Est-ce cohérent avec les faits stylisés sur les rendements financiers ?
- **(iii) Élimination des alternatives :** Expliquez spécifiquement pourquoi une distribution de Cauchy ($\nu = 1$) et une distribution quasi-normale ($\nu > 60$) sont toutes deux implausibles pour les rendements mensuels d'actifs. Utilisez la forme de la vraisemblance profilée comme preuve, et non uniquement votre intuition préalable.
- **(iv) Conséquences pour le Problème 1 :** Les statistiques $t$ et le test $J$ dans `Q1_AssetPricingModel()` supposent que les résidus MCO sont suffisamment bien comportés pour une inférence asymptotique standard. Votre $\nu$ estimé vous préoccupe-t-il quant à la validité de ces statistiques de test ? Expliquez.

---

## Problème 3 [15 pts], ACP et modèles factoriels

Ce problème ne nécessite aucun code. Vous démontrerez que vous pouvez appliquer la logique de l'analyse en composantes principales et des modèles factoriels linéaires à un problème réel de votre choix.

**a.** Trouvez un article de presse récent (publié au cours des deux dernières années) décrivant un problème en économie, finance, ou un domaine connexe que vous pensez pouvoir être abordé par l'ACP ou un modèle factoriel. Fournissez une référence complète (auteur(s), titre, publication, date, et URL ou DOI) et rédigez 2–3 phrases résumant le problème décrit. **[3 pts]**

**b.** Formulez une hypothèse vérifiable découlant de l'article. Votre hypothèse doit être suffisamment spécifique pour être réfutable : elle doit avancer une affirmation claire sur le nombre de facteurs systématiques, la direction de certaines expositions factorielles, ou la fraction de variance expliquée par les composantes principales dominantes. **[3 pts]**

**c.** Décrivez avec précision comment l'ACP serait appliquée pour répondre à votre hypothèse. Précisez : (i) quelle matrice $\mathbf{X}$ ($T \times N$) vous construiriez (que représentent les lignes et les colonnes ?) ; (ii) si vous centreriez ou standardiseriez $\mathbf{X}$ avant d'effectuer l'ACP et pourquoi ; (iii) quel critère vous utiliseriez pour sélectionner le nombre de composantes principales $K$ à retenir. **[3 pts]**

**d.** Rédigez le modèle mathématique de votre ACP dans son intégralité. Soit $\mathbf{X}$ votre matrice de données $T \times N$. Définissez la matrice de covariance $\boldsymbol{\Sigma}$, sa décomposition propre $\boldsymbol{\Sigma} = \mathbf{V} \boldsymbol{\Lambda} \mathbf{V}'$, et l'approximation à $K$ facteurs $\mathbf{X} \approx \mathbf{F} \mathbf{V}_K'$ où $\mathbf{F} = \mathbf{X} \mathbf{V}_K$ est la matrice $T \times K$ des scores factoriels. Précisez ce que représentent $\boldsymbol{\Lambda}$ et $\mathbf{V}_K$ dans le contexte de votre application spécifique. **[3 pts]**

**e.** Expliquez comment vous convertiriez votre ACP en modèle factoriel linéaire dans l'esprit de `Q1_AssetPricingModel()` du Problème 1. Plus spécifiquement : (i) comment les colonnes de $\mathbf{V}_K$ (les vecteurs propres) correspondent-elles aux expositions factorielles ? (ii) comment testeriez-vous si les $K$ facteurs extraits portent une prime de risque statistiquement significative ? (iii) quelles données vous seraient nécessaires et qui n'auraient pas été requises pour l'étape d'ACP seule ? **[3 pts]**