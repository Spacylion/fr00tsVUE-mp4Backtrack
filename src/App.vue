


<template>

  <div class="side">
    <div class="connect-button fixed right-0 top-0 z-20 " v-if="!getUserAccount">
      <button type="button" id="connect-wallet" class="button" @click.prevent="onConnect">
        Connect wallet
      </button>
      <a href="https://twitter.com/fr00ts">
        <img src="./images/follow.png" class=" fixed left-4 top-4" alt="">
      </a>
    </div>
    <div v-else class="col-auto" style="height: 145px">
      <h1 href="">CONNECTED</h1>
    </div>
    <div class="video-container">
      <iframe
        src="https://www.youtube.com/embed/yCYHHWZjuDg?controls=0&autoplay=1&mute=1&playsinline=1&loop=1&playlist=yCYHHWZjuDg"></iframe>

      <div class="w-screen h-screen flex flex-col justify-between items-center content-center relative 
      transition-all ease-out duration-500">
        <div class="relative mx-4 w-5/6 h-28 md:h-1/4 lg:h-1/3 mt-20 md:mt-12 transition-all ease-out duration-500 ">
          <span>
            <img alt="Head" src="./images/headNEW.png"
              class="absolute inset-0 box-border p-0 m-auto block w-0 h-0 min-w-full max-w-full min-h-full max-h-full object-contain" />
          </span>
        </div>
        <div
          class="relative mx-4 w-1/4 h-1/4 rad sm:p-0 md:h-1/4 lg:h-full md:mt-12 transition-all ease-out duration-500 ">
          <img alt="Head" src="./images/head.jpg"
            class=" rounded-3xl  min-w-full max-w-full min-h-full max-h-full object-contain" />
        </div>
        <div class="">
          <div class="relative mx-4 w-1/2 h-28 md:h-1/4  lg:h-1/3 mt-20 md:mt-12 transition-all ease-out duration-500 ">
            <span>
              <img alt="Head" src="./images/ooo11.png"
                class="absolute inset-0 box-border p-0 m-auto block w-0 h-0 min-w-full max-w-full min-h-full max-h-full object-contain" />
            </span>
          </div>
        </div>
        <div class="bananaglass">
          <div class="t000tzBuut">


          </div>
          <div>
            <select class="inputmint" v-model="mintAmount">
              <option value="1">1</option>
              <option value="5">5</option>
              <option value="10">10</option>
              <option value="15">15</option>
              <option value="20">20</option>
              <option value="30">30</option>
            </select>
          </div>
          <div class="button-info">
            <div v-if="!getUserAccount">
              <a class="button" id="mint-button" href="#">10 000</a>
            </div>
            <div v-else class="col-auto" style="height: 145px">
              <div>
                <button type="button" class="button _app_2DtRB mint" @click.prevent="mintToken">
                  <a class="button">
                    {{ mintText }}
                  </a>
                </button>
              </div>
              <div>
                <h2 class="inputmint">{{ totalSupply }} / 10 000</h2>
              </div>
            </div>
            <br />
            <br />
            <span class="price">0.0042 ETH each</span>
          </div>
          <div class="mint-info">
            <span class="minted-description red-description"><a href="https://opensea.io/collection/fr00ts">Ethereum
                Collection
              </a></span>
            <span class="minted-description red-description"><a
                href="https://etherscan.io/address/0xC6cE183fB60AD4A05d207EE740AF50Ff0614Cf04">
                Etherscan
              </a></span>
            <span class="minted-description">minted</span>
            <span class="minted-description">fr00ts left:</span>
            <h2 class="effect-holo">{{ totalSupply }} / 10000</h2>
            <br />
            <br />
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Web3 from "web3";
import Web3Modal from "web3modal";
import WalletConnectProvider from "@walletconnect/web3-provider";
import { mapActions, mapGetters } from "vuex";

