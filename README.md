# Modélisation de la relation entre tests positifs et hospitalisation.
Modélisation des hospitalisations causées par le COVID 19 en Angleterre. 

### Données

Le Royaume-Uni a un <a href="https://coronavirus-data-gov-uk.translate.goog/?_x_tr_sl=auto&_x_tr_tl=fr&_x_tr_hl=fr">site web de données COVID-19</a> avec une API publique permettant d'examiner le nombre quotidien de test COVID-19, les admissions à l'hôpital, les décès et d'autres statistiques concernant la pandémie.
Durant ce projet je m'intéresse, essentiellement à déterminer la relation qu'il y a entre les tests positifs et les admissions à l'hôpital quelques temps plus tard. 
Parce que les tests n'étaient pas toujours cohérents au début de la pandémie, je concentre mon analyse sur la "deuxième vague" d'automne, et j'utilise les données concernant les test positifs à partir de mai 2020, l'idée est d'avoir une fenêtre de 60 jours pour lier les admissions à l'hôpital avec les tests. Cela permet de prendre en compte la possibilité d'un long délai de corrélation asynchrone.

De même, puisque les premières doses ont été administrées fin décembre 2020 en Angleterre, nous avons coupé notre période d'analyse au 31 décembre 2020, afin d'être certain que nous adaptons le modèle à une période 
où aucun vaccin n'avaient été administrés.


### Modèle

Pour examiner les admissions à l'hôpital, nous devons développer un modèle qui prédit deux choises:

- Combien d'admission en hôpital en fonction du nombre de test COVID positif ?
- Quand ces admissions à l'hôpital se produisent ? 

Pour répondre à ces questions je procède de la façon suivante.
1. Tous les test positifs ont une probabilité fixe ψ d'être suivi par une hospitalisation à une date ultérieure.
2. Pour ceux qui seront admis à l'hôpital, nous dévons déterminer quand la personne sera hôspitaliser. Pour modéliser la date d'admission je procède comme suit:
- Chaque jour, un patient atteint du COVID 19 qui nécessitera éventuellement une hospitalisation a une probabilité fixe p de l'aggravation de leur état.
- Une fois que leur état s'est aggravé k fois, ils sont admis à l'hôpital.
- Ce processus suit une <b> distribution binomial négative</b>, décalée de k jours. Une façon équivalente de voir cela est de dire que la date d'admission à l'hôpital suit une distribution multinomiale.

Une distribution binomiale négative décrit le nombre d'essaies de Bernoulli réussis attendu avant d'obtenir un nombre fixe d'échecs. Dans notre cas ici il s'agit du nombre de jours passé pour une personne atteinte du COVID avant d'obtenir k aggravation succésive.


Une fois que nous pouvons prédire le nombre d'admissions à l'hôpital et quand elles se produisent, il nous suffit de fournir au modèle le nombre de tests positifs chaque jour et le modèle produira une prédiction du nombre d'dmissions à l'hôpital chaque jour. Ceci peut ensuite comparé aux données pour estimes les valeurs de ψ,p et k.


### Inférence bayésienne

Pour ajuster le modèle et tenir compte de l'incertitue j'utilise l'inférence bayésienne. L'idée est calibrer les paramètres de notre modèle θ=(ψ,p,k) de tel sorte qu'ils collent le plus à nos données. Pour calibrer nos paramètres nous utilisons la règle de bayes:

<center>
  <img src="https://render.githubusercontent.com/render/math?math=Pr(\theta | y) = \frac{Pr(y | \theta)   Pr(\theta)}{Pr(y)}" style="text-align:center">
</center>

Nous devons spécifier nos croyances initales sur ce que nous pensons être des paramètres de modèle raisonnables. Je fais l'hypothèse (conforme aux données) qu'une fraction faible des tests positifs entraîne des hospitalisations. Je peux quantifier cela grâce à une distribution bêta, une distribution flexible bien adaptée à la modélisation de paramètres qui sont contraints entre  0 et 1. Les distributions bêta ont deux paramètres de forme α et β qui contrôlent où la distribution a la densité la plus élevée. Ainsi nous souhaitons que notre paramètre ψ soit de faible probabilité, donc nous choisissons α = 0.5 et β = 2, ce qui place la majeur partie de la masse de distribution à des valeurs plus petites.

Pour k et p, nous utilisons une distribiution uniforme ainsi nous excluons aucun scénario, une distribution uniforme discrète pour k entre 1 et 20 et une distribution Béta avec  α = 1 et β = 1.

Pour approximer la vraisemblance   <img src="https://render.githubusercontent.com/render/math?math=Pr(y | \theta)  " style="text-align:center"> j'utilise la calcul bayésien approximatif.
Plutôt que d'exiger que le modèle proposé produise le même ensemble d'admissions à l'hôpital que celui observé dans les données, nous essaierons plutôt de trouverun modèle assez proche des données. J'utiliserai également une mesure gaussienne de dissemblance qui pénalise les modèles qui montrent une grande différence entre les données réelles et les prédictions du modèles.


### Échantillonnage séquentiel de Monte Carlo

work in progress...
