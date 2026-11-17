# Design system evaluations

The purpose of this repo is to provide instructions for evaluating Brad Frost skills against a design system and providing a sample design system.

## Setup

1. Clone this repo
2. Be running Node v20
3. Install requirements for the example design system, Emulsify
4. Copy the Figma file
5. Profit

### Be running Node v20

Run `which node` in the terminal to see what version of node you're running.

If you have it installed, you'll see something like `/Users/your-account/.nvm/versions/node/v24.15.0/bin/node`. If it has `node/v20.X.X`, you're all set. If not, you'll have to install it.

#### Installing node with nvm

[nvm](https://github.com/nvm-sh/nvm) is a tool that lets you install and manage multiple versions of node. This is the best way to manage node.

1. Visit [Installing and Updating nvm](https://github.com/nvm-sh/nvm#installing-and-updating)
2. Run the script that looks like `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.41.0/install.sh | bash` in your terminal.
3. Restart your terminal
4. Run `nvm ls` in the terminal to list the versions of node you have.
5. Run `nvm install 20` to install the latest version of node 20.
6. Run `nvm use 20`

### Install requirements for the example design system, Emulsify

1. `cd emulsify-ui-kit` to navigate into the design system
2. `npm ci` to install dependenices
3. `npm run develop` to launch storybook
4. Visit http://localhost:6006/ to see the design system.

You'll want to run the system at least once so everything gets built. You do not have to keep it running to evaluate the design system.

### Copy the Figma file

1. Visit [Emulsify UI Kit • Beta](https://www.figma.com/community/file/1141071510618977331/emulsify-ui-kit-beta)
2. Click "Open in Figma"

## Testing the design system

This repo comes with a copy of [Brad Frost's design system skills](https://github.com/bradfrost/skills).

To evaluate other design systems, you'll need to have them installed globally on your computer. See [Install](https://github.com/bradfrost/skills) when you're ready to do that.

