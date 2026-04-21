# Analyse d’une application Android avec code natif (JNI)

## Objectif du lab

Ce laboratoire a pour but d’apprendre à analyser une application Android qui cache sa logique critique dans une bibliothèque native (`.so`).
L’objectif final est de retrouver le secret attendu par l’application.

---

##  Outils utilisés

* ADB (Android Debug Bridge)
* Émulateur Android
* JADX (décompilation Java)
* Ghidra (reverse engineering natif)
* Outils ZIP (extraction APK)

---
## Partie 1 — Observation de l’application

### Étapes réalisées

* Installation de l’APK avec ADB
* Lancement de l’application
* Test de plusieurs entrées (test, 1234, hello…)

### Résultat

* L’application affiche un message d’erreur pour toute mauvaise entrée
* Cela montre qu’une **comparaison avec une valeur secrète** est effectuée

---
## Partie 2 — Analyse du code Java (JADX)

### Étapes réalisées

* Ouverture de l’APK avec JADX
* Analyse de `MainActivity`

### Résultat

* L’entrée utilisateur est récupérée
* Elle est envoyée à une méthode :

  ```java
  this.m.a(userInput);
  ```

### Conclusion

* La vérification n’est pas faite dans `MainActivity`
* Elle est déléguée à une autre classe

---

## Partie 3 — Analyse de la classe CodeCheck

### Code observé



### Analyse

* La méthode `bar` est **native**
* L’entrée utilisateur est convertie en **byte[]**
* La logique est donc dans une bibliothèque native

### Conclusion

* Le secret n’est pas visible en Java

---

## Partie 4 — Extraction de l’APK

### Étapes réalisées

* Extraction de l’APK
* Exploration du dossier `lib`

### Résultat

* Présence de plusieurs architectures :

  * armeabi-v7a
  * x86
* Identification du fichier :

  ```
  libfoo.so
  ```

### Conclusion

* La logique de vérification est dans `libfoo.so`

---

##  Partie 5 — Analyse avec Ghidra

### Étapes réalisées

* Import de `libfoo.so` dans Ghidra
* Analyse automatique
* Recherche de la fonction JNI

### Fonction trouvée

```
Java_sg_vantagepoint_uncrackable2_CodeCheck_bar
```

### Code observé


### Analyse

* La chaîne secrète est stockée en clair
* `strncmp` compare l’entrée utilisateur avec cette chaîne

---

## Partie 6 — Compréhension de strncmp

### Fonctionnement

```c
strncmp(chaine1, chaine2, longueur)
```

* Compare deux chaînes
* Retourne 0 si elles sont identiques

### Dans notre cas

* `__s1` = entrée utilisateur
* `local_38` = chaîne secrète

---

## 🏁 Résultat final

### 🔑 Secret trouvé

```
Thanks for all the fish
```

### Validation

* Saisie de la chaîne dans l’application
* Message de succès affiché

---

##  Conclusion générale

Ce laboratoire montre que :

* Les applications Android peuvent cacher leur logique dans du code natif (JNI)
* Le code Java seul ne suffit pas pour comprendre le fonctionnement
* Les outils de reverse engineering permettent de retrouver les secrets
* L’obfuscation par JNI n’est pas une protection forte




