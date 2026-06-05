# Design In Practice UI

A single-page viewer for Rich Hickey-style design decision matrices, using
plain XML or YAML instead of a Google spreadsheet.

Try it here:

https://bmillare.github.io/design_in_practice_ui/

## Motivation

Rich Hickey's Design in Practice talk argues that design work should produce
visible artifacts that help a team think: problem statements, use cases,
decision matrices, questions, tradeoffs, and the reasons behind decisions.
The decision matrix is especially useful because it puts approaches and
criteria next to each other, making contrast easy.

The talk recommends live-editing spreadsheets for this because prose documents
linearize the work and make comparison difficult. Spreadsheets are good for
people working together in real time, but they are not ideal as a durable data
format:

- they are awkward to edit in plain text
- they are awkward to generate or revise with LLMs
- they hide structure behind a UI
- they do not fit naturally into source control
- they make it harder to copy, diff, review, and reuse design artifacts

This project keeps the decision matrix idea, but represents it as structured
text. DIP-XML and DIP-YAML are intended to be easy for both people and LLMs to
write, edit, generate, review, and version. The web page then renders that data
back into the visual matrix form that makes the technique useful.

## What It Does

Open `index.html` in a browser, or use the hosted page, then paste DIP-XML or
DIP-YAML. Parsing runs as you type and renders:

- the A1 problem statement
- user intentions with a blank "How" column
- approaches, including the required `status_quo`
- criteria
- matrix cells aligned for contrast
- color-coded subjective assessment
- questions found by scanning for `?`
- warnings for missing or invalid structure
- out-of-scope items

The whole tool is a single HTML file. DIP-XML is the default format and uses
the browser's built-in XML parser. DIP-YAML is available from the format
selector and uses `js-yaml` from a CDN.

## Data Model

A useful decision matrix captures:

- `problem`: the problem or decision being taken on
- `intentions`: what users want to accomplish, not how they will do it
- `out_of_scope`: explicit boundaries
- `approaches`: candidate strategies or designs, with `status_quo` first
- `criteria`: the things that matter when deciding
- `matrix`: one aspect per approach per criterion
- `color`: subjective assessment of a cell

Supported colors:

- `white`: neutral
- `green`: particularly desirable or better
- `yellow`: tradeoff, challenge, or possible negative
- `red`: blocking or failing to address the problem

## DIP-XML Example

```xml
<dip>
  <problem>
    Users are abandoning checkout because it is slow and asks them to
    re-enter data they already provided.
  </problem>

  <intentions>
    <intention>I wish I could check out without reaching for my wallet.</intention>
  </intentions>

  <out_of_scope>
    <item>Replacing the payment processor.</item>
  </out_of_scope>

  <approaches>
    <approach id="status_quo">Standard multi-page web form</approach>
    <approach id="spa_modal">Single-page checkout modal</approach>
  </approaches>

  <criteria>
    <criterion id="latency">User waiting time between actions</criterion>
    <criterion id="dev_effort">Development and testing effort</criterion>
  </criteria>

  <matrix>
    <row criterion="latency">
      <cell approach="status_quo" color="yellow">Full page reload per step.</cell>
      <cell approach="spa_modal" color="green">Instant UI updates; API calls run in the background.</cell>
    </row>
    <row criterion="dev_effort">
      <cell approach="status_quo" color="green">No new effort.</cell>
      <cell approach="spa_modal" color="red">Requires rewriting validation logic.</cell>
    </row>
  </matrix>
</dip>
```

## DIP-YAML Example

```yaml
problem_statement: >
  Users are abandoning checkout because it is slow and asks them to
  re-enter data they already provided.

intentions:
  - I wish I could check out without reaching for my wallet.

out_of_scope:
  - Replacing the payment processor.

approaches:
  status_quo: Standard multi-page web form
  spa_modal: Single-page checkout modal

criteria:
  latency: User waiting time between actions
  dev_effort: Development and testing effort

matrix:
  latency:
    status_quo:
      text: Full page reload per step.
      color: yellow
    spa_modal:
      text: Instant UI updates; API calls run in the background.
      color: green
  dev_effort:
    status_quo:
      text: No new effort.
      color: green
    spa_modal:
      text: Requires rewriting validation logic.
      color: red
```

## References

- Rich Hickey, Design in Practice, Clojure Conj 2023:
  https://www.youtube.com/watch?v=c5QF2HjHLSE
- Design in Practice slides:
  https://download.clojure.org/presentations/DesignInPractice.pdf
- Clojure Conj 2023 event page:
  https://clojure.org/events/2023/clojure-conj-2023--1880831835
- Rich Hickey, Hammock Driven Development:
  https://github.com/matthiasn/talk-transcripts/blob/master/Hickey_Rich/HammockDrivenDev-mostly-text.md
- Stuart Halloway, Debugging with the Scientific Method:
  https://github.com/matthiasn/talk-transcripts/blob/master/Halloway_Stuart/DebuggingWithTheScientificMethod.md
- `js-yaml`, used for browser-side YAML parsing:
  https://github.com/nodeca/js-yaml
