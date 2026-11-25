# How agents work on these boards

Agents of several people move the cards on the 5G-MAG boards. People can follow the same rules. There are two kinds of card:

- **Feature**: something the system offers (for example "Object Repair mechanism for FLUTE"), as it arrived in a release. It says what the feature is, where the specifications describe it, which Work Items and functions are involved. It has no cases of its own and is not worked on directly: it is Supported when all its tasks are, and the sync tool sets that.
- **Task**: the cases one function must meet for one feature in one release, quoted from the specifications. This is what you claim and move. Each task is a sub-issue of its feature.

Read a card with `gh issue view <number> -R 5G-MAG/ProjectTrackers --json title,body,labels,state,assignees` (the plain form can fail on some versions of `gh`). Where the code is, and how to install the toolkit, is in [README.md](README.md#where-the-code-is-and-how-to-build-and-test-it).

**Reading needs no account.** Every card is public: `https://api.github.com/repos/5G-MAG/ProjectTrackers/issues/<number>` gives it as JSON (the `body` is the whole card, the `labels` say the board and the release); the open tasks of a board are `https://api.github.com/repos/5G-MAG/ProjectTrackers/issues?labels=project:5gms,type:task&state=open&assignee=none&per_page=100` (the labels `project:5gms`, `project:5g-mbs`, `project:5g-broadcast` and `project:rtc` name the boards; add `&page=2` for more); these files are at `https://raw.githubusercontent.com/5G-MAG/ProjectTrackers/main/<file>`; the boards open in a browser. Assigning a card to yourself, commenting on it and moving it on the board may need an account that a 5G-MAG maintainer has given access (the repository limits who can create issues, and only members with write access change a board). If you cannot, do the work in the code repository, link the card in the pull request, put the audit report in the description of that pull request, and ask a maintainer to copy it to the card and move the card. Two things to know when you read: the field `status` that the issue API shows (Backlog on every open issue) is GitHub's own default and is not the board Status, which is only on the board page; and anonymous API calls are limited to 60 an hour, so read one card at a time and fetch files from `raw.githubusercontent.com`.

**The toolkit and the repositories are not all public.** The 3gpp-toolkit (`3gpp-clause`, `3gpp-implement`, `3gpp-verify`) lives in the private 5G-MAG repository Standards2Deployments, and some code repositories are private (a card says `(private)` next to them). Where you cannot open one, read the clause in the specification itself (each card links the public 3GPP archive of its specifications), and ask a maintainer to run `3gpp-verify --strict` on your pull request.

Everything is drawn from the specifications alone (through the 3gpp-toolkit) and starts as Not audited. The board column (Status) says where a task stands; the field Introduced in says the release its cases came with, so "what is new in Rel-19" is a filter.

## The statuses, and who may move a card

| Status | Meaning | Set by | Next |
| --- | --- | --- | --- |
| Not audited | Filed from the specification. Nothing is known yet about the code | the filing tool | the audit moves it to Backlog, Verifying or Needs review |
| Backlog | The audit found a gap: a case is Absent, Differs from the specification, or the specification is inconsistent | the audit | In progress |
| In progress | Someone is building it | whoever claimed it | In review, or back to Backlog when the claim is released |
| In review | A pull request is open | the author, when opening it | Verifying, or back to In progress |
| Verifying | Every case reads as Implemented (by the audit, or after a merge). Waiting for the cross-check | the auditor or the author | Supported, or back to In progress or Backlog |
| Supported | Cross-checked by someone other than the author, and read as implemented on `main`. The issue is closed | the verifier | none |
| Needs review | The readers could not settle it. A person decides | the audit | a person only |
| On hold | Parked | a person | a person only |

## Audit (Not audited)

1. Pick a task with the Status Not audited and no assignee. Take the feature or function you were asked to work on. Claim it (see Claims below) with the comment `auditing`. If someone else is already assigned, pick another task.
2. Read the clause at the pinned version with the command in the issue. The sentences in the table are quotes, not the whole clause.
3. Find the code of the component in the repositories listed under [Where the code is](README.md#where-the-code-is-and-how-to-build-and-test-it), at the head of `main`. Judge each case:
   - **Implemented**: the code does what the case says.
   - **Differs from the specification**: the code does something, and it is not what the case says.
   - **Absent**: nothing in the code does it.
   - **Specification inconsistent**: the case cannot be built as written (it contradicts another clause or itself). Say which clause.
   - **Not applicable**: the case does not fall on this function (the statement was placed here by the reading rules by mistake), or it is a `may` case that the feature does not need and nobody implements (say Not applicable (optional)). Say why. A person checks the first kind; neither counts as a gap.
4. Post the audit report (below). Then set the Status: any Absent, Differs or Inconsistent case, Backlog; every case Implemented (or Not applicable), Verifying; a case you cannot settle, Needs review.

A code reading is not a run. Say in the report which cases you also ran, and which you only read.

When the card names no repository for the function (the board README and `code.yaml` list none), there is no code to read: post the report with `Code: none, no repository for <function>`, give every case the verdict Absent with the evidence "no repository", and set Backlog. Do not invent a repository; a person decides where the code lives. When a repository has no test framework (its recipe says so), a case has no existing test to cite: the evidence is the source line, and the build adds the first test and says how to run it.

### The audit report

One comment on the issue, in this form. It is what the Website reads for the case table of this requirement.

````
Audit of #<issue>, <specification> <version> clause <x.y.z>, function <the function the card names>
Code: <repository>@<commit> (main), read on <date>. Run or read only: <which>.

| Case id | Verdict | What the code does | Evidence |
| --- | --- | --- | --- |
| <id> | Implemented / Differs from the specification / Absent / Specification inconsistent / Not applicable | <one sentence, in plain words> | <path:line in a public repository, or the test name> |

Decision: <Backlog | Verifying | Needs review>. Why: <one line>.
````

Evidence is the file and line (or the test) in a public 5G-MAG repository. For a private repository give the test name only. Everything on this board is public.

## Cases that do not ask for behaviour

- **lead-in**: a sentence that ends with a colon and only introduces the cases under it. It has no test of its own and is not ticked.
- **describes**: the specification states behaviour as a fact. Test the observable it implies, and write that observable in the test's first line.
- **may**: optional. Implement it only if the feature needs it; otherwise report it Not applicable (optional). A `may` case counts as needed when the feature cannot work without it (for example the operation an API exists to offer): then it is built and tested like a `shall`. It counts as ticked and never stops a card from reaching Supported. A `may` case you do implement has a test like any other.
- **Function column**: the function the sentence names (blank: it names none, so it falls on the function of the card by its clause, unless it reads as a peer's duty). A sentence that falls on a peer (a consumer, a client, a provider, the other side of an interface) is not this function's duty: report it Not applicable, with the clause, and do not build it here.

## Tests are required

Every case that asks for behaviour (`shall`, `should`, a `may` you implement, and the observable of a `describes` case) is covered by an automated test that carries the case id in its name. A card is not done without those tests, and they pass in the pull request.

- Write the test first: watch it fail for the right reason (the behaviour is missing, not the setup), then implement until it passes. Put the case id in the test name in the style of the language, for example `test_<id>_<what it checks>` (Python), `it('<id>: <what it checks>')` (JavaScript) or `fun test_<id>_<what_it_checks>()` (Kotlin); a verifier finds the test by that id.
- If the repository has no test framework, adding the smallest one that fits its language, with one documented command that runs it, is part of the first card that is claimed for the repository. Other cards of that repository build on its pull request (or wait for it) and do not add a second framework. Say so in the pull request and put the command in the repository's README. Do not wait for another card, and do not skip the tests because the repository has none.
- The one exception is a case that cannot be tested automatically (it needs a device, a radio or a person). The pull request then says why, gives the manual steps and their result, and the verifier repeats them.
- A card that only adds tests for behaviour that already exists is valid work and moves like any other.
- The verifier runs the tests itself on the base and the head commit (see Cross-check); a case without a test is not ticked.

## Build (Backlog)

1. Pick a task with the Status Backlog, no assignee, and not On hold. Claim it (see Claims below), set the Status to In progress. If someone else is already assigned, pick another task. The first assignee keeps it.
2. Read the whole issue and the audit report. Implement each case that is Absent or Differs, in the repository named in the audit report, with one test per case (see Tests are required above; where the repository has no test framework, adding one is part of this card).
3. Exercise the feature as the issue says (the flow of the clause, between the function and its peers, against the reference implementations or mocks) and attach the log and the messages exchanged to the pull request.
4. Open the pull request (one in each repository when the cases fall in several; each links the issue), quote each clause as `Basis: <specification> <version> clause <x.y.z>: "<sentence>"`, name the test that covers each case, run `3gpp-verify --commits <base>..HEAD --strict`, and set the Status to In review.
5. When it is merged (all of them, when there are several), set the Status to Verifying. The pull request goes against the branch the recipe of the repository names under Conventions for contributors (for most 5G-MAG repositories `development`); the audit reads `main`.

## Claims

Several people run agents on the same board, so a claim says whose it is. Assign the task to the GitHub account the agent runs under and comment `claimed by <person>, agent <name or run>`. Set the field Claimed by to the same words. A claim with no activity (comment, commit or status change) for 7 days may be released by anyone: comment `released: no activity since <date>`, remove the assignee, and set the Status back (In progress to Backlog). One agent holds at most 3 claims at a time.

## Cross-check (Verifying)

Someone other than the author of the code or of the audit report does this; where possible an agent run by a different person. Re-read each case against the clause at the pinned version. Run the named tests on the base and the head commit and check that each fails without the change and passes with it; for a case the audit found Implemented, check the evidence in the report and run the existing tests that cover it. Run `3gpp-verify --commits <base>..HEAD --strict` on the pull request, if there is one. Then tick the cases in the issue (a `may` case that nobody implements counts as ticked when it is reported Not applicable (optional)), post the verification report, set the Status to Supported and close the issue. If a case does not hold, comment why and set the Status back to In progress (or Backlog if nobody is building it).

### The verification report

The cross-check ends with a comment on the issue, from the verifier, in this form. It is the evidence for the move to Supported. A card without it does not move.

````
Verification of #<issue>, <specification> <version> clause <x.y.z>
Base: <commit>. Head: <commit>. Pull request: <link, or none when the audit found every case Implemented>.

| Case id | Test | Fails on base | Passes on head | Clause re-read |
| --- | --- | --- | --- | --- |
| <id> | <test name> | yes: <command, result> | yes: <command, result> | yes, at <version> |

Cases the audit found Implemented: evidence checked and existing tests pass: <command, result>.
3gpp-verify --commits <base>..HEAD --strict: <result, or not applicable>.
Decision: Supported. (or: back to In progress, cases <ids> do not hold because <reason>.)
````

The verifier runs the tests itself. A report that only repeats what the author or the auditor wrote is not a verification.

## Rules

- Never work on a feature card: move its tasks. Never edit the machine block under **For an agent** or the table of cases. Comment instead. The tick list is ticked only by the verifier.
- Never cross-check your own work, and never set Supported on it. The auditor and the verifier of a card are not the same agent.
- Never move a card into or out of On hold. Needs review is set by the audit; only a person moves a card out of it.
- If an issue looks wrong (the specification says something else, or a case does not apply), comment with the clause and the reason and leave the Status. A person decides.
- Write each status change as a comment: `Status: <from> to <to>. Why: <one line>. Evidence: <audit report, pull request, test names, commit>`.
- Everything here is public. No secrets, no personal data, and no specification text beyond the quoted sentences.
