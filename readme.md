# Design system evaluations

The purpose of this repo is to provide instructions for evaluating Brad Frost skills against a design system and providing a sample design system.

## Setup

1. Clone this repo
2. Use Node v20
3. Use Figma Console MCP in Figma
4. Install requirements for the example design system, Emulsify
5. Copy the Figma file
6. Profit

### Use Node v20

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

### Use Figma Console MCP in Figma

1. Make sure you have the [Figma Console MCP installed](https://docs.figma-console-mcp.southleft.com/setup). 
2. You are running the Figma Desktop Bridge plugin in Figma.

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

To evaluate other design systems, you'll need to have these skills installed globally on your computer. See [Install](https://github.com/bradfrost/skills) when you're ready to do that.

### How to test in Claude

1. Open the copy of the Emulsify Figma file, run the Figma Desktop Bridge, and copy the link to the file.
2. In Claude, run `Run /ds-inspection. This is the Figma file: [LINK_TO_FIGMA_FILE]`.

The `ds-inspection` skill will run a full review of the design system—code and Figma. It will generate reports in a new folder named "ds-inspection".

**GARAGE.md** — the intake form. Before touching the design system, it writes down what it is: who it’s for, where its design file and code live, what access was actually confirmed (not assumed), and what’s explicitly out of scope for this pass. It’s the “here’s the car, here’s its mileage and known quirks” sheet a mechanic fills out before popping the hood — so later, anyone reading the report knows exactly what was and wasn’t checked.

**reports/YYYY-MM-DD-inspection.md** — the diagnostic readout. This is the actual inspection: all 10 stations (coverage, accessibility, testing, governance, etc.), each scored red/yellow/green with evidence, rolling up to the 42/100 overall score. It’s the mechanic’s full write-up of everything found wrong (and right) during the inspection — a snapshot of condition, not a to-do list.

**work-orders/YYYY-MM-DD-work-order.md** — the repair ticket. This turns the report’s findings into action: reds become “fix now” items, yellows become “schedule it” items, each with a concrete first move, effort estimate, and suggested timing. It’s what you’d actually hand to a technician (or a team) on Monday morning to start doing the work — the plan, not the findings.
