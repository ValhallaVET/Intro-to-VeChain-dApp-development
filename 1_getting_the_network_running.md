I’m creating these possible series to hopefully start a dialog between those that are interested in dApp development on VeChain. I’ve created a few dApps on Ethereum (nothing published) so I have a bit of insight on how all this jazz is put together. It should be noted that i’m in no way a professional dApp developer, especially with building dApps on VeChain, so if anyone would like to chime in, correct me, or offer constructive criticism I would be grateful as it helps out me and the community. I thought about ironing out all the details and presenting one body of work but I too am learning and thought it would be nice to get feedback along the way.

This is the initial steps and is no way a complete guide to dApp development. I’ll be discussing how to connect to the VeChain blockchain locally on your computer. This should (crossing my fingers) hopefully get you in a place to interact with the smart contracts you deploy to ~~a local blockchain~~ (see edit) the testnet from your computer. I have a mac so there probably will be some differences getting this going on a PC. Hopefully we all can help each other. Enough talking lets get to it!

## Installation

To build a dApp locally you’ll need to have ~~a local~~ (see edit) the local network running the blockchain. To do that you’ll need to install [VeChain Thor](https://github.com/vechain/thor). I guess thats the name of it? Thats all it says in the repo. Anyways to install make sure you have Go installed.

To install Go, follow this [link](https://golang.org/doc/install). Once you’ve install Go, clone the VeChain Thor repo using git:

    git clone https://github.com/vechain/thor.git
    cd thor

Now in the README.md under “Installation” there is a heading entitled “Dependency management” and there are a few steps you can take. Those steps are `make dep` or to manually install dependencies you can run `dep ensure`. Because I didn’t have the source code in my gopath I kept receiving the error `is not within a known GOPATH/src`. So i just had to create my gopath and add it to my `.bash_profile`. If you still get an error check out these resources:

* [https://github.com/golang/dep/issues/148](https://github.com/golang/dep/issues/148)
* [https://github.com/golang/dep/issues/911](https://github.com/golang/dep/issues/911)
* [go - Not in any GOPATH while using dep init - Stack Overflow](https://stackoverflow.com/questions/44142289/not-in-any-gopath-while-using-dep-init)
* [go - Golang dependency management on windows does not work (GOPATH error) - Stack Overflow](https://stackoverflow.com/questions/46802512/golang-dependency-management-on-windows-does-not-work-gopath-error) (PC version?)

Really it was a headache getting that to work so I took a break from my computer and ate a cupcake.

Once you have installed the dependancies you’ll need to cd back to the `thor` repo that you just cloned and run:

`make`

or as the instructions say “if you want to build the full suite” run:

`make all`

Does anyone know the difference between the full suite and the non full suite? (see edit 2)

Once you have built the main app you are ready to run the Thor network.

## Running the Thor network

For our purposes we will want to connect to the testnet. To connect run:

`bin/thor --network test`

Again make sure you are running this command in the `thor` repo that you cloned. To connect to the mainnet replace “test” with “main”.

Per the README if you want to see all the commands and play around run `bin/thor -h` to get some help.

Once you run this you should see a lot of stuff being printed in your console. Let it sit and open up a new terminal window. You can rejoice because got the testnet running locally on your machine!

Note: These instructions are all on the [VeChain/Thor README](https://github.com/vechain/thor).

## Proxy to the Thor network

Now we will need to proxy to the network using something VeChain provides us called `web3-gear`. My educated guess is that this is the medium between the network (local blockchain??) and the services running on your client, aka web3.

You won’t need to clone the `web3-gear` repo. You’ll use a package manager to install it.

On a mac run:

`brew install openssl`

To install the dependancies needed to run `web3-gear`. Once that completes run:

`pip3 install web3-gear`

Make sure you have Python 3.6+ to run both of these steps. On a PC you’ll just have to run `pip install web3-gear` but make sure beforehand you have Visual C++ Build Tools and [script-py](https://pypi.org/project/scrypt/#files).

Once you’re done install `web3-gear` run it by calling its name “web3-gear”.

You now have the web proxy up and running and can connect to it using different tools.

Want to play around with some smart contracts? Connect to the network using [Remix](http://remix.ethereum.org/). Go to the “Run” tab and select “Web3 Provider” from the “Environment” tab. You’ll see a bunch of stuff happening in your console which is pretty neat.

If you want to continue your development locally using something like Vue, React or Node you can keep reading.

## Connect your client to the Thor network

So at this point i was pretty familiar with using `web3` to connect to the services running on the backend. But VeChain is a bit different (superior in my opinion) and had to use [Thorify](https://github.com/vechain/thorify) to adapt `web3` to work alongside the background services. For this part i’ll use React as i’ve been working with it for the past 2 years.

Lets get react up and running with [create-react-app](https://github.com/facebook/create-react-app). To install create-react-app just run:

    npx create-react-app thor-web-app
    cd thor-web-app

Next we will want to install `Thorify` and `Web3`. To do so run:

    npm install --save thorify
    npm install --save web3

Now run `npm run start` and your app just pop up.

So by now you have an app running and you are about to connect to the VeChain Thor network but you might need some smart contracts. For that I recommend [Truffle](https://truffleframework.com/). Truffle is a suite that helps compile, deploy and test your smart contracts. It does more as well but we will just need it now for compiling and deploying your smart contracts.

To install truffle run:

`npm install -g truffle`

Bow! You got truffle.

So lets get some contracts into your app. Navigate, clone or download the [truffle-webpack-demo](https://github.com/ConsenSys/truffle-webpack-demo) repo. In this repo you will find 3 things that you need. They are the `contracts` folder, the `migrations` folder, and `truffle.js`. Copy those to the root of your `thor-web-app`.

Note: On windows systems you might have trouble running `truffle` in your main folder due to the `truffle.js` file so create `truffle.config.js` and put it in the root of your `thor-web-app`.

What we just copied over are really elementary contracts but it should give you an idea of how a contract works. I’m not really gonna go into detail on understanding Solidity right now but hopefully that gives you a starting point.

We will need to configure Truffle to deploy our contracts to the right network. To do so add this to your `truffle.js` or `truffle.config.js` files:

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        }
      }
    };

Once we have those items in place we need to compile and migrate contracts.

Solidity is what our contracts are written in but we need to compile them to bytecode to deploy to ~~our local~~ the testnet (see edit) network. To do so run:

`truffle compile`

in the root of your app. This will create a `build` directory. Then we will want to migrate the newly compiled contracts to the development network that we set up previously. To do so run:

`truffle migrate --development`

Boom! Your contracts have been migrated and now should be living on the blockchain. You can now interact with your contracts.

Here is where I could have done things differently. The `build` folder creates JSON files that your app will need to read. The JSON files are essentially a blueprint of your contracts that your client will read. Because the `build` folder is built and placed in the root of our app React can’t reach those files. So I had to move the `build` folder into the `src` folder. That’s not optimal. You don’t want to have to keep moving it. I believe you can tell Truffle where to place your build files. But for now just move the `build` folder into the `src` folder.

Once you have the `build` folder inside your `src` folder its time to get that data into your app. For ease of this article I chose to put my `web3` code in `App.js` but it really can be done in many places. Open up `App.js` and import `Thorify`, `web3`, and your contract data like so:

    // ES6 style
    import { thorify } from "thorify";
    import MetaCoin from './build/contracts/MetaCoin.json';
    const Web3 = require("web3"); // Its recommended to use require instead of import
    const web3 = thorify(new Web3(), "http://localhost:8669");

If you want to write ES5 style code:

    const thorify = require("thorify").thorify;
    const MetaCoin = require('./build/contracts/MetaCoin.json');
    const Web3 = require("web3");
    
    
    const web3 = thorify(new Web3(), "http://localhost:8669");

I messed up here and thought that I needed to connect to port “8545” but that is wrong. Make sure you are connecting to port “8669”.

Now lets connect it all!

`const MetaCoinContract = new web3.eth.Contract(MetaCoin.abi);`

From the web3 docs:

>The web3.eth.Contract object makes it easy to interact with smart contracts on the ethereum blockchain. When you create a new contract object you give it the json interface of the respective smart contract and web3 will auto convert all calls into low level ABI calls over RPC for you.  
>  
>This allows you to interact with smart contracts as if they were JavaScript objects.

If you console log `MetaCoinContract` you’ll see an object with many different properties. Some of the properties you will see are `methods` and these methods should correspond to the functions inside your Solidity contract. You run these methods to execute those functions!

Alright I’m going to leave you guys now but hopefully this is a good start for you guys. What do you think? Can anything be optimized? What can be done better or different?

Also we are trying to get this VeChain open source community going so if you have an idea or need help PM me.

​

edit: it seems as though we aren't connecting to a local standalone blockchain on your local machine per the docs. This is different from my Ethereum development as you create a standalone testing blockchain. Maybe someone can provide further information. I will update the post to reflect information given by the community.

​

edit 2 (thanks u/davy_li): Running `make` without a target name will process the first Makefile target that doesn't start with `a` . (so in this case, equivalent to running `make thor`). On the other hand, `make all` will run the thor and disco targets, where "disco" is a boostrap node for the Ethereum discovery protocol.

​

​

P.S. Community site is coming along nicely.
