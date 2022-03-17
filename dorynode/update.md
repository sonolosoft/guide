# Update Dorynode to latest Nodejs+NPM

https://github.com/tempage/dorynode

You can try to update dorynode to latest node and npm with termux binaries for arm7a and other archs. You may encounter issues and surprises so make backups.

## Termux

https://github.com/termux/termux-app/releases

```shell
termux-setup-storage
apt update
apt install openssl
# just press enter when it asks about config conflict
apt install nodejs

cd
dory=`realpath storage/shared/doryupdate`
mkdir -p $dory/usr/lib
mkdir -p $dory/bin
cp ../usr/lib/{libc++*,libcares.so,libcrypto.so.3,libicu*.so.70,libssl.so.3,libz.so.1} $dory/usr/lib
cp ../usr/bin/node $dory/bin
cp -RL ../usr/lib/node_modules/npm $dory
```

## Dorynode shell

```shell
cd
dory=`realpath /storage/emulated/0/doryupdate`
cp ../files/npm/cli.js $dory/npm
mv ../files/npm ../files/npm3
mv ../files/bin/node ../files/bin/node10
cp $dory/usr/lib/* ../files/usr/lib
cp $dory/bin/node ../files/bin
cp -R $dory/npm ../files
```

## Dorynode individual script settings

```
environment 1
LD_LIBRARY_PATH
data/user/0/io.tempage.dorynode/files/usr/lib
```

## Symlink workaround

Some node modules will give errors about symlink when you install. FAT doesn't support symlinks. But ln -s symlinks can point to FAT.

Here are 2 solutions:

You can install modules globally.

```shell
npm i -g @vue/cli
```

Or you can install modules in the home dir and just let node search up the folder tree until it finds node_modules.

```shell
cd
# takes you to home folder

ln -s /storage/emulated/0 storage
mkdir storage/node
# you now have a shortcut storage pointing to a node folder on your internal storage

npm i download-size express
# node_modules will be created in your home folder, it supports symlinks
```

The node_modules folder here is visible to all your projects in your node folder. Node searches up the tree when looking for modules.

## Issues

Where can we put LD_LIBRARY_PATH by default for all scripts? And for dorynode's repl?

If you symlink npx-cli.js to bin and chmod +x it will still say can't execute: permission denied.

Some modules cause 8.5.2 npm install to say "Exit handler never called".
https://github.com/npm/cli/issues/417

Watch out, the new npm will delete/replace your node_modules.
