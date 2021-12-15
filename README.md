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
1. Tous les test positifs ont une probabilité fixe $\psi$
