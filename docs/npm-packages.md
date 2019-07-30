# Color npm Packages

Currently there are two npm packages of Color platform

*Color-keys <br>
*Color-ledger 

### 1. [Color-keys](https://www.npmjs.com/package/@rnssolution/color-keys)
Color-Keys package provides following functionalities to developers. <br>
* Generate new Wallet (pubkey,privkey,address). <br>
* Generate wallet from seed. <br>
* Sign message with wallet private key.<br>

Color-keys package was initially forked from cosmos-keys npm package. Now to generate color address instead of cosmos we had to change wallet address prefix. So, to change that: <br>
<br>
* Go to source folder <br>
* Open cosmos-keys.ts file <br>
* Move to line 61 and change the address prefix to desired prefix i.e `color`
* Now change the name and version of the package from `package.json` file.
* Publish the updated package to npm packages, using `--access public` tag.


### 2.[Color-Legder](https://www.npmjs.com/package/@rnssolution/color-ledger)
This package helps interfacing with Color Ledger App.<br>

This package was also forked from cosmos ledger package. Following changes are made in it to make it run smoothly with color platform and color ledger app. <br>
* Navigate to `cosmos-ledger.ts` in the src folder.<br>
* On line #2 import color-keys package instead of cosmos-keys package. <br>
* Now on line #23 change the address prefix from cosmos to `color`.
* On line # 107 there is a check on the ledger app name, change it to `color`. 

Now change the name and version of the package from `package.json` file.Publish the updated package to npm packages, using `--access public` tag.
