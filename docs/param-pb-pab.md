# Parametrised Piggy Bank 

---
[original github repository](https://github.com/eselkin/param-pb-pab) \
[video documentation](https://www.youtube.com/watch?v=yeZE5MAjFTI)

---


We're going to talk about a smart contract for the plutus application
backend which has been parameterized. By a parametrized script we mean one where the script address is parametrized by
some value. For today's example we are illustrating this parameterization with a piggybank script like the 
[gift example](https://github.com/input-output-hk/plutus-pioneer-program/blob/main/code/week02/src/Week02/Gift.hs) from
[plutus pioneer program](https://github.com/input-output-hk/plutus-pioneer-program) but where the script holds the value 
at some address with a parameterised the piggy bank script addresses are parametrized by what we call a bank
param here is the bank program the bank program is simple it's a new type which in record syntax takes a
beneficiary which is a public key hash this means there will be a different script address for each public key hash
the schema is simple with no changes from a non-parametrized script and has three endpoints put empty and inspect put add
some lovelace value to a script address empty takes your public key hash and if there are adequate funds pays them to
your wallet address initiating the transaction an inspect takes a public key hash and checks the value at the address for the
script parametrized by that hash the script does not require any datum or redeemer so the bank data
so in the bank data each are the unit value in order to make the bank param work as a parameter to a script address we need
to include the make lift template haskell for the type which auto generates the lift instances then in our make validator
[Music] then in our make validator function we slightly modify this from the prior examples to have a bank param as the
first argument the only other value that we're interested in for the make validator function is the script context which
will tell us if the transaction is signed by the beneficiary and that's the the the fourth argument here
and you'll see it's a script context so bank param script context the context helps us test things so we test two
things in our validator whether the transaction is signed by the beneficiary so we'll test that here by using the
context info and testing if that is this signatory for the beneficiary [Music] bank program and then we're going to test if there's
more than a million love lists we test a million love lists just could not have a test but then if anyone
 were emptying their piggy bank and they were still going to have to pay some lovelace as the transaction fee then
they might be losing money on emptying their piggy bank which we would really not like to have someone do you'll notice that
 the script address takes a bank param because the validator takes one bank param and the type validator one bank param
and that bank paramp here in the type validator is being lifted by the pluto dx lift code and applied to the compiled plutus tx
make validator script now let's talk about the contract endpoints so these contract functions are put inspect and empty like we
saw with the schema before the put takes one put params like we said before and the put parens allows us to generate
the bank param but also to tell how many lovelace are being paid to the script that is being identified by that bank param so take
the type validator to get this script address for the transaction and we know that the transaction is going to be paying to the 
script this amount of lovelace and then we wait for the confirmation of that transaction and we log out how much was being paid
to the script address and for the from the put parameters the second function's inspect which doesn't perform any transactions 
it just finds the script address a given public key hash so we make a bank program for the public key hash and we get the utxos at
[Music]the script address and we map out and collect the values using the view lens on the on each of those utxos
and then we log out what that value was so then the third function which is empty which is where you get all the money
[Music]takes a unit argument and produces a contract but here we don't care what that value was we only
care about the your own public key hash so the unit can be sent via json in curl with the opening closed word
brackets and so the reason it doesn't take any arguments because it already knows your public key hash and therefore
can construct a bank param for you and then spends the unspent outputs to your wallet you'll see here the lookup
verifies that the other script that at the other script it spins from your own public key and so
the bp was created using your own public key and the other script is the validator at that bank param which was created with 
your public key hash and then these three are attached together in a promise at the end points for with the give prime grab 
prime and respect which are given those keywords to be used in the schema and now we'll test this out by first we'll run the pab
well first we'll build it [Music] and because i built it already this is going to be pretty fast but if you're this is
the first time you're building it it might take a while and it's ppb because we've changed the the name of the executable to ppb 
 but it could still be some plutus dash daughter-bab if you're starting from the starter and you don't want to change
that name but you'll see here we're also compiling our contract parameterized piggy bank from the source plutus contracts
parametrized piggy bank and so the executable then is we'll do the kabul exact ppb and that'll start up our pab instance
 and we've got it running on port 9080 and let me switch over and we're going to be using postman to test this as opposed to curl
first of all we need two values whenever we create a wallet we need both the id which you could normally get from jq with one
value but we're also going to need the public key hash and so it's much easier to do that with the tests for postman but you could just as easily
make an array that gets returned from the jq and store those in the array environment variable but it's up to you how you want to do that so we'll
create the wallet we'll create a second wallet and we'll create a third wallet and then we're going to create an instance for the contract for
wallet one and an instance for the contract for wallet two and the third instance for wallet three and so using that first instance we're
gonna put here at the endpoint put using instance one to the public key hash for wallet three and we're going to be putting one
million love lists there and you'll see we get the unit output and then we're going to put from instance 2 which is from wallet 2
also to the wallet 3's public key hash and then wallet 2 instance is going to evaluate or inspect using the inspect endpoint
 what's at the script address for the public key hash for wallet three and you'll notice it just comes back with the
unit the json unit and what we need to do is we need to go back into the logs and in the logs first you can go up and you can see the
two puts the first put put one million to the script address 334 whatever which is the script address that is parametrized by that public key
hash and then wallet 2 also put a million at the same script address because the script address is the same from either places but
the wallet addresses are are not the same as those addresses so we're not paying directly to the wallet we're paying to the script address 
and you'll see here in that inspect we inspected from wallet 2 inspecting the script that's at this 33.44 address that has now 2 million
lovelace and so what we need to do from that point is from instant three which is the instance for wallet three we need to run
the empty endpoint or execute that empty contract  and so we'll do that and so now the value that was in while in that script address 334
 i should show running it on postman but i'm sorry it was run on postman and it emptied the piggy bank for wallet through i'll show you just
running that here so we did the instance three endpoint empty with no parameters it's just the unit the open close square brackets
json and so that emptied into initiated the transaction to pay to wallet three the unspec outputs at the script address and so now we'll take a look
back at the terminal when we end the pap you get the final balances and you'll see one script a one wallet has two million or a little under 2
million you would take the or take the lovelace transaction fees added to their balance and you'll see one two otherwallets that had about a million
 lovelies taken off and so that's the end of the parametrized piggy bank demo i will also include the url for our github repository that thank
you very much


    {-# LANGUAGE DataKinds                     #-}
    {-# LANGUAGE DeriveAnyClass                #-}
    {-# LANGUAGE DeriveGeneric                 #-}
    {-# LANGUAGE FlexibleContexts              #-}
    {-# LANGUAGE GeneralizedNewtypeDeriving    #-}
    {-# LANGUAGE MultiParamTypeClasses         #-}
    {-# LANGUAGE NoImplicitPrelude             #-}
    {-# LANGUAGE OverloadedStrings             #-}
    {-# LANGUAGE ScopedTypeVariables           #-}
    {-# LANGUAGE TemplateHaskell               #-}
    {-# LANGUAGE TypeApplications              #-}
    {-# LANGUAGE TypeFamilies                  #-}
    {-# LANGUAGE TypeOperators                 #-}
    {-# OPTIONS_GHC -fno-warn-unused-imports   #-}
    {-# OPTIONS_GHC -fno-warn-unused-top-binds #-}

    module Plutus.Contracts.ParameterisedPiggyBank where

    import           Control.Lens         (view)
    import           Control.Monad        hiding (fmap)
    import           Data.Aeson           (ToJSON, FromJSON)
    import           Data.Map             as Map hiding (empty)
    import           Data.Text            (Text, unpack, pack)
    import           Data.Monoid          (Last (..))
    import           Data.Void            (Void)
    import           GHC.Generics         (Generic)
    import           Plutus.Contract.Types
    import           PlutusTx             (toBuiltinData)
    import qualified PlutusTx
    import           PlutusTx.Prelude     hiding (Semigroup(..), unless)
    import qualified PlutusTx.Prelude     as Plutus
    import           Ledger               hiding (singleton)
    import           Ledger.Constraints   as Constraints
    import qualified Ledger.Typed.Scripts as Scripts
    import           Ledger.Ada           as Ada
    import           Playground.Contract  (printJson, printSchemas, ensureKnownCurrencies, stage, ToSchema)
    import           Playground.TH        (mkKnownCurrencies, mkSchemaDefinitions)
    import           Playground.Types     (KnownCurrency (..))
    import           Prelude              (IO, Semigroup (..), String, Show (..))
    import           Text.Printf          (printf)
    import           Data.Text.Prettyprint.Doc.Extras (PrettyShow (..))
    import           Plutus.Contract       as Contract
    import           Plutus.V1.Ledger.Value (Value (..), assetClass, assetClassValueOf)
    import qualified Data.Map             as Map

    -- A value which initates 
    data PutParams = PutParams
        { ppBeneficiary :: !PubKeyHash
        , ppAmount      :: !Integer
        } deriving (Generic, ToJSON, FromJSON, ToSchema, Show)

    -- A beneficiary gets their own script address
    newtype BankParam = BankParam
        { beneficiary :: PubKeyHash
        } deriving Show

    -- Endpoints of schema
    type ParameterisedPiggyBankSchema =
                Endpoint "put" PutParams
            .\/ Endpoint "empty" ()
            .\/ Endpoint "inspect" PubKeyHash

    {- 
        Typed validator instance (probably should be PiggyTyped)
        There's no Datum even though you might think there is one from PutParams
    -}
    data Bank
    instance Scripts.ValidatorTypes Bank where
        type instance DatumType Bank    = ()
        type instance RedeemerType Bank = ()

    PlutusTx.makeLift ''BankParam

    {- 
        here p is to get validate the scriptAddress needs the beneficiary to have signed the current context 
        Remember, no datum or redeemer (since it's checking the person's own signature)
    -}
    {-# INLINABLE mkValidator #-}
    mkValidator :: BankParam -> () -> () -> ScriptContext -> Bool
    mkValidator bp () () ctx =
        hasSufficientAmount &&
        signedByBeneficiary

    where
      contextTxInfo :: TxInfo
      contextTxInfo = scriptContextTxInfo ctx

      hasSufficientAmount :: Bool
      hasSufficientAmount =
          traceIfFalse "Sorry. Not enough lovelace" $ checkAmount $ inValue contextTxInfo

      signedByBeneficiary :: Bool
      signedByBeneficiary = txSignedBy contextTxInfo $ beneficiary bp

    {-# INLINABLE inValue #-}
    inValue :: TxInfo -> Value
    inValue = valueSpent

    {-# INLINABLE checkAmount #-}
    checkAmount :: Value -> Bool
    checkAmount val = assetClassValueOf val (assetClass Ada.adaSymbol Ada.adaToken) > 1000000


    typedValidator :: BankParam -> Scripts.TypedValidator Bank
    typedValidator p = Scripts.mkTypedValidator @Bank
        ($$(Plutusx.compile [|| mkValidator ||]) `PlutusTx.applyCode` PlutusTx.liftCode p)
        $$(PlutusTx.compile [|| wrap ||])
        where
            wrap = Scripts.wrapValidator @() @()


    validator :: BankParam -> Validator
    validator = Scripts.validatorScript . typedValidator

    valHash :: BankParam ->  Ledger.ValidatorHash
    valHash = Scripts.validatorHash . typedValidator

    {-
        scrAddress needs the BankParam (for the beneficiary!)
    -}
    scrAddress :: BankParam ->  Ledger.Address
    scrAddress = scriptAddress . validator

    put :: PutParams -> Contract w s Text ()
    put pp =  do
        let bp  = BankParam
                    { beneficiary = ppBeneficiary pp
                    }
            tx = mustPayToTheScript () $ Ada.lovelaceValueOf (ppAmount pp)
        ledgerTx <- submitTxConstraints (typedValidator bp) tx
        void $ awaitTxConfirmed $ txId ledgerTx
        logInfo  @String $ "Added " ++ show (ppAmount pp) ++ " to scrAddress for " ++ show (ppBeneficiary pp)

    inspect :: forall w s . PubKeyHash -> Contract w s Text ()
    inspect pkh = do
        let bp = BankParam { beneficiary = pkh}
        os  <- PlutusTx.Prelude.map snd . Map.toList <$> utxosAt (scrAddress bp)
        let totalVal = mconcat [view ciTxOutValue o | o <- os]
        logInfo @String
            $ "Logging total Value : " <> show totalVal
        logInfo @String $ "Inspect complete"

    empty :: forall w s . () -> Contract w s Text ()
    empty _ = do
        pkh <- pubKeyHash <$> ownPubKey
        let bp  = BankParam
                        { beneficiary = pkh
                        }
        utxos <- utxosAt $ scrAddress bp
        let orefs   = fst <$> Map.toList utxos
            lookups = Constraints.unspentOutputs utxos <>
                      Constraints.otherScript (validator bp)
            tx :: TxConstraints Void Void
            tx      = mconcat [mustSpendScriptOutput oref $ Redeemer $ toBuiltinData () | oref <- orefs]
        ledgerTx <- submitTxConstraintsWith @Void lookups tx
        awaitTxConfirmed $ txId ledgerTx
        logInfo @String $ "Emptied piggy bank."



    endpoints :: Contract () ParameterisedPiggyBankSchema Text e
    endpoints = do
        logInfo @String "Waiting for empty or put or inspect."
        selectList [give', grab', inspect'] >> endpoints
        where
            give' = endpoint @"put" put
            grab' = endpoint @"empty" empty
            inspect' = endpoint @"inspect" inspect

    mkSchemaDefinitions ''ParameterisedPiggyBankSchema

    $(mkKnownCurrencies [])

