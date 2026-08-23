# cloison-core

Moteur CLOISON (Rust, natif + WASM) : **détection déterministe de PII**, **tokenisation
HMAC + sel + somme de contrôle**, **coffre chiffré** et **généralisation des faibles
cardinalités** (k-anonymat). C'est le composant de confiance du proxy : il compile en
natif et en `wasm32-unknown-unknown`, sans framework HTTP.

Fait partie du projet [CLOISON](https://github.com/coucagog/cloison) — proxy de
confidentialité PII compatible OpenAI. La promesse : *« vos données personnelles
n'atteignent jamais le modèle en clair, et nous ne les voyons pas »* — rendue
vérifiable par l'architecture (moteur chez le client, cloud aveugle).

## Invariants (non négociables)

1. **Zéro PII sur le plan de contrôle** — le coffre (`jeton ↔ valeur`) ne vit qu'au bord.
2. **Restaurer uniquement ce qu'on a émis** — registre d'émission par requête + MAC ;
   jamais une chaîne qui « ressemble » à un jeton (anti-collision).
3. **Échouer bruyamment** — un échec de restauration bloque ou émet un marqueur neutre,
   jamais un jeton brut ni une mauvaise valeur en silence.
4. **Généraliser, pas tokeniser** les faibles cardinalités (sexe, ville, âge, date).
5. **Déterminisme intra-session, rotation inter-sessions** (`HMAC(clé_locataire ‖ sel_session, valeur)`).

## Usage

```bash
cargo test -p cloison-core            # unit + invariants bloquants (17)
cargo clippy -- -D warnings
cargo check --target wasm32-unknown-unknown   # build WASM
```

Voir `src/engine.rs` (`Engine::tokenize` / `Engine::restore`) et `tests/invariants.rs`.

## Licence

Apache-2.0 — voir [LICENSE](LICENSE).
