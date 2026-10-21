# Note de spécialité — Data

**TP2 — ShopLite | Binôme : Mahmoud Sow et Istichad Papa Mhoma**

1. **Champs d’une commande.** Dans `Order.js`, `id`, `customerId`, `lines` et `createdAt` sont stockés ; `total` est calculé par somme de `qty × unitPrice`. Vérification : lecture de `Order.js`.
2. **Prix unitaire.** Le prix est copié dans chaque ligne pour conserver le prix historique si le catalogue change. Vérification : `PlaceOrder.js` et exécution CLI (total : 117 €).
3. **Commande absente.** `Map.get(id)` renvoie `undefined` ; l’API répond `404 Commande introuvable`. Ce comportement n’est pas documenté directement. Vérification : `InMemoryOrderRepository.js` et requête GET.
4. **Redémarrage et identifiants.** Le stockage en mémoire perd les commandes à l’arrêt ; le compteur de `container.js` repart de `CMD-0001`. Vérification : redémarrage du serveur, puis GET de `CMD-0001` (404).
5. **Schéma PostgreSQL proposé.** `orders(id PRIMARY KEY, customer_id NOT NULL, created_at NOT NULL)` et `order_lines(order_id NOT NULL REFERENCES orders(id) ON DELETE CASCADE, line_no, sku NOT NULL, qty CHECK (qty > 0), unit_price CHECK (unit_price >= 0))`, avec clé primaire `(order_id, line_no)`. Le total serait calculé par `SUM(qty * unit_price)` ; le mapping serait réalisé dans l’adaptateur secondaire `PostgresOrderRepository`.

**Tests et architecture :** 3 tests réussis, 0 échec. Le lint a détecté l’import interdit dans `domain/Order.js`, puis a réussi après sa suppression.