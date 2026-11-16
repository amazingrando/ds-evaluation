# Design system evaluations

The purpose of this repo is to provide instructions for evaluating Brad Frost skills against a design system and providing a sample design system.

## Setup

### 1. Clone this repo

All three options below download the same files. Pick the one that matches the tool you already use.

#### Terminal (`git`)

Choose this when Git is already installed and you work in a terminal. This is a public repo, so you do not need to sign in to GitHub. The same commands work on macOS, Windows, and Linux.

```bash
git clone https://github.com/amazingrando/ds-evaluation.git
cd ds-evaluation
```

If the shell says `git` is not found, install Git first. On macOS, run `xcode-select --install`, or install it from [git-scm.com](https://git-scm.com/).

#### GitHub CLI (`gh`)

Choose this when you already use the [GitHub CLI](https://cli.github.com/) for the rest of your GitHub work. You pass `owner/repo` instead of the full URL, and the clone uses the protocol you set when you logged in (HTTPS or SSH).

```bash
gh repo clone amazingrando/ds-evaluation
cd ds-evaluation
```

If you have not signed in on this machine yet, run `gh auth login` first. If `gh` is not found, install it from [cli.github.com](https://cli.github.com/).

#### GitHub Desktop (Mac app)

Choose this when you want to clone, commit, and push from an app instead of a terminal. GitHub Desktop includes Git, so you do not install Git separately.

1. Install [GitHub Desktop](https://desktop.github.com/) and sign in to GitHub.
2. Choose **File → Clone repository**. On the start screen, the same action is **Clone a repository from the Internet**.
3. Open the **URL** tab and paste:

   `https://github.com/amazingrando/ds-evaluation`

4. Choose a local folder and click **Clone**.

### Which one to use

- **`git clone`** when you only need the files and you already use a terminal. Nothing else to install if Git is present.
- **`gh repo clone`** when the GitHub CLI is already how you talk to GitHub. It follows the login and the HTTPS or SSH setting you already configured.
- **GitHub Desktop** when you would rather use the Mac app and skip typing Git commands.

### 2. Install Brad Frost's Design System evaluation skills

The skills come from [bradfrost/skills](https://github.com/bradfrost/skills). This repo has a copy that can be used with only this project. You can install the skills on your own machine (Claude, Codex, etc.). The two copies do different jobs.

#### Why the skills are in this repo

The files live in `.agents/skills/`. 

These files do not get updated. When Brad updates the collection, the copy here stays put, so the instructions and the sample design system still match the skills you are evaluating.

An agent opened on this folder can use this project's skills with no extra install. Cursor reads `.agents/skills/`. Claude Code reads the same folders through `.claude/skills/`.

#### Why a global install is still the one to use

Claude Desktop reads skills from your account. It does not read `.agents/skills/` in a project. Uploading them once makes them available in every conversation.

That is also the install you want for the work these skills are for. The design-system skills are meant to run inside the design system or product you are inspecting. A global install is there when you open that repo. The copy in this repo is there so this evaluation has a fixed version.

Skills you turn on in Claude also load in Claude Code when you sign in with the same account.

#### Claude Code or Cursor, outside this repo

Use the global install when you want the current collection in every project, not the pinned copy above. From any directory run the following:

```bash
npx skills add bradfrost/skills -g -a claude-code cursor
```

Open a new session and run `/setup-brad-frost-skills`.

### 3. Run the Emulsify UI kit

The design system under evaluation is the [Emulsify UI kit](https://github.com/emulsify-ds/emulsify-ui-kit), already in `emulsify-ui-kit/`. This copy is commit `2f30c46`. Cloning this repo includes the components, tokens, and stories, so there is no second checkout.

Dependencies and Storybook's build output stay off this repo. Install those locally before you run the system.

The kit pins Node 20 in `emulsify-ui-kit/.nvmrc`. From that folder, with [nvm](https://github.com/nvm-sh/nvm):

```bash
cd emulsify-ui-kit
nvm install
nvm use
npm ci
npm run develop
```

Without nvm, install Node 20 from [nodejs.org](https://nodejs.org/), then run `npm ci` and `npm run develop` from `emulsify-ui-kit/`.

`npm ci` installs the locked dependencies. `develop` builds the component assets and starts Storybook at [http://localhost:6006/](http://localhost:6006/). The token CSS is already in the kit, so you do not need a separate token build for this first run.

`npm run storybook` on its own expects a `dist` folder that does not exist until that build has run.
