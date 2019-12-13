<div class="cwikmeta">
{
"title": "CWIK"
} </div>

## Overview

*Cwik* (pronounced 'quick') is an editable documentation web site (wiki) that is quite different from what has been done before:

 - All pages are stored in an enhanced Markdown format
 - Pages are stored in a source control system (git)
 - Edits are signed by cryptocurrency tokens
 - Integrated with source code documentation generators

## Enhanced Markdown
Here are some examples:

### Math
[Full Documentation](https://katex.org/docs/supported.html)
The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral
$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

### Flowchart
[Full Documentation](https://mermaidjs.github.io/#/flowchart)
```mermaid
graph LR
A[Square Rect] -- paren type controls shape --> B((Circle))
A --> C(Round Rect)
B --> D{Rhombus}
C --> D
```

```mermaid
graph BT
A["bottom"] == Link text ==> B[TOP]
style B fill:#906,stroke:#333,stroke-width:2px;
style A fill:#ff6,stroke:#333,stroke-width:8px;
```
```mermaid
graph RL
    %% Comments after double percent signs
    A(("(){}[]"))-->B>";place special chars in quotes"]
```

### Protocol (Sequence) diagram
[Full Documentation](https://mermaidjs.github.io/#/sequenceDiagram)
```mermaid
sequenceDiagram
  Alice ->> Bob: Hello Bob, how are you?
  Bob-->>John: How about you John?
  Bob--x Alice: I am good thanks!
  Bob-x John: I am good thanks!
  Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

  Bob-->Alice: Checking with John...
  Alice->John: Yes... John, how are you?
```

## Git storage

Your changes accumulate in local storage on the server.  Press the "commit" button (upper right) to commit all edits.

Look here: https://github.com/bitcoin-unlimited/BUwiki to see the backing repository.

## Token Integration
TBD

## Source Documentation Generation
**To be implemented**

This system is able to integrate multiple Git-hosted repositories.  A typical configuration would be one documentation focused repository and multiple source repositories.  These source repositories can provide inline documentation that is extracted via various documentation generators (subsequently called "codedoc") and provide access to "raw" source code.

Linking between documentation and code/codedoc or codedoc to codedoc occurs via a special syntax that allows references to code blocks, is tolerant of some code reorganizations, and identifies and records broken links for human repair.

There is a processing step to convert these links to .md format.  So the full source data preparation steps are:

```mermaid
graph TB
A["git source code"] == doc generator ==> B["code doc"]
D["git doc files"] ==> E(("Link resolver"))
A ==> E
B ==> E
E ==> F["Markdown files"]
E ==> G["broken link report<br/>(markdown file)"]
style A fill:#ff6,stroke:#333,stroke-width:8px;
style D fill:#ff6,stroke:#333,stroke-width:8px;
style F fill:#3A3,stroke:#334,stroke-width:4px;
style G fill:#3A3,stroke:#334,stroke-width:4px;
```