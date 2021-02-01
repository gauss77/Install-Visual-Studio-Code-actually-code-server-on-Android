1. Install [Termux](https://termux.com/), an Android terminal emulator that provides a Linux execution environment and various tools.

2. Update system packages in Termux:

   ```sh
   $ pkg update -y
   ```

   <img src="https://imgur.com/Btpla5P.png" width="200">

3. Install dependencies, including python, nodejs, and yarn:

   ```sh
   $ pkg install -y python nodejs yarn git
   ```
   
   <img src="https://imgur.com/ik2dIa2.png" width="200">

4. Install [code-server](https://github.com/cdr/code-server), an adaptation of Visual Studio Code that runs as a web app using the following command. The installation may take a while:

   ```sh
   $ yarn global add code-server
   ```
   
   <img src="https://imgur.com/0sACq9W.png" width="200">

5. Before starting code-server, we are going to fix an error caused by spdlog, which is a dependency of Visual Studio Code. First, change your working directory to `~/.config/yarn/global/node_modules/code-server/lib/vscode/node_modules/spdlog/`:

   ```sh
   $ cd ~/.config/yarn/global/node_modules/code-server/lib/vscode/node_modules/spdlog/
   ```

   <img src="https://imgur.com/QV44UnH.png" width="200">
   
   **Update**
   
   If you do not find the `~/.config/yarn/global/node_modules/code-server/lib/vscode/node_modules`, then you are using a newer version of `code-server` (https://github.com/cdr/code-server/issues/2285). In this case, you need to go to the `vscode` directory and install the dependencies manually:
   
   ```sh
   $ cd ~/.config/yarn/global/node_modules/code-server/lib/vscode/
   $ pkg install git
   $ yarn install
   $ rm node_modules.asar && ln -s node_modules node_modules.asar
   $ cd ~/.config/yarn/global/node_modules/code-server/lib/vscode/node_modules/spdlog/
   ```
   
   It does not matter if you meet a `gyp` error during the installation.

6. You will need a text editor for the next step. Here Vim is used for this task, but you are free to use whatever text editor you love:

   ```sh
   $ pkg install vim
   ```

   <img src="https://imgur.com/1GOq6tZ.png" width="200">

7. Open `binding.gyp` and enter edit mode. You can do this by pressing `i` once you open the file using Vim:

   ```sh
   $ vim binding.gyp
   ```

   <img src="https://imgur.com/z7Lrb1Z.png" width="200">

8. Add `"libraries": [ "-latomic" ]` below `"target_name": "spdlog",`. This helps spdlog compile on Android.

   ```diff
   "targets": [{
           "target_name": "spdlog",
   +       "libraries": [ "-latomic" ]
           "sources": [
                   "src/main.cc",
                   "src/logger.cc"
           ],
   ```

   <img src="https://imgur.com/ffA6at3.png" width="200">
  
9. Save the file and quit text editor. You can do this by pressing `ESC` and then `wq` in Vim:

   <img src="https://imgur.com/9V0Tld6.png" width="200">

10. Recompile spdlog using the following command:

    ```sh
    $ npm install
    ```

    <img src="https://imgur.com/khh6kIv.png" width="200">

11. (Optional) To see if spdlog works, run:

    ```sh
    $ npm test
    ```

12. To fix the file search function of code-server, install `ripgrep` via `pkg` and link it to the correct place:

    ```sh
    $ pkg install ripgrep -y
    $ cd ~/.config/yarn/global/node_modules/code-server/lib/vscode/node_modules/vscode-ripgrep/bin
    $ ln -s $(which rg) .
    ```

13. Finally, change the working directory back to home and start code-server with the following command:

    ```sh
    $ cd ~
    $ code-server --auth none --disable-telemetry
    ```

    <img src="https://imgur.com/CxOgPvg.png" width="200">

14. Now, you can open your browser and visit code-server at [http://localhost:8080](http://localhost:8080).

    <img src="https://imgur.com/dNOPggB.png" width="200">

15. (Bonus) If you wish to visit this code-server from another device in the same local network, you can expose the HTTP service to all network interfaces by starting code-server with `--bind-addr`:

    ```sh
    $ code-server --bind-addr 0.0.0.0:8080 --disable-telemetry
    ```

    You can find the password under `~/.config/code-server/config.yaml`:

    ```sh
    $ cat ~/.config/code-server/config.yaml
    ```

    <img src="https://imgur.com/JXVH21G.png" width="200">

    You may also need to enable HTTPS to enable clipboard and others features of Visual Studio Code in browsers. To do this, install `openssl-tool` and let code-server generate a certificate on startup:

    ```sh
    $ pkg install openssl-tool
    $ code-server --bind-addr 0.0.0.0:8080 --cert --disable-telemetry
    ```

    <img src="https://imgur.com/uvahAtt.png" width="200">

    You can get the public IP address of your device using the command below in Termux:
    
    ```sh
    $ ifconfig
    ```
    
    <img src="https://imgur.com/bpC0dkO.png" width="400">
