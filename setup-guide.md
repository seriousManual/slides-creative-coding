# Setup Guide

## git

### Windows

A git package for Windows can be downloaded from
[https://git-scm.org](https://git-scm.com/). Or you can use chocolatey

```bash
choco install git
```

### Linux

Use your package manager to install git.

### macOS

There are multiple ways to install git on macOS

1. Install Xcode and then the Xcode command line tools via

   ```sh
   xcode-select --install
   ```

2. Use brew

   ```sh
   brew install git
   ```

3. Use the install package from [https://git-scm.org](https://git-scm.com/).

## Node.js and NPM

### Windows

Install Node.js from the project homepage
[https://nodejs.org](https://nodejs.org). It is recommended to use the
**Current** version instead of the LTS version. At some point the installer will
ask you to install **Tools for native Modules** (see figure
\ref{native_modules}). You do not need these tools for the exercises in this
training. If you want to continue to use Node.js and NPM it makes sense to
install these additional tools by checking **Automatically install the necessary
tools**.

![The installer will ask about **Tools for Native Modules**\label{native_modules}](./media/setup-guide-install-node.png)

### Linux and macOS

It is recommended to use the node version manager `nvm`
([https://github.com/creationix/nvm](https://github.com/creationix/nvm)) to
install the latest version of Node.js. Follow the instructions on the `nvm` Github
page to setup `nvm` first and then install and load Node.js 10 with

```sh
nvm install 10
```

## Check that everything works

First, clone the repository

```sh
git clone ssh://git@scm.methodpark.de:29419/cmpe-webdev/assignment.git
```

Then cd into the  cloned directory and verify, that Node.js and NPM work as
expected

```sh
cd assignment
npm install
npm start
```

This should install all dependencies and start the development server. It also
may open up a browser tab at [http://localhost:3000](http://localhost:3000).