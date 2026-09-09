# Chapitre 4 -- Le paquet signer en Go

Le contrat verifie une signature ECDSA, mais ne dit rien sur comment la
produire : c est le role du paquet `signer/`, cote serveur. Deux fichiers :
`user_operation.go` definit la structure `UserOperation` (le miroir Go de
la struct Solidity ERC-4337) et les methodes de hachage/signature ;
`signer.go` definit l interface `Signer` et une implementation simple par
cle privee.

`PaymasterHash` reconstruit exactement le meme `keccak256` que
`getHash` cote Solidity, mais avec l API `abi.Arguments` de go-ethereum
plutot que `abi.encode` : memes noms de champs, meme ordre, memes types
(`address`, `uint256`, `uint48`, `bytes32`). Cette symetrie stricte est ce
qui garantit que la signature produite hors chaine sera acceptee par le
contrat.

`PaymasterSign` ne signe pas directement le hash calcule : il applique
d abord `accounts.TextHash`, l equivalent Go du prefixe
`\x19Ethereum Signed Message:\n32` utilise par `personal_sign`. C est ce
meme prefixe que le contrat retire implicitement via
`ECDSA.toEthSignedMessageHash` avant de comparer les signatures -- les deux
cotes doivent s accorder sur ce format, sinon la verification echoue
silencieusement (mauvaise adresse recuperee, pas d erreur explicite).

`PrivateKeySigner`, dans `signer.go`, est la seule implementation fournie
de l interface `Signer` : elle signe avec `crypto.Sign` puis ajuste le
dernier octet (`signature[64] += 27`) pour respecter la convention Ethereum
du parametre de recuperation `v`. En production, remplacer cette
implementation (par un HSM ou un service de signature distant) suffit :
tout le reste du code -- hachage, encodage, contrat -- reste inchange.
