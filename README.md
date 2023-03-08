# Frax Style Guide (Work In Progress)

## The intention of this style guide is first and foremost to increase readability of code and reduce the potential for bugs.  Readability reduces overhead for new readers which includes colleagues and most importantly auditors.  Additionally, consistency reduces cognitive overhead.  When people read our code they should walk away feeling “Holy shit these guys are obsessed”. This comes with trade-offs, most obvious the speed at which code is **********initially********** written.

## This style guide is meant to be collaborative and in its current state is just a first pass (with some highly opinionated items), so if you disagree please reach out or make a PR instead of just ignoring it.  Good programmers are flexible and the guide is pointless if we don’t follow it.

# Principles

### Optimize for the reader (auditor), not the writer

We explicitly optimize for the experience of the auditor, not the writer.  Implicitly this means that the speed of writing new code will decrease.  The ultimate goal of these rules is to provide a framework that allows new readers the ability to quickly understand the implications of the codebase and to reduce the surface area for auditor focus.

### Be consistent

Good engineers are flexible not dogmatic, don’t mix and match styles.  Consistency reduces cognitive overhead for readers.

# Repository

### Use kebab-case for repository names and folder names

Example: `@FraxFinance/fraxlend-dev`

### Use `-dev` suffix to indicate internal repos, public repos should have the same name without `-dev`

Example:
Private Repo: `fraxlend-dev`
Public Repo: `fraxlend`

### Utilize Prettier as the default formatter

This repo contains a .prettierrc.json file for use with prettier

### Utilize solhint as the default linter

This repo contains a .solhint.json file for use with solhint

### Utilize husky and lint-staged to lint all files pre-commit

This repo has an example `.lintstagedrc` file and `.husky` folder for reference

## File Names

### One Contract/Interface/Library per File

Utilize an import file if you would like to group files together by namespace for easy importing

Example:

```solidity
// This file aggregates the difference contracts and should be named appropriately
import { SomeContract } from ".SomeContract.sol";
import { SomeContract1 } from ".SomeContract1.sol";
import { SomeContract2 } from ".SomeContract2.sol";
import { SomeContract3 } from ".SomeContract3.sol";
```

### Use PascalCase.sol for file names containing Contracts

### Use IPascalCase.sol for file names containing interfaces

### File Name should exactly match the name of the contract/interface/library

### Files containing test contracts should start with word `Test` and end with `.t.sol`

Example: `TestFraxlendPairCore.t.sol`

### Use camelCase for files containing a group of free functions

Example: `deployFunctions.sol` (contains no contracts just a collection of free functions)

### Root directory should contains `src`, `test`, `script` directories

`src/contracts` contains contracts and interfaces which are deployed

`test` contains test contracts

`script` contains files for scripting including repo management and deploy scripts

`src` top-level contains any files which are shared across src/contracts and other places (e.g. Constants file)

# Misc

### Prefer node_module packages over github packages for secure code

Why: Latest code can be unstable and npm packages are typically release candidates

# Language Features

### Do not rely on via-ir to solve stack-too-deep errors

Why: via-ir is slow and reduces testing speed.  If reaching stack too deep complexity it probably too high.  For src code, reduce complexity for tests use param/return structs.

### Use named input params when invoking functions

Example:

```solidity
function fooBar(address _foo, address _bar) internal;

//Good
fooBar({ _foo: address(123), _bar: address(345)});

//Bad
fooBar(address(345), address(123));
```

Why: 

- Reduces potential bugs introduced during refactors which change order of functions
- Reduces overhead for readers as code intention is more clear, less need to look at function definition

# Source File Structure

### File Structure should be in the following order:

1. license
2. pragma
3. Frax Ascii art
4. imports
5. ConstructorParams Struct
6. Contract

### Imports should occur in the following order:

1. github packages
2. node_module packages
3. contracts
4. interfaces
- within each category, items should be alphabetical

### Use named imports { SomeContract } from "./SomeContract.sol";

Why:

- Reduces the need for the reader to have additional context when coming across an identifier.  Because identifiers are otherwise inherited, the location of an identifier is not known to the reader without a global search or IDE tooling
- Solves issues with naming collisions
- Makes compilation (and therefore testing) faster and makes verified code smaller

### Call inherited contract constructors in the order they are inherited

Why: Helps solve stack-too-deep errors, consistency, reinforces order of constructor execution

## Naming

### Immutable and constant variables should be CONSTANT_CASE

### Internal and private functions should be prepended with an underscore _someInternalFunction _somePrivateFunction

all other variables use camelCase (use google algorithm for acronyms)
i.e. FxsErc20 not FXSERC20, someVarVeFxs, Gohm

