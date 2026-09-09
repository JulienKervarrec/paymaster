# Chapitre 2 -- Le contrat Paymaster.sol

`Paymaster` herite de `BasePaymaster` (de la bibliotheque account-abstraction
d eth-infinitism), qui gere deja le depot de fonds aupres de l `EntryPoint`
et le squelette du hook `validatePaymasterUserOp`. Le contrat ajoute un seul
etat propre : `verifyingSigner`, une adresse immuable fixee au deploiement.

Le constructeur verifie trois choses avant d accepter cette adresse :
l `EntryPoint` passe doit etre un contrat deploye (`code.length > 0`),
`verifyingSigner` ne doit pas etre l adresse zero, et il ne doit pas etre
egal a `msg.sender` (le proprietaire). Cette derniere regle separe
volontairement deux roles : le proprietaire administre le contrat
(deposits, retraits, transfert de propriete), le signataire approuve les
UserOperations au cas par cas. Melanger les deux romprait cette separation
des pouvoirs.

Le contrat redefinit aussi `renounceOwnership()` pour qu elle echoue
toujours (`revert`), et `transferOwnership()` pour interdire de transferer
la propriete au signataire lui-meme. Deux garde-fous qui empechent de
casser accidentellement l invariant proprietaire != signataire.
