#simple nft minter

---
- [original github repo](https://github.com/SamJeffrey8/simple-nft-minter)
- [video documentation](https://www.youtube.com/watch?v=NBf8nezLIaU&t=563s)
---

Nfts are the tokens that have the quantity of exactly one.


        {-# INLINABLE mkPolicy #-}
        mkPolicy :: TxOutRef -> TokenName -> () -> ScriptContext -> Bool
        mkPolicy oref tn () ctx = traceIfFalse "UTxO not consumed"   hasUTxO           &&
                          traceIfFalse "wrong amount minted" checkMintedAmount
        where
            info :: TxInfo
            info = scriptContextTxInfo ctx

            hasUTxO :: Bool
            hasUTxO = any (\i -> txInInfoOutRef i == oref) $ txInfoInputs info

            checkMintedAmount :: Bool
            checkMintedAmount = case flattenValue (txInfoMint  info) of
                [(cs, tn', amt)] -> cs  == ownCurrencySymbol ctx && tn' == tn && amt == 1
                _                -> False

In order to make this nft we must check that the script contains the specific utxo as input. 
We will delegate this task to the `hasUTxo` helper function here we use the `any` function to see if
any of the transaction in for outreach from the transaction info inputs from the transaction info
field of the context matches the utxo for which we are validating for better clarity we have this 
function which returns the transaction info from which takes the script context let's look at 
this policy okay right now we have a policy that can only meant to burn once but of course in 
that single transaction we can mint as many tokens as we like now we think about what we actually 
want maybe we want a policy that allows us to mint just one token for the currency symbol or perhaps 
we would like to be able to min many nfts at once each with different token name it's up to us
but let's say we go with the first option we just want to mint one token so it makes sense to pass 
the token name as a parameter and we need a second condition that checks that we mean just a specific 
coin for which we have `checkMintedAmount` first of all we need access to the minted value 
there are many approaches to do that and we'll use the flattened value we can check that the
output of flatten value is exactly one triple that matches the symbol token and value that
we expect but we still have a problem to solve we need to know what the currency symbol is given 
that the currency symbol is hash of the policy it seems as if we have a chicken and a problem
here but we have this one currency symbol function that exists to solve this problem so we check 
if the currency symbol is equal to the one currency symbol we check if the token name is equal to
the token name that we have passed in and the total dom amount is equal to one. 

        policy :: TxOutRef -> TokenName -> Scripts.MintingPolicy
        policy oref tn = mkMintingPolicyScript $
            $$(PlutusTx.compile [|| \oref' tn' -> Scripts.wrapMintingPolicy $ mkPolicy oref' tn' ||])
            `PlutusTx.applyCode`
            PlutusTx.liftCode oref
            `PlutusTx.applyCode`
            PlutusTx.liftCode tn

        curSymbol :: TxOutRef -> TokenName -> CurrencySymbol
        curSymbol oref tn = scriptCurrencySymbol $ policy oref tn
        


okay then we have 
these boilerplate code right here now we are done with the on chain code and this is how the whole
minting policy would be 



now let's come to the off chain part of the code first we need a utxo and 
we need to provide one of our own however we don't need to pass that in because we can look it up
directly we can just pass the token name we need to get the list of etxos that belong to us
the plutus dot contract modules gives us utx add function which will help us look get the 
utxos at a given address and we have to pass our public key address into this function um in order 
to get our own pub key address we can use this pub key address function we write a case statement 
here that will either log an error if we have no utxos available or we'll use the first utxo in the 
list continue with the minting code here we have the value as oneness we want only one token to be 
minted and then secondly we have the token name argument to the lookups now we need to provide a 
lookup that gives access to where the utxof can be found at the end of this option mint function
we log the required information and that is it we are done with the plutus code for minting a 
simple nfd and you can connect it and test endpoints using jq on the terminal or use
postman 




okay now okay now let's look at the cl react client all right now i'll just walk you 
through this react line okay let me give npm start wait for it okay so this is the repository
of this react client has all the data that you need okay let's look at this so by the time it 
loads i'll just show you the point the functions that we use to call these apis so this is the 
path of this endpoint it's api contract and then definitions we are first getting the
end points that are available in this contract and then we are having this post request
um in this endpoint i and this is the wallet id it's one or two or three i
think that we are on 10 wallets you can activate the contract in any any wallet
that you like and this is the and that's this and we have the mint token and this
post request returns us the instance id so that instance id should be passed into this
mint token function and that instance id will be added to the url and this post request
will have the data as json which is on token name and the gmbl is what we have set as the
um nfd so you can change it to whatever you like so gmb is the short form of gimbal labs
okay so let's look at the site okay it has loaded now we have the gimbal labs logo at the background 
and this is get our endpoint let's give that okay i think i didn't start here let's kill carball
okay let's first about build it's up to date now for kabal execute about execute sample nft so 
you see here the pab back-end server runs on port 9080 but we have this react client running on
localhost 3000 so that'll be a course error here so cars is the cross origin resource
sharing so the the localhost 3000 and the localhost 1980 are in different domains so
um there won't be any resource sharing from this local host to that we can enable course
by specifying it in the backend code um but we can do that in the pluto's um there are a 
few ways to do that but the more the easier way is to um set up a proxy in our react client
so you see here this is http proxy middleware we have to use this to set up a proxy
and see it's configured in 1980 yeah proxying it into 1980 so by doing that we can get away 
with the course error so let me just get all endpoints so you see here we have the nfd contracts 
name we have the untoken as we already saw that is what we have here and then um let me just 
open up the dev console okay you see here this is what the cars error looks like i think 
this error occurred when we didn't start this server we didn't start the 1980 i think there 
are messages from that time after we started the pab server it shouldn't come you can see here
then we don't get the error now and let me give activate wallet now if i activate wallet 
the contract instance id is returned you can see it here and if i press on press mint again 
it returns instance id i just logged it there but if you go to our pab that's running here
let me zoom in okay see um here we have the instance id returned on our pab and this is the
output that we ha has come in the pab when we minted it you can and if i give enter here
you can see i'm i've entered the mock chain now and at the end of the mock chain you can 
see that the balances has gone a bit low in the wallet one um that includes the transaction fee for
minting a nft and you can see the other wallets doesn't have a lower balance and we have one gmbl 
in this wallet so that is how it's done i will share some links in the description about how to
set up a proxy um and this is the packages that you use and that's pretty much it and another thing 
that you can do is um we have the mint here and the start instance here you can maybe have a
input you can maybe have a input field here and activate whatever wallet that is put
in in this input field and you can activate that and then you can and then in this mint
also you can set up a um input field where you can specify the text for minting the nfd
instead of having this as predefined and i think that's it for this video happy blue testing
see you guys in the next video you


{-# INLINABLE mkPolicy #-}
mkPolicy :: TxOutRef -> TokenName -> () -> ScriptContext -> Bool
mkPolicy oref tn () ctx = traceIfFalse "UTxO not consumed"   hasUTxO           &&
                          traceIfFalse "wrong amount minted" checkMintedAmount
  where
    info :: TxInfo
    info = scriptContextTxInfo ctx

    hasUTxO :: Bool
    hasUTxO = any (\i -> txInInfoOutRef i == oref) $ txInfoInputs info

    checkMintedAmount :: Bool
    checkMintedAmount = case flattenValue (txInfoMint  info) of
        [(cs, tn', amt)] -> cs  == ownCurrencySymbol ctx && tn' == tn && amt == 1
        _                -> False

policy :: TxOutRef -> TokenName -> Scripts.MintingPolicy
policy oref tn = mkMintingPolicyScript $
    $$(PlutusTx.compile [|| \oref' tn' -> Scripts.wrapMintingPolicy $ mkPolicy oref' tn' ||])
    `PlutusTx.applyCode`
    PlutusTx.liftCode oref
    `PlutusTx.applyCode`
    PlutusTx.liftCode tn

curSymbol :: TxOutRef -> TokenName -> CurrencySymbol
curSymbol oref tn = scriptCurrencySymbol $ policy oref tn

type NFTSchema = Endpoint "mint" TokenName

mint :: TokenName -> Contract w NFTSchema Text ()
mint tn = do
    pk    <- Contract.ownPubKey
    utxos <- utxoAt (pubKeyAddress pk)
    case Map.keys utxos of
        []       -> Contract.logError @String "no utxo found"
        oref : _ -> do
            let val     = Value.singleton (curSymbol oref tn) tn 1
                lookups = Constraints.mintingPolicy (policy oref tn) <> Constraints.unspentOutputs utxos
                tx      = Constraints.mustMintValue val <> Constraints.mustSpendPubKeyOutput oref
            ledgerTx <- submitTxConstraintsWith @Void lookups tx
            void $ awaitTxConfirmed $ txId ledgerTx
            Contract.logInfo @String $ printf "forged %s" (show val)

mint' :: Promise () NFTSchema Text ()
mint' = endpoint @"mint" mint

nft :: AsContractError e => Contract () NFTSchema Text e
nft = do
    selectList [mint'] >> nft

mkSchemaDefinitions ''NFTSchema

mkKnownCurrencies []

