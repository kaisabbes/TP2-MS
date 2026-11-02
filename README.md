# TP2 – Service web REST « Calculatrice » (Compte Rendu & Réponses)

---

## 3. Partie A – Conception du service (A.2)

1. **Pourquoi les calculs directs utilisent-ils le verbe GET et non POST ?**  
   Un calcul direct est juste une lecture : il ne modifie rien sur le serveur. `GET` est fait pour ça (il est sûr et sans effets secondaires). `POST`, à l'inverse, sert à créer ou modifier des données sur le serveur.

2. **Dans `/calcul/somme?a=2&b=3`, comment s'appelle la partie située après le point d'interrogation ?**  
   C'est la **chaîne de requête (query string)**. Les éléments `a=2` et `b=3` sont des paramètres de requête sous forme de paires clé-valeur.

3. **Quelle différence entre `/operations` et `/operations/3` ?**  
   `/operations` représente toute la collection des opérations, tandis que `/operations/3` cible une ressource précise dans cette collection (l'opération numéro 3).

4. **Quel code de statut HTTP pour `/operations/99` si cette opération n'existe pas ?**  
   **404 Not Found**.

---

## 5. Partie C – Premier service REST : la somme

### Question C.1. Remplacez GET par POST dans Postman. Quel code de statut obtenez-vous ? Expliquez-le.
* **Statut :** 405 Method Not Allowed.
* **Explication :** L'URI existe, mais la méthode Java n'écoute que le verbe `GET` (`@GET`). JAX-RS renvoie donc un 405 pour indiquer que la ressource est là, mais que le verbe HTTP utilisé n'est pas autorisé.

---

## 6. Partie D – Compléter la calculatrice

### Question D.1. Quelle valeur JAX-RS donne-t-il à un paramètre entier absent ?
Comme le paramètre est un type primitif `int`, JAX-RS lui assigne la valeur par défaut de Java, soit **`0`**. Par exemple, `somme?a=2` calcule $2 + 0 = 2$.

### Question D.2. Quel statut obtient-on quand le paramètre n'est pas un entier ? La méthode somme a-t-elle été exécutée ?
* **Statut :** 404 Not Found (car la conversion du paramètre échoue avant d'atteindre la méthode).
* **Exécution :** Non, la méthode n'est pas exécutée du tout ; l'erreur est interceptée par Jersey pendant la lecture des paramètres.

### Question D.3. Différence entre `@QueryParam` et `@PathParam` ? Laquelle pour lire l'identifiant dans `/operations/{id}` ?
* `@QueryParam` lit les paramètres dans l'URL après le point d'interrogation (`?param=valeur`), parfaits pour les filtres ou options.
* `@PathParam` lit les variables directement intégrées dans le chemin de l'URL (définies par `{nom}` dans `@Path`).
* Pour `/operations/{id}`, on utilise **`@PathParam("id")`**.

---

## 7. Partie E – Échanger des données en JSON

### Question E.1. Aucune ligne de code n'écrit ce JSON. Qui a effectué la conversion ? D'où viennent les noms `a`, `b`, `operation`, `resultat` ?
* **Qui l'a fait :** C'est **Jersey** (via JSON-B / Eclipse Yasson) qui convertit automatiquement l'objet Java en JSON grâce à l'annotation `@Produces(MediaType.APPLICATION_JSON)`.
* **D'où viennent les noms :** Des accesseurs (les getters) de la classe `Resultat` (convention JavaBean : `getResultat()` devient `resultat`, `getA()` devient `a`, etc.).

### Question E.2. Pourquoi la seconde requête (`Accept: application/xml`) est-elle refusée ?
Le client demande explicitement du XML, mais le service n'est configuré (via `@Produces`) que pour renvoyer du JSON. Le serveur refuse donc de répondre dans un format non supporté et renvoie un **406 Not Acceptable**.
