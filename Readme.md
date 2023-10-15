# Become GraphGuru

This repo is your starting point to work on The Graph during Workshop.

This repo contains step-by-step instructions on building a subgraph and using it's endpoint to showcase the data. 

Let's Dive into Google of Blockchain for Data Indexing!


## Deploy your Subgraph

### Description

Deploy a simple smart contract on the Polygon Mumbai testnet and deploy a subgraph for it.

### BUIDLing!!!

- Open [Remix IDE](https://remix.ethereum.org/). Create a Solidity file `GraphGuru.sol` and paste the following code.

    ```solidity
    //SPDX-License-Identifier: MIT
    pragma solidity ^0.8.7;

    contract GraphGuru {

    event ChangeNameEvent (string name);
    event ChangeTwitterNameEvent (string name);
    event TransferEvent(address from, address to, uint amount);

    string public name = "The Graph";
    string public twitterName = "graphprotocol";

    function changeName(string calldata _name) public {
        name = _name;
        emit ChangeNameEvent(_name);
    }

    function changeTwitterName(string calldata _twitterName) public {
        twitterName = _twitterName;
        emit ChangeTwitterNameEvent(_twitterName);
    }

    function transfer(address payable to) public payable {
        to.transfer(msg.value);
        emit TransferEvent(msg.sender, to, msg.value);
    }
    }
    ```
- Compile the contract and deploy it to Mumbai testnet using `Injected Provider` as the environment. Note down the contract address.
- Verify and publish your smart contract (Don't know how? See [this](https://medium.com/etherscan-blog/verifying-contracts-on-etherscan-f995ab772327))
- Go to the [Subgraph studio](https://thegraph.com/studio/) and connect your wallet.
- Click on `Create a Subgraph`. Name your subgraph and select 'Polygon Mumbai' as the blockchain network. Your subgraph is created 🤩.
- You need to install The Graph protocol CLI to work with the subgraph.
    ```
    npm install -g @graphprotocol/graph-cli
    ```
    OR
    ```
    yarn global add @graphprotocol/graph-cli
    ```
- Create an empty folder and open it in your code editor (I love VSCode ❤️). Change the directory to that folder and fire up your terminal pointing to that folder.
- Now we will initialize the subgraph using the following command
    ```
    graph init --studio SUBGRAPH_NAME
    ```
- Select `ethereum` as Protocol
- Press Enter for `subgraph slug` and `Directory to create subgraph in`.
- Select `mumbai` as our network.
- Enter the contract address of your deployed contract from above. It will automatically fetch the ABI as we have verified our contract.
- Enter the block number in which the contract is created.
- Enter `GraphGuru` as the contract name.
- Press Enter for `Index contract events as entities`.
- You have successfully created your first Subgraph. (YOU ARE AWESOMEEE! 🙇🏻‍♂️)
-------

- You need to authenticate your subgraph. Refer dashboard for the key.
     ```
    graph auth --studio 5a6288af6001705f65d514e5423b018d
    ```
- Change the directory to your deployed subgraph folder
    ```
    cd GraphGuru
    ```
- Now we build our subgraph using the following command
  ```
  graph codegen && graph build
  ```
- Final Step - Deploy your subgraph. 🥳🥳🥳
  ```
  graph deploy --studio GraphGuru
  ```
- You can give the version as `v0.1`

- Play around with your contract. Transfer some ETH / MATIC or change your name or Twitter username using the Remix IDE.


> Congrats!!! You have created and deployed your subgraph endpoint.


--- 

## Using Subgraph

### Description

Since you deployed your subgraph now it's time to use it.
To start using follow these steps: 

### BUIDLing!!!

- Create a folder/directory and open it in your code editor.

- We will create a React app. Open your terminal pointing towards the directory and run the following command
  ```
  npm create vite@latest
  ```
- Enter the Project Name as `frontend` or `client`.
- Select `React` as the framework and then select `JavaScript` (without SWC ❌).
- Run the following commands to start. (You can refer to your terminal)
  ```
  cd client
  ```
  ```
  npm install
  ```
  ```
  npm run dev
  ```

- We will also need to install `graphql` to query the data and `urql` which is a highly customizable and versatile GraphQL client for React.
  ```
  npm install graphql urql@3.0.3
  ```

- Inside the directory, go to the `src/App.jsx` and replace the existing code with
  ```javascript
  import { useState, useEffect } from 'react';
  import './App.css';
  
  function App() {
    return (
      <>
        <div>
  
        </div>
      </>
    )
  }

  export default App;
  ```

- We are going to make use of the deployed subgraph for this. See it on your dashboard.
  
- Click on `Query` button on the right and copy the Query URL to access the subgraph.

- Go back to our `App.jsx` and store the URL in a variable inside the App function.
    ```javascript
    const QueryURL = "https://api.studio.thegraph.com/query/39471/GraphGuru/version/latest"
    ```
- You need an API Key to query the data. Under the Query URL you can see the option to `Manage API Keys`. Click on that and it will take you to create it. You can also create it using [this](https://thegraph.com/studio/apikeys/) link.
![Screenshot](https://github.com/TheGraphIndia/Graph-A-Thon/raw/main/assets/47234407/81f2507f-c1fe-4eaa-a1c4-44386746502e.png)

- Click on `Create API Key`.
- You get 1,000 free queries for the API key. Enter your email address and claim your queries.
- Copy the generated API key and paste it into our query URL.

- Add the data you want to query below the `QueryURL` variable. Here we will get the data about the first 5 token details of Uniswap.
    ```javascript
    const query = `{
      changeNameEvents(first: 5) {
        id
        name
        blockNumber
        blockTimestamp
      }
   }`
    ```

- Create a client to access Uniswap. We will make of `urql` for this. Add the following import statement at the top
    ```javascript
    import { createClient } from 'urql';
    ```

- To create the client add the following code after the `query` variable
    ```javascript
    const client = createClient({
    url: QueryURL
    })
    ```

- We will make use of useEffect and useState to track the state. Add the following code in the App function
    ```javascript
    const [names, setNames] = useState([]);
    ```

    ```javascript
    useEffect(() => {
      const getNames = async () => {
        const { data } = await client.query(query).toPromise();
        console.log(data);
        setNames(data.names);
      }
      getNames();
    }, [])
    ```

- To display the data on the frontend, we will return the following.
    ```javascript
    return (
    <>
      <div>
        <h1>Information</h1>
        {names !== null && names.length > 0 && names.map((name) => {
          return (
            <div key={name.id}>
              <div>{name.id}</div>
              <div>{name.name}</div>
            </div>
          );
        })}
      </div>
    </>
  );
    ```

- Your final `App.jsx` should look like this 👇🏻 -:
    ```javascript
    import { useEffect, useState } from 'react';
    import { createClient } from 'urql';
    import './App.css';

    function App() {
    const [names, setNames] = useState([]);

    const QueryURL = "https://api.studio.thegraph.com/query/39471/GraphGuru/version/latest";

    const client = createClient({
    url: QueryURL
    });

    const query = `{
      changeNameEvents(first: 5) {
        id
        name
        blockNumber
        blockTimestamp
      }
   }`

    useEffect(() => {
      const getNames = async () => {
        const { data } = await client.query(query).toPromise();
        console.log(data);
        setNames(data.names);
      }
      getNames();
    }, [])

    return (
    <>
      <div>
        <h1>Information</h1>
        {names !== null && names.length > 0 && names.map((name) => {
          return (
            <div key={name.id}>
              <div>{name.id}</div>
              <div>{name.name}</div>
            </div>
          );
        })}
      </div>
    </>
  );
    }

    export default App;
    ```

- Run `npm run dev` to view the frontend.

- Make use of The Graph Playground in the dashboard to use different queries and display that data.= (SKY IS THE LIMIT 🌌)

> Wohooo! You did it. 🥳 Congratulationssss! Share it with the world.

---

FOR ANY QUERIES JOIN THE 👉🏻[TELEGRAM GROUP](https://t.me/TheGraph_India)👈🏻
