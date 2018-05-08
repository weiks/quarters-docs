# Welcome to Quarters

Welcome to the Quarters documentation! Quarters is a universal cryptocurrency
for games. This document includes SDK, APIs and code snippets. With them, you
can easily integrate Quarters tokens to your games.

If you feel like contributing to the docs, like changing or adding things, feel
free to submit a [Pull Request](https://github.com/weiks/quarters-docs/pulls) at
[https://github.com/weiks/quarters-docs](https://github.com/weiks/quarters-docs)

For our website visit
[Pocketful of Quarters](https://pocketfulofquarters.com).

» **For a quick video on how to get started with Quarters, [click here](https://www.youtube.com/watch?v=hxyYOSmfFHc)!**

Alternatively if you are not a developer and accidentally got here [watch a video on How to buy Quarters](https://www.youtube.com/watch?v=ltYgQwfIOlk), or visit our website [Pocketful of Quarters](https://pocketfulofquarters.com).

### Development tools and library

Here is the list of tools and libraries quarters project provides to get
started.

**Quarters contract**

Quarters contract is Ethereum smart contract and written in solidity. It
includes ERC20 token and withdraw functions. You can check code and ABI here on
etherscan.

Mainnet: _[Yet to be deployed]_

Ropsten:
[0x8c05ebef0b4419416e8efb4c26edb4aa6cd7bb27](https://ropsten.etherscan.io/address/0x8c05ebef0b4419416e8efb4c26edb4aa6cd7bb27#code)

**Quarters buttons**

Quarters buttons are small snippets. It is easy and fastest way to integrate
"Play with Quarters" feature on game. Here is quick setup to embed buttons on
any web based game to accept Quarters.

```html
<script
    src="https://pocketfulofquarters.com/embeds/button.js"
    data-script-name="quarters-embeds"
    data-app-id="<APP-ID>"
    data-app-key="<APP-KEY>"
    charset="utf-8">
</script>

<button
    class="buy-quarters-button"
    data-widget-type="button"
    data-order-code="1"
    data-amount="2">
  Play with 2 Quarters
</button>
```

To check complete documentation, [visit Quarters Buttons](quarters-buttons.md).

**JavaScript SDK**

JavaScript SDK provides rich API interface to help build oauth on Quarters.
Developers can use `Quarters oauth` to add authentication to the game and
retrieve information for client-only games.

```shell
  npm install --save quarters-js
```

To check complete documentation, [visit JavaScript SDK](sdk/js.md).

**Node SDK (for server)**

Node SDK provides helpers to interact with Quarters server and contract.

```shell
  npm install --save node-quarters
```

To check complete documentation, [visit Node SDK](sdk/node.md).

**Unity SDK**

To check complete documentation, [visit Unity SDK](sdk/unity.md).

**Unity SDK - PlayFab Module**

PlayFab Module allows transferring quarters from the Quarters app to the users through PlayFab cloud script.

To check complete documentation, [visit Unity SDK - PlayFab Module](sdk/unity-playfab.md).
