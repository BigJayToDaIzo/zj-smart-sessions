<h1 align="center">zj-smart-sessions 🔀🧠</h1>

<p align="center">
  An opinionated session manager for zellij trying to be smart.
  <br><br>
  <a href="https://github.com/dj95/zj-smart-sessions/actions/workflows/lint.yml">
    <img alt="clippy check" src="https://github.com/dj95/zj-smart-sessions/actions/workflows/lint.yml/badge.svg" />
  </a>
  <a href="https://github.com/dj95/zj-smart-sessions/releases">
    <img alt="latest version" src="https://img.shields.io/github/v/tag/dj95/zj-smart-sessions.svg?sort=semver" />
  </a>
  <a href="https://github.com/dj95/zj-smart-sessions/wiki">
    <img alt="GitHub Wiki" src="https://img.shields.io/badge/documentation-wiki-wiki?logo=github">
  </a>

  <br><br>
  The goal of this project is to speed up my workflow with zellij sessions. It implements fuzzy finding,
  that matches on a combination of session and tab name to directly find the corresponding tab in a
  session.

  In addition to listing existing session, zj-smart-session offers a way to create new ones based on directories.
  For this feature, a command/script that returns a list of all possible directories must be configured. It then
  executes (and caches) the command and provides a fuzzy search. Sessions are created based on the last path
  name. If a session with this name exists, the existing session will be attached.
</p>

![screenshot displaying the session manager](./assets/demo.png)

> [!IMPORTANT]
> This is an early development version and does not have all features implemented. With future APIs, there will be the last used session at the top. Also one goal is to navigate to the last focused tab & pane, when attaching to a session.

## 🚀 Usage

When zj-smart-sessions is installed and configured with the keybindings, simply invoke the keybinding to start the plugin.

It will pop up in a floating window. On first start, it will ask for permissions to fetch certain events and control zellij.
After granting permissions, you can navigate with the arrow keys between the sessions. Right arrow key will expand the session or tab; left arrow will fold it again.

The search acts with a fuzzy search and is implemented in a way, that speeds up finding the correct tab in the correct session.
Simply start typing to search the session first. If the correct sessions is selected, type a ' '(space) to start fuzzy finding the tab. 
When you type a ' '*(space)* again, you can also search for panes in the selected tab.

When pressing the enter key, your session will be switched to the selected destination. The delete key will kill the selected session.

## 📦 Installation

Download the latest binary in the GitHub releases. Place it somewhere, zellij is able to access it. Then the
plugin can be included by referencing it either via [plugin aliases](https://zellij.dev/documentation/plugin-aliases) or directly in the keybindings section of the *config.kdl*.

You could also refer to the plugin guide from zellij, after downloading the binary: [https://zellij.dev/documentation/plugin-loading](https://zellij.dev/documentation/plugin-loading)

Here's an example for creating the keybinding, that override the default session manager, with help of a plugin alias.

```javascript
plugins {
  zj-smart-sessions location="file:/abolute/path/to/zj-smart-sessions.wasm"
}
keybinds {
    session {
        bind "w" { // "normal" session manager
            LaunchOrFocusPlugin "zj-smart-sessions" {
                floating true
            };
            SwitchToMode "Normal"
        }
        bind "W" { // execute directories.script and offer a fuzzy search over the directories
                   // to attach or create new sessions based on the directory name
            LaunchOrFocusPlugin "zj-smart-sessions" {
                floating true
                find_command "/Users/username/script/for/returning/directories.script"
                base_path "/User/username/Developer" // path prefix required for relative paths from
                                                     // the script output
            };
            SwitchToMode "Normal"
        }
    }
}
```

An example for such a script for the `find_command` can be found at [./find_command](./find_command). It will find
all `.git` directories with *fd* and removes the `.git/` suffix from the path in `~/Developer`.

As an example, if your directories are like `/home/user/proj/rust/zellij` and your `find_command` returns just
`rust/zellij` for better readability, you **must** configure the `base_path` as `/home/user/proj`. Otherwise
zj-smart-sessions will start the session at the wrong CWD!

## ❄️ Installation with nix flake

Add this repository to your inputs and then with the following overlay to your packages.
Then you are able to install and refer to it with `pkgs.zj-smart-sessions`. When templating the
config file, you can use `${pkgs.zj-smart-sessions}/bin/zj-smart-sessions.wasm` as the path.

```nix
inputs = {
# ...

zj-smart-sessions = {
  url = "github:dj95/zj-smart-sessions";
};
};


# define the outputs of this flake - especially the home configurations
outputs = { self, nixpkgs, zj-smart-sessions, ... }@inputs:
let
inherit (inputs.nixpkgs.lib) attrValues;

overlays = with inputs; [
  # ...
  (final: prev: {
    zj-smart-sessions = zj-smart-sessions.packages.${prev.system}.default;
  })
];
```


## 🤝 Contributing

If you are missing features or find some annoying bugs please feel free to submit an issue or a bugfix within a pull request :)

## 📝 License

© 2024 Daniel Jankowski

This project is licensed under the MIT license.

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
