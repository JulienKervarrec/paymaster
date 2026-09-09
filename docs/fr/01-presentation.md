# Chapitre 1 -- Presentation de paymaster

Ce depot contient un seul contrat Solidity, `src/Paymaster.sol`, et un petit
paquet Go, `signer/`. Ensemble ils forment un paymaster ERC-4337 : un
contrat qui accepte de payer le gas d une UserOperation a la place du
compte qui l envoie, a condition qu un signataire externe de confiance ait
approuve cette UserOperation au prealable.

Le contrat est un clone du `VerifyingPaymaster` de la bibliotheque
eth-infinitism (la reference du standard ERC-4337), auquel Base a ajoute
une fonction `receive()` pour les depots simples et quelques changements
suite a un audit. Le paquet Go n est pas un service : c est un exemple de
comment produire, cote serveur, la signature que le contrat va verifier
on-chain.

Le service de production qui utilise ce contrat est expose derriere
`https://paymaster.base.org`, une API JSON-RPC a deux methodes :
`eth_paymasterAndDataForEstimateGas` et `eth_paymasterAndDataForUserOperation`.
Ce parcours documente le contrat et le paquet de signature ; le service
HTTP lui-meme n est pas dans ce depot et reste hors perimetre.
