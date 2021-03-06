[![MELPA](https://melpa.org/packages/lsp-java-badge.svg)](https://melpa.org/#/lsp-java)
[![Build Status](https://travis-ci.com/emacs-lsp/lsp-java.svg?branch=master)](https://travis-ci.com/emacs-lsp/lsp-java)

Java support for lsp-mode using the [Eclipse JDT Language Server](https://projects.eclipse.org/projects/eclipse.jdt.ls).

## Features
LSP java mode supports the following JDT Features:
* As you type reporting of parsing and compilation errors (via [flycheck](https://github.com/flycheck/flycheck)/[lsp-ui](https://github.com/emacs-lsp/lsp-ui))
* Code completion - using [company-lsp](https://github.com/tigersoldier/company-lsp) or builtin ```complete-at-point```
* Javadoc hovers - using [lsp-ui](https://github.com/emacs-lsp/lsp-ui)
* Code actions - using [lsp-ui](https://github.com/emacs-lsp/lsp-ui)
* Code outline - using builtin [imenu](https://www.gnu.org/software/emacs/manual/html_node/emacs/Imenu.html)
* Code navigation - using builtin [xref](https://www.gnu.org/software/emacs/manual/html_node/emacs/Xref.html)
* Code lens (references/implementations) - using builtin [xref](https://www.gnu.org/software/emacs/manual/html_node/emacs/Xref.html)
* Highlights
* Code formatting
* Maven pom.xml project support
* Limited Gradle support
* Visual debugger - [dap-mode](https://github.com/yyoncho/dap-mode/)
* Test runner - [dap-mode](https://github.com/yyoncho/dap-mode/)

## Installation
### Spacemacs
[lsp-java](https://github.com/emacs-lsp/lsp-java) is included in spacemacs (for now only on the dev branch). If you are using the development version of
spacemacs you can simply add `(java :variables java-backend 'lsp)` to `dotspacemacs-configuration-layers`.

### Install via melpa
The recommended way to install LSP Java is via `package.el` - the built-in package
manager in Emacs. LSP Java is available on the two major `package.el` community
maintained repos - [MELPA Stable](http://stable.melpa.org) and [MELPA](http://melpa.org).

<kbd>M-x</kbd> `package-install` <kbd>[RET]</kbd> `lsp-java` <kbd>[RET]</kbd>

Then add the following lines to your `.emacs` and open a file from the any of the specified projects.
```emacs-lisp
(require 'lsp-java)
(add-hook 'java-mode-hook #'lsp-java-enable)

;; set the projects that are going to be imported into the workspace.
(setq lsp-java--workspace-folders (list "/path/to/project1"
                                        "/path/to/project2"
                                        ...))
```
### [Eclipse JDT Language Server](https://projects.eclipse.org/projects/eclipse.jdt.ls)

[lsp-java](https://github.com/emacs-lsp/lsp-java) will automatically detect when the server is missing and it will download [Eclipse JDT Language Server](https://projects.eclipse.org/projects/eclipse.jdt.ls) before the first startup. The server installation will be in `lsp-java-server-install-dir`. It will detect whether [dap-mode](https://github.com/yyoncho/dap-mode/) is present and it will download the required server side plugins/components. If you want to update the server you can run `lsp-java-update-server`. To run specific version of [Eclipse JDT Language Server](https://projects.eclipse.org/projects/eclipse.jdt.ls) use `lsp-java-server-install-dir`.

#### Quick start
Minimal configuration with [company-lsp](https://github.com/tigersoldier/company-lsp) and [lsp-ui](https://github.com/emacs-lsp/lsp-ui). Make sure you have replaced the XXX placeholder with the list of the projects you want to import.
```elisp
(require 'cc-mode)

(condition-case nil
    (require 'use-package)
  (file-error
   (require 'package)
   (add-to-list 'package-archives '("melpa" . "http://melpa.org/packages/"))
   (package-initialize)
   (package-refresh-contents)
   (package-install 'use-package)
   (require 'use-package)))

(use-package lsp-mode
  :ensure t
  :init (setq lsp-inhibit-message nil ; you may set this to t to hide messages from message area
              lsp-eldoc-render-all nil
              lsp-highlight-symbol-at-point nil))

(use-package company-lsp
  :after  company
  :ensure t
  :config
  (add-hook 'java-mode-hook (lambda () (push 'company-lsp company-backends)))
  (setq company-lsp-enable-snippet t
        company-lsp-cache-candidates t)
  (push 'java-mode company-global-modes))

(use-package lsp-ui
  :ensure t
  :config
  (setq lsp-ui-sideline-enable t
        lsp-ui-sideline-show-symbol t
        lsp-ui-sideline-show-hover t
        lsp-ui-sideline-show-code-actions t
        lsp-ui-sideline-update-mode 'point))

(use-package lsp-java
  :ensure t
  :requires (lsp-ui-flycheck lsp-ui-sideline)
  :config
  (add-hook 'java-mode-hook  'lsp-java-enable)
  (add-hook 'java-mode-hook  'flycheck-mode)
  (add-hook 'java-mode-hook  'company-mode)
  (add-hook 'java-mode-hook  (lambda () (lsp-ui-flycheck-enable t)))
  (add-hook 'java-mode-hook  'lsp-ui-sideline-mode)
  (setq lsp-java--workspace-folders (list (error "XXX Specify your projects here"))))
```
## Supported commands
### LSP Mode commands
  | Command name | Description |
  |-----------------------------|--------------------------------------------------------------|
  | lsp-execute-code-action     | Execute code action.                                         |
  | lsp-rename                  | Rename symbol at point                                       |
  | lsp-describe-thing-at-point | Display help for the thing at point.                         |
  | lsp-goto-type-definition    | Go to type definition                                        |
  | lsp-goto-implementation     | Go to implementation                                         |
  | lsp-workspace-restart       | Restart project                                              |
  | lsp-format-buffer           | Format current buffer                                        |
  | lsp-symbol-highlight        | Highlight all relevant references to the symbol under point. |
### LSP Java commands

  | Command name                          | Description                                                   |
  |---------------------------------------|---------------------------------------------------------------|
  | lsp-java-organize-imports             | Organize imports                                              |
  | lsp-java-build-project                | Perform partial or full build for the projects                |
  | lsp-java-update-project-configuration | Update project configuration                                  |
  | lsp-java-actionable-notifications     | Resolve actionable notifications                              |
  | lsp-java-update-user-settings         | Update user settings (Check the options in the table bellow.) |
  | lsp-java-update-server                | Update server instalation.                                    |
#### Refactoring

LSP Java provides rich set of refactorings via [Eclipse JDT Language
Server](https://projects.eclipse.org/projects/eclipse.jdt.ls) code actions and
some of them are bound to Emacs commands:

  | Command name                       | Description                  |
  |------------------------------------|------------------------------|
  | lsp-java-extract-to-constant       | Extract constant refactoring |
  | lsp-java-add-unimplemented-methods | Extract constant refactoring |
  | lsp-java-create-parameter          | Create parameter refactoring |
  | lsp-java-create-field              | Create field refactoring     |
  | lsp-java-create-local              | Create local refactoring     |
  | lsp-java-extract-method            | Extract method refactoring   |
  | lsp-java-add-import                | Add missing import           |
## Supported settings
  | Setting                               | Description                                                                                                     |
  |---------------------------------------|-----------------------------------------------------------------------------------------------------------------|
  | lsp-java-server-install-dir           | Install directory for eclipsejdtls-server                                                                       |
  | lsp-java-java-path                    | Path of the java executable                                                                                     |
  | lsp-java-workspace-dir                | LSP java workspace directory                                                                                    |
  | lsp-java-workspace-cache-dir          | LSP java workspace cache directory                                                                              |
  | lsp-java--workspace-folders           | LSP java workspace folders storing files downloaded from JDT                                                    |
  | lsp-java-vmargs                       | Specifies extra VM arguments used to launch the Java Language Server                                            |
  | lsp-java-incomplete-classpath         | Specifies the severity of the message when the classpath is incomplete for a Java file                          |
  | lsp-java-update-build-configuration   | Specifies how modifications on build files update the Java classpath/configuration                              |
  | lsp-java-import-exclusions            | Configure glob patterns for excluding folders                                                                   |
  | lsp-java-favorite-static-members      | Defines a list of static members or types with static members                                                   |
  | lsp-java-import-order                 | Defines the sorting order of import statements                                                                  |
  | lsp-java-trace-server                 | Traces the communication between Emacs and the Java language server                                             |
  | lsp-java-enable-file-watch            | Defines whether the client will monitor the files for changes                                                   |
  | lsp-java-format-enabled               | Specifies whether or not formatting is enabled on the language server                                           |
  | lsp-java-format-settings-url          | Specifies the file path to the formatter xml url                                                                |
  | lsp-java-format-settings-profile      | Specifies the formatter profile name                                                                            |
  | lsp-java-format-comments-enabled      | Preference key used to include the comments during the formatting                                               |
  | lsp-java-save-action-organize-imports | Organize imports on save                                                                                        |
  | lsp-java-organize-imports             | Specifies whether or not organize imports is enabled as a save action                                           |
  | lsp-java-bundles                      | List of bundles that will be loaded in the JDT server                                                           |
  | lsp-java-import-gradle-enabled        | Enable/disable the Gradle importer                                                                              |
  | lsp-java-import-maven-enabled         | Enable/disable the Maven importer                                                                               |
  | lsp-java-auto-build                   | Enable/disable the 'auto build'                                                                                 |
  | lsp-java-progress-report              | [Experimental] Enable/disable progress reports from background processes on the server                          |
  | lsp-java-completion-guess-arguments   | When set to true, method arguments are guessed when a method is selected from as list of code assist proposals. |
## Screenshot
![demo](images/demo.png)
## Additional packages
* [lsp-ui](https://github.com/emacs-lsp/lsp-ui) : Flycheck, documentation and code actions support.
* [company-lsp](https://github.com/tigersoldier/company-lsp) : LSP company backend.
## FAQ
* LSP Java is showing to many debug messages, how to stop that?

Add the following configuration.
```emacs-lisp
(setq lsp-inhibit-message t)
```
* [lsp-ui](https://github.com/emacs-lsp/lsp-ui) does not display all of the actions on the current point(e. g "Extract constant")?

LSP UI by default sends current line bounds for action region which breaks forces JDT server to return only "Extract method action."
```emacs-lisp
(setq lsp-ui-sideline-update-mode 'point)
```
* LSP Java does not provide completion, go to definition for some of the files?

Make sure the project is properly imported according to instructions. When particular file is not part of imported project [Eclipse JDT Language Server](https://projects.eclipse.org/projects/eclipse.jdt.ls) could not calculate the current classpath.
