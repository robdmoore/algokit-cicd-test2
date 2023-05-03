# albogit-cicd-2

This project has been generated using AlgoKit. See below for default getting started instructions.

# Setup

### Initial setup

1. Clone this repository locally
2. Install pre-requisites:
   - Install `AlgoKit` - [Link](https://github.com/algorandfoundation/algokit-cli#install): Ensure you can execute `algokit --version`.
   - Bootstrap your local environment; run `algokit bootstrap all` within this folder, which will:
     - Install `Poetry` - [Link](https://python-poetry.org/docs/#installation): The minimum required version is `1.2`. Ensure you can execute `poetry -V` and get `1.2`+
     - Run `poetry install` in the root directory, which will set up a `.venv` folder with a Python virtual environment and also install all Python dependencies
     - Copy `.env.template` to `.env`
     - Run `npm install` in `smart_contracts` to install NPM packages

3. Open the project and start debugging / developing via:
   - VS Code
     1. Open the repository root in VS Code
     2. Install recommended extensions
     3. Hit F5 (or whatever you have debug mapped to) and it should start running with breakpoint debugging.
        > **Note**
        > If using Windows: Before running for the first time you will need to select the Python Interpreter.
        1. Open the command palette (Ctrl/Cmd + Shift + P)
        2. Search for `Python: Select Interpreter`
        3. Select `./.venv/Scripts/python.exe`
   - IDEA (e.g. PyCharm)
     1. Open the repository root in the IDE
     2. It should automatically detect it's a Poetry project and set up a Python interpreter and virtual environment.
     3. Hit Shift+F9 (or whatever you have debug mapped to) and it should start running with breakpoint debugging.
   - Other
     1. Open the repository root in your text editor of choice
     2. In a terminal run `poetry shell`
     3. Run `python -m smart_contracts` through your debugger of choice

### Subsequently

1. If you update to the latest source code and there are new dependencies you will need to run `algokit bootstrap all` again
2. Follow step 3 above

### Continuous Integration / Continuous Deployment (CI/CD)

This project uses [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions) to define CI/CD workflows, which are located in the [`.github/workflows`](./.github/workflows) folder.

#### Setting up GitHub for CI/CD workflow and TestNet deployment

  1. Every time you have a change to your smart contract, and when you first initialise the project you need to [build the contract](#initial-setup) and then commit the `smart_contracts/artifacts` folder so the [output stability](https://github.com/algorandfoundation/algokit-cli/blob/main/docs/articles/output_stability.md) tests pass
  2. Decide what values you want to use for the `allowUpdate` and `allowDelete` parameters specified in `deploy-config.ts`
     When deploying to LocalNet these values are both set to `true` for convenience. But for non-LocalNet networks
     they are more conservative and use `false`
     These default values will allow the smart contract to be deployed initially, but will not allow the app to be updated or deleted if is changed and the build will instead fail.
     To help you decide it may be helpful to read the [AlgoKit Utils app deployment documentation](https://github.com/algorandfoundation/algokit-utils-ts/blob/main/docs/capabilities/app-deploy.md) or the [AlgoKit smart contract deployment architecture](https://github.com/algorandfoundation/algokit-cli/blob/main/docs/architecture-decisions/2023-01-12_smart-contract-deployment.md#upgradeable-and-deletable-contracts).
  3. Create a [Github Environment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#creating-an-environment) named `Test`.
     Note: If you have a private repository and don't have GitHub Enterprise then Environments won't work and you'll need to convert the GitHub Action to use a different approach.
  4. Create or obtain a mnemonic for an Algorand account for use on TestNet to deploy apps, referred to as the `DEPLOYER` account.
  5. Store the mnemonic as a [secret](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#environment-secrets) `DEPLOYER_MNEMONIC`
     in the Test environment created in step 3.
  6. The account used to deploy the smart contract will require enough funds to create the app, and also fund it. There are two approaches available here:
     * Either, ensure the account is funded outside of CI/CD.

       In Testnet, funds can be obtained by using the [Algorand TestNet dispenser](https://bank.testnet.algorand.network/) and we recommend provisioning 50 ALGOs.
     * Or, fund the account as part of the CI/CD process by using a `DISPENSER_MNEMONIC` GitHub Environment secret to point to a separate `DISPENSER` account that you maintain ALGOs in (similarly, you need to provision ALGOs into this account using the [TestNet dispenser](https://bank.testnet.algorand.network/)).

#### Continuous Integration

For pull requests and pushes to `main` branch against this repository the following checks are automatically performed by GitHub Actions:
 - Python dependencies are audited using [pip-audit](https://pypi.org/project/pip-audit/)
 - Code formatting is checked using [Black](https://github.com/psf/black)
 - Linting is checked using [Ruff](https://github.com/charliermarsh/ruff)
 - Types are checked using [mypy](https://mypy-lang.org/)
 - Python tests are executed using [pytest](https://docs.pytest.org/)
 - Smart contract artifacts are built
 - Smart contract artifacts are checked for [output stability](https://github.com/algorandfoundation/algokit-cli/blob/main/docs/articles/output_stability.md)
 - Smart contract is deployed to a AlgoKit LocalNet instance

#### Continuous Deployment

For pushes to `main` branch, after the above checks pass, the following deployment actions are performed:
  - The smart contract(s) are deployed to TestNet using [AlgoNode](https://algonode.io).

# Tools

This project makes use of Python to build Algorand smart contracts. The following tools are in use:

- [Algorand](https://www.algorand.com/) - Layer 1 Blockchain; [Developer portal](https://developer.algorand.org/), [Why Algorand?](https://developer.algorand.org/docs/get-started/basics/why_algorand/)
- [AlgoKit](https://github.com/algorandfoundation/algokit-cli) - One-stop shop tool for developers building on the Algorand network; [docs](https://github.com/algorandfoundation/algokit-cli/blob/main/docs/algokit.md), [intro tutorial](https://github.com/algorandfoundation/algokit-cli/blob/main/docs/tutorials/intro.md)
- [Beaker](https://github.com/algorand-devrel/beaker) - Smart contract development framework for PyTeal; [docs](https://beaker.algo.xyz), [examples](https://github.com/algorand-devrel/beaker/tree/master/examples)
- [PyTEAL](https://github.com/algorand/pyteal) - Python language binding for Algorand smart contracts; [docs](https://pyteal.readthedocs.io/en/stable/)
- [AlgoKit Utils](https://github.com/algorandfoundation/algokit-utils-ts) - A set of core Algorand utilities that make it easier to build solutions on Algorand.
- [Poetry](https://python-poetry.org/): Python packaging and dependency management.- [Black](https://github.com/psf/black): A Python code formatter.
- [Ruff](https://github.com/charliermarsh/ruff): An extremely fast Python linter.

- [mypy](https://mypy-lang.org/): Static type checker.
- [pytest](https://docs.pytest.org/): Automated testing.
 - [pip-audit](https://pypi.org/project/pip-audit/): Tool for scanning Python environments for packages with known vulnerabilities.
- [npm](https://www.npmjs.com/): Node.js package manager
- [TypeScript](https://www.typescriptlang.org/): Strongly typed programming language that builds on JavaScript
- [ts-node-dev](https://github.com/wclr/ts-node-dev): TypeScript development execution environment

It has also been configured to have a productive dev experience out of the box in [VS Code](https://code.visualstudio.com/), see the [.vscode](./.vscode) folder.

