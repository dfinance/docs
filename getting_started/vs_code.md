Out of date.

### Move IDE for **VSCode**

Repository is here: [https://github.com/damirka/vscode-move-ide](https://github.com/damirka/vscode-move-ide)

VSCode marketplace link: [https://marketplace.visualstudio.com/items?itemName=damirka.move-ide](https://marketplace.visualstudio.com/items?itemName=damirka.move-ide).

First, go to plugin settings and update default account and compiler URL:

* Default account: use your **dfinance** address.
* Compiler: **tcp://rpc.testnet.dfinance.co:50053**

Then:

* Create a new workspace.
* Put a configuration file into the workspace under .mvconfig.json name.

```javascript
{
    "network": "dfinance",
    "defaultAccount": "<address>",
    "compiler": "rpc.testnet.dfinance.co:50053",
    "compilerDir": "./out"
}
```

Replace **&lt;address&gt;** with your **dfinance** address.

* Put file **send.move** contains script into the workspace.
* Open file **send.move** in the editor and run the command: **'Move: Compile'**.
* Check **./out** folder in the workspace to see the compiled file.
* Done!
