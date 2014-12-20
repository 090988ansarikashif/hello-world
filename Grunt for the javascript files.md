# Installing Grunt

To minify and uglify the javascript you will need to use Grunt. To do so, install it:

    sudo npm install -g grunt-cli

It depends on NodeJS, so you might want to add it too before that: 

    sudo apt-get install nodejs

> Note that you may have an issue with NodeJS. Indeed, there was some shenanigans over its name in the Debian's repositories.
> If you see `/usr/bin/env: node: File or folder not found` at launch time, you may have to either install nodejs-legacy, or execute `sudo ln -s /usr/bin/nodejs /usr/bin/node`

# Executing Grunt

grunt-cli doesn't install the tool grunt, it only prepare the field. You'll have to download the package used by the gruntfile first. 
In the case of Searx, you'll have to do this:

    cd searx/static/oscar
    npm install

Then, you can finally execute grunt here, or go back at the root of the project, and execute: 

    make grunt