export default {
  name: "Header",
  data() {
    return {
      cost: "0.0042",
      chainId: "1",

      mintAmount: 1,
      totalSupply: 0,

      web3Modal: "",
      mintText: "MINT",

    };
  },

  beforeMount() {
    const providerOptions = {
      walletconnect: {
        package: WalletConnectProvider,
        options: {
          infuraId: "ff64e719dd8d46c78b04eb7582fcf233",
        },
      },
    };

    this.web3Modal = new Web3Modal({
      providerOptions,
      cacheProvider: false,
      disableInjectedProvider: false,
    });
  },
  methods: {
    ...mapActions("wallet", ["SET_WEB3", "SET_USER_ACCOUNT", "SET_INSTANCE"]),

    async onConnect() {
      try {
        let provider = await this.web3Modal.connect();
        this.onProvider(provider);
      } catch (e) {
        console.log("Could not get a wallet connection", e);
        return;
      }
    },

    async onProvider(provider) {
      try {
        let web3 = new Web3(provider);
        let chain = await web3.eth.getChainId();
        console.log(chain);

        if (this.chainId != chain) {
          this.$toasted.show("Connect your wallet to the Ethereum network");
          return;
        }

        this.SET_WEB3(web3);

        let accounts = await web3.eth.getAccounts();
        this.SET_USER_ACCOUNT(accounts[0]);

        let instance = new this.getWeb3.eth.Contract(
          this.getContractABI,
          this.getContractAddress
        );

        this.SET_INSTANCE(instance);
        this.readValue();
      } catch (e) {
        console.log("Could not get a wallet connection", e);
        return;
      }

      provider.on("accountsChanged", (accounts) => {
        this.SET_USER_ACCOUNT(accounts[0]);
      });
    },

    mintToken() {
      if (!this.mintAmount) {
        this.$toasted.show("Enter minting amount");
        return;
      }
      this.mintText = "Minting...";

      let gasLimit = Number(this.gasLimit) * Number(this.mintAmount);
      let mintPrice = Number(this.mintAmount) * Number(this.cost);
      let value = this.getWeb3.utils.toWei(mintPrice.toString(), "ether");

      this.getInstance.methods
        .mint(Number(this.mintAmount))
        .send({
          gasLimit: gasLimit,
          from: this.getUserAccount,
          to: this.getContractAddress,
          value: value,
        })
        .on("transactionHash", (hash) => {
          console.log("Transaction Hash: ", hash);
          this.$toasted.show("Transaction is submitted");
        })
        .on("receipt", (receipt) => {
          this.mintText = "MINT NFT";
          this.readValue();
          console.log("Receipt: ", receipt);
          this.$toasted.show("Transaction Completed Successfully");
        })
        .on("error", (error, receipt) => {
          this.mintText = "MINT NFT";
          console.log("Error receipt: ", receipt);
          this.$toasted.show("Transaction Rejected");
        });
    },

    readValue() {
      Promise.all([this.getInstance.methods.totalSupply().call()]).then(
        ([totalSupply]) => {
          this.totalSupply = totalSupply;
        }
      );
    },

    addrTruncation(addr) {
      return addr.slice(0, 6) + "…" + addr.slice(addr.length - 4, addr.length);
    },

    disconnect() {
      this.SET_WEB3(null);
      this.SET_INSTANCE(null);
      this.SET_USER_ACCOUNT(null);
    },
  },
  computed: {
    ...mapGetters("wallet", [
      "getWeb3",
      "getUserAccount",
      "getInstance",
      "getContractABI",
      "getContractAddress",
    ]),
  },
};



</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Oswald:wght@400;700&display=swap");

html {
  scroll-behavior: smooth;
}

.video-container {
  width: 100vw;
  height: 100vh;
}

iframe {
  position: fixed;
  /* top: 50%;
  left: 50%;
  scale: 1.6;
  width: 100vw;
  height: 100vh;
  transform: translate(-50%, -50%);

 */
  scale: 1.6;
  pointer-events: none;
  z-index: -999;
  top: 0px;
  left: 0;
  width: 100vw;
  height: 100vh;
  padding: 0 0px;
}

body {
  width: auto;
  height: 100vh;
  font-family: Sans-Serif;
  background-repeat: no-repeat;
  background-attachment: fixed;
  margin: 0px;
  background-color: #ff000000;
  width: 100%;
}

.bananaglass {
  background: rgba(255, 255, 255, 0.35);
  box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
  backdrop-filter: blur(1.5px);
  -webkit-backdrop-filter: blur(1.5px);
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.18);
}




.t000tzBuut {
  align-items: center;
  justify-content: center;
}

.button {
  border-radius: 10px;
  padding: 20px 15px;
  align-items: center;
  justify-content: center;
  text-decoration: none;
  color: #000;
  text-transform: lowercase;
  font-size: 28px;
  width: 220px;
  line-height: 1.3em;
  font-weight: 600;
  letter-spacing: 2px;
  text-align: center;
  transition: all 0.3s ease 0s;
}

.button:hover {
  border-color: #3160ff;
  color: #3160ff;
}

#mint-button:hover {
  border-color: #ff0000;
  color: #ff0000;
}

.inputmint {
  border: 6px solid #000;
  border-radius: 10px;
  padding: 20px 15px;
  align-items: center;
  justify-content: center;

  text-decoration: none;
  color: #000;
  text-transform: lowercase;
  font-size: 28px;
  width: 220px;
  line-height: 1.3em;
  font-weight: 600;
  letter-spacing: 2px;
  text-align: center;
  transition: all 0.3s ease 0s;
}

