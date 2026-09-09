# Chapitre 3 -- Le flux de validation on-chain

Quand l `EntryPoint` traite une UserOperation qui porte l adresse de ce
paymaster, il appelle `_validatePaymasterUserOp`. Cette fonction fait trois
choses dans l ordre : elle decoupe `paymasterAndData` en ses trois parties,
verifie la longueur de la signature, puis verifie la signature elle-meme.

Le decoupage est fait par `parsePaymasterAndData`, qui lit
`paymasterAndData` par tranches d octets fixes : les 20 premiers octets
sont l adresse du paymaster (deja consommee par l `EntryPoint` avant
l appel), les 64 suivants encodent `validUntil` et `validAfter`
(deux timestamps uint48), et tout le reste est la signature. Seules les
signatures de 65 octets sont acceptees -- une longueur differente est
rejetee immediatement, ce qui limite les vecteurs de rejeu de signature.

`getHash` reconstruit le message qui a ete signe hors chaine : un
`keccak256` sur le sender, le nonce, les hachages du `initCode` et du
`callData`, les limites de gas, `block.chainid`, l adresse du paymaster
lui-meme et les deux timestamps de validite. Le contrat recalcule ce hash
on-chain et verifie, via `ECDSA.recover`, qu il correspond bien a
`verifyingSigner`. En cas d echec, la fonction ne revert pas : elle
retourne `_packValidationData(true, ...)`, le code standard ERC-4337 pour
signaler un echec de validation sans faire echouer toute la transaction du
bundler.
