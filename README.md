# TRC20
TRC20 base on TRON blockchain writing the string into the memo field of the transaction to achieve this.

## Token Economic
 - Token: TRXI
 - Supply: 2100000000
 - limit: 1000

## Method
 - deploy: `data:,{"p":"trc-20","op":"deploy","tick":"trxi","max":"2100000000","lim":"1000"}`
 - mint: `data:,{"p":"trc-20","op":"mint","tick":"trxi","amt":"1000"}`
 - transfer: `data:,{"p":"trc-20","op":"transfer","tick":"trxi","detail":[{"to":"TRON Address","amt":"1000"}]}`

## Deploy txid
https://tronscan.org/#/transaction/3200ca62dde62e4a79d6f6bbaf3bab3ff81a2a67164f709bc172835003ba1599

## Mint TRXI with nodejs
1. Install Node.js
2. Create a directory,such as `TRC20Mint`
3. Open `TRC20Mint`, execute command:`npm init`
4. Execute command:`npm install tronweb `
5. Create an index.js file,copy the code below
6. Run index.js:`node index.js`

```javascript
const TronWeb = require('tronweb');

const RPC_URL = "https://api.trongrid.io" // RPC 节点
const PRIVATE_KEY = "your private key"; // 输入你的密钥
const MINT_TIMES = 1000; // 铸造次数

const HttpProvider = TronWeb.providers.HttpProvider;

const fullNode = new HttpProvider(RPC_URL);
const solidityNode = new HttpProvider(RPC_URL);
const eventServer = new HttpProvider(RPC_URL);
const tronWeb = new TronWeb(fullNode, solidityNode, eventServer, PRIVATE_KEY);

const blackHole = "T9yD14Nj9j7xAB4dbGeiX9h8unkKHxuWwb";  //black hole address

const memo = 'data:,{"p":"trc-20","op":"mint","tick":"trxi","amt":"1000"}';

async function mint() {
    const unSignedTxn = await tronWeb.transactionBuilder.sendTrx(blackHole, 1); //0.000001 TRX is the minimum transfer amount.
    const unSignedTxnWithNote = await tronWeb.transactionBuilder.addUpdateData(unSignedTxn, memo, 'utf8');
    const signedTxn = await tronWeb.trx.sign(unSignedTxnWithNote);
    const ret = await tronWeb.trx.sendRawTransaction(signedTxn);
    if (ret.result) {
      console.info(`mint success, transaction: ${JSON.stringify(ret.transaction)}\n`)
    } else {
      console.error(`mint failed, error code: ${ret.code}; txID: ${signedTxn.txID}\n`)
    }
}

async function batchMint(times) {
  for (let i = 0; i < times; i++) {
    console.log(`mint ${i+1} times`)
    await mint()
  }
}

batchMint(MINT_TIMES) // 批量铸造
```

## Mint TRXI with TokenPocket Wallet
 - Receiver address:T9yD14Nj9j7xAB4dbGeiX9h8unkKHxuWwb.
 - Transfer amount 0.000001 TRX
 - Click on Advanced Settings and fill in `data:,{"p":"trc-20","op":"mint","tick":"trxi","amt":"1000"}`



## Idea for developing indexer
1. Recording the block number of deploy inscription.
2. Get all transactions of black hole address.
3. Use the FULL NODE HTTP API to get the `from`,`to`,`data` field of each txid, match all mint inscriptions. For more details, please refer to the following link: https://developers.tron.network/reference/wallet-gettransactionbyid.
4. The obtained addresses need to undergo encoding conversion to Tron wallet addresses. For more details, please refer to the following link: https://www.btcschools.net/tron/tron_tool_base58check_hex.php.

## Indexer(TBA)


## FAQ
### Why you need transfer to `T9yD14Nj9j7xAB4dbGeiX9h8unkKHxuWwb` account?
Because TRON blockchain cannot transfer to yourself address.We decided transfer to the black hole address of the TRON blockchain. Refer to the black hole address given in the official [documentation](https://developers.tron.network/docs/faq#3-what-is-the-destruction-address-of-tron) of the TRON blockchain.

### Why you need transfer to 0.000001 TRX to black hole address?
Because TRON blockchain cannot transfer zero amount, 0.000001 TRX is the minimum transfer amount.