Use underscore to differentiate between memory and storage variables and to avoid naming collisions with storage variables

### Avoid abbreviations and acronyms in variables names, prefer descriptive variable names when possible

Code should be self-documenting.  Give as descriptive a name as possible, within reason. Do not worry about saving horizontal space as it is far more important to make your code immediately understandable by a new reader. Do not use abbreviations that are ambiguous or unfamiliar to readers outside your project, and do not abbreviate by deleting letters within a word.

| Allowed |  |
| --- | --- |
| errorCount | No abbreviation. |
| dnsConnectionIndex | Most people know what "DNS" stands for. |
| referrerUrl | Ditto for "URL". |
| customerId | "Id" is both ubiquitous and unlikely to be misunderstood. |

| Disallowed |  |
| --- | --- |
| n | Meaningless. |
| nErr | Ambiguous abbreviation. |
| nCompConns | Ambiguous abbreviation. |
| wgcConnections | Only your group knows what this stands for. |
| pcReader | Lots of things can be abbreviated "pc". |
| cstmrId | Deletes internal letters. |
| kSecondsPerDay | Do not use Hungarian notation. |

## Method names

### Method names are written in camelCase and are typically verbs or verb phrases

Example: sendMessage or stopProcess. 

### Enum names are written in PascalCase and should generally be singular nouns. Individual items within the enum are named in CONSTANT_CASE.

### Parameter names are written in camelCase and prepended with an _ if memory or without to designate storage pointers

Example:

```solidity
function sendMessage(string memory _messageBody, string storage messageHeader); 
```

### Storage variable names are written in camelCase

### Local variable names are written in _camelCase and prepended with an underscore to differentiate from storage variables

### Use google’s camelCase algorithm to handle acronyms/abbreviations and edge cases

Additional clarity: for words like iOS veFXS or gOhm, when prefix letter is single treat as one word, when prefix letters are >1 (frxETH or veFXS) treat prefix as separate word:

| Prose form | Correct | Incorrect |
| --- | --- | --- |
| veFXS | VeFxs | veFXS |
| some gOhm item | someGohmItem | someGOHMItem |
| XML HTTP request | XmlHttpRequest | XMLHTTPRequest |
| iOS | ios | IOS |
| new customer ID | newCustomerId | newCustomerID |
| inner stopwatch | innerStopwatch | innerStopWatch |
| supports IPv6 on iOS? | supportsIpv6OnIos | supportsIPv6OnIOS |
| YouTube importer | YouTubeImporter | YoutubeImporter* |
| gOHM ERC-20 | gohmErc20 | gOHMERC20 |
| some veFXS item | someVeFxsItem | someVeFXSItem |

## Code Structure / Architecture

Be thoughtful about breaking up code and inheriting.  One pattern used in fraxlend is a separation of view helper functions and the core logic.  This is a separation by threat model. 

Group related code together, be thoughtful about the order in which you would like to present information to a new reader.  For internal functions used in a single place, group them next to the external function they are used in.  If they are used in multiple places follow guidelines below.  

### Within a contract code should be in the following order:

1. storage variables
2. constructor
3. internals/helpers used in multiple places
4. external functions
5. custom errors
6. Events should be defined in the interface.

Avoid public functions, instead define an internal and external version of the function.  This allows optimizer to work better.

Explicitly assign default values to named return params for maximum readability

Empty catch blocks should have comments explaining why they are acceptable

Separate calculation and storage mutation functions when possible.  This aids in testing and separation of concerns, helps adhere to checks-effects-interactions patterns

Avoid mutations when possible, gas savings is not a good reason to mutate.  Write readable code then optimize gas when necessary. Makes debugging easier and codde understanding more clear.

Avoid modifiers, create internal functions in the form of _requireCondition() which will revert on failure.  This allows optimizer to reduce bytecode more efficiently, works better with IDE and analysis tools, and increases code intention without needing to jump to modifier definition.  Also order of execution is explicit.

Use custom errors over require statements, saves both byte code and gas

# Tests

Separate scenario setup and test functions.

Utilize fuzz tests

Use setUp() sparingly

Separate helpers to separate file

Create invariant assertions that can be used in multiple places

Dont sleep on echidna

## Pre-Audit Checklist

- 100% coverage
- Helper library to wrap functions which return tuples, use a double underscore __nameOfFunction convention
- Have harness contract to expose internal functions for unit testing if necessary
- Example deployment instructions
- Slither and Mythril instructions and remaining issues addressed
- Video walkthrough of each external function flow
- Access control documentation
- Threat modeling documentation
- Full natspec with slither @optional items included
    - Heavy commenting aimed at new reader
- No TODO, console.log, or commented out code