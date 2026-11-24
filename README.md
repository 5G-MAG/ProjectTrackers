# ProjectTrackers

Implementation boards of the 5G-MAG projects. The high-level roadmaps stay in [5G-MAG/Roadmaps](https://github.com/5G-MAG/Roadmaps).

- **Boards:** 5GMS and MBS (user services and system), both public. More projects will follow.
- **What is on a board:** features and tasks, drawn from the 3GPP specifications alone by the baseline workflow in [Standards2Deployments](https://github.com/5G-MAG/Standards2Deployments/tree/main/baseline). A **feature** is something the system offers, as it arrived in a release. A **task** is what one function must do for that feature in that release: the cases quoted from the specifications, how to read the full clause, and what has to be true for the card to move on. Tasks are sub-issues of their feature. A task is named **Feature: Function, Release N** (for example "Content hosting: Application Server (AS), Release 18"): there is one task for each feature, release and function, and the clauses it draws on are listed inside the card, not in its title. Functions that have only a few cases in a feature and release share one task, "other functions"; a task whose cases do not fit in one GitHub issue is cut at clause boundaries into parts ("part 1 of 2"). Everything starts as Not audited: the code is read by the audit, not by the filing.
- **Who moves the cards:** agents and people, by [the rules in AGENTS.md](AGENTS.md).
- **Who creates issues:** 5G-MAG only. The repository limits issue creation to collaborators.
- **Who can read:** everyone, with no account: the cards, the boards and these files are public (how to read a card and list the open tasks through the web API is in [AGENTS.md](AGENTS.md)).
- **Tests are required:** a card is not done without automated tests that carry the case ids, and where a repository has no test framework, adding one is part of the first card that touches it ([AGENTS.md](AGENTS.md#tests-are-required)).

## Finding work

The board views: Kanban (by Status), Ready to pick (Backlog, no assignee), Features and Tasks (tables: group them by Introduced in, to see what came with Rel-17, Rel-18, Rel-19 or Rel-20, or by Parent issue, to see the tasks under their feature, with the view menu). The fields: Status, Kind (Feature or Task), Part, Introduced in, Feature, Component (the function named by the specification), Document, Clause, Claimed by, Code repository (set by the audit) and Cases.

## Where the code is and how to build and test it

The audit looks for the code of a function here, and a task is built here. Each public repository has a **recipe** in [`recipes/`](recipes/): how to build it, run its tests, run it, exercise a feature, and what the repository does not say (read from the repository's own files, every fact cites its file). Read the recipe first, then the repository's own README (and `AGENTS.md` or `CLAUDE.md` where present); the toolkit skill `3gpp-implement` does the same. The audit report names the repository it read. For a private repository, an audit report gives the test name as evidence, not a file and line.

### 5GMS

| Function | What it is | Repository | Language and build | Tests | Recipe |
| --- | --- | --- | --- | --- | --- |
| AF | 5GMS Application Function | [rt-5gms-application-function](https://github.com/5G-MAG/rt-5gms-application-function) | C, meson | ABTS in `tests/msaf/`; no documented run command | [rt-5gms-application-function](recipes/rt-5gms-application-function.md) |
| AS | 5GMS Application Server | [rt-5gms-application-server](https://github.com/5G-MAG/rt-5gms-application-server) | Python, pyproject | none (`tests/` is a manual M3 client) | [rt-5gms-application-server](recipes/rt-5gms-application-server.md) |
| Client: Media Session Handler | Android service that talks to the AF | [rt-5gms-media-session-handler](https://github.com/5G-MAG/rt-5gms-media-session-handler) | Kotlin, Gradle (`./gradlew`) | JUnit 4, placeholder test only | [rt-5gms-media-session-handler](recipes/rt-5gms-media-session-handler.md) |
| Client: Media Stream Handler | Android player side | [rt-5gms-media-stream-handler](https://github.com/5G-MAG/rt-5gms-media-stream-handler) | Kotlin, Gradle | Gradle test tasks | [rt-5gms-media-stream-handler](recipes/rt-5gms-media-stream-handler.md) |
| Client: common library and application | Shared library and 5GMS-aware application | [rt-5gms-common-android-library](https://github.com/5G-MAG/rt-5gms-common-android-library), [rt-5gms-application](https://github.com/5G-MAG/rt-5gms-application) | Kotlin, Gradle | Gradle test tasks | [rt-5gms-common-android-library](recipes/rt-5gms-common-android-library.md)<br>[rt-5gms-application](recipes/rt-5gms-application.md) |
| App Provider | 5GMS Application Provider | [rt-5gms-application-provider](https://github.com/5G-MAG/rt-5gms-application-provider) | Python, Docker | see its README | [rt-5gms-application-provider](recipes/rt-5gms-application-provider.md) |
| Examples, mock AF | Docker set-up and an Express mock AF to exercise a feature | [rt-5gms-examples](https://github.com/5G-MAG/rt-5gms-examples) | JavaScript, Docker | exercise runs | [rt-5gms-examples](recipes/rt-5gms-examples.md) |

### MBS (user services and system)

| Function | What it is | Repository | Language and build | Tests | Recipe |
| --- | --- | --- | --- | --- | --- |
| MBSF | MBS Function (with the MBS Application Function) | [rt-mbs-function](https://github.com/5G-MAG/rt-mbs-function) | C++, meson | none registered today (see the recipe) | [rt-mbs-function](recipes/rt-mbs-function.md) |
| MBSTF | MBS Transport Function | [rt-mbs-transport-function](https://github.com/5G-MAG/rt-mbs-transport-function) | C++, meson | `meson test`, `tests/` | [rt-mbs-transport-function](recipes/rt-mbs-transport-function.md) |
| FLUTE | FLUTE protocol and profiles used by the MBSTF | [rt-libflute](https://github.com/5G-MAG/rt-libflute) | C++, CMake | `ctest`, `tests/` (has `AGENTS.md`) | [rt-libflute](recipes/rt-libflute.md) |
| MBS Client | MBS client | rt-mbs-client (private) | see its README | see its README | none (private) |
| MBS-Aware App | MBS-aware application | rt-mbs-application (private) | see its README | see its README | none (private) |
| Provider | MBS Application Provider | rt-mbs-application-provider (private) | see its README | see its README | none (private) |
| Examples | Docker set-up, mock media server, scripts to exercise a feature | [rt-mbs-examples](https://github.com/5G-MAG/rt-mbs-examples) | Shell, Docker | `test/` (has `CLAUDE.md`) | [rt-mbs-examples](recipes/rt-mbs-examples.md) |

The system part names functions of the core network and the radio access network. Their reference code:

| Function | Repository | Language and build | Recipe |
| --- | --- | --- | --- |
| Core network functions (AMF, SMF, UPF, NEF, PCF and the MB-SMF and MB-UPF) | [open5gs](https://github.com/5G-MAG/open5gs) | C, meson, `tests/` | none yet |
| gNB | srsRAN_Project_mbs (private) | C++, CMake | none (private) |
| UE | srsRAN_4G_mbs (private) | C++, CMake, CTest | none (private) |

### 5G Broadcast (LTE-based, TV and radio services and Emergency Alerts)

Read from the Website's pages for these tools; the repositories have no recipe yet because they were not read for this board. The audit and the first pull request add the build and test facts.

| Function | What it is | Repository |
| --- | --- | --- |
| E-UTRAN (eNB) | Transmitter: an srsRAN-based eNodeB with FeMBMS; no M2 interface and no MCE | [rt-mbms-tx](https://github.com/5G-MAG/rt-mbms-tx), [rt-mbms-tx-for-qrd-and-crd](https://github.com/5G-MAG/rt-mbms-tx-for-qrd-and-crd) (also the eNodeB of Emergency Alerts) |
| MBMS-GW | MBMS gateway | [rt-mbms-gw](https://github.com/5G-MAG/rt-mbms-gw) |
| BM-SC | Broadcast Multicast Service Centre | [rt-mbms-bmsc](https://github.com/5G-MAG/rt-mbms-bmsc) |
| UE | Receiver modem | [rt-mbms-modem](https://github.com/5G-MAG/rt-mbms-modem) |
| MBMS Client | FLUTE reassembly, service announcement, HTTP | [rt-mbms-client](https://github.com/5G-MAG/rt-mbms-client) |
| MBMS Application | Monitoring application, and the PWS client of Emergency Alerts | [rt-mbms-application](https://github.com/5G-MAG/rt-mbms-application) |
| Content Provider | xMB-C client and SBc-AP origination | [rt-mbms-application-provider](https://github.com/5G-MAG/rt-mbms-application-provider) |
| FLUTE | FLUTE library | [rt-libflute](https://github.com/5G-MAG/rt-libflute) ([recipe](recipes/rt-libflute.md)) |
| CBC, PWS | Cell Broadcast Centre (Emergency Alerts) | [rt-pws-cbc](https://github.com/5G-MAG/rt-pws-cbc) |
| Examples | Examples for 5G Broadcast | [rt-mbms-examples](https://github.com/5G-MAG/rt-mbms-examples) |

The MME and the MCE have no repository.

### RTC (5G-RTC and SWAP)

There is no dedicated RTC tool yet. The only code is [rt-3gpp-swap](https://github.com/5G-MAG/rt-3gpp-swap): the SWAP reference implementation (a Node.js signalling server and a client library), which stands for the WebRTC Signalling Function. The RTC Application Provider, RTC AF and AS, Media Session Handler, Access Function, ICE function and the others have no repository: a task for them starts from the specification, with mocks for the peers.

### Which repository a card names

Each task card carries a block "Where to build, test and exercise" with the repositories of its functions. It is written from [`code.yaml`](code.yaml) (this README's tables, the recipes and the Website's pages; no repository is read to write it). A function that `code.yaml` does not list has no repository known on that board.

## Tools, and how to read a card

The commands `3gpp-clause`, `3gpp-implement` and `3gpp-verify` come from the [3gpp-toolkit](https://github.com/5G-MAG/Standards2Deployments/tree/main/3gpp-toolkit): `git clone https://github.com/5G-MAG/Standards2Deployments.git`, then `./setup.sh` in `3gpp-toolkit/`. Read a card with `gh issue view <number> -R 5G-MAG/ProjectTrackers --json title,body,labels,state,assignees` (the plain form can fail with a Projects (classic) error on some versions of `gh`) or `gh api repos/5G-MAG/ProjectTrackers/issues/<number>`. Pull requests go against the branch the recipe of the repository names under Conventions for contributors (for most 5G-MAG repositories `development`); the audit reads `main`.

A function with no repository here (a standard function nobody has implemented yet) has none to build in: the audit says so, and the task starts from the specification.

## The board fields

Status (the stages in AGENTS.md), Kind (Feature or Task), Part, Introduced in, Feature, Component (the function the specification names), Document, Clause, Claimed by, Code repository (set by the audit) and Cases.
