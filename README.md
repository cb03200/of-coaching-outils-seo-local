# Outils de mesure SEO local — OF-Coaching

Par Claude Barbey, consultant indépendant à Brugheas, près de Vichy.

## Construire un lien UTM sans service tiers

Le script Python ci-dessous ajoute les paramètres de campagne à une URL HTTP(S), conserve ses autres paramètres et remplace les anciens paramètres UTM. Il fonctionne avec la bibliothèque standard, sans installation ni appel réseau.

Ne placez jamais de nom de prospect, adresse e-mail ou autre donnée personnelle dans une URL de suivi. Les liens sont visibles et peuvent être journalisés.

```python
from urllib.parse import urlsplit, urlunsplit, parse_qsl, urlencode

def lien_utm(url, source, medium, campagne):
    p = urlsplit(url.strip())
    if p.scheme not in ("http", "https") or not p.netloc:
        raise ValueError("Utilisez une URL HTTP ou HTTPS complète")
    valeurs = {"utm_source": source, "utm_medium": medium, "utm_campaign": campagne}
    if any(not valeur.strip() for valeur in valeurs.values()):
        raise ValueError("Source, medium et campagne sont obligatoires")
    params = [(k, v) for k, v in parse_qsl(p.query, keep_blank_values=True)
              if not k.lower().startswith("utm_")]
    params.extend((k, v.strip()) for k, v in valeurs.items())
    return urlunsplit((p.scheme, p.netloc, p.path, urlencode(params), p.fragment))

print(lien_utm("https://example.org/contact?lang=fr#formulaire",
               "linkedin", "social", "guide-local"))
```

Résultat attendu :

```text
https://example.org/contact?lang=fr&utm_source=linkedin&utm_medium=social&utm_campaign=guide-local#formulaire
```

## Mesurer une évolution du nombre d’avis

Variation absolue = total final − total initial. Variation relative = variation / total initial × 100, uniquement si le total initial est supérieur à zéro. Exemple fictif : de 10 à 15 avis, la hausse est de 5 avis, soit 50 %. Ce calcul ne démontre aucune hausse des ventes.

## Comparer deux cartes de positions

Conservez la même requête, les mêmes coordonnées et le même moteur. Une valeur « 20+ » est une observation censurée, pas la position 21. Comparez la proportion de points classés 1 à 3 en affichant le nombre de points mesurés et les dates. Écartez les points manquants de façon explicite.

## Grille mensuelle

| Période | Source | Impressions | Clics | Contacts réels | Contacts qualifiés | Ventes confirmées | Actions |
|---|---|---|---|---|---|---|---|
| À compléter | À compléter | | | | | | |

Comparez des périodes de même durée. Une donnée indisponible reste vide, elle ne vaut pas zéro. Notez les campagnes, jours de fermeture et changements de site. Un clic sur le téléphone n’est pas nécessairement un appel abouti.

## Limites et attribution

Les UTM facilitent l’attribution dans un outil configuré pour les lire ; ils ne mesurent rien seuls et n’améliorent pas le classement. Les données locales et les réponses d’IA peuvent varier. Ces ressources ne garantissent aucune position.

Ressource publiée par [OF-Coaching](https://of-coaching.fr/audit-seo-gratuit/). Version du 18 septembre 2026.
