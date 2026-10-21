# ADR-0001 — Isoler le stockage derrière `OrderRepository`

- **Décideurs :** Mahmoud Sow et Istichad Papa Mhoma

## Contexte

Le module Commande utilise `InMemoryOrderRepository`, qui stocke les commandes dans une `Map`. Les données disparaissent à l’arrêt du programme. Une future version pourrait utiliser PostgreSQL pour conserver les commandes. Le domaine et le cas d’utilisation ne doivent pas dépendre d’une technologie de stockage précise.

## Décision

Nous décidons de faire passer les opérations de stockage par le port `orderRepository`, avec les méthodes `save(order)` et `findById(id)`. Les implémentations concrètes, comme le repository en mémoire ou un futur repository PostgreSQL, restent dans les adaptateurs secondaires et sont assemblées dans `container.js`.

La règle d’architecture garantit que le domaine reste indépendant des adaptateurs techniques.

## Conséquences

**Avantages :**
- Le stockage peut être remplacé sans réécrire les règles métier.
- Les tests peuvent utiliser un repository en mémoire sans lancer PostgreSQL.
- Un adaptateur PostgreSQL pourra gérer la persistance indépendamment du domaine.

**Inconvénients :**
- Chaque implémentation du repository doit être maintenue et testée.
- Le mapping entre `Order` et les tables doit être écrit et maintenu.
- Les ports et la composition root ajoutent des fichiers et une indirection, parfois coûteux pour une application simple.

## Alternatives considérées

- **Accéder directement à PostgreSQL depuis le domaine :** rejeté, car cela lierait les règles métier à une technologie et compliquerait les tests.
- **Conserver uniquement la mémoire :** non retenu pour une application devant conserver les commandes après redémarrage.
- **Utiliser une architecture en couches avec accès direct au repository concret :** non retenu, car le remplacement du stockage serait moins facile à isoler et à tester.

## Vérification

Les trois tests de `PlaceOrder.test.js` ont réussi : **3 tests réussis, 0 échec**. Le lint d’architecture a détecté l’import interdit de `FakePayment.js` dans `domain/Order.js`. Après suppression de cet import, le lint a de nouveau confirmé que la règle de dépendance était respectée.