.button-info .price {
  font-size: 28px;
  font-weight: 600;
  text-align: center;
}

.primary .container .main img {
  width: 100%;
}

.side {
  display: grid;
  grid-template-columns: auto;
  place-items: center;
  align-content: center;
  gap: 50px;
  -webkit-animation: bgcolorchange 20s infinite;
  animation: 20s infinite bgcolorchange;
}





select.mint-input {
  border: 5px solid #000;
  font-size: 32px;
  border-radius: 10px;
  width: 120px;
  margin-left: 0px;
  text-align: center;
  padding: 15px 10px;
  font-weight: 600;
}


#mint-button {
  font-size: 38px;
  padding: 30px 15px;
  letter-spacing: 4px;
  align-items: center;
}

.mint-info {
  display: grid;
  gap: 10px;
  place-items: center;
  font-size: 24px;
}

.minted-description.red-description {
  color: #ff0000;
  /* to be removed */
}

.minted-description.red-description a {
  color: #ff0000;
  /* to be removed */
}

#minted {
  width: 176px;
  height: 37px;
  display: block;
}

#minted img {
  width: 100%;
}

.show-mobile {
  display: none;
}

@media screen and (max-width: 980px) {
  #logo {
    grid-column: 1 / span 2;
  }
}

@media screen and (max-width: 800px) {
  .header {
    padding: 0 30px;
  }

  iframe {
    position: fixed;
    /* top: 50%;
  left: 50%;
  scale: 1.6;
  width: 100vw;
  height: 100vh;
  transform: translate(-50%, -50%);

 */
    scale: 1.8;
    pointer-events: none;
    z-index: -999;
    top: 0px;
    left: 0;
    width: 100vw;
    height: 100vh;
    padding: 0 0px;
  }

  .header .container {
    grid-template-columns: 1fr;
    gap: 30px;
  }

  #logo {
    grid-column: 1;
    grid-row: 2;
  }

  #connect-wallet {
    grid-column: 1;
    grid-row: 1;
  }

  .primary {
    margin-top: 20px;
  }

  .primary .container {
    grid-template-columns: 1fr;
    gap: 5px;
  }

  .primary .container .side {
    gap: 30px;
    align-items: top;
    margin-bottom: 30px;
  }

  .hide-mobile {
    display: none;
  }

  .show-mobile {
    display: block;
  }

  .primary .container .main {
    grid-column: 1;
  }

  .primary .container .bottom {
    grid-column: 1;
    grid-row: 2;
  }

  .bottom {
    padding: 0;
    margin-bottom: 10px;
  }
}

#alert-bar {
  -webkit-box-align: center;
  align-items: center;
  background: rgba(255, 255, 255, 0.9);
  border-bottom: 2px solid #000;
  color: #000000;
  display: flex;
  -webkit-box-pack: center;
  justify-content: center;
  padding: 5px 0;
  font-weight: 500;
  min-height: 48px;
  height: auto;
  position: absolute;
  width: 100%;
  top: 0px;
  opacity: 0;
  transition: all 0.3s ease 0s;
  z-index: 10000;
  font-weight: 700;
  line-height: 47px;
  font-size: 18px;
  letter-spacing: 0.5px;
}

#alert-bar a {
  color: #3160ff;
  display: inline-block;
}

#alert-bar .alert-content {
  margin-right: 20px;
  line-height: 1.5em;
}

#close-alert-bar {
  width: 15px;
}

#close-alert-bar img {
  width: 100%;
}

.alert-content a {
  text-decoration: underline;
}

@media (max-width: 680px) {
  #alert-bar {
    position: relative;
    display: none;
    height: auto;
  }

  .alert-content {
    padding-left: 20px;
    padding-right: 20px;
    line-height: 1.5em;
  }

  iframe {
    position: fixed;
    /* top: 50%;
  left: 50%;
  scale: 1.6;
  width: 100vw;
  height: 100vh;
  transform: translate(-50%, -50%);

 */
    scale: 6;
    pointer-events: none;
    z-index: -999;
    top: 0px;
    left: 0;
    width: 100vw;
    height: 100vh;
    padding: 0 0px;
  }

  #close-alert-bar {
    position: absolute;
    right: 10px;
  }
}

.footer {
  text-align: center;
  text-transform: uppercase;
  margin-bottom: 30px;
}

.footer a {
  color: #000;
  text-decoration: underline;
  font-weight: 600;
  letter-spacing: 1px;
}

.footer a:hover {
  color: #3160ff;
}
</style>
