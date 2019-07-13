# SDK CONFIGURATION


## Cloning the repository

To configure cosmos sdk perform the following steps.

* Fork the repository form the main sdk repository
   ```
   [https://github.com/cosmos/cosmos-sdk](https://github.com/cosmos/cosmos-sdk)
   ```
* Change the name to colors sdk of the repository using settings in github


## Updating the mod file 

Mod file in project ensure the version management. To reconfigure the mod file perform the following steps.

* To inside the colors project directory and open terminal here, run the following commands
  ```
  go mod edit -replace github.com/cosmos/cosmos-sdk => github.com/RNSSolution/cosmos-sdk v0.28.2-0.20190622092459-7b5e6cee0787
  ```

make sure the commit number and version number in go mod edit

* make the project 
  ```
  make install
  ```
## Making changes in sdk project

* Checkout the version you specfied in go mod edit.
* pull the following command to add tag
  ```
  git tag v0.28.3
  ```
* Push new changes to github

* get time stamp of git commit
  ```
  git show --no-patch --no-notes --pretty='%cd' [place commit hash here]
  ```
* replace the time stamp and git hash in go mod file by building hte following file
  ```
  v0.28.2-0.20190622092459-7b5e6cee078
  ```
  
