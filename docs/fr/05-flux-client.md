# Chapitre 5 -- Le flux cote client (paymaster.base.org)

Le README documente le protocole que suit un client qui veut faire
sponsoriser une UserOperation par le service Base, meme si le service HTTP
lui-meme n est pas dans ce depot. Le flux se deroule en six etapes.

D abord, construire l UserOperation sans paymaster et sans signature.
Ensuite, appeler `eth_paymasterAndDataForEstimateGas` sur
`https://paymaster.base.org` avec l UserOperation, l adresse de l
`EntryPoint` et le chain ID : la reponse contient un `paymasterAndData`
utilisable uniquement pour estimer le gas (signature factice, non valide
pour l execution reelle). Puis, estimer le gas aupres du bundler choisi, et
ajouter une marge sur `PreVerificationGas` et `VerificationGasLimit` pour
couvrir la verification supplementaire que le paymaster va effectuer.

Ensuite seulement, appeler `eth_paymasterAndDataForUserOperation` : cette
fois la reponse contient la vraie signature, celle que `_validatePaymasterUserOp`
va accepter. Le README insiste sur un point de securite : toute
modification de l UserOperation apres cette etape (sauf le champ `sig` final
du compte) invalide la signature, puisque `getHash`/`PaymasterHash`
couvrent tous les autres champs. Enfin, signer l UserOperation (signature du
compte, distincte de celle du paymaster) et la soumettre au bundler.

Si le service refuse (erreur ou reponse vide) a n importe quelle etape, le
README recommande explicitement de basculer vers un autre paymaster ou
l auto-financement, plutot que de bloquer l utilisateur.
