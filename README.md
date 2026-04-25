# flymake-markdownlint-cli2 (fork)

This repository is a fork of the original project:

👉 https://github.com/ewilderj/flymake-markdownlint-cli2

## Overview

This package provides a Flymake backend for running `markdownlint-cli2` in Emacs.

However, the original implementation does not correctly parse the output format of recent versions of `markdownlint-cli2`, which results in diagnostics not being displayed in Emacs.

This fork fixes that issue.

---

## What was fixed

### Problem

The original implementation assumes the following output format:

```
stdin:LINE[:COLUMN] RULE
```

However, recent versions of `markdownlint-cli2` output messages like:

```
README.md:72 error MD001/heading-increment Heading levels should only increment by one level at a time
```

Key differences:

* Uses actual file names instead of `stdin`
* Includes severity (`error` / `warning`)
* Format does not match the original regex

Because of this mismatch, Flymake fails to parse any diagnostics.

---

### Fix

The parsing logic (regular expression) was updated to match the current output format of `markdownlint-cli2`.

#### Before

```
^\\(stdin\\):\\([0-9]+\\):?[0-9]* \\([A-Z]+[0-9]+/.*\\)$
```

#### After

```
^\\([^:]+\\):\\([0-9]+\\) \\(error\\|warning\\) \\([A-Z]+[0-9]+/[^ ]+\\) \\(.*\\)$
```

This allows:

* Proper file name handling
* Severity recognition
* Accurate diagnostic extraction

---

## Why this fork exists

The original repository appears to target an older or different output mode of `markdownlint-cli2`, and has not been updated to support the current default format.

This fork was created to:

* Restore compatibility with modern `markdownlint-cli2`
* Enable correct Flymake diagnostics
* Avoid requiring workarounds such as output rewriting

---

## Installation

(Describe your preferred installation method here: straight.el, manual clone, etc.)

Example (straight.el):

```elisp
(straight-use-package
 '(flymake-markdownlint-cli2
   :type git
   :host github
   :repo "tamajio/flymake-markdownlint-cli2"))
(add-hook 'markdown-mode-hook 'flymake-mode)
(add-hook 'markdown-mode-hook 'flymake-markdownlint-cli2-setup)
```

---

## Notes

* This fork focuses only on fixing parsing issues.
* No additional features have been added.
* Behavior should remain consistent with the original package aside from the fix.

---

## License

Same as the original project.
