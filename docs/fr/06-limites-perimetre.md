# Chapitre 6 -- Limites et perimetre de ce parcours

Ce parcours couvre l integralite du code source de ce depot : le contrat
`src/Paymaster.sol` et le paquet `signer/` (`user_operation.go`,
`signer.go`).

Sont volontairement laisses hors champ : le service HTTP
`paymaster.base.org` lui-meme (politique de sponsoring, limites de taux,
liste d autorisation de comptes ou d applications) qui n est pas dans ce
depot ; la bibliotheque `BasePaymaster` d eth-infinitism, dont ce contrat
herite mais qui appartient au depot `eth-infinitism/account-abstraction` ;
et le contenu du dossier `audits/`, qui documente les changements faits en
reponse a un audit externe mais ne fait pas partie de la logique metier
elle-meme.

L objectif reste le meme que pour les parcours precedents de cette
bibliotheque : comprendre precisement comment un contrat relativement
court transforme une signature hors chaine en une garantie on-chain que
quelqu un d autre paiera le gas, sans pretendre couvrir l integralite de
l infrastructure ERC-4337 de Base.
