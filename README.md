# vsys-sdk-go
The golang library for V Systems Blockchain.

## Installing

Use `go get` to retrieve the SDK sto add it to your `GOPATH` workspace, or
project's Go module dependencies.

	go get github.com/walkbean/vsys-sdk-go
	
### Dependencies
The SDK includes a vendor folder containing the runtime dependencies of the SDK. The metadata of the SDK's dependencies can be found in the GoVendor file vendor/vendor.json.

## Usage

### Account 

#### Create Account From Seed

```go
acc := vsys.InitAccount(TestnetByte)
acc.BuildFromSeed("<SEED>", 0)
```

#### Create Account From PrivateKey
```go
acc := vsys.InitAccount(TestnetByte)
acc.BuildFromPrivateKey("<PRIVATE_KEY>")
```

### Transaction

1. Init Api

```go
// For Mainnet
vsys.InitApi("https://wallet.v.systems/api", vsys.Mainnet)
// For TestNet
vsys.InitApi("http://test.v.systems:9922", vsys.Testnet)

```

2. Make Transaction
```go
// Create Payment Transaction (send 1 vsys, attachment empty)
tx := acc.BuildPayment("<RECIPIENT_ADDRESS>", 1e8, []byte{})
vsys.SendPaymentTx(tx)
	
// Create Lease Transaction (lease 1 vsys)
tx = acc.BuildLeasing("<RECIPIENT_ADDRESS>", 1e8)
vsys.SendLeasingTx(tx)
    
// Create Cancel Lease Transaction
tx = acc.BuildCancelLeasing("<TRANSACTION_ID>")
vsys.SendCancelLeasingTx(tx)
```

### Contract

#### Register Contract

Contract type now only support : `vsys.TokenContract` or `vsys.TokenContractWithSplit`

```go
tx := acc.BuildRegisterContract(
    <CONTRACT_TYPE>,
    <MAX>,
    <UNITY>,
    <TOKEN_DESCRIPTION>,
    <CONTRACT_DESCRIPTION>)
vsys.SendRegisterContractTx(tx)
```

#### Execute Contract


##### Issue Token

```go
a := &vsys.Contract{
    Amount:     3 * 1e8,
}
// Build issue func data, Amount is necessary
funcData := a.BuildIssueData()
tx := acc.BuildExecuteContract(
    <CONTRACT_ID>,
    <FUNC_INDEX>,
    <FUNCDATA>,
    <ATTACHMENT>)
vsys.SendExecuteContractTx(tx)
```

##### Destroy Token

```go
a := &vsys.Contract{
    Amount: 4 * 1e8,
}
// Build destroy func data, Amount is necessary
funcData = a.BuildDestroyData()
tx := acc.BuildExecuteContract(
    <CONTRACT_ID>,
    <FUNC_INDEX>,
    <FUNC_DATA>,
    <ATTACHMENT>)
vsys.SendExecuteContractTx(tx)
```

##### Send Token

```go
a := &vsys.Contract{
    Amount:     3 * 1e7,
    Recipient:  "AUDRgBJjXM5zFMERzMML7pLPWikajTf8AKh",
}
// Build send func data, Amount and Recipient are necessary
funcData = a.BuildSendData()
tx = acc.BuildExecuteContract(
    <CONTRACT_ID>,
    <FUNC_INDEX>, // eg. vsys.FuncidxSend or vsys.FuncidxSendSplit
    funcData,
    <ATTACHMENT>)
// Send contract transaction
vsys.SendExecuteContractTx(tx)
```

or

```go
tx := acc.BuildSendTokenTransaction(
    <TOKEN_ID>,
    <RECEPIENT>,
    <AMOUNT>,
    <IS_SPLIT_SUPPORTED>,
    <ATTACHMENT>)
vsys.SendExecuteContractTx(tx)
```

##### Split Token

```go
a := &vsys.Contract{
    NewUnity: 1e4,
}
// Build send func data, NewUnity is necessary
funcData := a.BuildSplitData()
tx := acc.BuildExecuteContract(
    TokenId2ContractId(testToken),
    vsys.FuncidxSplit,
    funcData,
    <ATTACHMENT>) 
vsys.SendExecuteContractTx(tx)
```